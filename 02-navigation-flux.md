# Guide de Navigation et Flux Utilisateur - DealAuto

---
inclusion: always
---

## 🎯 Objectif

Ce document définit tous les flux de navigation de l'application DealAuto. **Chaque transition DOIT être fluide, cohérente et respecter les durées définies.**

---

## 🗺️ Flux Principal d'Authentification

```
┌─────────────────────┐
│    SplashScreen     │ ← Affichage 2-3 secondes
│   (Logo DealAuto)   │
└──────────┬──────────┘
           │ fadeRoute (auto)
           ▼
┌─────────────────────┐
│   WelcomeScreen     │
│ ┌─────────────────┐ │
│ │ Me connecter    │─┼──────────────────────────────┐
│ └─────────────────┘ │                              │
│ ┌─────────────────┐ │                              │
│ │ Créer un compte │─┼───────┐                      │
│ └─────────────────┘ │       │                      │
└─────────────────────┘       │                      │
                              │                      │
           ┌──────────────────┘                      │
           │ fadeSlideRoute                          │ fadeSlideRoute
           ▼                                         ▼
┌─────────────────────┐                   ┌─────────────────────┐
│ RegistrationScreen  │                   │    LoginScreen      │
│ ┌─────────────────┐ │                   │ ┌─────────────────┐ │
│ │ Créer compte    │─┼───┐               │ │ Me connecter    │─┼───┐
│ └─────────────────┘ │   │               │ └─────────────────┘ │   │
│ ┌─────────────────┐ │   │               │ ┌─────────────────┐ │   │
│ │ Déjà un compte  │─┼───┼───────────────┼─│ Mot de passe    │ │   │
│ └─────────────────┘ │   │               │ │ oublié ?        │─┼───┼───┐
└─────────────────────┘   │               │ └─────────────────┘ │   │   │
                          │               └─────────────────────┘   │   │
           ┌──────────────┘                                         │   │
           │ fadeSlideRoute                                         │   │
           ▼                                                        │   │
┌─────────────────────┐                                             │   │
│ RegistrationOTP     │                                             │   │
│ (Vérification code) │                                             │   │
└──────────┬──────────┘                                             │   │
           │ pushReplacement                                        │   │
           ▼                                                        │   │
┌─────────────────────┐                                             │   │
│ ProfileSelection    │                                             │   │
│ (Choix du profil)   │                                             │   │
└──────────┬──────────┘                                             │   │
           │ pushReplacement                                        │   │
           ▼                                                        │   │
┌─────────────────────┐                                             │   │
│ Congratulations     │                                             │   │
│ (Félicitations !)   │                                             │   │
└──────────┬──────────┘                                             │   │
           │                                                        │   │
           │ pushAndRemoveUntil                                     │   │
           │ (supprime historique)                                  │   │
           ▼                                                        │   │
┌─────────────────────────────────────────────────────────────────┐ │   │
│                      HomeDashboardScreen                        │◄┘   │
│  ┌─────────────────────────────────────────────────────────┐    │     │
│  │ Header | Recherche | Bannière | À la une | Catégories   │    │     │
│  │ Ventes Urgentes | Bottom Navigation                     │    │     │
│  └─────────────────────────────────────────────────────────┘    │     │
└─────────────────────────────────────────────────────────────────┘     │
                                                                        │
                              ┌─────────────────────────────────────────┘
                              │ fadeSlideRoute
                              ▼
                   ┌─────────────────────┐
                   │ PasswordRecovery    │
                   │ (Saisie téléphone)  │
                   └──────────┬──────────┘
                              │ fadeSlideRoute
                              ▼
                   ┌─────────────────────┐
                   │ OtpVerification     │
                   │ (Code récupération) │
                   └──────────┬──────────┘
                              │ pushReplacement
                              ▼
                   ┌─────────────────────┐
                   │ SetNewPassword      │
                   │ (Nouveau mot passe) │
                   └──────────┬──────────┘
                              │ pushAndRemoveUntil
                              ▼
                   ┌─────────────────────┐
                   │ HomeDashboardScreen │
                   └─────────────────────┘
```

---

## ⏱️ Service de Navigation

### Fichier : `lib/services/navigation_service.dart`

```dart
import 'package:flutter/material.dart';

class NavigationService {
  // Durées standard
  static const Duration _defaultDuration = Duration(milliseconds: 200);
  static const Duration _maxDuration = Duration(milliseconds: 300);
  
  /// Transition fade (fondu) - Pour transitions automatiques
  static Route<T> fadeRoute<T>({
    required Widget page,
    Duration? duration,
  }) {
    return PageRouteBuilder<T>(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: duration ?? _defaultDuration,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        return FadeTransition(opacity: animation, child: child);
      },
    );
  }
  
  /// Transition slide (glissement) - Pour navigation standard
  static Route<T> slideRoute<T>({
    required Widget page,
    Duration? duration,
  }) {
    return PageRouteBuilder<T>(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: duration ?? _defaultDuration,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        const begin = Offset(1.0, 0.0);
        const end = Offset.zero;
        final tween = Tween(begin: begin, end: end)
            .chain(CurveTween(curve: Curves.easeInOut));
        return SlideTransition(
          position: animation.drive(tween),
          child: child,
        );
      },
    );
  }
  
  /// Transition combinée (recommandée) - Fade + Slide
  static Route<T> fadeSlideRoute<T>({
    required Widget page,
    Duration? duration,
  }) {
    return PageRouteBuilder<T>(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: duration ?? _defaultDuration,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        const begin = Offset(0.3, 0.0);
        const end = Offset.zero;
        final slideTween = Tween(begin: begin, end: end)
            .chain(CurveTween(curve: Curves.easeOut));
        
        return FadeTransition(
          opacity: animation,
          child: SlideTransition(
            position: animation.drive(slideTween),
            child: child,
          ),
        );
      },
    );
  }
}
```

---

## 📱 Détail des Écrans et Navigations

### 1. SplashScreen → WelcomeScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/main.dart` |
| Durée affichage | 2-3 secondes |
| Transition | `fadeRoute` (automatique) |
| Type | `pushReplacement` |

```dart
@override
void initState() {
  super.initState();
  Future.delayed(const Duration(seconds: 2), () {
    if (mounted) {
      Navigator.of(context).pushReplacement(
        NavigationService.fadeRoute(page: const WelcomeScreen()),
      );
    }
  });
}
```

---

### 2. WelcomeScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/main.dart` |
| Bouton retour | ❌ Non |
| Actions | Login, Registration |

```dart
// Vers LoginScreen
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(page: const LoginScreen()),
);

// Vers RegistrationScreen
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(page: const RegistrationScreen()),
);
```

---

### 3. LoginScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/login_screen.dart` |
| Bouton retour | ✅ Oui (haut gauche) |
| Actions | Connexion, Mot de passe oublié, Créer compte |

```dart
// Retour
Navigator.pop(context);

// Mot de passe oublié
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(page: const PasswordRecoveryScreen()),
);

// Connexion réussie → Dashboard (supprime historique)
Navigator.pushAndRemoveUntil(
  context,
  NavigationService.fadeRoute(page: const HomeDashboardScreen()),
  (route) => false, // Supprime TOUT l'historique
);

// Créer un compte (remplace l'écran actuel)
Navigator.pushReplacement(
  context,
  NavigationService.fadeSlideRoute(page: const RegistrationScreen()),
);
```

---

### 4. RegistrationScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/registration_screen.dart` |
| Bouton retour | ✅ Oui |
| Actions | Créer compte, Déjà un compte |

```dart
// Retour
Navigator.pop(context);

// Vers OTP après validation formulaire
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(
    page: RegistrationOtpScreen(
      phoneNumber: _phoneController.text,
      countryCode: _selectedCountry.dialCode,
      nom: _nomController.text,
      prenom: _prenomController.text,
    ),
  ),
);

// Déjà un compte
Navigator.pushReplacement(
  context,
  NavigationService.fadeSlideRoute(page: const LoginScreen()),
);
```

---

### 5. RegistrationOtpScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/registration_otp_screen.dart` |
| Bouton retour | ✅ Oui |
| Actions | Valider OTP, Renvoyer code |

```dart
// Retour
Navigator.pop(context);

// OTP validé → Sélection profil (remplace)
Navigator.pushReplacement(
  context,
  NavigationService.fadeSlideRoute(page: const ProfileSelectionScreen()),
);
```

---

### 6. ProfileSelectionScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/profile_selection_screen.dart` |
| Bouton retour | ❌ Non (flux inscription) |
| Actions | Sélectionner profil, Suivant |

```dart
// Profil sélectionné → Félicitations (remplace)
Navigator.pushReplacement(
  context,
  NavigationService.fadeSlideRoute(page: const CongratulationsScreen()),
);
```

---

### 7. CongratulationsScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/congratulations_screen.dart` |
| Bouton retour | ❌ Non |
| Actions | Entrer dans l'univers DealAuto |

```dart
// Vers Dashboard (supprime TOUT l'historique)
Navigator.pushAndRemoveUntil(
  context,
  NavigationService.fadeRoute(page: const HomeDashboardScreen()),
  (route) => false,
);
```

---

### 8. HomeDashboardScreen

| Propriété | Valeur |
|-----------|--------|
| Fichier | `lib/screens/home_dashboard_screen.dart` |
| Bouton retour | ❌ Non (écran principal) |
| Actions | Navigation interne, Bottom nav |

```dart
// Vers détail véhicule (à implémenter)
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(
    page: VehicleDetailScreen(vehicle: vehicle),
  ),
);

// Vers catégorie
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(
    page: CategoryScreen(category: category),
  ),
);
```

---

### 9. Flux Récupération Mot de Passe

#### PasswordRecoveryScreen

```dart
// Vers OTP vérification
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(
    page: OtpVerificationScreen(
      phoneNumber: _phoneController.text,
      countryCode: _selectedCountry.dialCode,
    ),
  ),
);
```

#### OtpVerificationScreen

```dart
// OTP validé → Nouveau mot de passe (remplace)
Navigator.pushReplacement(
  context,
  NavigationService.fadeSlideRoute(page: const SetNewPasswordScreen()),
);
```

#### SetNewPasswordScreen

```dart
// Mot de passe changé → Dashboard (supprime historique)
Navigator.pushAndRemoveUntil(
  context,
  NavigationService.fadeRoute(page: const HomeDashboardScreen()),
  (route) => false,
);
```

---

## 📋 Règles de Navigation

### Types de Navigation

| Méthode | Usage | Effet |
|---------|-------|-------|
| `push` | Navigation standard | Ajoute à la pile |
| `pushReplacement` | Remplacer écran actuel | Remplace sans retour |
| `pushAndRemoveUntil` | Nouveau départ | Supprime historique |
| `pop` | Retour | Retire de la pile |

### Quand Utiliser Chaque Type

```dart
// push - Navigation standard (permet retour)
Navigator.push(context, route);
// Usage : Détail véhicule, Catégorie, Paramètres

// pushReplacement - Remplace l'écran (pas de retour à l'écran précédent)
Navigator.pushReplacement(context, route);
// Usage : OTP validé → Profil, Login ↔ Register

// pushAndRemoveUntil - Nouveau départ (supprime tout l'historique)
Navigator.pushAndRemoveUntil(context, route, (route) => false);
// Usage : Connexion réussie → Dashboard, Inscription terminée → Dashboard

// pop - Retour simple
Navigator.pop(context);
// Usage : Bouton retour, Fermer dialog
```

---

## ⬅️ Bouton Retour

### Style Standard

```dart
// Position : Haut gauche
// Taille touch target : 48x48px minimum
Positioned(
  top: MediaQuery.of(context).padding.top + 8,
  left: 8,
  child: Semantics(
    label: 'Retour',
    button: true,
    child: SizedBox(
      width: 48,
      height: 48,
      child: IconButton(
        icon: const Icon(
          Icons.arrow_back_ios,
          color: Color(0xFF0D3B66),
          size: 24,
        ),
        onPressed: () => Navigator.pop(context),
      ),
    ),
  ),
)
```

### Gestion Bouton Retour Android

```dart
WillPopScope(
  onWillPop: () async {
    // Logique personnalisée si nécessaire
    // Exemple : Confirmation avant de quitter un formulaire
    if (_hasUnsavedChanges) {
      return await _showExitConfirmation();
    }
    return true; // Autoriser le retour
  },
  child: Scaffold(...),
)
```

---

## ⏱️ Durées et Animations

| Type | Durée | Usage |
|------|-------|-------|
| Transition standard | 200ms | Navigation entre écrans |
| Transition maximale | 300ms | Animations complexes |
| Splash screen | 2000-3000ms | Affichage logo |
| Feedback tactile | 100ms | Ripple, highlight |

### Respect du Reduced Motion

```dart
@override
Widget build(BuildContext context) {
  final reducedMotion = MediaQuery.of(context).disableAnimations;
  
  if (reducedMotion) {
    // Navigation instantanée sans animation
    return MaterialPageRoute(builder: (_) => page);
  }
  
  // Navigation avec animation
  return NavigationService.fadeSlideRoute(page: page);
}
```

---

## ✅ Checklist Navigation

### Avant Chaque Navigation
- [ ] Vérifier `mounted` avant navigation async
- [ ] Utiliser le bon type de navigation (push/replace/removeUntil)
- [ ] Transition fluide (200ms)
- [ ] Bouton retour fonctionnel (si applicable)

### Accessibilité
- [ ] Semantics label sur bouton retour
- [ ] Touch target >= 48px
- [ ] Respect du reduced motion

### Sécurité
- [ ] Pas de navigation après dispose (SafeStateMixin)
- [ ] Historique correctement géré
- [ ] Pas de boucle de navigation possible


---

## 🔮 Écrans Futurs à Implémenter

### Flux Véhicules (À CRÉER)

```
┌─────────────────────┐
│ HomeDashboardScreen │
└──────────┬──────────┘
           │
    ┌──────┴──────┬──────────────┬─────────────┐
    │             │              │             │
    ▼             ▼              ▼             ▼
┌─────────┐ ┌──────────┐ ┌────────────┐ ┌──────────┐
│ Détail  │ │ Liste    │ │ Catégorie  │ │ Recherche│
│ Véhicule│ │ Favoris  │ │ Véhicules  │ │ Avancée  │
└────┬────┘ └──────────┘ └────────────┘ └──────────┘
     │
     ▼
┌─────────────────────┐
│ Contact Vendeur     │
│ (Appel / Message)   │
└─────────────────────┘
```

### Écrans à Créer

| Écran | Fichier | Description | Priorité |
|-------|---------|-------------|----------|
| VehicleDetailScreen | `vehicle_detail_screen.dart` | Détail complet d'un véhicule | Haute |
| VehicleListScreen | `vehicle_list_screen.dart` | Liste paginée de véhicules | Haute |
| CategoryScreen | `category_screen.dart` | Véhicules par catégorie | Haute |
| AddVehicleScreen | `add_vehicle_screen.dart` | Formulaire ajout véhicule | Haute |
| EditVehicleScreen | `edit_vehicle_screen.dart` | Modification véhicule | Moyenne |
| FavoritesScreen | `favorites_screen.dart` | Liste des favoris | Moyenne |
| UserProfileScreen | `user_profile_screen.dart` | Profil utilisateur | Moyenne |
| EditProfileScreen | `edit_profile_screen.dart` | Modification profil | Moyenne |
| SearchScreen | `search_screen.dart` | Recherche avancée | Moyenne |
| SettingsScreen | `settings_screen.dart` | Paramètres app | Basse |
| NotificationsScreen | `notifications_screen.dart` | Liste notifications | Basse |

### Navigation VehicleDetailScreen (Template)

```dart
/// Navigation vers le détail d'un véhicule
/// 
/// Depuis : HomeDashboardScreen, VehicleListScreen, CategoryScreen, FavoritesScreen
/// Vers : ContactVendeur (appel/message)
class VehicleDetailScreen extends StatefulWidget {
  final Vehicle vehicle;
  // OU
  final String vehicleId; // Pour chargement async
  
  const VehicleDetailScreen({
    super.key,
    required this.vehicle,
  });
  
  @override
  State<VehicleDetailScreen> createState() => _VehicleDetailScreenState();
}

// Navigation vers cet écran
Navigator.push(
  context,
  NavigationService.fadeSlideRoute(
    page: VehicleDetailScreen(vehicle: vehicle),
  ),
);

// Actions depuis cet écran
// 1. Retour
Navigator.pop(context);

// 2. Appeler le vendeur
await launchUrl(Uri.parse('tel:${vehicle.ownerPhone}'));

// 3. Envoyer un message WhatsApp
await launchUrl(Uri.parse('https://wa.me/${vehicle.ownerPhone}'));

// 4. Partager le véhicule
await Share.share('Découvrez ce véhicule sur DealAuto: ${vehicle.shareUrl}');

// 5. Ajouter/Retirer des favoris
await _toggleFavorite();
```

### Navigation AddVehicleScreen (Template)

```dart
/// Navigation vers l'ajout d'un véhicule
/// 
/// Accessible uniquement aux profils vendeurs
/// Depuis : HomeDashboardScreen (FAB ou menu), UserProfileScreen
class AddVehicleScreen extends StatefulWidget {
  const AddVehicleScreen({super.key});
}

// Vérification avant navigation
void _navigateToAddVehicle() {
  final user = context.read<UserProvider>().currentUser;
  
  if (user == null) {
    // Rediriger vers login
    Navigator.push(context, NavigationService.fadeSlideRoute(
      page: const LoginScreen(),
    ));
    return;
  }
  
  if (!user.profileType.canPublishVehicles) {
    // Afficher message d'erreur
    ScaffoldMessenger.of(context).showSnackBar(
      const SnackBar(
        content: Text('Seuls les vendeurs peuvent publier des annonces'),
      ),
    );
    return;
  }
  
  // Navigation autorisée
  Navigator.push(
    context,
    NavigationService.fadeSlideRoute(page: const AddVehicleScreen()),
  );
}

// Après ajout réussi
Navigator.pop(context, newVehicle); // Retourne le véhicule créé
```

---

## 🧭 Bottom Navigation (À IMPLÉMENTER)

### Structure

```dart
/// Bottom Navigation Bar DealAuto
/// 
/// 5 onglets :
/// 1. Accueil (Home)
/// 2. Recherche (Search)
/// 3. Ajouter (Add) - FAB central
/// 4. Favoris (Favorites)
/// 5. Profil (Profile)
class MainNavigationScreen extends StatefulWidget {
  const MainNavigationScreen({super.key});
  
  @override
  State<MainNavigationScreen> createState() => _MainNavigationScreenState();
}

class _MainNavigationScreenState extends State<MainNavigationScreen> {
  int _currentIndex = 0;
  
  final List<Widget> _screens = [
    const HomeDashboardScreen(),
    const SearchScreen(),
    const SizedBox(), // Placeholder pour FAB
    const FavoritesScreen(),
    const UserProfileScreen(),
  ];
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      body: IndexedStack(
        index: _currentIndex,
        children: _screens,
      ),
      bottomNavigationBar: _buildBottomNav(),
      floatingActionButton: _buildAddFAB(),
      floatingActionButtonLocation: FloatingActionButtonLocation.centerDocked,
    );
  }
  
  Widget _buildBottomNav() {
    return BottomAppBar(
      shape: const CircularNotchedRectangle(),
      notchMargin: 8,
      child: Row(
        mainAxisAlignment: MainAxisAlignment.spaceAround,
        children: [
          _buildNavItem(0, Icons.home_outlined, Icons.home, 'Accueil'),
          _buildNavItem(1, Icons.search_outlined, Icons.search, 'Recherche'),
          const SizedBox(width: 48), // Espace pour FAB
          _buildNavItem(3, Icons.favorite_outline, Icons.favorite, 'Favoris'),
          _buildNavItem(4, Icons.person_outline, Icons.person, 'Profil'),
        ],
      ),
    );
  }
  
  Widget _buildNavItem(int index, IconData icon, IconData activeIcon, String label) {
    final isActive = _currentIndex == index;
    return Semantics(
      label: label,
      button: true,
      selected: isActive,
      child: InkWell(
        onTap: () => setState(() => _currentIndex = index),
        child: Padding(
          padding: const EdgeInsets.symmetric(horizontal: 16, vertical: 8),
          child: Column(
            mainAxisSize: MainAxisSize.min,
            children: [
              Icon(
                isActive ? activeIcon : icon,
                color: isActive 
                    ? const Color(0xFF0D3B66) 
                    : const Color(0xFF999999),
              ),
              const SizedBox(height: 4),
              Text(
                label,
                style: TextStyle(
                  fontSize: 12,
                  color: isActive 
                      ? const Color(0xFF0D3B66) 
                      : const Color(0xFF999999),
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
  
  Widget _buildAddFAB() {
    return FloatingActionButton(
      onPressed: _navigateToAddVehicle,
      backgroundColor: const Color(0xFFE07A2F),
      child: const Icon(Icons.add, color: Colors.white),
    );
  }
}
```

---

## 🔐 Navigation Authentifiée

### Garde de Navigation

```dart
/// Middleware de navigation pour vérifier l'authentification
/// 
/// Usage :
/// ```dart
/// Navigator.push(context, AuthGuard.protectedRoute(
///   page: const AddVehicleScreen(),
///   context: context,
/// ));
/// ```
class AuthGuard {
  /// Route protégée nécessitant une authentification
  static Route<T> protectedRoute<T>({
    required Widget page,
    required BuildContext context,
  }) {
    final authService = context.read<AuthService>();
    
    if (!authService.isAuthenticated) {
      // Rediriger vers login avec callback
      return NavigationService.fadeSlideRoute(
        page: LoginScreen(
          onLoginSuccess: () {
            Navigator.pushReplacement(
              context,
              NavigationService.fadeSlideRoute(page: page),
            );
          },
        ),
      );
    }
    
    return NavigationService.fadeSlideRoute(page: page);
  }
  
  /// Route nécessitant un profil vendeur
  static Route<T> sellerRoute<T>({
    required Widget page,
    required BuildContext context,
  }) {
    final user = context.read<UserProvider>().currentUser;
    
    if (user == null) {
      return protectedRoute(page: page, context: context);
    }
    
    if (!user.profileType.canPublishVehicles) {
      // Afficher écran d'upgrade de profil
      return NavigationService.fadeSlideRoute(
        page: const UpgradeProfileScreen(),
      );
    }
    
    return NavigationService.fadeSlideRoute(page: page);
  }
}
```

---

## 📱 Deep Links (À IMPLÉMENTER)

### Configuration

```dart
/// Deep links DealAuto
/// 
/// Schémas supportés :
/// - dealauto://vehicle/{id} → VehicleDetailScreen
/// - dealauto://category/{name} → CategoryScreen
/// - dealauto://profile/{id} → UserProfileScreen
/// - https://dealauto.tg/v/{id} → VehicleDetailScreen (web)
class DeepLinkHandler {
  static void handleDeepLink(Uri uri, BuildContext context) {
    final path = uri.pathSegments;
    
    if (path.isEmpty) return;
    
    switch (path[0]) {
      case 'vehicle':
      case 'v':
        if (path.length > 1) {
          _navigateToVehicle(path[1], context);
        }
        break;
      case 'category':
        if (path.length > 1) {
          _navigateToCategory(path[1], context);
        }
        break;
      case 'profile':
        if (path.length > 1) {
          _navigateToProfile(path[1], context);
        }
        break;
    }
  }
  
  static void _navigateToVehicle(String vehicleId, BuildContext context) {
    Navigator.push(
      context,
      NavigationService.fadeSlideRoute(
        page: VehicleDetailScreen(vehicleId: vehicleId),
      ),
    );
  }
}
```

---

## 📊 Analytics de Navigation (À IMPLÉMENTER)

### Tracking des Écrans

```dart
/// Observer de navigation pour analytics
class AnalyticsNavigatorObserver extends NavigatorObserver {
  @override
  void didPush(Route<dynamic> route, Route<dynamic>? previousRoute) {
    _trackScreen(route);
  }
  
  @override
  void didPop(Route<dynamic> route, Route<dynamic>? previousRoute) {
    if (previousRoute != null) {
      _trackScreen(previousRoute);
    }
  }
  
  void _trackScreen(Route<dynamic> route) {
    final screenName = route.settings.name ?? 'unknown';
    
    // Firebase Analytics
    // FirebaseAnalytics.instance.setCurrentScreen(screenName: screenName);
    
    // Ou autre service d'analytics
    print('Screen viewed: $screenName');
  }
}

// Usage dans MaterialApp
MaterialApp(
  navigatorObservers: [
    AnalyticsNavigatorObserver(),
  ],
  // ...
)
```
