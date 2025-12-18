# Guide Accessibilité et Performance - DealAuto

---
inclusion: always
---

## 🎯 Objectif

Ce document définit les standards d'accessibilité et de performance pour DealAuto. **Ces standards sont obligatoires** pour garantir une expérience utilisateur optimale pour tous.

---

## ♿ Accessibilité (A11y)

### Standards Cibles

| Standard | Niveau | Statut |
|----------|--------|--------|
| WCAG 2.1 | AA | ✅ Implémenté |
| Contraste couleurs | 4.5:1 minimum | ✅ |
| Touch targets | 48x48px minimum | ✅ |
| Reduced motion | Supporté | ✅ |
| Screen readers | Compatible | ✅ |

---

### Semantics Labels

**OBLIGATOIRE** : Tous les éléments interactifs doivent avoir un label Semantics.

```dart
// ✅ Correct
Semantics(
  label: 'Bouton de connexion',
  button: true,
  child: ElevatedButton(
    onPressed: _onLogin,
    child: const Text('Me connecter'),
  ),
)

// ✅ Correct - IconButton
Semantics(
  label: 'Retour à l\'écran précédent',
  button: true,
  child: IconButton(
    icon: const Icon(Icons.arrow_back_ios),
    onPressed: () => Navigator.pop(context),
  ),
)

// ✅ Correct - Image
Semantics(
  label: 'Photo du véhicule Toyota Corolla',
  image: true,
  child: Image.network(imageUrl),
)

// ❌ Incorrect - Pas de Semantics
IconButton(
  icon: const Icon(Icons.favorite),
  onPressed: _toggleFavorite,
)
```


### Contraste des Couleurs

```dart
// lib/utils/accessibility_utils.dart

class AccessibilityUtils {
  /// Calcule le ratio de contraste WCAG
  static double calculateContrastRatio(Color foreground, Color background) {
    final l1 = _calculateRelativeLuminance(foreground);
    final l2 = _calculateRelativeLuminance(background);
    final lighter = l1 > l2 ? l1 : l2;
    final darker = l1 > l2 ? l2 : l1;
    return (lighter + 0.05) / (darker + 0.05);
  }
  
  static double _calculateRelativeLuminance(Color color) {
    final r = _linearize(color.red / 255);
    final g = _linearize(color.green / 255);
    final b = _linearize(color.blue / 255);
    return 0.2126 * r + 0.7152 * g + 0.0722 * b;
  }
  
  static double _linearize(double value) {
    return value <= 0.03928
        ? value / 12.92
        : pow((value + 0.055) / 1.055, 2.4).toDouble();
  }
  
  /// Vérifie si le contraste est suffisant (WCAG AA)
  static bool meetsContrastRequirement(Color foreground, Color background) {
    return calculateContrastRatio(foreground, background) >= 4.5;
  }
}
```

### Combinaisons de Couleurs Validées

| Texte | Fond | Ratio | Statut |
|-------|------|-------|--------|
| `#0D3B66` (Dark Blue) | `#F5F5F0` (Background) | 8.2:1 | ✅ AAA |
| `#0D3B66` (Dark Blue) | `#FFFFFF` (White) | 9.1:1 | ✅ AAA |
| `#666666` (Grey) | `#FFFFFF` (White) | 5.7:1 | ✅ AA |
| `#FFFFFF` (White) | `#0D3B66` (Dark Blue) | 9.1:1 | ✅ AAA |
| `#E07A2F` (Orange) | `#FFFFFF` (White) | 3.1:1 | ⚠️ Large text only |

---

### Touch Targets

**OBLIGATOIRE** : Tous les éléments interactifs doivent avoir une taille minimale de 48x48px.

```dart
// Constante à utiliser
const double kMinTouchTarget = 48.0;

// ✅ Correct - SizedBox pour garantir la taille
SizedBox(
  width: kMinTouchTarget,
  height: kMinTouchTarget,
  child: IconButton(
    icon: const Icon(Icons.favorite),
    onPressed: _toggleFavorite,
  ),
)

// ✅ Correct - Padding pour agrandir la zone tactile
InkWell(
  onTap: _onTap,
  child: Padding(
    padding: const EdgeInsets.all(12), // 24 + 24 = 48
    child: Icon(Icons.settings, size: 24),
  ),
)

// ❌ Incorrect - Trop petit
IconButton(
  iconSize: 20, // Zone tactile trop petite
  icon: const Icon(Icons.close),
  onPressed: _onClose,
)
```

### Utilitaire Touch Target

```dart
// lib/utils/responsive_utils.dart

class ResponsiveUtils {
  static const double minTouchTargetSize = 48.0;
  static const double minTouchSpacing = 8.0;
  
  /// Retourne la taille de touch target ajustée au text scale
  static double getMinTouchTargetSize(double textScaleFactor) {
    // Augmenter légèrement pour les grands textes
    return minTouchTargetSize * (textScaleFactor > 1.2 ? 1.1 : 1.0);
  }
  
  /// Vérifie si une taille respecte le minimum
  static bool isValidTouchTarget(double width, double height) {
    return width >= minTouchTargetSize && height >= minTouchTargetSize;
  }
}
```

---

### Reduced Motion

**OBLIGATOIRE** : Respecter la préférence utilisateur pour les animations réduites.

```dart
// Vérifier la préférence
@override
Widget build(BuildContext context) {
  final reducedMotion = MediaQuery.of(context).disableAnimations;
  
  if (reducedMotion) {
    // Pas d'animation ou animation minimale
    return _buildStaticContent();
  }
  
  // Animation normale
  return AnimatedContainer(
    duration: const Duration(milliseconds: 200),
    child: _buildContent(),
  );
}

// Exemple avec SuccessIndicator
class SuccessIndicator extends StatefulWidget {
  @override
  State<SuccessIndicator> createState() => _SuccessIndicatorState();
}

class _SuccessIndicatorState extends State<SuccessIndicator>
    with SingleTickerProviderStateMixin {
  late AnimationController _controller;
  
  @override
  void didChangeDependencies() {
    super.didChangeDependencies();
    final reducedMotion = MediaQuery.of(context).disableAnimations;
    
    if (reducedMotion) {
      _controller.stop();
      _controller.value = 1.0; // État final sans animation
    } else {
      _controller.repeat(reverse: true);
    }
  }
  
  // ...
}
```


---

## ⚡ Performance

### Objectifs de Performance

| Métrique | Cible | Mesure |
|----------|-------|--------|
| First Contentful Paint | < 2s | DevTools |
| Time to Interactive | < 3s | DevTools |
| Frame rate | 60 FPS | Performance overlay |
| Memory usage | < 150MB | DevTools |
| App size (APK) | < 30MB | Build output |

---

### Optimisations Obligatoires

#### 1. Utiliser `const` pour les Widgets Statiques

```dart
// ✅ Correct - const évite les rebuilds inutiles
const Text('Titre statique');
const SizedBox(height: 16);
const Icon(Icons.check, color: Colors.green);

// ❌ Incorrect - Rebuild à chaque build()
Text('Titre statique');
SizedBox(height: 16);
Icon(Icons.check, color: Colors.green);
```

#### 2. RepaintBoundary pour Isoler les Repaints

```dart
// ✅ Correct - Isole les animations coûteuses
RepaintBoundary(
  child: AnimatedWidget(...),
)

// ✅ Correct - Isole les images
RepaintBoundary(
  child: Image.network(
    url,
    fit: BoxFit.cover,
  ),
)

// Quand utiliser RepaintBoundary :
// - Animations complexes
// - Images qui changent
// - Widgets qui se repeignent fréquemment
// - Listes avec beaucoup d'éléments
```

#### 3. SafeStateMixin pour Éviter les Erreurs

```dart
// lib/utils/safe_state_mixin.dart

/// Mixin pour éviter setState() après dispose()
mixin SafeStateMixin<T extends StatefulWidget> on State<T> {
  /// Appelle setState seulement si le widget est monté
  bool safeSetState(VoidCallback fn) {
    if (mounted) {
      setState(fn);
      return true;
    }
    return false;
  }
}

// Utilisation
class MyScreen extends StatefulWidget {
  @override
  State<MyScreen> createState() => _MyScreenState();
}

class _MyScreenState extends State<MyScreen> with SafeStateMixin {
  Future<void> _loadData() async {
    final data = await fetchData();
    
    // ✅ Correct - Vérifie mounted automatiquement
    safeSetState(() {
      _data = data;
    });
  }
}
```

#### 4. Lazy Loading des Images

```dart
// ✅ Correct - Chargement différé avec placeholder
Image.network(
  imageUrl,
  loadingBuilder: (context, child, loadingProgress) {
    if (loadingProgress == null) return child;
    return Container(
      color: Colors.grey[200],
      child: const Center(
        child: CircularProgressIndicator(),
      ),
    );
  },
  errorBuilder: ImageErrorHandler.createErrorBuilder(
    width: 100,
    height: 100,
    icon: Icons.image_not_supported,
  ),
)

// ✅ Correct - Précacher les images critiques
@override
void didChangeDependencies() {
  super.didChangeDependencies();
  precacheImage(
    const AssetImage('assets/images/logo.png'),
    context,
  );
}
```

#### 5. ListView.builder pour les Longues Listes

```dart
// ✅ Correct - Virtualisation automatique
ListView.builder(
  itemCount: vehicles.length,
  itemExtent: 120, // Hauteur fixe = meilleure performance
  itemBuilder: (context, index) {
    return VehicleCard(vehicle: vehicles[index]);
  },
)

// ❌ Incorrect - Tous les widgets créés d'un coup
ListView(
  children: vehicles.map((v) => VehicleCard(vehicle: v)).toList(),
)
```

#### 6. Debouncing pour les Recherches

```dart
// lib/utils/debouncer.dart

class Debouncer {
  final Duration delay;
  Timer? _timer;
  
  Debouncer({this.delay = const Duration(milliseconds: 300)});
  
  void run(VoidCallback action) {
    _timer?.cancel();
    _timer = Timer(delay, action);
  }
  
  void dispose() {
    _timer?.cancel();
  }
}

// Utilisation
class SearchWidget extends StatefulWidget {
  @override
  State<SearchWidget> createState() => _SearchWidgetState();
}

class _SearchWidgetState extends State<SearchWidget> {
  final _debouncer = Debouncer();
  
  void _onSearchChanged(String query) {
    _debouncer.run(() {
      // Recherche effectuée après 300ms d'inactivité
      _performSearch(query);
    });
  }
  
  @override
  void dispose() {
    _debouncer.dispose();
    super.dispose();
  }
}
```


---

### Gestion des Erreurs d'Images

```dart
// lib/utils/image_error_handler.dart

class ImageErrorHandler {
  /// Crée un errorBuilder standard pour Image.network
  static Widget Function(BuildContext, Object, StackTrace?) createErrorBuilder({
    required double width,
    required double height,
    IconData icon = Icons.image_not_supported,
    Color? backgroundColor,
    Color? iconColor,
  }) {
    return (context, error, stackTrace) {
      return Container(
        width: width,
        height: height,
        decoration: BoxDecoration(
          color: backgroundColor ?? Colors.grey[200],
          borderRadius: BorderRadius.circular(8),
        ),
        child: Icon(
          icon,
          size: width * 0.3,
          color: iconColor ?? Colors.grey[400],
        ),
      );
    };
  }
}

// Utilisation
Image.network(
  vehicle.imageUrl,
  width: 160,
  height: 120,
  fit: BoxFit.cover,
  errorBuilder: ImageErrorHandler.createErrorBuilder(
    width: 160,
    height: 120,
    icon: Icons.directions_car,
  ),
)
```

---

### Optimisation des Transitions

```dart
// lib/services/navigation_service.dart

class NavigationService {
  // Durée maximale recommandée : 300ms
  static const Duration _defaultDuration = Duration(milliseconds: 200);
  
  /// Transition avec respect du reduced motion
  static Route<T> adaptiveRoute<T>({
    required Widget page,
    required BuildContext context,
  }) {
    final reducedMotion = MediaQuery.of(context).disableAnimations;
    
    if (reducedMotion) {
      // Transition instantanée
      return MaterialPageRoute(builder: (_) => page);
    }
    
    // Transition animée
    return fadeSlideRoute(page: page);
  }
  
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

### Isolation des Rebuilds avec ScopedStateBuilder

```dart
// lib/widgets/scoped_state_builder.dart

/// Widget pour isoler les rebuilds d'état
class ScopedStateBuilder<T> extends StatefulWidget {
  final T initialValue;
  final Widget Function(BuildContext context, T value, void Function(T) setValue) builder;
  
  const ScopedStateBuilder({
    super.key,
    required this.initialValue,
    required this.builder,
  });
  
  @override
  State<ScopedStateBuilder<T>> createState() => _ScopedStateBuilderState<T>();
}

class _ScopedStateBuilderState<T> extends State<ScopedStateBuilder<T>> {
  late T _value;
  
  @override
  void initState() {
    super.initState();
    _value = widget.initialValue;
  }
  
  void _setValue(T newValue) {
    setState(() => _value = newValue);
  }
  
  @override
  Widget build(BuildContext context) {
    return widget.builder(context, _value, _setValue);
  }
}

// Utilisation - Isole le rebuild du toggle password
ScopedStateBuilder<bool>(
  initialValue: false,
  builder: (context, isVisible, setVisible) {
    return TextField(
      obscureText: !isVisible,
      decoration: InputDecoration(
        suffixIcon: IconButton(
          icon: Icon(isVisible ? Icons.visibility_off : Icons.visibility),
          onPressed: () => setVisible(!isVisible),
        ),
      ),
    );
  },
)
```

---

## ✅ Checklist Accessibilité et Performance

### Accessibilité
- [ ] Tous les boutons ont un Semantics label
- [ ] Toutes les images ont un Semantics label
- [ ] Contraste des couleurs ≥ 4.5:1
- [ ] Touch targets ≥ 48x48px
- [ ] Reduced motion respecté
- [ ] Navigation au clavier fonctionnelle

### Performance
- [ ] `const` utilisé pour widgets statiques
- [ ] `RepaintBoundary` pour animations
- [ ] `SafeStateMixin` pour async setState
- [ ] `ListView.builder` pour longues listes
- [ ] Debouncing pour recherches
- [ ] Images avec errorBuilder
- [ ] Transitions ≤ 300ms

### Tests
- [ ] Tests property-based pour accessibilité
- [ ] Tests de contraste des couleurs
- [ ] Tests de touch targets
- [ ] Profiling avec DevTools


---

## 🌐 Internationalisation (i18n) - Préparation Future

### Structure Recommandée

```dart
// lib/l10n/app_fr.arb (fichier principal)
{
  "@@locale": "fr",
  "appTitle": "DealAuto",
  "welcomeTitle": "Bienvenue sur DealAuto",
  "loginButton": "Me connecter",
  "registerButton": "Créer un compte",
  "phoneHint": "Numéro de téléphone",
  "passwordHint": "Mot de passe",
  "forgotPassword": "Mot de passe oublié ?",
  "vehiclePrice": "{price} FCFA",
  "@vehiclePrice": {
    "placeholders": {
      "price": {
        "type": "String"
      }
    }
  }
}
```

### Configuration pubspec.yaml

```yaml
dependencies:
  flutter_localizations:
    sdk: flutter
  intl: ^0.19.0

flutter:
  generate: true
```

### Configuration l10n.yaml

```yaml
arb-dir: lib/l10n
template-arb-file: app_fr.arb
output-localization-file: app_localizations.dart
output-class: AppLocalizations
```

---

## 📱 Optimisation Spécifique Android

### Configuration ProGuard

```proguard
# android/app/proguard-rules.pro

# Flutter
-keep class io.flutter.app.** { *; }
-keep class io.flutter.plugin.** { *; }
-keep class io.flutter.util.** { *; }
-keep class io.flutter.view.** { *; }
-keep class io.flutter.** { *; }
-keep class io.flutter.plugins.** { *; }

# Firebase (si utilisé)
-keep class com.google.firebase.** { *; }

# Supabase (si utilisé)
-keep class io.supabase.** { *; }
```

### Optimisation de la Taille APK

```kotlin
// android/app/build.gradle.kts
android {
    buildTypes {
        release {
            isMinifyEnabled = true
            isShrinkResources = true
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    
    // Split APK par ABI
    splits {
        abi {
            isEnable = true
            reset()
            include("armeabi-v7a", "arm64-v8a", "x86_64")
            isUniversalApk = false
        }
    }
}
```

---

## 🔋 Optimisation Batterie

### Bonnes Pratiques

```dart
// 1. Éviter les timers permanents
// ❌ Mauvais
Timer.periodic(const Duration(seconds: 1), (_) {
  // Mise à jour continue
});

// ✅ Bon - Utiliser des streams ou listeners
StreamSubscription? _subscription;

@override
void initState() {
  super.initState();
  _subscription = dataStream.listen(_onDataChanged);
}

@override
void dispose() {
  _subscription?.cancel();
  super.dispose();
}

// 2. Suspendre les animations hors écran
class _MyWidgetState extends State<MyWidget> with WidgetsBindingObserver {
  late AnimationController _controller;
  
  @override
  void initState() {
    super.initState();
    WidgetsBinding.instance.addObserver(this);
  }
  
  @override
  void dispose() {
    WidgetsBinding.instance.removeObserver(this);
    super.dispose();
  }
  
  @override
  void didChangeAppLifecycleState(AppLifecycleState state) {
    if (state == AppLifecycleState.paused) {
      _controller.stop();
    } else if (state == AppLifecycleState.resumed) {
      _controller.repeat();
    }
  }
}

// 3. Utiliser le cache pour les images
CachedNetworkImage(
  imageUrl: url,
  placeholder: (context, url) => const CircularProgressIndicator(),
  errorWidget: (context, url, error) => const Icon(Icons.error),
  memCacheWidth: 300, // Limiter la taille en mémoire
  memCacheHeight: 300,
)
```

---

## 📊 Monitoring en Production

### Crashlytics (Firebase)

```dart
// Configuration dans main.dart
void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  
  // Capturer les erreurs Flutter
  FlutterError.onError = (errorDetails) {
    FirebaseCrashlytics.instance.recordFlutterFatalError(errorDetails);
  };
  
  // Capturer les erreurs async
  PlatformDispatcher.instance.onError = (error, stack) {
    FirebaseCrashlytics.instance.recordError(error, stack, fatal: true);
    return true;
  };
  
  runApp(const MyApp());
}
```

### Logging Structuré

```dart
/// Service de logging DealAuto
class LogService {
  static const String _tag = 'DealAuto';
  
  static void debug(String message, [dynamic error, StackTrace? stack]) {
    if (kDebugMode) {
      print('[$_tag] DEBUG: $message');
      if (error != null) print('Error: $error');
      if (stack != null) print('Stack: $stack');
    }
  }
  
  static void info(String message) {
    print('[$_tag] INFO: $message');
    // Envoyer à analytics si nécessaire
  }
  
  static void warning(String message, [dynamic error]) {
    print('[$_tag] WARNING: $message');
    if (error != null) print('Error: $error');
  }
  
  static void error(String message, dynamic error, [StackTrace? stack]) {
    print('[$_tag] ERROR: $message');
    print('Error: $error');
    if (stack != null) print('Stack: $stack');
    
    // Envoyer à Crashlytics en production
    if (!kDebugMode) {
      // FirebaseCrashlytics.instance.recordError(error, stack);
    }
  }
}
```

---

## 🔒 Sécurité

### Stockage Sécurisé

```dart
// Utiliser flutter_secure_storage pour les données sensibles
import 'package:flutter_secure_storage/flutter_secure_storage.dart';

class SecureStorageService {
  static const _storage = FlutterSecureStorage(
    aOptions: AndroidOptions(
      encryptedSharedPreferences: true,
    ),
  );
  
  // Tokens d'authentification
  static Future<void> saveAccessToken(String token) async {
    await _storage.write(key: 'access_token', value: token);
  }
  
  static Future<String?> getAccessToken() async {
    return await _storage.read(key: 'access_token');
  }
  
  static Future<void> deleteAccessToken() async {
    await _storage.delete(key: 'access_token');
  }
  
  // Effacer toutes les données (déconnexion)
  static Future<void> clearAll() async {
    await _storage.deleteAll();
  }
}
```

### Validation des Entrées

```dart
/// Validateurs DealAuto
class DealAutoValidators {
  /// Valide un numéro de téléphone togolais
  static String? validatePhone(String? value) {
    if (value == null || value.isEmpty) {
      return 'Le numéro de téléphone est requis';
    }
    
    // Supprimer les espaces et tirets
    final cleaned = value.replaceAll(RegExp(r'[\s-]'), '');
    
    // Vérifier le format (8 chiffres pour le Togo)
    if (!RegExp(r'^\d{8}$').hasMatch(cleaned)) {
      return 'Numéro de téléphone invalide';
    }
    
    // Vérifier le préfixe (90, 91, 92, 93, 96, 97, 98, 99 pour le Togo)
    final prefix = cleaned.substring(0, 2);
    final validPrefixes = ['90', '91', '92', '93', '96', '97', '98', '99'];
    if (!validPrefixes.contains(prefix)) {
      return 'Préfixe de numéro invalide';
    }
    
    return null;
  }
  
  /// Valide un mot de passe
  static String? validatePassword(String? value) {
    if (value == null || value.isEmpty) {
      return 'Le mot de passe est requis';
    }
    
    if (value.length < 8) {
      return 'Le mot de passe doit contenir au moins 8 caractères';
    }
    
    if (!RegExp(r'[A-Z]').hasMatch(value)) {
      return 'Le mot de passe doit contenir au moins une majuscule';
    }
    
    if (!RegExp(r'[a-z]').hasMatch(value)) {
      return 'Le mot de passe doit contenir au moins une minuscule';
    }
    
    if (!RegExp(r'[0-9]').hasMatch(value)) {
      return 'Le mot de passe doit contenir au moins un chiffre';
    }
    
    return null;
  }
  
  /// Valide un nom/prénom
  static String? validateName(String? value, String fieldName) {
    if (value == null || value.trim().isEmpty) {
      return '$fieldName est requis';
    }
    
    if (value.trim().length < 2) {
      return '$fieldName doit contenir au moins 2 caractères';
    }
    
    if (!RegExp(r'^[a-zA-ZÀ-ÿ\s\-]+$').hasMatch(value)) {
      return '$fieldName contient des caractères invalides';
    }
    
    return null;
  }
  
  /// Valide un prix
  static String? validatePrice(String? value) {
    if (value == null || value.isEmpty) {
      return 'Le prix est requis';
    }
    
    final price = int.tryParse(value.replaceAll(RegExp(r'[^\d]'), ''));
    
    if (price == null || price <= 0) {
      return 'Prix invalide';
    }
    
    if (price < 50000) {
      return 'Le prix minimum est de 50 000 FCFA';
    }
    
    if (price > 500000000) {
      return 'Le prix maximum est de 500 000 000 FCFA';
    }
    
    return null;
  }
}
```

---

## ✅ Checklist Complète Avant Release

### Accessibilité
- [ ] Tous les éléments interactifs ont un Semantics label
- [ ] Contraste des couleurs >= 4.5:1 (WCAG AA)
- [ ] Touch targets >= 48x48px
- [ ] Reduced motion respecté
- [ ] Navigation au clavier fonctionnelle
- [ ] Textes lisibles avec zoom 200%

### Performance
- [ ] First Contentful Paint < 2s
- [ ] Time to Interactive < 3s
- [ ] Frame rate stable à 60 FPS
- [ ] Mémoire < 150MB en utilisation normale
- [ ] Taille APK < 30MB
- [ ] Pas de memory leaks détectés

### Sécurité
- [ ] Tokens stockés de manière sécurisée
- [ ] Validation des entrées côté client ET serveur
- [ ] Pas de données sensibles en logs
- [ ] HTTPS uniquement pour les API
- [ ] Certificat SSL valide

### Qualité
- [ ] Tous les tests passent
- [ ] Couverture de code >= 70%
- [ ] Pas de warnings `flutter analyze`
- [ ] Code formaté avec `dart format`
- [ ] Documentation à jour
