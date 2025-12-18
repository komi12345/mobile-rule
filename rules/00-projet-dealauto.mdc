# DealAuto - Guide Principal du Projet

---
inclusion: always
---

## 📋 Informations Générales

| Attribut | Valeur |
|----------|--------|
| **Nom du projet** | DealAuto |
| **Type** | Application mobile marketplace automobile |
| **Framework** | Flutter 3.10+ (Dart 3.0+) |
| **Plateformes** | Android (prioritaire), iOS (prévu) |
| **Pays cible** | Togo (Afrique de l'Ouest) |
| **Devise** | FCFA (Franc CFA - XOF) |
| **Langue UI** | Français |
| **Statut** | MVP en développement (~45% complété) |
| **Date de création** | Décembre 2025 |

---

## 🎯 Vision et Objectifs du Projet

### Vision
Créer la première marketplace automobile de référence au Togo, connectant acheteurs et vendeurs de véhicules dans un environnement de confiance, moderne et accessible.

### Objectifs MVP
1. **Authentification sécurisée** par téléphone (SMS OTP)
2. **Catalogue de véhicules** avec recherche et filtres
3. **Profils utilisateurs** différenciés (acheteur, vendeur, concessionnaire)
4. **Contact direct** entre acheteurs et vendeurs
5. **Interface intuitive** adaptée au marché togolais

### Objectifs Post-MVP
- Système de paiement intégré (Fedapay, Mobile Money)
- Chat en temps réel entre utilisateurs
- Notifications push pour les nouvelles annonces
- Système d'abonnements pour les professionnels
- Vérification des véhicules et vendeurs
- Statistiques et analytics pour les vendeurs

---

## 🎨 Identité Visuelle Officielle

### Logo DealAuto
- **"Deal"** : Bleu foncé (`#0D3B66`) - Représente la confiance et le professionnalisme
- **"Auto"** : Orange (`#E07A2F`) - Représente l'énergie et l'action
- **Widget** : `lib/widgets/deal_auto_logo.dart`
- **Usage** : `DealAutoLogo(size: 32)` - paramètre `size` ajustable

### Palette de Couleurs Complète

```dart
/// Palette de couleurs officielle DealAuto
/// À utiliser dans TOUT le projet pour garantir la cohérence visuelle
class DealAutoColors {
  // ══════════════════════════════════════════════════════════════
  // COULEURS PRIMAIRES
  // ══════════════════════════════════════════════════════════════
  
  /// Bleu foncé - Couleur principale
  /// Usage : Textes principaux, boutons primaires, headers
  static const Color darkBlue = Color(0xFF0D3B66);
  
  /// Orange - Couleur d'accent
  /// Usage : Accents, mot "Auto" du logo, éléments d'action secondaires
  static const Color orange = Color(0xFFE07A2F);
  
  // ══════════════════════════════════════════════════════════════
  // COULEURS DE FOND
  // ══════════════════════════════════════════════════════════════
  
  /// Crème léger - Fond principal des écrans
  /// Usage : Background de tous les Scaffold
  static const Color backgroundColor = Color(0xFFF5F5F0);
  
  /// Blanc pur - Fond des cartes et champs
  /// Usage : Cards, TextFields, Dialogs
  static const Color white = Color(0xFFFFFFFF);
  
  /// Gris très clair - Fond des éléments désactivés
  static const Color lightGrey = Color(0xFFF0F0F0);
  
  // ══════════════════════════════════════════════════════════════
  // COULEURS DE TEXTE
  // ══════════════════════════════════════════════════════════════
  
  /// Texte principal - Même que darkBlue
  static const Color textPrimary = Color(0xFF0D3B66);
  
  /// Texte secondaire - Gris moyen
  /// Usage : Descriptions, sous-titres, métadonnées
  static const Color textSecondary = Color(0xFF666666);
  
  /// Texte placeholder/hint - Gris clair
  /// Usage : Placeholders dans les champs de formulaire
  static const Color textHint = Color(0xFF999999);
  
  /// Texte sur fond sombre - Blanc
  static const Color textOnDark = Color(0xFFFFFFFF);
  
  /// Texte sur fond sombre avec opacité
  static const Color textOnDarkMuted = Color(0xCCFFFFFF); // 80% opacity
  
  // ══════════════════════════════════════════════════════════════
  // COULEURS DE BORDURE ET ÉTATS
  // ══════════════════════════════════════════════════════════════
  
  /// Bordure normale des champs
  static const Color border = Color(0xFFE0E0E0);
  
  /// Bordure au focus
  static const Color borderFocus = Color(0xFF0D3B66);
  
  /// Bordure en erreur
  static const Color borderError = Color(0xFFE53935);
  
  /// État désactivé
  static const Color disabled = Color(0xFFCCCCCC);
  
  // ══════════════════════════════════════════════════════════════
  // COULEURS DE FEEDBACK ET BADGES
  // ══════════════════════════════════════════════════════════════
  
  /// Succès - Bleu clair
  static const Color success = Color(0xFF4A90D9);
  
  /// Succès alternatif - Vert
  static const Color successGreen = Color(0xFF43A047);
  
  /// Erreur - Rouge
  static const Color error = Color(0xFFE53935);
  
  /// Avertissement - Orange
  static const Color warning = Color(0xFFFF9800);
  
  /// Information - Bleu
  static const Color info = Color(0xFF2196F3);
  
  /// Badge PROMO - Rouge vif
  static const Color promoRed = Color(0xFFE53935);
  
  /// Badge NOUVEAU - Vert
  static const Color newGreen = Color(0xFF43A047);
  
  /// Badge URGENT - Orange
  static const Color urgentOrange = Color(0xFFFF5722);
  
  // ══════════════════════════════════════════════════════════════
  // COULEURS SPÉCIFIQUES AUX CATÉGORIES
  // ══════════════════════════════════════════════════════════════
  
  /// Voitures
  static const Color categoryVoitures = Color(0xFF0D3B66);
  
  /// Camions
  static const Color categoryCamions = Color(0xFF1565C0);
  
  /// Bus
  static const Color categoryBus = Color(0xFF7B1FA2);
  
  /// Motos
  static const Color categoryMotos = Color(0xFFE07A2F);
  
  /// Concessionnaires
  static const Color categoryConcessionnaires = Color(0xFF2E7D32);
  
  /// Pièces détachées
  static const Color categoryPieces = Color(0xFF5D4037);
}
```


### Espacements et Dimensions Standards

```dart
/// Système d'espacement DealAuto
/// Basé sur une grille de 4px pour la cohérence
class DealAutoSpacing {
  // Espacements de base
  static const double xs = 4.0;    // Extra small
  static const double sm = 8.0;    // Small
  static const double md = 16.0;   // Medium (défaut)
  static const double lg = 24.0;   // Large
  static const double xl = 32.0;   // Extra large
  static const double xxl = 48.0;  // Extra extra large
  
  // Espacements spécifiques
  static const double screenPadding = 24.0;      // Padding horizontal des écrans
  static const double sectionSpacing = 24.0;     // Entre les sections
  static const double elementSpacing = 16.0;     // Entre les éléments
  static const double compactSpacing = 8.0;      // Espacement compact
  static const double cardPadding = 16.0;        // Padding interne des cartes
  
  // Dimensions des composants
  static const double buttonHeight = 56.0;       // Hauteur des boutons
  static const double inputHeight = 56.0;        // Hauteur des champs
  static const double touchTarget = 48.0;        // Taille minimale tactile
  static const double iconSize = 24.0;           // Taille des icônes standard
  static const double iconSizeSmall = 20.0;      // Petites icônes
  static const double iconSizeLarge = 32.0;      // Grandes icônes
  
  // Border radius
  static const double radiusSmall = 8.0;         // Petits éléments
  static const double radiusMedium = 12.0;       // Champs, boutons
  static const double radiusLarge = 16.0;        // Cartes
  static const double radiusXLarge = 24.0;       // Bottom sheets
}
```

---

## 🏗️ Architecture du Projet

### Structure des Dossiers Complète

```
dealauto/
├── lib/
│   ├── main.dart                    # Point d'entrée de l'application
│   │                                # Contient : MyApp, SplashScreen, WelcomeScreen
│   │
│   ├── config/                      # [À CRÉER] Configuration de l'application
│   │   ├── app_config.dart          # Variables d'environnement
│   │   ├── routes.dart              # Définition des routes nommées
│   │   └── theme.dart               # Thème MaterialApp complet
│   │
│   ├── data/
│   │   └── country_data.dart        # Liste des 240+ pays (ISO 3166-1)
│   │
│   ├── models/
│   │   ├── country.dart             # Modèle Country (name, code, dialCode, flag)
│   │   ├── profile_type.dart        # Enum ProfileType (5 types)
│   │   ├── profile_card_data.dart   # Données pour les cartes de profil
│   │   ├── vehicle.dart             # Modèle Vehicle complet
│   │   ├── vehicle_category.dart    # Enum VehicleCategory (6 catégories)
│   │   └── user.dart                # [À CRÉER] Modèle User
│   │
│   ├── screens/
│   │   ├── auth/                    # [À RÉORGANISER] Écrans d'authentification
│   │   │   ├── login_screen.dart
│   │   │   ├── registration_screen.dart
│   │   │   ├── registration_otp_screen.dart
│   │   │   ├── password_recovery_screen.dart
│   │   │   ├── otp_verification_screen.dart
│   │   │   └── set_new_password_screen.dart
│   │   │
│   │   ├── onboarding/              # [À RÉORGANISER] Écrans d'onboarding
│   │   │   ├── profile_selection_screen.dart
│   │   │   └── congratulations_screen.dart
│   │   │
│   │   ├── home/                    # [À RÉORGANISER] Écrans principaux
│   │   │   └── home_dashboard_screen.dart
│   │   │
│   │   ├── vehicles/                # [À CRÉER] Écrans véhicules
│   │   │   ├── vehicle_detail_screen.dart
│   │   │   ├── vehicle_list_screen.dart
│   │   │   ├── add_vehicle_screen.dart
│   │   │   └── edit_vehicle_screen.dart
│   │   │
│   │   ├── profile/                 # [À CRÉER] Écrans profil
│   │   │   ├── user_profile_screen.dart
│   │   │   └── edit_profile_screen.dart
│   │   │
│   │   ├── favorites/               # [À CRÉER] Écrans favoris
│   │   │   └── favorites_screen.dart
│   │   │
│   │   └── settings/                # [À CRÉER] Écrans paramètres
│   │       └── settings_screen.dart
│   │
│   ├── services/
│   │   ├── country_data_service.dart      # Service async pour les pays
│   │   ├── image_optimization_service.dart # Optimisation et cache images
│   │   ├── navigation_service.dart         # Transitions de navigation
│   │   ├── auth_service.dart              # [À CRÉER] Service d'authentification
│   │   ├── vehicle_service.dart           # [À CRÉER] Service véhicules
│   │   ├── user_service.dart              # [À CRÉER] Service utilisateurs
│   │   ├── storage_service.dart           # [À CRÉER] Service stockage fichiers
│   │   └── notification_service.dart      # [À CRÉER] Service notifications
│   │
│   ├── repositories/                # [À CRÉER] Couche d'accès aux données
│   │   ├── user_repository.dart
│   │   ├── vehicle_repository.dart
│   │   └── favorites_repository.dart
│   │
│   ├── providers/                   # [À CRÉER] State management
│   │   ├── auth_provider.dart
│   │   ├── user_provider.dart
│   │   ├── vehicle_provider.dart
│   │   └── favorites_provider.dart
│   │
│   ├── utils/
│   │   ├── accessibility_utils.dart  # Calcul contraste WCAG, helpers a11y
│   │   ├── debouncer.dart            # Debouncing pour recherche
│   │   ├── error_text_utils.dart     # Gestion overflow textes d'erreur
│   │   ├── image_error_handler.dart  # Placeholders pour images en erreur
│   │   ├── responsive_utils.dart     # Breakpoints, touch targets, scaling
│   │   ├── safe_state_mixin.dart     # Mixin pour setState sécurisé
│   │   ├── validators.dart           # [À CRÉER] Validateurs de formulaires
│   │   ├── formatters.dart           # [À CRÉER] Formatage prix, dates, etc.
│   │   └── constants.dart            # [À CRÉER] Constantes globales
│   │
│   └── widgets/
│       ├── common/                   # [À RÉORGANISER] Widgets communs
│       │   ├── deal_auto_logo.dart
│       │   ├── decorative_dots.dart
│       │   └── success_indicator.dart
│       │
│       ├── forms/                    # [À RÉORGANISER] Widgets de formulaire
│       │   ├── phone_input_field.dart
│       │   ├── password_text_field.dart
│       │   ├── name_text_field.dart
│       │   ├── country_picker_dialog.dart
│       │   └── country_dropdown_field.dart
│       │
│       ├── cards/                    # [À RÉORGANISER] Widgets de cartes
│       │   ├── profile_card.dart
│       │   └── vehicle_card.dart
│       │
│       ├── home/                     # [À RÉORGANISER] Widgets du dashboard
│       │   ├── category_icon.dart
│       │   ├── search_bar_widget.dart
│       │   └── contact_banner_widget.dart
│       │
│       └── shared/                   # [À CRÉER] Widgets partagés
│           ├── loading_indicator.dart
│           ├── error_widget.dart
│           ├── empty_state_widget.dart
│           └── scoped_state_builder.dart
│
├── test/                            # 52 fichiers de tests
│   ├── unit/                        # [À RÉORGANISER] Tests unitaires
│   ├── property/                    # [À RÉORGANISER] Tests property-based
│   └── widget/                      # [À RÉORGANISER] Tests widgets
│
├── assets/
│   └── images/                      # Images de l'application
│       ├── logo.png                 # [À AJOUTER] Logo haute résolution
│       ├── splash_logo.png          # [À AJOUTER] Logo pour splash screen
│       └── placeholder_vehicle.png  # [À AJOUTER] Placeholder véhicule
│
├── android/                         # Configuration Android
├── ios/                             # Configuration iOS
├── pubspec.yaml                     # Dépendances
└── analysis_options.yaml            # Configuration linter
```


---

## 👥 Types de Profils Utilisateur

### Définition Complète

| ID | Type | Code | Description | Fonctionnalités |
|----|------|------|-------------|-----------------|
| 1 | Client simple | `clientSimple` | Acheteur de véhicules | Recherche, favoris, contact vendeurs |
| 2 | Vendeur simple | `vendeurSimple` | Vendeur particulier | + Publier annonces (limite 5) |
| 3 | Propriétaire de parc | `proprietaireParc` | Parc auto Port de Lomé | + Annonces illimitées, badge vérifié |
| 4 | Concessionnaire | `concessionnaire` | Professionnel automobile | + Vitrine dédiée, statistiques |
| 5 | Vendeur pièces | `vendeurPiecesDetachees` | Vendeur pièces auto | + Catégorie pièces détachées |

### Implémentation

```dart
// lib/models/profile_type.dart
enum ProfileType {
  clientSimple,
  vendeurSimple,
  proprietaireParc,
  concessionnaire,
  vendeurPiecesDetachees,
}

extension ProfileTypeExtension on ProfileType {
  String get displayName {
    switch (this) {
      case ProfileType.clientSimple:
        return 'Client simple';
      case ProfileType.vendeurSimple:
        return 'Vendeur simple';
      case ProfileType.proprietaireParc:
        return 'Propriétaire de parc auto';
      case ProfileType.concessionnaire:
        return 'Concessionnaire';
      case ProfileType.vendeurPiecesDetachees:
        return 'Vendeur de pièces détachées';
    }
  }
  
  String get description {
    switch (this) {
      case ProfileType.clientSimple:
        return 'Je cherche à acheter un véhicule';
      case ProfileType.vendeurSimple:
        return 'Je veux vendre mon véhicule';
      case ProfileType.proprietaireParc:
        return 'Je gère un parc automobile au port de Lomé';
      case ProfileType.concessionnaire:
        return 'Je suis un professionnel de l\'automobile';
      case ProfileType.vendeurPiecesDetachees:
        return 'Je vends des pièces détachées automobiles';
    }
  }
  
  IconData get icon {
    switch (this) {
      case ProfileType.clientSimple:
        return Icons.person_outline;
      case ProfileType.vendeurSimple:
        return Icons.sell_outlined;
      case ProfileType.proprietaireParc:
        return Icons.business_outlined;
      case ProfileType.concessionnaire:
        return Icons.store_outlined;
      case ProfileType.vendeurPiecesDetachees:
        return Icons.build_outlined;
    }
  }
  
  Color get color {
    switch (this) {
      case ProfileType.clientSimple:
        return const Color(0xFF4A90D9);
      case ProfileType.vendeurSimple:
        return const Color(0xFFE07A2F);
      case ProfileType.proprietaireParc:
        return const Color(0xFF2E7D32);
      case ProfileType.concessionnaire:
        return const Color(0xFF7B1FA2);
      case ProfileType.vendeurPiecesDetachees:
        return const Color(0xFF5D4037);
    }
  }
  
  bool get canPublishVehicles {
    return this != ProfileType.clientSimple;
  }
  
  int get maxVehicles {
    switch (this) {
      case ProfileType.clientSimple:
        return 0;
      case ProfileType.vendeurSimple:
        return 5;
      case ProfileType.proprietaireParc:
      case ProfileType.concessionnaire:
      case ProfileType.vendeurPiecesDetachees:
        return -1; // Illimité
    }
  }
}
```

---

## 🚗 Catégories de Véhicules

### Définition Complète

| ID | Catégorie | Code | Icône | Couleur |
|----|-----------|------|-------|---------|
| 1 | Voitures | `voitures` | `Icons.directions_car` | `#0D3B66` |
| 2 | Camions | `camions` | `Icons.local_shipping` | `#1565C0` |
| 3 | Bus | `bus` | `Icons.directions_bus` | `#7B1FA2` |
| 4 | Motos | `motos` | `Icons.two_wheeler` | `#E07A2F` |
| 5 | Concessionnaires | `concessionnaires` | `Icons.store` | `#2E7D32` |
| 6 | Pièces détachées | `piecesDetachees` | `Icons.build` | `#5D4037` |

---

## 📏 Conventions de Code

### Nommage

| Élément | Convention | Exemple | Règle |
|---------|------------|---------|-------|
| Fichiers | snake_case | `login_screen.dart` | Tout en minuscules |
| Classes | PascalCase | `LoginScreen` | Première lettre majuscule |
| Variables | camelCase | `selectedCountry` | Première lettre minuscule |
| Variables privées | _camelCase | `_selectedCountry` | Préfixe underscore |
| Constantes | camelCase + static const | `static const darkBlue` | Dans une classe |
| Enums | PascalCase | `ProfileType` | Valeurs en camelCase |

### Imports - Ordre Obligatoire

```dart
// 1. Packages Dart core
import 'dart:async';
import 'dart:convert';
import 'dart:io';

// 2. Packages Flutter
import 'package:flutter/material.dart';
import 'package:flutter/services.dart';

// 3. Packages externes (pub.dev)
import 'package:provider/provider.dart';
import 'package:supabase_flutter/supabase_flutter.dart';

// 4. Imports locaux - par ordre alphabétique
import '../models/vehicle.dart';
import '../services/auth_service.dart';
import '../utils/constants.dart';
```

### Structure d'un Widget - Template

```dart
/// Description du widget en une ligne
/// 
/// Ce widget est utilisé pour [description détaillée].
/// 
/// Exemple d'utilisation :
/// ```dart
/// MonWidget(
///   title: 'Titre',
///   onTap: () => print('Tapped'),
/// )
/// ```
/// 
/// Requirements: X.X, X.X
class MonWidget extends StatelessWidget {
  // ══════════════════════════════════════════════════════════════
  // CONSTANTES STATIQUES
  // ══════════════════════════════════════════════════════════════
  
  static const Color _primaryColor = Color(0xFF0D3B66);
  static const double _defaultPadding = 16.0;
  
  // ══════════════════════════════════════════════════════════════
  // PROPRIÉTÉS
  // ══════════════════════════════════════════════════════════════
  
  /// Titre affiché dans le widget
  final String title;
  
  /// Callback appelé lors du tap (optionnel)
  final VoidCallback? onTap;
  
  /// Indique si le widget est activé
  final bool isEnabled;
  
  // ══════════════════════════════════════════════════════════════
  // CONSTRUCTEUR
  // ══════════════════════════════════════════════════════════════
  
  const MonWidget({
    super.key,
    required this.title,
    this.onTap,
    this.isEnabled = true,
  });
  
  // ══════════════════════════════════════════════════════════════
  // BUILD
  // ══════════════════════════════════════════════════════════════
  
  @override
  Widget build(BuildContext context) {
    return Semantics(
      label: title,
      button: onTap != null,
      enabled: isEnabled,
      child: _buildContent(context),
    );
  }
  
  // ══════════════════════════════════════════════════════════════
  // MÉTHODES PRIVÉES
  // ══════════════════════════════════════════════════════════════
  
  Widget _buildContent(BuildContext context) {
    return Container(
      padding: const EdgeInsets.all(_defaultPadding),
      child: Text(title),
    );
  }
}
```


---

## 📦 Dépendances du Projet

### Dépendances Actuelles

```yaml
# pubspec.yaml - État actuel
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^1.0.8

dev_dependencies:
  flutter_test:
    sdk: flutter
  glados: ^1.1.1           # Property-based testing
  flutter_lints: ^6.0.0    # Règles de linting
```

### Dépendances à Ajouter (selon le backend choisi)

```yaml
# ══════════════════════════════════════════════════════════════
# OPTION 1 : FIREBASE
# ══════════════════════════════════════════════════════════════
dependencies:
  firebase_core: ^3.8.1
  firebase_auth: ^5.3.4
  cloud_firestore: ^5.6.0
  firebase_storage: ^12.4.0
  firebase_messaging: ^15.1.6    # Push notifications

# ══════════════════════════════════════════════════════════════
# OPTION 2 : SUPABASE
# ══════════════════════════════════════════════════════════════
dependencies:
  supabase_flutter: ^2.8.3
  hive: ^2.2.3                   # Stockage local offline
  hive_flutter: ^1.1.0

# ══════════════════════════════════════════════════════════════
# OPTION 3 : VPS CUSTOM
# ══════════════════════════════════════════════════════════════
dependencies:
  http: ^1.2.0                   # Requêtes HTTP
  dio: ^5.4.0                    # Alternative HTTP avancée
  flutter_secure_storage: ^9.2.2 # Stockage sécurisé tokens

# ══════════════════════════════════════════════════════════════
# COMMUN À TOUTES LES OPTIONS
# ══════════════════════════════════════════════════════════════
dependencies:
  # State Management
  provider: ^6.1.2               # Recommandé pour MVP
  # OU
  flutter_riverpod: ^2.5.1       # Alternative plus avancée
  
  # UI et UX
  cached_network_image: ^3.3.1   # Cache images réseau
  shimmer: ^3.0.0                # Effet de chargement
  flutter_svg: ^2.0.10           # Support SVG
  
  # Fonctionnalités
  url_launcher: ^6.2.5           # Ouvrir liens, appels téléphoniques
  image_picker: ^1.0.7           # Sélection photos
  permission_handler: ^11.3.0    # Gestion permissions
  share_plus: ^7.2.2             # Partage
  
  # Utilitaires
  intl: ^0.19.0                  # Internationalisation, formatage
  connectivity_plus: ^6.0.2      # Vérification connexion
  package_info_plus: ^5.0.1      # Infos de l'app
```

---

## 📞 Informations de Contact et Configuration

### Contacts Support

| Type | Valeur | Usage |
|------|--------|-------|
| Téléphone dédouanement | +228 90 48 06 06 | Bannière contact dashboard |
| WhatsApp support | +228 XX XX XX XX | [À DÉFINIR] Support client |
| Email support | support@dealauto.tg | [À DÉFINIR] Support email |

### Configuration par Défaut

| Paramètre | Valeur | Description |
|-----------|--------|-------------|
| Pays par défaut | Togo (TG) | Sélection initiale |
| Indicatif par défaut | +228 | Préfixe téléphone |
| Devise | FCFA (XOF) | Affichage des prix |
| Langue | Français | Interface utilisateur |
| Timezone | Africa/Lome | Fuseau horaire |

---

## 🔗 Références Documentaires

### Documents Steering

| Document | Chemin | Description | Inclusion |
|----------|--------|-------------|-----------|
| Guide Principal | `00-projet-dealauto.md` | Ce document | always |
| Design UI Figma | `01-design-ui-figma.md` | Conformité maquettes | always |
| Navigation | `02-navigation-flux.md` | Flux utilisateur | always |
| Backend Firebase | `03-backend-firebase.md` | Solution Firebase | manual |
| Backend Supabase | `04-backend-supabase.md` | Solution Supabase | manual |
| Backend VPS | `05-backend-vps-contabo.md` | Solution VPS | manual |
| Qualité & Tests | `06-qualite-tests.md` | Standards qualité | always |
| Accessibilité | `07-accessibilite-performance.md` | Performance & A11y | always |

### Specs Existantes

Le projet contient 15 specs dans `.kiro/specs/` documentant les fonctionnalités implémentées :
- `android-optimization` - Optimisations Android
- `async-country-loading` - Chargement async des pays
- `congratulations-screen` - Écran de félicitations
- `flutter-performance-optimization` - Optimisations Flutter
- `home-dashboard-screen` - Dashboard principal
- `login-back-button` - Bouton retour login
- `login-screen` - Écran de connexion
- `otp-verification-screen` - Vérification OTP
- `password-recovery-screen` - Récupération mot de passe
- `profile-selection-screen` - Sélection du profil
- `registration-otp-verification` - OTP inscription
- `registration-screen` - Écran d'inscription
- `set-new-password-screen` - Nouveau mot de passe
- `splash-screen` - Écran de démarrage
- `welcome-screen` - Écran d'accueil

---

## 🚀 Prochaines Étapes de Développement

### Phase 1 : Backend (Priorité Haute)
1. Choisir la solution backend (Firebase/Supabase/VPS)
2. Configurer l'authentification SMS
3. Implémenter les repositories
4. Connecter les écrans existants

### Phase 2 : Fonctionnalités Core (Priorité Haute)
1. Écran détail véhicule
2. Écran ajout/modification véhicule
3. Écran profil utilisateur
4. Liste des favoris

### Phase 3 : Améliorations (Priorité Moyenne)
1. Recherche avancée et filtres
2. Notifications push
3. Chat entre utilisateurs
4. Statistiques vendeurs

### Phase 4 : Monétisation (Priorité Basse)
1. Système d'abonnements
2. Intégration paiement (Fedapay)
3. Annonces sponsorisées
