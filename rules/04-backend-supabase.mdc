# Guide Backend - Solution Supabase

---
inclusion: manual
---

## 📊 Vue d'Ensemble

Ce document détaille l'implémentation complète du backend DealAuto avec **Supabase** (alternative open-source à Firebase basée sur PostgreSQL).

### Évaluation de la Solution

| Critère | Note | Commentaire |
|---------|------|-------------|
| Facilité d'intégration Flutter | ⭐⭐⭐⭐ | SDK officiel, bonne documentation |
| Auth téléphone (SMS) | ⭐⭐⭐ | Via Twilio (configuration requise) |
| Scalabilité | ⭐⭐⭐⭐ | PostgreSQL robuste et éprouvé |
| Coût initial | ⭐⭐⭐⭐⭐ | Plan gratuit très généreux |
| Coût à l'échelle | ⭐⭐⭐⭐ | Plus prévisible que Firebase |
| Offline support | ⭐⭐⭐ | Moins natif, nécessite Hive/Isar |
| Contrôle des données | ⭐⭐⭐⭐⭐ | SQL standard, export facile |
| Open Source | ⭐⭐⭐⭐⭐ | Self-hosting possible |

### Avantages Clés pour DealAuto

1. **SQL Standard** - Requêtes complexes, jointures, agrégations
2. **Coûts prévisibles** - Pas de surprise sur la facturation
3. **Self-hosting possible** - Migration vers VPS si nécessaire
4. **Row Level Security** - Sécurité au niveau des lignes PostgreSQL


---

## 📦 Packages à Installer

```yaml
# pubspec.yaml
dependencies:
  # Supabase Flutter SDK
  supabase_flutter: ^2.8.3
  
  # Stockage local pour offline (recommandé)
  hive: ^2.2.3
  hive_flutter: ^1.1.0
  
  # Ou alternative moderne
  isar: ^3.1.0+1
  isar_flutter_libs: ^3.1.0+1
```

---

## 🔧 Configuration Initiale

### Étape 1 : Créer le Projet Supabase

1. Aller sur [Supabase Dashboard](https://supabase.com/dashboard)
2. Cliquer "New Project"
3. Remplir les informations :
   - Organization : Créer ou sélectionner
   - Name : `dealauto-togo`
   - Database Password : (générer un mot de passe fort)
   - Region : `West EU (Ireland)` (plus proche du Togo)
4. Cliquer "Create new project"
5. Attendre la création (~2 minutes)

### Étape 2 : Récupérer les Clés

1. Aller dans Settings → API
2. Noter :
   - **Project URL** : `https://xxxxx.supabase.co`
   - **anon public key** : `eyJhbGciOiJIUzI1NiIs...`
   - **service_role key** : (garder secret, pour admin uniquement)

### Étape 3 : Configuration Flutter

```dart
// lib/main.dart
import 'package:supabase_flutter/supabase_flutter.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  await Supabase.initialize(
    url: 'https://votre-projet.supabase.co',
    anonKey: 'votre-clé-anon-publique',
    authOptions: const FlutterAuthClientOptions(
      authFlowType: AuthFlowType.pkce,
    ),
    realtimeClientOptions: const RealtimeClientOptions(
      logLevel: RealtimeLogLevel.info,
    ),
  );
  
  runApp(const MyApp());
}

// Accès global au client Supabase
final supabase = Supabase.instance.client;
```


### Étape 4 : Configuration Auth SMS (Twilio)

1. Créer un compte [Twilio](https://www.twilio.com)
2. Obtenir :
   - Account SID
   - Auth Token
   - Phone Number ou Message Service SID
3. Dans Supabase Dashboard → Authentication → Providers
4. Activer "Phone"
5. Configurer Twilio :
   - Twilio Account SID
   - Twilio Auth Token
   - Twilio Message Service SID (ou Phone Number)

---

## 🗄️ Schéma de Base de Données PostgreSQL

### Création des Tables

```sql
-- ==================== EXTENSIONS ====================
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- ==================== TABLE USERS ====================
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  auth_id UUID UNIQUE REFERENCES auth.users(id) ON DELETE CASCADE,
  phone_number TEXT UNIQUE NOT NULL,
  nom TEXT NOT NULL,
  prenom TEXT NOT NULL,
  country_code TEXT NOT NULL DEFAULT '+228',
  residence_country TEXT NOT NULL DEFAULT 'TG',
  profile_type TEXT NOT NULL DEFAULT 'clientSimple'
    CHECK (profile_type IN (
      'clientSimple', 
      'vendeurSimple', 
      'proprietaireParc', 
      'concessionnaire', 
      'vendeurPiecesDetachees'
    )),
  avatar_url TEXT,
  is_verified BOOLEAN DEFAULT TRUE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Index pour recherche par téléphone
CREATE INDEX idx_users_phone ON users(phone_number);
CREATE INDEX idx_users_auth_id ON users(auth_id);

-- ==================== TABLE VEHICLES ====================
CREATE TABLE vehicles (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  name TEXT NOT NULL,
  description TEXT,
  image_urls TEXT[] DEFAULT '{}',
  price_in_fcfa INTEGER NOT NULL CHECK (price_in_fcfa > 0),
  year INTEGER CHECK (year >= 1900 AND year <= 2100),
  mileage_km INTEGER CHECK (mileage_km >= 0),
  city TEXT,
  category TEXT NOT NULL
    CHECK (category IN (
      'voitures', 
      'camions', 
      'bus', 
      'motos', 
      'concessionnaires', 
      'piecesDetachees'
    )),
  brand TEXT,
  model TEXT,
  fuel_type TEXT CHECK (fuel_type IN ('essence', 'diesel', 'hybride', 'electrique')),
  transmission TEXT CHECK (transmission IN ('automatique', 'manuelle')),
  is_featured BOOLEAN DEFAULT FALSE,
  is_urgent BOOLEAN DEFAULT FALSE,
  is_promo BOOLEAN DEFAULT FALSE,
  is_new BOOLEAN DEFAULT FALSE,
  status TEXT DEFAULT 'active'
    CHECK (status IN ('active', 'sold', 'pending', 'deleted')),
  owner_id UUID REFERENCES users(id) ON DELETE CASCADE,
  owner_phone TEXT,
  view_count INTEGER DEFAULT 0,
  favorite_count INTEGER DEFAULT 0,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  updated_at TIMESTAMPTZ DEFAULT NOW()
);

-- Index pour les requêtes fréquentes
CREATE INDEX idx_vehicles_featured ON vehicles(is_featured) WHERE is_featured = TRUE;
CREATE INDEX idx_vehicles_urgent ON vehicles(is_urgent) WHERE is_urgent = TRUE;
CREATE INDEX idx_vehicles_category ON vehicles(category);
CREATE INDEX idx_vehicles_owner ON vehicles(owner_id);
CREATE INDEX idx_vehicles_status ON vehicles(status);
CREATE INDEX idx_vehicles_created ON vehicles(created_at DESC);


-- ==================== TABLE FAVORITES ====================
CREATE TABLE favorites (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id) ON DELETE CASCADE,
  vehicle_id UUID REFERENCES vehicles(id) ON DELETE CASCADE,
  created_at TIMESTAMPTZ DEFAULT NOW(),
  UNIQUE(user_id, vehicle_id)
);

CREATE INDEX idx_favorites_user ON favorites(user_id);
CREATE INDEX idx_favorites_vehicle ON favorites(vehicle_id);

-- ==================== TRIGGERS ====================

-- Fonction pour mettre à jour updated_at
CREATE OR REPLACE FUNCTION update_updated_at()
RETURNS TRIGGER AS $$
BEGIN
  NEW.updated_at = NOW();
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

-- Trigger pour users
CREATE TRIGGER users_updated_at
  BEFORE UPDATE ON users
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at();

-- Trigger pour vehicles
CREATE TRIGGER vehicles_updated_at
  BEFORE UPDATE ON vehicles
  FOR EACH ROW
  EXECUTE FUNCTION update_updated_at();

-- ==================== FONCTIONS UTILITAIRES ====================

-- Fonction de recherche véhicules
CREATE OR REPLACE FUNCTION search_vehicles(search_query TEXT)
RETURNS SETOF vehicles AS $$
BEGIN
  RETURN QUERY
  SELECT * FROM vehicles
  WHERE status = 'active'
    AND (
      name ILIKE '%' || search_query || '%'
      OR city ILIKE '%' || search_query || '%'
      OR brand ILIKE '%' || search_query || '%'
      OR model ILIKE '%' || search_query || '%'
    )
  ORDER BY created_at DESC;
END;
$$ LANGUAGE plpgsql;

-- Fonction pour incrémenter les vues
CREATE OR REPLACE FUNCTION increment_view_count(vehicle_uuid UUID)
RETURNS void AS $$
BEGIN
  UPDATE vehicles 
  SET view_count = view_count + 1 
  WHERE id = vehicle_uuid;
END;
$$ LANGUAGE plpgsql;
```

---

## 🔐 Authentification par Téléphone

### Service d'Authentification Supabase

```dart
// lib/services/supabase_auth_service.dart
import 'package:supabase_flutter/supabase_flutter.dart';

class SupabaseAuthService {
  final SupabaseClient _client = Supabase.instance.client;
  
  // Singleton
  static final SupabaseAuthService _instance = SupabaseAuthService._internal();
  factory SupabaseAuthService() => _instance;
  SupabaseAuthService._internal();
  
  // État de l'utilisateur
  Stream<AuthState> get authStateChanges => _client.auth.onAuthStateChange;
  User? get currentUser => _client.auth.currentUser;
  bool get isAuthenticated => _client.auth.currentUser != null;
  
  /// Envoyer le code OTP
  Future<bool> sendOtp(String phoneNumber) async {
    try {
      await _client.auth.signInWithOtp(
        phone: phoneNumber,
      );
      return true;
    } on AuthException catch (e) {
      throw Exception(_mapAuthError(e.message));
    }
  }
  
  /// Vérifier le code OTP
  Future<AuthResponse> verifyOtp({
    required String phoneNumber,
    required String otpCode,
  }) async {
    try {
      final response = await _client.auth.verifyOTP(
        phone: phoneNumber,
        token: otpCode,
        type: OtpType.sms,
      );
      return response;
    } on AuthException catch (e) {
      throw Exception(_mapAuthError(e.message));
    }
  }
  
  /// Renvoyer le code OTP
  Future<bool> resendOtp(String phoneNumber) async {
    return await sendOtp(phoneNumber);
  }
  
  /// Déconnexion
  Future<void> signOut() async {
    await _client.auth.signOut();
  }
  
  /// Mapper les erreurs d'authentification
  String _mapAuthError(String message) {
    if (message.contains('Invalid phone')) {
      return 'Numéro de téléphone invalide';
    }
    if (message.contains('rate limit')) {
      return 'Trop de tentatives. Réessayez plus tard.';
    }
    if (message.contains('Invalid OTP')) {
      return 'Code invalide';
    }
    if (message.contains('expired')) {
      return 'Code expiré. Renvoyez un nouveau code.';
    }
    return message;
  }
}
```


---

## 📁 Repository Pattern

### UserRepository

```dart
// lib/repositories/supabase_user_repository.dart
import 'package:supabase_flutter/supabase_flutter.dart';

class SupabaseUserRepository {
  final SupabaseClient _client = Supabase.instance.client;
  final String _table = 'users';
  
  // Singleton
  static final SupabaseUserRepository _instance = SupabaseUserRepository._internal();
  factory SupabaseUserRepository() => _instance;
  SupabaseUserRepository._internal();
  
  /// Créer un utilisateur après inscription
  Future<void> createUser({
    required String phoneNumber,
    required String nom,
    required String prenom,
    required String countryCode,
    required String residenceCountry,
    required String profileType,
  }) async {
    final authId = _client.auth.currentUser?.id;
    if (authId == null) throw Exception('Utilisateur non authentifié');
    
    await _client.from(_table).insert({
      'auth_id': authId,
      'phone_number': phoneNumber,
      'nom': nom,
      'prenom': prenom,
      'country_code': countryCode,
      'residence_country': residenceCountry,
      'profile_type': profileType,
    });
  }
  
  /// Récupérer l'utilisateur courant
  Future<Map<String, dynamic>?> getCurrentUser() async {
    final authId = _client.auth.currentUser?.id;
    if (authId == null) return null;
    
    final response = await _client
        .from(_table)
        .select()
        .eq('auth_id', authId)
        .maybeSingle();
    return response;
  }
  
  /// Vérifier si l'utilisateur existe
  Future<bool> userExists() async {
    final authId = _client.auth.currentUser?.id;
    if (authId == null) return false;
    
    final response = await _client
        .from(_table)
        .select('id')
        .eq('auth_id', authId)
        .maybeSingle();
    return response != null;
  }
  
  /// Mettre à jour le profil
  Future<void> updateProfile({
    String? nom,
    String? prenom,
    String? profileType,
    String? avatarUrl,
  }) async {
    final authId = _client.auth.currentUser?.id;
    if (authId == null) throw Exception('Utilisateur non authentifié');
    
    final updates = <String, dynamic>{};
    if (nom != null) updates['nom'] = nom;
    if (prenom != null) updates['prenom'] = prenom;
    if (profileType != null) updates['profile_type'] = profileType;
    if (avatarUrl != null) updates['avatar_url'] = avatarUrl;
    
    if (updates.isNotEmpty) {
      await _client
          .from(_table)
          .update(updates)
          .eq('auth_id', authId);
    }
  }
  
  /// Stream temps réel de l'utilisateur courant
  Stream<Map<String, dynamic>?> streamCurrentUser() {
    final authId = _client.auth.currentUser?.id;
    if (authId == null) return Stream.value(null);
    
    return _client
        .from(_table)
        .stream(primaryKey: ['id'])
        .eq('auth_id', authId)
        .map((list) => list.isNotEmpty ? list.first : null);
  }
}
```

### VehicleRepository

```dart
// lib/repositories/supabase_vehicle_repository.dart
import 'package:supabase_flutter/supabase_flutter.dart';
import '../models/vehicle.dart';

class SupabaseVehicleRepository {
  final SupabaseClient _client = Supabase.instance.client;
  final String _table = 'vehicles';
  
  // Singleton
  static final SupabaseVehicleRepository _instance = SupabaseVehicleRepository._internal();
  factory SupabaseVehicleRepository() => _instance;
  SupabaseVehicleRepository._internal();
  
  /// Récupérer les véhicules à la une (Future)
  Future<List<Map<String, dynamic>>> getFeaturedVehicles({int limit = 10}) async {
    final response = await _client
        .from(_table)
        .select()
        .eq('is_featured', true)
        .eq('status', 'active')
        .order('created_at', ascending: false)
        .limit(limit);
    return List<Map<String, dynamic>>.from(response);
  }
  
  /// Stream temps réel des véhicules à la une
  Stream<List<Map<String, dynamic>>> streamFeaturedVehicles({int limit = 10}) {
    return _client
        .from(_table)
        .stream(primaryKey: ['id'])
        .eq('is_featured', true)
        .order('created_at', ascending: false)
        .limit(limit)
        .map((list) => list.where((v) => v['status'] == 'active').toList());
  }
  
  /// Récupérer les ventes urgentes
  Future<List<Map<String, dynamic>>> getUrgentSales({int limit = 20}) async {
    final response = await _client
        .from(_table)
        .select()
        .eq('is_urgent', true)
        .eq('status', 'active')
        .order('created_at', ascending: false)
        .limit(limit);
    return List<Map<String, dynamic>>.from(response);
  }
  
  /// Récupérer par catégorie
  Future<List<Map<String, dynamic>>> getByCategory(String category, {int limit = 50}) async {
    final response = await _client
        .from(_table)
        .select()
        .eq('category', category)
        .eq('status', 'active')
        .order('created_at', ascending: false)
        .limit(limit);
    return List<Map<String, dynamic>>.from(response);
  }
  
  /// Recherche full-text
  Future<List<Map<String, dynamic>>> search(String query, {int limit = 20}) async {
    // Utiliser la fonction PostgreSQL
    final response = await _client
        .rpc('search_vehicles', params: {'search_query': query})
        .limit(limit);
    return List<Map<String, dynamic>>.from(response);
  }
  
  /// Ajouter un véhicule
  Future<Map<String, dynamic>> addVehicle(Map<String, dynamic> vehicle) async {
    final response = await _client
        .from(_table)
        .insert(vehicle)
        .select()
        .single();
    return response;
  }
  
  /// Mettre à jour un véhicule
  Future<void> updateVehicle(String vehicleId, Map<String, dynamic> updates) async {
    await _client
        .from(_table)
        .update(updates)
        .eq('id', vehicleId);
  }
  
  /// Supprimer un véhicule (soft delete)
  Future<void> deleteVehicle(String vehicleId) async {
    await _client
        .from(_table)
        .update({'status': 'deleted'})
        .eq('id', vehicleId);
  }
  
  /// Incrémenter les vues
  Future<void> incrementViewCount(String vehicleId) async {
    await _client.rpc('increment_view_count', params: {'vehicle_uuid': vehicleId});
  }
}
```


---

## 📷 Supabase Storage

### Configuration du Bucket

```sql
-- Dans Supabase Dashboard → Storage → Create bucket
-- Nom: vehicles
-- Public: true

-- Ou via SQL
INSERT INTO storage.buckets (id, name, public)
VALUES ('vehicles', 'vehicles', true);

INSERT INTO storage.buckets (id, name, public)
VALUES ('avatars', 'avatars', true);
```

### Service de Stockage

```dart
// lib/services/supabase_storage_service.dart
import 'dart:io';
import 'package:supabase_flutter/supabase_flutter.dart';
import 'package:path/path.dart' as path;

class SupabaseStorageService {
  final SupabaseClient _client = Supabase.instance.client;
  
  // Singleton
  static final SupabaseStorageService _instance = SupabaseStorageService._internal();
  factory SupabaseStorageService() => _instance;
  SupabaseStorageService._internal();
  
  /// Upload une image de véhicule
  Future<String> uploadVehicleImage({
    required File imageFile,
    required String vehicleId,
    required int index,
  }) async {
    final extension = path.extension(imageFile.path);
    final fileName = '$vehicleId/image_$index$extension';
    
    await _client.storage.from('vehicles').upload(
      fileName,
      imageFile,
      fileOptions: const FileOptions(
        contentType: 'image/jpeg',
        upsert: true,
      ),
    );
    
    return _client.storage.from('vehicles').getPublicUrl(fileName);
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
    final files = await _client.storage.from('vehicles').list(path: vehicleId);
    
    if (files.isNotEmpty) {
      final paths = files.map((f) => '$vehicleId/${f.name}').toList();
      await _client.storage.from('vehicles').remove(paths);
    }
  }
  
  /// Upload avatar utilisateur
  Future<String> uploadUserAvatar({
    required File imageFile,
    required String userId,
  }) async {
    final fileName = '$userId.jpg';
    
    await _client.storage.from('avatars').upload(
      fileName,
      imageFile,
      fileOptions: const FileOptions(
        contentType: 'image/jpeg',
        upsert: true,
      ),
    );
    
    return _client.storage.from('avatars').getPublicUrl(fileName);
  }
}
```

---

## 🔒 Row Level Security (RLS)

### Politiques pour users

```sql
-- Activer RLS
ALTER TABLE users ENABLE ROW LEVEL SECURITY;

-- Lecture : tout utilisateur authentifié peut voir les profils
CREATE POLICY "Users can view all profiles"
  ON users FOR SELECT
  TO authenticated
  USING (true);

-- Création : utilisateur authentifié pour son propre profil
CREATE POLICY "Users can create their profile"
  ON users FOR INSERT
  TO authenticated
  WITH CHECK (auth.uid() = auth_id);

-- Mise à jour : propriétaire uniquement
CREATE POLICY "Users can update own profile"
  ON users FOR UPDATE
  TO authenticated
  USING (auth.uid() = auth_id)
  WITH CHECK (auth.uid() = auth_id);

-- Suppression : interdite
CREATE POLICY "Users cannot delete profiles"
  ON users FOR DELETE
  TO authenticated
  USING (false);
```

### Politiques pour vehicles

```sql
ALTER TABLE vehicles ENABLE ROW LEVEL SECURITY;

-- Lecture : public (même non authentifié)
CREATE POLICY "Vehicles are viewable by everyone"
  ON vehicles FOR SELECT
  USING (true);

-- Création : utilisateur authentifié
CREATE POLICY "Authenticated users can create vehicles"
  ON vehicles FOR INSERT
  TO authenticated
  WITH CHECK (
    owner_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );

-- Mise à jour : propriétaire uniquement
CREATE POLICY "Users can update own vehicles"
  ON vehicles FOR UPDATE
  TO authenticated
  USING (
    owner_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );

-- Suppression : propriétaire uniquement
CREATE POLICY "Users can delete own vehicles"
  ON vehicles FOR DELETE
  TO authenticated
  USING (
    owner_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );
```

### Politiques pour favorites

```sql
ALTER TABLE favorites ENABLE ROW LEVEL SECURITY;

-- Lecture : propriétaire uniquement
CREATE POLICY "Users can view own favorites"
  ON favorites FOR SELECT
  TO authenticated
  USING (
    user_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );

-- Création : propriétaire uniquement
CREATE POLICY "Users can create own favorites"
  ON favorites FOR INSERT
  TO authenticated
  WITH CHECK (
    user_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );

-- Suppression : propriétaire uniquement
CREATE POLICY "Users can delete own favorites"
  ON favorites FOR DELETE
  TO authenticated
  USING (
    user_id IN (SELECT id FROM users WHERE auth_id = auth.uid())
  );
```

### Politiques Storage

```sql
-- Bucket vehicles
CREATE POLICY "Public read access for vehicles"
  ON storage.objects FOR SELECT
  USING (bucket_id = 'vehicles');

CREATE POLICY "Authenticated users can upload vehicle images"
  ON storage.objects FOR INSERT
  TO authenticated
  WITH CHECK (bucket_id = 'vehicles');

CREATE POLICY "Users can delete own vehicle images"
  ON storage.objects FOR DELETE
  TO authenticated
  USING (bucket_id = 'vehicles');

-- Bucket avatars
CREATE POLICY "Public read access for avatars"
  ON storage.objects FOR SELECT
  USING (bucket_id = 'avatars');

CREATE POLICY "Users can upload own avatar"
  ON storage.objects FOR INSERT
  TO authenticated
  WITH CHECK (
    bucket_id = 'avatars' 
    AND (storage.foldername(name))[1] = auth.uid()::text
  );
```


---

## 💰 Estimation des Coûts

### Plan Free (Gratuit)

| Service | Limite | Suffisant pour |
|---------|--------|----------------|
| Base de données | 500MB | ~50K véhicules |
| Storage | 1GB | ~2000 véhicules (5 images) |
| Bandwidth | 2GB/mois | ~2000 utilisateurs/mois |
| Auth | Illimité | ✅ |
| Edge Functions | 500K invocations | ✅ |

**⚠️ SMS via Twilio : ~0.05$/SMS (non inclus)**

**✅ Suffisant pour MVP**

### Plan Pro (25$/mois)

| Service | Limite |
|---------|--------|
| Base de données | 8GB |
| Storage | 100GB |
| Bandwidth | 50GB/mois |
| Backups quotidiens | ✅ |
| Support prioritaire | ✅ |

### Coût SMS Twilio

| Volume | Coût/SMS | Total estimé |
|--------|----------|--------------|
| 100 SMS/mois | ~0.05$ | ~5$/mois |
| 1000 SMS/mois | ~0.05$ | ~50$/mois |
| 5000 SMS/mois | ~0.04$ | ~200$/mois |

**💵 Estimation totale : 25-75$/mois pour 1000 utilisateurs actifs**

---

## ⚡ Avantages Spécifiques Supabase

### 1. Requêtes SQL Complexes

```dart
// Jointures et agrégations
final response = await supabase
    .from('vehicles')
    .select('''
      *,
      owner:users!owner_id (
        nom,
        prenom,
        phone_number
      )
    ''')
    .eq('category', 'voitures')
    .gte('price_in_fcfa', 5000000)
    .lte('price_in_fcfa', 10000000)
    .order('created_at', ascending: false);
```

### 2. Fonctions PostgreSQL

```sql
-- Statistiques vendeur
CREATE OR REPLACE FUNCTION get_seller_stats(seller_uuid UUID)
RETURNS TABLE (
  total_vehicles BIGINT,
  total_views BIGINT,
  total_favorites BIGINT,
  avg_price NUMERIC
) AS $$
BEGIN
  RETURN QUERY
  SELECT 
    COUNT(*)::BIGINT,
    COALESCE(SUM(view_count), 0)::BIGINT,
    COALESCE(SUM(favorite_count), 0)::BIGINT,
    COALESCE(AVG(price_in_fcfa), 0)::NUMERIC
  FROM vehicles
  WHERE owner_id = seller_uuid AND status = 'active';
END;
$$ LANGUAGE plpgsql;
```

### 3. Temps Réel Natif

```dart
// Écouter les nouveaux véhicules
final subscription = supabase
    .from('vehicles')
    .stream(primaryKey: ['id'])
    .eq('status', 'active')
    .listen((List<Map<String, dynamic>> data) {
      // Mise à jour automatique
      setState(() => _vehicles = data);
    });

// Ne pas oublier de cancel
@override
void dispose() {
  subscription.cancel();
  super.dispose();
}
```

---

## ✅ Checklist Implémentation Supabase

### Configuration Initiale
- [ ] Créer projet Supabase Dashboard
- [ ] Noter URL et clé anon
- [ ] Installer supabase_flutter
- [ ] Initialiser Supabase dans main.dart

### Base de Données
- [ ] Créer tables SQL
- [ ] Créer index
- [ ] Créer triggers
- [ ] Créer fonctions utilitaires

### Sécurité
- [ ] Activer RLS sur toutes les tables
- [ ] Créer politiques users
- [ ] Créer politiques vehicles
- [ ] Créer politiques favorites
- [ ] Créer politiques storage

### Authentification
- [ ] Configurer Twilio
- [ ] Activer Phone Auth
- [ ] Implémenter SupabaseAuthService
- [ ] Tester flux OTP

### Repositories
- [ ] Implémenter UserRepository
- [ ] Implémenter VehicleRepository
- [ ] Implémenter FavoritesRepository
- [ ] Implémenter StorageService

### Tests
- [ ] Tester inscription complète
- [ ] Tester connexion
- [ ] Tester CRUD véhicules
- [ ] Tester favoris
- [ ] Tester temps réel


---

## 🔄 Migration et Évolution

### Migration depuis Firebase

```sql
-- Script de migration des données
-- Exécuter dans Supabase SQL Editor

-- 1. Créer une table temporaire pour l'import
CREATE TABLE temp_firebase_users (
  firebase_uid TEXT,
  phone_number TEXT,
  nom TEXT,
  prenom TEXT,
  profile_type TEXT,
  created_at TIMESTAMPTZ
);

-- 2. Importer les données (via CSV ou API)
-- COPY temp_firebase_users FROM '/path/to/export.csv' WITH CSV HEADER;

-- 3. Migrer vers la table finale
INSERT INTO users (phone_number, nom, prenom, profile_type, created_at)
SELECT phone_number, nom, prenom, profile_type, created_at
FROM temp_firebase_users;

-- 4. Nettoyer
DROP TABLE temp_firebase_users;
```

### Backup Automatique

```sql
-- Fonction de backup (à appeler via cron)
CREATE OR REPLACE FUNCTION backup_to_storage()
RETURNS void AS $$
DECLARE
  backup_data JSONB;
BEGIN
  -- Exporter les données en JSON
  SELECT jsonb_agg(row_to_json(u)) INTO backup_data FROM users u;
  
  -- Sauvegarder dans storage (via Edge Function)
  -- Implémenter selon vos besoins
END;
$$ LANGUAGE plpgsql;
```

---

## 📊 Monitoring et Analytics

### Supabase Dashboard

Supabase fournit un dashboard intégré avec :
- Métriques de base de données
- Logs d'authentification
- Utilisation du storage
- Requêtes lentes

### Logs Personnalisés

```sql
-- Table de logs d'activité
CREATE TABLE activity_logs (
  id UUID PRIMARY KEY DEFAULT uuid_generate_v4(),
  user_id UUID REFERENCES users(id),
  action TEXT NOT NULL,
  entity_type TEXT,
  entity_id UUID,
  metadata JSONB,
  ip_address INET,
  user_agent TEXT,
  created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Index pour les requêtes fréquentes
CREATE INDEX idx_activity_logs_user ON activity_logs(user_id);
CREATE INDEX idx_activity_logs_action ON activity_logs(action);
CREATE INDEX idx_activity_logs_created ON activity_logs(created_at DESC);

-- Fonction pour logger une activité
CREATE OR REPLACE FUNCTION log_activity(
  p_user_id UUID,
  p_action TEXT,
  p_entity_type TEXT DEFAULT NULL,
  p_entity_id UUID DEFAULT NULL,
  p_metadata JSONB DEFAULT NULL
)
RETURNS void AS $$
BEGIN
  INSERT INTO activity_logs (user_id, action, entity_type, entity_id, metadata)
  VALUES (p_user_id, p_action, p_entity_type, p_entity_id, p_metadata);
END;
$$ LANGUAGE plpgsql;
```

### Analytics Flutter

```dart
// Service d'analytics pour Supabase
class SupabaseAnalyticsService {
  final SupabaseClient _client = Supabase.instance.client;
  
  /// Logger une activité utilisateur
  Future<void> logActivity({
    required String action,
    String? entityType,
    String? entityId,
    Map<String, dynamic>? metadata,
  }) async {
    final userId = _client.auth.currentUser?.id;
    
    await _client.rpc('log_activity', params: {
      'p_user_id': userId,
      'p_action': action,
      'p_entity_type': entityType,
      'p_entity_id': entityId,
      'p_metadata': metadata,
    });
  }
  
  /// Événements prédéfinis
  Future<void> logVehicleView(String vehicleId) async {
    await logActivity(
      action: 'vehicle_view',
      entityType: 'vehicle',
      entityId: vehicleId,
    );
  }
  
  Future<void> logSearch(String query, int resultCount) async {
    await logActivity(
      action: 'search',
      metadata: {
        'query': query,
        'result_count': resultCount,
      },
    );
  }
  
  Future<void> logContactSeller(String vehicleId, String method) async {
    await logActivity(
      action: 'contact_seller',
      entityType: 'vehicle',
      entityId: vehicleId,
      metadata: {'method': method},
    );
  }
}
```

---

## 🔔 Notifications avec Supabase

### Edge Functions pour Notifications

```typescript
// supabase/functions/send-notification/index.ts
import { serve } from 'https://deno.land/std@0.168.0/http/server.ts'
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

const supabaseUrl = Deno.env.get('SUPABASE_URL')!
const supabaseKey = Deno.env.get('SUPABASE_SERVICE_ROLE_KEY')!
const twilioAccountSid = Deno.env.get('TWILIO_ACCOUNT_SID')!
const twilioAuthToken = Deno.env.get('TWILIO_AUTH_TOKEN')!
const twilioPhoneNumber = Deno.env.get('TWILIO_PHONE_NUMBER')!

serve(async (req) => {
  const { userId, title, body, data } = await req.json()
  
  const supabase = createClient(supabaseUrl, supabaseKey)
  
  // Récupérer le numéro de téléphone de l'utilisateur
  const { data: user } = await supabase
    .from('users')
    .select('phone_number')
    .eq('id', userId)
    .single()
  
  if (!user) {
    return new Response(JSON.stringify({ error: 'User not found' }), {
      status: 404,
    })
  }
  
  // Envoyer un SMS via Twilio
  const twilioUrl = `https://api.twilio.com/2010-04-01/Accounts/${twilioAccountSid}/Messages.json`
  
  const response = await fetch(twilioUrl, {
    method: 'POST',
    headers: {
      'Authorization': `Basic ${btoa(`${twilioAccountSid}:${twilioAuthToken}`)}`,
      'Content-Type': 'application/x-www-form-urlencoded',
    },
    body: new URLSearchParams({
      To: user.phone_number,
      From: twilioPhoneNumber,
      Body: `${title}\n${body}`,
    }),
  })
  
  const result = await response.json()
  
  return new Response(JSON.stringify(result), {
    headers: { 'Content-Type': 'application/json' },
  })
})
```

### Trigger pour Notifications Automatiques

```sql
-- Trigger pour notifier quand un véhicule favori baisse de prix
CREATE OR REPLACE FUNCTION notify_price_drop()
RETURNS TRIGGER AS $$
DECLARE
  fav RECORD;
BEGIN
  -- Vérifier si le prix a baissé
  IF NEW.price_in_fcfa < OLD.price_in_fcfa THEN
    -- Récupérer les utilisateurs qui ont ce véhicule en favori
    FOR fav IN 
      SELECT u.id, u.phone_number 
      FROM favorites f
      JOIN users u ON f.user_id = u.id
      WHERE f.vehicle_id = NEW.id
    LOOP
      -- Insérer une notification dans une table de queue
      INSERT INTO notification_queue (user_id, title, body, data)
      VALUES (
        fav.id,
        'Baisse de prix !',
        format('%s est passé de %s à %s FCFA', 
          NEW.name, 
          OLD.price_in_fcfa, 
          NEW.price_in_fcfa
        ),
        jsonb_build_object('vehicle_id', NEW.id)
      );
    END LOOP;
  END IF;
  
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER vehicle_price_drop_trigger
  AFTER UPDATE ON vehicles
  FOR EACH ROW
  EXECUTE FUNCTION notify_price_drop();
```

---

## 🔧 Troubleshooting Supabase

### Erreurs Courantes

| Erreur | Cause | Solution |
|--------|-------|----------|
| `PGRST301` | JWT expiré | Rafraîchir le token |
| `PGRST204` | Aucun résultat | Vérifier la requête |
| `42501` | Permission refusée | Vérifier les politiques RLS |
| `23505` | Violation d'unicité | Données dupliquées |
| `23503` | Violation de clé étrangère | Référence invalide |

### Debug Mode

```dart
// Activer les logs détaillés
Supabase.initialize(
  url: supabaseUrl,
  anonKey: supabaseAnonKey,
  debug: kDebugMode,
);

// Intercepter les erreurs
try {
  final response = await supabase.from('vehicles').select();
} on PostgrestException catch (e) {
  print('Code: ${e.code}');
  print('Message: ${e.message}');
  print('Details: ${e.details}');
  print('Hint: ${e.hint}');
}
```

---

## 🚀 Optimisations Avancées

### Requêtes Optimisées

```dart
// Pagination efficace avec curseur
Future<List<Vehicle>> getVehiclesPaginated({
  required int limit,
  String? lastId,
}) async {
  var query = supabase
      .from('vehicles')
      .select()
      .eq('status', 'active')
      .order('created_at', ascending: false)
      .limit(limit);
  
  if (lastId != null) {
    query = query.lt('id', lastId);
  }
  
  final response = await query;
  return response.map((json) => Vehicle.fromJson(json)).toList();
}

// Requête avec jointure
Future<List<VehicleWithOwner>> getVehiclesWithOwners() async {
  final response = await supabase
      .from('vehicles')
      .select('''
        *,
        owner:users!owner_id (
          id,
          nom,
          prenom,
          phone_number,
          avatar_url
        )
      ''')
      .eq('status', 'active');
  
  return response.map((json) => VehicleWithOwner.fromJson(json)).toList();
}
```

### Cache Local avec Hive

```dart
// Service de cache pour mode offline
class CacheService {
  static late Box<String> _vehiclesBox;
  
  static Future<void> initialize() async {
    await Hive.initFlutter();
    _vehiclesBox = await Hive.openBox<String>('vehicles_cache');
  }
  
  static Future<void> cacheVehicles(List<Vehicle> vehicles) async {
    final json = jsonEncode(vehicles.map((v) => v.toJson()).toList());
    await _vehiclesBox.put('featured', json);
    await _vehiclesBox.put('cached_at', DateTime.now().toIso8601String());
  }
  
  static List<Vehicle>? getCachedVehicles() {
    final json = _vehiclesBox.get('featured');
    if (json == null) return null;
    
    final cachedAt = DateTime.parse(_vehiclesBox.get('cached_at')!);
    final isExpired = DateTime.now().difference(cachedAt).inMinutes > 30;
    
    if (isExpired) return null;
    
    final list = jsonDecode(json) as List;
    return list.map((json) => Vehicle.fromJson(json)).toList();
  }
}
```
