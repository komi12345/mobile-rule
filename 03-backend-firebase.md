# Guide Backend - Solution Firebase

---
inclusion: manual
---

## 📊 Vue d'Ensemble

Ce document détaille l'implémentation complète du backend DealAuto avec **Firebase** (Google Cloud Platform).

### Évaluation de la Solution

| Critère | Note | Commentaire |
|---------|------|-------------|
| Facilité d'intégration Flutter | ⭐⭐⭐⭐⭐ | FlutterFire officiel, excellente documentation |
| Auth téléphone (SMS) | ⭐⭐⭐⭐⭐ | Natif, simple, auto-vérification Android |
| Scalabilité | ⭐⭐⭐⭐ | Automatique, serverless |
| Coût initial | ⭐⭐⭐⭐ | Plan Spark gratuit généreux |
| Coût à l'échelle | ⭐⭐⭐ | Peut devenir cher avec beaucoup d'utilisateurs |
| Offline support | ⭐⭐⭐⭐⭐ | Excellent, natif Firestore |
| Contrôle des données | ⭐⭐ | Données sur serveurs Google |
| Temps de mise en place | ⭐⭐⭐⭐⭐ | Très rapide (quelques heures) |

### Avantages Clés pour DealAuto

1. **Auth SMS native** - Parfait pour le Togo où l'email est moins utilisé
2. **Offline-first** - Essentiel pour les zones à connexion instable
3. **Temps réel** - Mises à jour instantanées des annonces
4. **Pas de serveur à gérer** - Focus sur l'application

---

## 📦 Packages à Installer

```yaml
# pubspec.yaml
dependencies:
  # Firebase Core
  firebase_core: ^3.8.1
  
  # Authentification
  firebase_auth: ^5.3.4
  
  # Base de données
  cloud_firestore: ^5.6.0
  
  # Stockage fichiers
  firebase_storage: ^12.4.0
  
  # Notifications push (optionnel MVP)
  firebase_messaging: ^15.1.6
  
  # Analytics (optionnel)
  firebase_analytics: ^11.3.6
```

---

## 🔧 Configuration Initiale

### Étape 1 : Créer le Projet Firebase

1. Aller sur [Firebase Console](https://console.firebase.google.com)
2. Cliquer "Ajouter un projet"
3. Nom du projet : `dealauto-togo`
4. Désactiver Google Analytics (optionnel pour MVP)
5. Créer le projet

### Étape 2 : Ajouter l'Application Android

1. Dans Firebase Console → Vue d'ensemble du projet
2. Cliquer sur l'icône Android
3. Remplir les informations :
   - Package name : `com.dealauto.app` (ou `com.example.dealauto` pour dev)
   - Nom de l'application : `DealAuto`
   - Certificat SHA-1 : (obtenir avec `keytool`)
4. Télécharger `google-services.json`
5. Placer dans `android/app/google-services.json`

### Obtenir le SHA-1 (Debug)

```bash
# Windows
keytool -list -v -keystore %USERPROFILE%\.android\debug.keystore -alias androiddebugkey -storepass android -keypass android

# Mac/Linux
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

### Étape 3 : Configuration Android

```kotlin
// android/build.gradle.kts
plugins {
    id("com.google.gms.google-services") version "4.4.2" apply false
}

// android/app/build.gradle.kts
plugins {
    id("com.android.application")
    id("kotlin-android")
    id("dev.flutter.flutter-gradle-plugin")
    id("com.google.gms.google-services") // Ajouter cette ligne
}

android {
    defaultConfig {
        minSdk = 23 // Minimum pour Firebase Auth
    }
}
```

### Étape 4 : Initialisation Flutter

```dart
// lib/main.dart
import 'package:firebase_core/firebase_core.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Initialiser Firebase
  await Firebase.initializeApp();
  
  runApp(const MyApp());
}
```

---

## 🔐 Authentification par Téléphone

### Configuration Firebase Auth

1. Firebase Console → Authentication → Sign-in method
2. Activer "Téléphone"
3. Ajouter des numéros de test pour le développement :
   - `+22890000001` → Code : `123456`
   - `+22890000002` → Code : `123456`

### Service d'Authentification Complet

```dart
// lib/services/firebase_auth_service.dart
import 'package:firebase_auth/firebase_auth.dart';

class FirebaseAuthService {
  final FirebaseAuth _auth = FirebaseAuth.instance;
  
  // Singleton
  static final FirebaseAuthService _instance = FirebaseAuthService._internal();
  factory FirebaseAuthService() => _instance;
  FirebaseAuthService._internal();
  
  // État de l'utilisateur
  Stream<User?> get authStateChanges => _auth.authStateChanges();
  User? get currentUser => _auth.currentUser;
  bool get isAuthenticated => _auth.currentUser != null;
  
  // Variables pour la vérification
  String? _verificationId;
  int? _resendToken;
  
  /// Envoyer le code OTP
  Future<void> sendOtp({
    required String phoneNumber,
    required Function(String verificationId) onCodeSent,
    required Function(String error) onError,
    required Function(PhoneAuthCredential credential) onAutoVerify,
    int? forceResendingToken,
  }) async {
    try {
      await _auth.verifyPhoneNumber(
        phoneNumber: phoneNumber,
        timeout: const Duration(seconds: 60),
        forceResendingToken: forceResendingToken ?? _resendToken,
        
        // Auto-vérification Android (lecture SMS automatique)
        verificationCompleted: (PhoneAuthCredential credential) {
          onAutoVerify(credential);
        },
        
        // Erreur de vérification
        verificationFailed: (FirebaseAuthException e) {
          String message;
          switch (e.code) {
            case 'invalid-phone-number':
              message = 'Numéro de téléphone invalide';
              break;
            case 'too-many-requests':
              message = 'Trop de tentatives. Réessayez plus tard.';
              break;
            case 'quota-exceeded':
              message = 'Quota SMS dépassé. Contactez le support.';
              break;
            default:
              message = e.message ?? 'Erreur de vérification';
          }
          onError(message);
        },
        
        // Code envoyé avec succès
        codeSent: (String verificationId, int? resendToken) {
          _verificationId = verificationId;
          _resendToken = resendToken;
          onCodeSent(verificationId);
        },
        
        // Timeout auto-retrieval
        codeAutoRetrievalTimeout: (String verificationId) {
          _verificationId = verificationId;
        },
      );
    } catch (e) {
      onError('Erreur inattendue: $e');
    }
  }
  
  /// Vérifier le code OTP
  Future<UserCredential?> verifyOtp({
    required String smsCode,
    String? verificationId,
  }) async {
    try {
      final credential = PhoneAuthProvider.credential(
        verificationId: verificationId ?? _verificationId!,
        smsCode: smsCode,
      );
      return await _auth.signInWithCredential(credential);
    } on FirebaseAuthException catch (e) {
      switch (e.code) {
        case 'invalid-verification-code':
          throw Exception('Code invalide');
        case 'session-expired':
          throw Exception('Session expirée. Renvoyez le code.');
        default:
          throw Exception(e.message ?? 'Erreur de vérification');
      }
    }
  }
  
  /// Connexion avec credential (auto-vérification)
  Future<UserCredential> signInWithCredential(PhoneAuthCredential credential) async {
    return await _auth.signInWithCredential(credential);
  }
  
  /// Renvoyer le code OTP
  Future<void> resendOtp({
    required String phoneNumber,
    required Function(String verificationId) onCodeSent,
    required Function(String error) onError,
  }) async {
    await sendOtp(
      phoneNumber: phoneNumber,
      onCodeSent: onCodeSent,
      onError: onError,
      onAutoVerify: (_) {},
      forceResendingToken: _resendToken,
    );
  }
  
  /// Déconnexion
  Future<void> signOut() async {
    await _auth.signOut();
  }
  
  /// Supprimer le compte
  Future<void> deleteAccount() async {
    await _auth.currentUser?.delete();
  }
}
```

---

## 🗄️ Structure Firestore

### Collection : `users`

```
users/
  {userId}/                      # UID Firebase Auth
    phoneNumber: "+22890123456"  # Numéro complet
    nom: "Kokou"
    prenom: "Afi"
    countryCode: "+228"
    residenceCountry: "TG"
    profileType: "clientSimple"  # Enum string
    avatarUrl: "https://..."     # URL Firebase Storage (optionnel)
    isVerified: true
    createdAt: Timestamp
    updatedAt: Timestamp
```

### Collection : `vehicles`

```
vehicles/
  {vehicleId}/                   # Auto-généré
    name: "Toyota Corolla 2020"
    description: "Véhicule en excellent état..."
    imageUrls: ["url1", "url2", "url3"]  # Max 5 images
    priceInFCFA: 8500000
    year: 2020
    mileageKm: 45000
    city: "Lomé"
    category: "voitures"         # Enum string
    brand: "Toyota"
    model: "Corolla"
    fuelType: "essence"          # essence, diesel, hybride, electrique
    transmission: "automatique"  # automatique, manuelle
    isFeatured: false
    isUrgent: false
    isPromo: false
    isNew: false
    status: "active"             # active, sold, pending, deleted
    ownerId: "userId"            # Référence vers users
    ownerPhone: "+22890123456"   # Pour contact direct
    viewCount: 0
    favoriteCount: 0
    createdAt: Timestamp
    updatedAt: Timestamp
```

### Collection : `favorites`

```
favorites/
  {favoriteId}/                  # Auto-généré
    userId: "userId"
    vehicleId: "vehicleId"
    createdAt: Timestamp
```

### Sous-collection : `users/{userId}/notifications`

```
users/{userId}/notifications/
  {notificationId}/
    type: "new_message"          # new_message, price_drop, vehicle_sold
    title: "Nouveau message"
    body: "Vous avez reçu un message..."
    data: { vehicleId: "..." }
    isRead: false
    createdAt: Timestamp
```

---

## 📁 Repository Pattern

### UserRepository

```dart
// lib/repositories/firebase_user_repository.dart
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_auth/firebase_auth.dart';

class FirebaseUserRepository {
  final FirebaseFirestore _firestore = FirebaseFirestore.instance;
  final String _collection = 'users';
  
  // Singleton
  static final FirebaseUserRepository _instance = FirebaseUserRepository._internal();
  factory FirebaseUserRepository() => _instance;
  FirebaseUserRepository._internal();
  
  /// Créer un utilisateur après inscription
  Future<void> createUser({
    required String phoneNumber,
    required String nom,
    required String prenom,
    required String countryCode,
    required String residenceCountry,
    required String profileType,
  }) async {
    final userId = FirebaseAuth.instance.currentUser?.uid;
    if (userId == null) throw Exception('Utilisateur non authentifié');
    
    await _firestore.collection(_collection).doc(userId).set({
      'phoneNumber': phoneNumber,
      'nom': nom,
      'prenom': prenom,
      'countryCode': countryCode,
      'residenceCountry': residenceCountry,
      'profileType': profileType,
      'isVerified': true,
      'createdAt': FieldValue.serverTimestamp(),
      'updatedAt': FieldValue.serverTimestamp(),
    });
  }
  
  /// Récupérer l'utilisateur courant
  Future<Map<String, dynamic>?> getCurrentUser() async {
    final userId = FirebaseAuth.instance.currentUser?.uid;
    if (userId == null) return null;
    
    final doc = await _firestore.collection(_collection).doc(userId).get();
    return doc.data();
  }
  
  /// Récupérer un utilisateur par ID
  Future<Map<String, dynamic>?> getUserById(String userId) async {
    final doc = await _firestore.collection(_collection).doc(userId).get();
    return doc.data();
  }
  
  /// Vérifier si l'utilisateur existe
  Future<bool> userExists(String userId) async {
    final doc = await _firestore.collection(_collection).doc(userId).get();
    return doc.exists;
  }
  
  /// Mettre à jour le profil
  Future<void> updateProfile({
    String? nom,
    String? prenom,
    String? profileType,
    String? avatarUrl,
  }) async {
    final userId = FirebaseAuth.instance.currentUser?.uid;
    if (userId == null) throw Exception('Utilisateur non authentifié');
    
    final updates = <String, dynamic>{
      'updatedAt': FieldValue.serverTimestamp(),
    };
    
    if (nom != null) updates['nom'] = nom;
    if (prenom != null) updates['prenom'] = prenom;
    if (profileType != null) updates['profileType'] = profileType;
    if (avatarUrl != null) updates['avatarUrl'] = avatarUrl;
    
    await _firestore.collection(_collection).doc(userId).update(updates);
  }
  
  /// Stream de l'utilisateur courant (temps réel)
  Stream<Map<String, dynamic>?> streamCurrentUser() {
    final userId = FirebaseAuth.instance.currentUser?.uid;
    if (userId == null) return Stream.value(null);
    
    return _firestore
        .collection(_collection)
        .doc(userId)
        .snapshots()
        .map((doc) => doc.data());
  }
}
```

### VehicleRepository

```dart
// lib/repositories/firebase_vehicle_repository.dart
import 'package:cloud_firestore/cloud_firestore.dart';
import '../models/vehicle.dart';

class FirebaseVehicleRepository {
  final FirebaseFirestore _firestore = FirebaseFirestore.instance;
  final String _collection = 'vehicles';
  
  // Singleton
  static final FirebaseVehicleRepository _instance = FirebaseVehicleRepository._internal();
  factory FirebaseVehicleRepository() => _instance;
  FirebaseVehicleRepository._internal();
  
  /// Récupérer les véhicules à la une (Stream temps réel)
  Stream<List<Vehicle>> streamFeaturedVehicles({int limit = 10}) {
    return _firestore
        .collection(_collection)
        .where('isFeatured', isEqualTo: true)
        .where('status', isEqualTo: 'active')
        .orderBy('createdAt', descending: true)
        .limit(limit)
        .snapshots()
        .map((snapshot) => snapshot.docs
            .map((doc) => Vehicle.fromFirestore(doc))
            .toList());
  }
  
  /// Récupérer les ventes urgentes (Stream temps réel)
  Stream<List<Vehicle>> streamUrgentSales({int limit = 20}) {
    return _firestore
        .collection(_collection)
        .where('isUrgent', isEqualTo: true)
        .where('status', isEqualTo: 'active')
        .orderBy('createdAt', descending: true)
        .limit(limit)
        .snapshots()
        .map((snapshot) => snapshot.docs
            .map((doc) => Vehicle.fromFirestore(doc))
            .toList());
  }
  
  /// Récupérer par catégorie
  Stream<List<Vehicle>> streamByCategory(String category, {int limit = 50}) {
    return _firestore
        .collection(_collection)
        .where('category', isEqualTo: category)
        .where('status', isEqualTo: 'active')
        .orderBy('createdAt', descending: true)
        .limit(limit)
        .snapshots()
        .map((snapshot) => snapshot.docs
            .map((doc) => Vehicle.fromFirestore(doc))
            .toList());
  }
  
  /// Recherche simple (par nom)
  Future<List<Vehicle>> searchVehicles(String query, {int limit = 20}) async {
    // Note: Firestore ne supporte pas la recherche full-text native
    // Pour une recherche avancée, utiliser Algolia ou Typesense
    final queryLower = query.toLowerCase();
    
    final snapshot = await _firestore
        .collection(_collection)
        .where('status', isEqualTo: 'active')
        .orderBy('name')
        .startAt([queryLower])
        .endAt(['$queryLower\uf8ff'])
        .limit(limit)
        .get();
    
    return snapshot.docs.map((doc) => Vehicle.fromFirestore(doc)).toList();
  }
  
  /// Ajouter un véhicule
  Future<String> addVehicle(Vehicle vehicle) async {
    final docRef = await _firestore.collection(_collection).add(
      vehicle.toFirestore(),
    );
    return docRef.id;
  }
  
  /// Mettre à jour un véhicule
  Future<void> updateVehicle(String vehicleId, Map<String, dynamic> updates) async {
    updates['updatedAt'] = FieldValue.serverTimestamp();
    await _firestore.collection(_collection).doc(vehicleId).update(updates);
  }
  
  /// Supprimer un véhicule (soft delete)
  Future<void> deleteVehicle(String vehicleId) async {
    await _firestore.collection(_collection).doc(vehicleId).update({
      'status': 'deleted',
      'updatedAt': FieldValue.serverTimestamp(),
    });
  }
  
  /// Incrémenter le compteur de vues
  Future<void> incrementViewCount(String vehicleId) async {
    await _firestore.collection(_collection).doc(vehicleId).update({
      'viewCount': FieldValue.increment(1),
    });
  }
  
  /// Récupérer les véhicules d'un utilisateur
  Stream<List<Vehicle>> streamUserVehicles(String userId) {
    return _firestore
        .collection(_collection)
        .where('ownerId', isEqualTo: userId)
        .where('status', whereIn: ['active', 'pending'])
        .orderBy('createdAt', descending: true)
        .snapshots()
        .map((snapshot) => snapshot.docs
            .map((doc) => Vehicle.fromFirestore(doc))
            .toList());
  }
}
```

---

## 📷 Firebase Storage

### Service de Stockage

```dart
// lib/services/firebase_storage_service.dart
import 'dart:io';
import 'package:firebase_storage/firebase_storage.dart';
import 'package:path/path.dart' as path;

class FirebaseStorageService {
  final FirebaseStorage _storage = FirebaseStorage.instance;
  
  // Singleton
  static final FirebaseStorageService _instance = FirebaseStorageService._internal();
  factory FirebaseStorageService() => _instance;
  FirebaseStorageService._internal();
  
  /// Upload une image de véhicule
  Future<String> uploadVehicleImage({
    required File imageFile,
    required String vehicleId,
    required int index,
  }) async {
    final extension = path.extension(imageFile.path);
    final ref = _storage.ref().child('vehicles/$vehicleId/image_$index$extension');
    
    final uploadTask = ref.putFile(
      imageFile,
      SettableMetadata(
        contentType: 'image/jpeg',
        customMetadata: {
          'vehicleId': vehicleId,
          'uploadedAt': DateTime.now().toIso8601String(),
        },
      ),
    );
    
    // Suivre la progression (optionnel)
    uploadTask.snapshotEvents.listen((TaskSnapshot snapshot) {
      final progress = snapshot.bytesTransferred / snapshot.totalBytes;
      print('Upload progress: ${(progress * 100).toStringAsFixed(0)}%');
    });
    
    final snapshot = await uploadTask;
    return await snapshot.ref.getDownloadURL();
  }
  
  /// Upload plusieurs images
  Future<List<String>> uploadVehicleImages({
    required List<File> imageFiles,
    required String vehicleId,
  }) async {
    final urls = <String>[];
    
    for (int i = 0; i < imageFiles.length; i++) {
      final url = await uploadVehicleImage(
        imageFile: imageFiles[i],
        vehicleId: vehicleId,
        index: i,
      );
      urls.add(url);
    }
    
    return urls;
  }
  
  /// Supprimer les images d'un véhicule
  Future<void> deleteVehicleImages(String vehicleId) async {
    final ref = _storage.ref().child('vehicles/$vehicleId');
    final listResult = await ref.listAll();
    
    for (final item in listResult.items) {
      await item.delete();
    }
  }
  
  /// Upload avatar utilisateur
  Future<String> uploadUserAvatar({
    required File imageFile,
    required String userId,
  }) async {
    final ref = _storage.ref().child('avatars/$userId.jpg');
    
    final uploadTask = ref.putFile(
      imageFile,
      SettableMetadata(contentType: 'image/jpeg'),
    );
    
    final snapshot = await uploadTask;
    return await snapshot.ref.getDownloadURL();
  }
}
```

---

## 🔒 Règles de Sécurité Firestore

```javascript
// firestore.rules
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Fonction helper : utilisateur authentifié
    function isAuthenticated() {
      return request.auth != null;
    }
    
    // Fonction helper : propriétaire de la ressource
    function isOwner(userId) {
      return isAuthenticated() && request.auth.uid == userId;
    }
    
    // ==================== USERS ====================
    match /users/{userId} {
      // Lecture : tout utilisateur authentifié peut voir les profils
      allow read: if isAuthenticated();
      
      // Création : uniquement pour son propre profil
      allow create: if isOwner(userId);
      
      // Mise à jour : uniquement son propre profil
      allow update: if isOwner(userId);
      
      // Suppression : interdite (soft delete uniquement)
      allow delete: if false;
      
      // Sous-collection notifications
      match /notifications/{notificationId} {
        allow read, write: if isOwner(userId);
      }
    }
    
    // ==================== VEHICLES ====================
    match /vehicles/{vehicleId} {
      // Lecture : public (même non authentifié)
      allow read: if true;
      
      // Création : utilisateur authentifié
      allow create: if isAuthenticated() 
        && request.resource.data.ownerId == request.auth.uid;
      
      // Mise à jour : propriétaire uniquement
      allow update: if isAuthenticated() 
        && resource.data.ownerId == request.auth.uid;
      
      // Suppression : propriétaire uniquement
      allow delete: if isAuthenticated() 
        && resource.data.ownerId == request.auth.uid;
    }
    
    // ==================== FAVORITES ====================
    match /favorites/{favoriteId} {
      // Lecture : propriétaire uniquement
      allow read: if isAuthenticated() 
        && resource.data.userId == request.auth.uid;
      
      // Création : pour soi-même uniquement
      allow create: if isAuthenticated() 
        && request.resource.data.userId == request.auth.uid;
      
      // Suppression : propriétaire uniquement
      allow delete: if isAuthenticated() 
        && resource.data.userId == request.auth.uid;
      
      // Mise à jour : interdite
      allow update: if false;
    }
  }
}
```

---

## 🔒 Règles de Sécurité Storage

```javascript
// storage.rules
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    
    // Images des véhicules
    match /vehicles/{vehicleId}/{allPaths=**} {
      // Lecture : public
      allow read: if true;
      
      // Écriture : utilisateur authentifié
      // Note: Vérification propriétaire via Cloud Function recommandée
      allow write: if request.auth != null
        && request.resource.size < 5 * 1024 * 1024  // Max 5MB
        && request.resource.contentType.matches('image/.*');
    }
    
    // Avatars utilisateurs
    match /avatars/{userId} {
      // Lecture : public
      allow read: if true;
      
      // Écriture : propriétaire uniquement
      allow write: if request.auth != null 
        && request.auth.uid == userId
        && request.resource.size < 2 * 1024 * 1024  // Max 2MB
        && request.resource.contentType.matches('image/.*');
    }
  }
}
```

---

## 💰 Estimation des Coûts

### Plan Spark (Gratuit)

| Service | Limite | Suffisant pour |
|---------|--------|----------------|
| Auth SMS | 10K vérifications/mois | ~10K nouveaux utilisateurs/mois |
| Firestore lectures | 50K/jour | ~500 utilisateurs actifs/jour |
| Firestore écritures | 20K/jour | ~200 annonces/jour |
| Storage | 5GB | ~1000 véhicules (5 images chacun) |
| Bandwidth | 1GB/jour | ~1000 chargements images/jour |

**✅ Suffisant pour MVP et premiers mois**

### Plan Blaze (Pay-as-you-go)

| Service | Coût | Estimation 1000 utilisateurs actifs |
|---------|------|-------------------------------------|
| Auth SMS | ~0.06$/vérification | ~60$/mois (1000 nouveaux) |
| Firestore lectures | ~0.06$/100K | ~6$/mois |
| Firestore écritures | ~0.18$/100K | ~2$/mois |
| Storage | ~0.026$/GB/mois | ~1$/mois |
| Bandwidth | ~0.12$/GB | ~5$/mois |

**💵 Estimation totale : 50-100$/mois pour 1000 utilisateurs actifs**

---

## ✅ Checklist Implémentation Firebase

### Configuration Initiale
- [ ] Créer projet Firebase Console
- [ ] Ajouter application Android
- [ ] Télécharger google-services.json
- [ ] Configurer build.gradle
- [ ] Installer packages Firebase
- [ ] Initialiser Firebase dans main.dart

### Authentification
- [ ] Activer Phone Auth
- [ ] Ajouter numéros de test
- [ ] Implémenter FirebaseAuthService
- [ ] Connecter LoginScreen
- [ ] Connecter RegistrationScreen
- [ ] Tester flux OTP complet

### Base de Données
- [ ] Créer collections Firestore
- [ ] Déployer règles de sécurité
- [ ] Implémenter UserRepository
- [ ] Implémenter VehicleRepository
- [ ] Implémenter FavoritesRepository
- [ ] Tester CRUD complet

### Storage
- [ ] Configurer bucket Storage
- [ ] Déployer règles Storage
- [ ] Implémenter StorageService
- [ ] Tester upload images
- [ ] Tester suppression images

### Tests
- [ ] Tester flux inscription complet
- [ ] Tester flux connexion
- [ ] Tester ajout véhicule
- [ ] Tester favoris
- [ ] Tester offline mode


---

## 🔄 Migration et Évolution

### Migration vers Production

```dart
// Configuration multi-environnement
class FirebaseConfig {
  static const String _devProjectId = 'dealauto-dev';
  static const String _prodProjectId = 'dealauto-prod';
  
  static String get projectId {
    return kDebugMode ? _devProjectId : _prodProjectId;
  }
}
```

### Backup et Restauration

```bash
# Export Firestore (via gcloud CLI)
gcloud firestore export gs://dealauto-backups/$(date +%Y%m%d)

# Import Firestore
gcloud firestore import gs://dealauto-backups/20251218
```

---

## 📊 Monitoring et Analytics

### Firebase Performance Monitoring

```dart
// Mesurer les performances des opérations
import 'package:firebase_performance/firebase_performance.dart';

Future<List<Vehicle>> getFeaturedVehicles() async {
  final trace = FirebasePerformance.instance.newTrace('get_featured_vehicles');
  await trace.start();
  
  try {
    final result = await _firestore
        .collection('vehicles')
        .where('isFeatured', isEqualTo: true)
        .get();
    
    trace.setMetric('vehicle_count', result.docs.length);
    return result.docs.map((doc) => Vehicle.fromFirestore(doc)).toList();
  } finally {
    await trace.stop();
  }
}
```

### Firebase Analytics Events

```dart
// Événements personnalisés
import 'package:firebase_analytics/firebase_analytics.dart';

class AnalyticsService {
  static final _analytics = FirebaseAnalytics.instance;
  
  // Événement : Véhicule consulté
  static Future<void> logVehicleView(Vehicle vehicle) async {
    await _analytics.logEvent(
      name: 'vehicle_view',
      parameters: {
        'vehicle_id': vehicle.id,
        'category': vehicle.category.name,
        'price': vehicle.priceInFCFA,
        'city': vehicle.city ?? 'unknown',
      },
    );
  }
  
  // Événement : Recherche effectuée
  static Future<void> logSearch(String query, int resultCount) async {
    await _analytics.logEvent(
      name: 'search',
      parameters: {
        'search_term': query,
        'result_count': resultCount,
      },
    );
  }
  
  // Événement : Favori ajouté
  static Future<void> logAddToFavorites(String vehicleId) async {
    await _analytics.logEvent(
      name: 'add_to_favorites',
      parameters: {'vehicle_id': vehicleId},
    );
  }
  
  // Événement : Contact vendeur
  static Future<void> logContactSeller(String vehicleId, String method) async {
    await _analytics.logEvent(
      name: 'contact_seller',
      parameters: {
        'vehicle_id': vehicleId,
        'method': method, // 'call', 'whatsapp', 'message'
      },
    );
  }
}
```

---

## 🔔 Notifications Push

### Configuration FCM

```dart
// lib/services/notification_service.dart
import 'package:firebase_messaging/firebase_messaging.dart';

class NotificationService {
  static final _messaging = FirebaseMessaging.instance;
  
  /// Initialiser les notifications
  static Future<void> initialize() async {
    // Demander la permission
    final settings = await _messaging.requestPermission(
      alert: true,
      badge: true,
      sound: true,
    );
    
    if (settings.authorizationStatus == AuthorizationStatus.authorized) {
      // Récupérer le token FCM
      final token = await _messaging.getToken();
      print('FCM Token: $token');
      
      // Sauvegarder le token dans Firestore
      await _saveTokenToFirestore(token);
    }
    
    // Écouter les messages en foreground
    FirebaseMessaging.onMessage.listen(_handleForegroundMessage);
    
    // Écouter les messages en background
    FirebaseMessaging.onBackgroundMessage(_handleBackgroundMessage);
    
    // Écouter les taps sur les notifications
    FirebaseMessaging.onMessageOpenedApp.listen(_handleNotificationTap);
  }
  
  static Future<void> _saveTokenToFirestore(String? token) async {
    if (token == null) return;
    
    final userId = FirebaseAuth.instance.currentUser?.uid;
    if (userId == null) return;
    
    await FirebaseFirestore.instance
        .collection('users')
        .doc(userId)
        .update({'fcmToken': token});
  }
  
  static void _handleForegroundMessage(RemoteMessage message) {
    print('Foreground message: ${message.notification?.title}');
    // Afficher une notification locale ou un snackbar
  }
  
  static void _handleNotificationTap(RemoteMessage message) {
    // Naviguer vers l'écran approprié
    final data = message.data;
    if (data['type'] == 'vehicle') {
      // Navigator.push(...VehicleDetailScreen)
    }
  }
}

// Handler pour les messages en background (doit être top-level)
@pragma('vm:entry-point')
Future<void> _handleBackgroundMessage(RemoteMessage message) async {
  print('Background message: ${message.notification?.title}');
}
```

### Cloud Functions pour Notifications

```javascript
// functions/index.js
const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();

// Notification quand un nouveau véhicule est ajouté
exports.onNewVehicle = functions.firestore
  .document('vehicles/{vehicleId}')
  .onCreate(async (snap, context) => {
    const vehicle = snap.data();
    
    // Récupérer les utilisateurs intéressés par cette catégorie
    const usersSnapshot = await admin.firestore()
      .collection('users')
      .where('favoriteCategories', 'array-contains', vehicle.category)
      .get();
    
    const tokens = usersSnapshot.docs
      .map(doc => doc.data().fcmToken)
      .filter(token => token);
    
    if (tokens.length === 0) return;
    
    // Envoyer la notification
    await admin.messaging().sendMulticast({
      tokens,
      notification: {
        title: 'Nouveau véhicule !',
        body: `${vehicle.name} - ${vehicle.priceInFCFA.toLocaleString()} FCFA`,
      },
      data: {
        type: 'vehicle',
        vehicleId: context.params.vehicleId,
      },
    });
  });

// Notification quand le prix d'un favori baisse
exports.onPriceDrop = functions.firestore
  .document('vehicles/{vehicleId}')
  .onUpdate(async (change, context) => {
    const before = change.before.data();
    const after = change.after.data();
    
    if (after.priceInFCFA >= before.priceInFCFA) return;
    
    // Récupérer les utilisateurs qui ont ce véhicule en favori
    const favoritesSnapshot = await admin.firestore()
      .collection('favorites')
      .where('vehicleId', '==', context.params.vehicleId)
      .get();
    
    const userIds = favoritesSnapshot.docs.map(doc => doc.data().userId);
    
    for (const userId of userIds) {
      const userDoc = await admin.firestore()
        .collection('users')
        .doc(userId)
        .get();
      
      const token = userDoc.data()?.fcmToken;
      if (!token) continue;
      
      await admin.messaging().send({
        token,
        notification: {
          title: 'Baisse de prix !',
          body: `${after.name} est passé de ${before.priceInFCFA.toLocaleString()} à ${after.priceInFCFA.toLocaleString()} FCFA`,
        },
        data: {
          type: 'vehicle',
          vehicleId: context.params.vehicleId,
        },
      });
    }
  });
```

---

## 🔧 Troubleshooting Firebase

### Erreurs Courantes

| Erreur | Cause | Solution |
|--------|-------|----------|
| `PERMISSION_DENIED` | Règles Firestore | Vérifier les règles de sécurité |
| `NOT_FOUND` | Document inexistant | Vérifier l'ID du document |
| `UNAVAILABLE` | Problème réseau | Activer le mode offline |
| `QUOTA_EXCEEDED` | Limite atteinte | Passer au plan Blaze |
| `INVALID_ARGUMENT` | Données invalides | Valider les données avant envoi |

### Debug Mode

```dart
// Activer les logs Firestore
FirebaseFirestore.instance.settings = const Settings(
  persistenceEnabled: true,
  cacheSizeBytes: Settings.CACHE_SIZE_UNLIMITED,
);

// Logs détaillés en debug
if (kDebugMode) {
  FirebaseFirestore.instance.useFirestoreEmulator('localhost', 8080);
  FirebaseAuth.instance.useAuthEmulator('localhost', 9099);
  FirebaseStorage.instance.useStorageEmulator('localhost', 9199);
}
```
