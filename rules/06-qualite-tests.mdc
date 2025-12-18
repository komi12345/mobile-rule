# Guide Qualité et Tests - DealAuto

---
inclusion: always
---

## 🎯 Objectif

Ce document définit les standards de qualité et la stratégie de tests pour le projet DealAuto. **Tout code doit respecter ces standards avant d'être intégré.**

---

## 📊 Métriques de Qualité Cibles

| Métrique | Cible | Actuel |
|----------|-------|--------|
| Couverture de tests | ≥70% | ~70% |
| Tests property-based | ≥35 fichiers | 35 fichiers |
| Tests unitaires | ≥17 fichiers | 17 fichiers |
| Warnings Dart | 0 | ~1 |
| Erreurs lint | 0 | 0 |

---

## 🧪 Stratégie de Tests

### Types de Tests

| Type | Outil | Usage | Priorité |
|------|-------|-------|----------|
| Tests unitaires | `flutter_test` | Logique métier, utils | Haute |
| Tests property-based | `glados` | Propriétés universelles | Haute |
| Tests widgets | `flutter_test` | Composants UI | Moyenne |
| Tests d'intégration | `integration_test` | Flux complets | Basse (MVP) |

### Structure des Fichiers de Tests

```
test/
├── *_unit_test.dart           # Tests unitaires
├── *_property_test.dart       # Tests property-based
└── *_widget_test.dart         # Tests widgets (optionnel)
```


---

## 🔬 Tests Property-Based avec Glados

### Qu'est-ce que le Property-Based Testing ?

Le property-based testing vérifie que des **propriétés universelles** sont vraies pour **toutes les entrées possibles**, pas seulement des exemples spécifiques.

### Configuration Glados

```yaml
# pubspec.yaml
dev_dependencies:
  glados: ^1.1.1
```

### Structure d'un Test Property-Based

```dart
// test/example_property_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:glados/glados.dart';

void main() {
  group('Nom du composant - Property Tests', () {
    
    // Générateur personnalisé si nécessaire
    Glados(any.int.inRange(0, 1000)).test(
      'Property 1: Description de la propriété',
      (value) {
        // Arrange
        final result = someFunction(value);
        
        // Assert - La propriété doit être vraie pour TOUTES les valeurs
        expect(result, satisfiesSomeCondition);
      },
    );
    
    Glados2(any.string, any.int).test(
      'Property 2: Avec plusieurs paramètres',
      (str, num) {
        // Test avec deux paramètres générés
      },
    );
  });
}
```

### Générateurs Glados Courants

```dart
// Entiers
any.int                          // Tout entier
any.int.inRange(0, 100)          // Entre 0 et 100
any.positiveInt                  // Entiers positifs

// Chaînes
any.string                       // Toute chaîne
any.nonEmptyString               // Chaîne non vide
any.letterOrDigits               // Lettres et chiffres

// Listes
any.list(any.int)                // Liste d'entiers
any.nonEmptyList(any.string)     // Liste non vide de chaînes

// Booléens
any.bool                         // true ou false

// Combinaisons
Glados2(any.string, any.int)     // Deux paramètres
Glados3(any.int, any.int, any.bool) // Trois paramètres
```

### Exemple Concret : Test de VehicleCard

```dart
// test/vehicle_card_property_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:glados/glados.dart';
import 'package:dealauto/models/vehicle.dart';

void main() {
  group('VehicleCard - Property Tests', () {
    
    // Générateur de Vehicle
    final vehicleGenerator = any.combine5(
      any.nonEmptyString,           // name
      any.positiveInt,              // priceInFCFA
      any.int.inRange(1990, 2025),  // year
      any.positiveInt,              // mileageKm
      any.bool,                     // isFeatured
    ).map((tuple) => Vehicle(
      id: 'test-id',
      name: tuple.$1,
      imageUrl: 'https://example.com/image.jpg',
      priceInFCFA: tuple.$2,
      year: tuple.$3,
      mileageKm: tuple.$4,
      isFeatured: tuple.$5,
      category: VehicleCategory.voitures,
    ));
    
    Glados(vehicleGenerator).test(
      'Property 1: Le prix formaté contient toujours "FCFA"',
      (vehicle) {
        expect(vehicle.formattedPrice, contains('FCFA'));
      },
    );
    
    Glados(vehicleGenerator).test(
      'Property 2: Le kilométrage formaté contient toujours "km"',
      (vehicle) {
        if (vehicle.mileageKm != null) {
          expect(vehicle.formattedMileage, contains('km'));
        }
      },
    );
    
    Glados(any.positiveInt).test(
      'Property 3: Le prix est toujours positif après formatage',
      (price) {
        final vehicle = Vehicle(
          id: 'test',
          name: 'Test',
          imageUrl: '',
          priceInFCFA: price,
          category: VehicleCategory.voitures,
        );
        expect(vehicle.priceInFCFA, greaterThan(0));
      },
    );
  });
}
```

---

## ✅ Tests Unitaires

### Structure d'un Test Unitaire

```dart
// test/example_unit_test.dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('Nom du composant', () {
    
    // Setup commun
    late SomeClass instance;
    
    setUp(() {
      instance = SomeClass();
    });
    
    tearDown(() {
      // Nettoyage si nécessaire
    });
    
    test('devrait faire X quand Y', () {
      // Arrange
      final input = 'test';
      
      // Act
      final result = instance.method(input);
      
      // Assert
      expect(result, equals(expectedValue));
    });
    
    test('devrait lever une exception quand Z', () {
      expect(
        () => instance.methodThatThrows(),
        throwsA(isA<SomeException>()),
      );
    });
  });
}
```

### Exemple : Test de ResponsiveUtils

```dart
// test/responsive_utils_unit_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:dealauto/utils/responsive_utils.dart';

void main() {
  group('ResponsiveUtils', () {
    
    test('getMinTouchTargetSize retourne au moins 48', () {
      final size = ResponsiveUtils.getMinTouchTargetSize(1.0);
      expect(size, greaterThanOrEqualTo(48.0));
    });
    
    test('isSmallScreen retourne true pour largeur < 360', () {
      expect(ResponsiveUtils.isSmallScreen(350), isTrue);
      expect(ResponsiveUtils.isSmallScreen(360), isFalse);
    });
    
    test('getScaledFontSize respecte les limites', () {
      final scaled = ResponsiveUtils.getScaledFontSize(16, 2.0);
      expect(scaled, lessThanOrEqualTo(16 * 1.4)); // Max 1.4x
    });
  });
}
```


---

## 🎨 Tests Widgets

### Structure d'un Test Widget

```dart
// test/example_widget_test.dart
import 'package:flutter/material.dart';
import 'package:flutter_test/flutter_test.dart';
import 'package:dealauto/widgets/my_widget.dart';

void main() {
  group('MyWidget', () {
    
    testWidgets('affiche le titre correctement', (tester) async {
      // Arrange & Act
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: MyWidget(title: 'Test Title'),
          ),
        ),
      );
      
      // Assert
      expect(find.text('Test Title'), findsOneWidget);
    });
    
    testWidgets('appelle onTap quand pressé', (tester) async {
      bool tapped = false;
      
      await tester.pumpWidget(
        MaterialApp(
          home: Scaffold(
            body: MyWidget(
              title: 'Test',
              onTap: () => tapped = true,
            ),
          ),
        ),
      );
      
      await tester.tap(find.byType(MyWidget));
      await tester.pump();
      
      expect(tapped, isTrue);
    });
    
    testWidgets('respecte l\'accessibilité', (tester) async {
      await tester.pumpWidget(
        const MaterialApp(
          home: Scaffold(
            body: MyWidget(title: 'Test'),
          ),
        ),
      );
      
      // Vérifier les Semantics
      expect(
        tester.getSemantics(find.byType(MyWidget)),
        matchesSemantics(label: 'Test'),
      );
    });
  });
}
```

---

## 🏃 Exécution des Tests

### Commandes de Base

```bash
# Exécuter tous les tests
flutter test

# Exécuter un fichier spécifique
flutter test test/vehicle_card_property_test.dart

# Exécuter avec couverture
flutter test --coverage

# Exécuter en mode verbose
flutter test --reporter expanded

# Exécuter les tests correspondant à un pattern
flutter test --name "Property"
```

### Script de Test Complet

```bash
# scripts/run_tests.sh
#!/bin/bash

echo "🧪 Exécution des tests DealAuto..."

# Analyser le code
echo "📊 Analyse statique..."
flutter analyze

# Exécuter les tests
echo "🔬 Tests unitaires et property-based..."
flutter test --coverage

# Générer le rapport de couverture
echo "📈 Génération du rapport de couverture..."
genhtml coverage/lcov.info -o coverage/html

echo "✅ Tests terminés!"
echo "📊 Rapport de couverture: coverage/html/index.html"
```

---

## 📋 Conventions de Nommage des Tests

### Fichiers

| Type | Pattern | Exemple |
|------|---------|---------|
| Unit test | `*_unit_test.dart` | `vehicle_unit_test.dart` |
| Property test | `*_property_test.dart` | `vehicle_card_property_test.dart` |
| Widget test | `*_widget_test.dart` | `login_screen_widget_test.dart` |

### Descriptions de Tests

```dart
// ✅ Bon : Descriptif et clair
test('devrait retourner le prix formaté avec séparateurs de milliers', () {});
test('Property 1: Le toggle favori inverse toujours l\'état', () {});

// ❌ Mauvais : Vague
test('test price', () {});
test('works', () {});
```

---

## 🔍 Analyse Statique

### Configuration Lint

```yaml
# analysis_options.yaml
include: package:flutter_lints/flutter.yaml

linter:
  rules:
    - always_declare_return_types
    - avoid_empty_else
    - avoid_print
    - avoid_unnecessary_containers
    - prefer_const_constructors
    - prefer_const_declarations
    - prefer_final_fields
    - prefer_final_locals
    - require_trailing_commas
    - sort_child_properties_last
    - use_key_in_widget_constructors

analyzer:
  errors:
    missing_required_param: error
    missing_return: error
  exclude:
    - "**/*.g.dart"
    - "**/*.freezed.dart"
```

### Commandes d'Analyse

```bash
# Analyser le code
flutter analyze

# Formater le code
dart format lib/ test/

# Vérifier le formatage
dart format --set-exit-if-changed lib/ test/
```

---

## ✅ Checklist Qualité Avant Commit

### Code
- [ ] Pas de warnings `flutter analyze`
- [ ] Code formaté avec `dart format`
- [ ] Pas de `print()` en production
- [ ] Pas de TODO non résolu critique

### Tests
- [ ] Tests unitaires pour nouvelle logique
- [ ] Tests property-based pour propriétés universelles
- [ ] Tous les tests passent (`flutter test`)
- [ ] Couverture maintenue ≥70%

### Documentation
- [ ] Commentaires dartdoc sur les classes publiques
- [ ] Requirements tracés dans les commentaires
- [ ] README mis à jour si nécessaire


---

## 🔧 Intégration Continue (CI/CD)

### GitHub Actions Workflow (À CONFIGURER)

```yaml
# .github/workflows/flutter-ci.yml
name: Flutter CI

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.10.0'
          channel: 'stable'
      
      - name: Install dependencies
        run: flutter pub get
      
      - name: Analyze code
        run: flutter analyze --fatal-infos
      
      - name: Check formatting
        run: dart format --set-exit-if-changed lib/ test/
      
      - name: Run tests
        run: flutter test --coverage
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: coverage/lcov.info
          fail_ci_if_error: true
          
  build-android:
    needs: test
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Setup Flutter
        uses: subosito/flutter-action@v2
        with:
          flutter-version: '3.10.0'
      
      - name: Build APK
        run: flutter build apk --release
      
      - name: Upload APK
        uses: actions/upload-artifact@v3
        with:
          name: release-apk
          path: build/app/outputs/flutter-apk/app-release.apk
```

---

## 📝 Documentation du Code

### Standards de Documentation

```dart
/// Description courte du widget/classe en une ligne.
/// 
/// Description détaillée sur plusieurs lignes si nécessaire.
/// Expliquer le but, le comportement et les cas d'utilisation.
/// 
/// ## Exemple d'utilisation
/// 
/// ```dart
/// final widget = MonWidget(
///   title: 'Exemple',
///   onTap: () => print('Tapped'),
/// );
/// ```
/// 
/// ## Paramètres
/// 
/// - [title] : Le titre affiché dans le widget
/// - [onTap] : Callback appelé lors du tap (optionnel)
/// 
/// ## Voir aussi
/// 
/// - [AutreWidget] pour un cas d'utilisation similaire
/// - [Documentation externe](https://example.com)
/// 
/// Requirements: 1.1, 1.2, 2.3
class MonWidget extends StatelessWidget {
  /// Le titre affiché dans le widget.
  /// 
  /// Ne peut pas être vide.
  final String title;
  
  /// Callback appelé lors du tap sur le widget.
  /// 
  /// Si null, le widget n'est pas interactif.
  final VoidCallback? onTap;
  
  /// Crée un nouveau [MonWidget].
  /// 
  /// Le [title] est requis et ne doit pas être vide.
  const MonWidget({
    super.key,
    required this.title,
    this.onTap,
  }) : assert(title.isNotEmpty, 'title ne peut pas être vide');
  
  @override
  Widget build(BuildContext context) {
    // ...
  }
}
```

### Commentaires de Code

```dart
// ══════════════════════════════════════════════════════════════
// SECTION : Nom de la section
// ══════════════════════════════════════════════════════════════

// TODO(username): Description de la tâche à faire
// FIXME(username): Description du bug à corriger
// HACK(username): Explication du hack temporaire
// NOTE: Information importante à retenir

// Commentaire explicatif pour une logique complexe
// Expliquer POURQUOI, pas QUOI (le code dit déjà quoi)
final result = complexCalculation(); // Mauvais
// Calcul du prix avec TVA togolaise (18%)
final result = price * 1.18; // Bon
```

---

## 🧪 Tests d'Intégration (Post-MVP)

### Structure des Tests d'Intégration

```dart
// integration_test/app_test.dart
import 'package:flutter_test/flutter_test.dart';
import 'package:integration_test/integration_test.dart';
import 'package:dealauto/main.dart' as app;

void main() {
  IntegrationTestWidgetsFlutterBinding.ensureInitialized();
  
  group('Flux d\'inscription complet', () {
    testWidgets('Inscription → OTP → Profil → Dashboard', (tester) async {
      app.main();
      await tester.pumpAndSettle();
      
      // 1. Écran Welcome
      expect(find.text('Créer un compte'), findsOneWidget);
      await tester.tap(find.text('Créer un compte'));
      await tester.pumpAndSettle();
      
      // 2. Écran Registration
      expect(find.text('Créer mon compte DealAuto'), findsOneWidget);
      
      // Remplir le formulaire
      await tester.enterText(
        find.byKey(const Key('phone_field')),
        '90123456',
      );
      await tester.enterText(
        find.byKey(const Key('nom_field')),
        'Kokou',
      );
      await tester.enterText(
        find.byKey(const Key('prenom_field')),
        'Afi',
      );
      await tester.enterText(
        find.byKey(const Key('password_field')),
        'Password123',
      );
      
      await tester.tap(find.text('Créer mon compte DealAuto'));
      await tester.pumpAndSettle();
      
      // 3. Écran OTP
      expect(find.text('Vérification'), findsOneWidget);
      
      // Entrer le code OTP (mock)
      await tester.enterText(find.byKey(const Key('otp_1')), '1');
      await tester.enterText(find.byKey(const Key('otp_2')), '2');
      await tester.enterText(find.byKey(const Key('otp_3')), '3');
      await tester.enterText(find.byKey(const Key('otp_4')), '4');
      
      await tester.tap(find.text('Suivant'));
      await tester.pumpAndSettle();
      
      // 4. Écran Sélection Profil
      expect(find.text('Client simple'), findsOneWidget);
      await tester.tap(find.text('Client simple'));
      await tester.tap(find.text('Suivant'));
      await tester.pumpAndSettle();
      
      // 5. Écran Félicitations
      expect(find.text('Félicitations !'), findsOneWidget);
      await tester.tap(find.text('Entrer dans l\'univers DealAuto'));
      await tester.pumpAndSettle();
      
      // 6. Dashboard
      expect(find.text('À la une DealAuto'), findsOneWidget);
    });
  });
}
```

---

## 🔍 Debugging et Profiling

### Outils de Debug

```dart
// Activer le mode debug pour les widgets
debugPaintSizeEnabled = true;        // Affiche les tailles
debugPaintBaselinesEnabled = true;   // Affiche les baselines
debugPaintLayerBordersEnabled = true; // Affiche les layers
debugRepaintRainbowEnabled = true;   // Colore les repaints

// Logs de debug
import 'package:flutter/foundation.dart';

if (kDebugMode) {
  print('Debug: $message');
}

// Assertions de développement
assert(() {
  // Code exécuté uniquement en debug
  return true;
}());
```

### Performance Profiling

```dart
// Mesurer le temps d'exécution
final stopwatch = Stopwatch()..start();
await expensiveOperation();
stopwatch.stop();
print('Durée: ${stopwatch.elapsedMilliseconds}ms');

// Timeline events pour DevTools
import 'dart:developer';

Timeline.startSync('MonOperation');
await monOperation();
Timeline.finishSync();
```

---

## 📋 Revue de Code Checklist

### Avant de Soumettre une PR

#### Code
- [ ] Le code compile sans erreurs
- [ ] `flutter analyze` ne retourne aucun warning
- [ ] Le code est formaté avec `dart format`
- [ ] Pas de `print()` en production (utiliser `debugPrint` ou logger)
- [ ] Pas de TODO critiques non résolus
- [ ] Les imports sont organisés et sans doublons

#### Tests
- [ ] Tests unitaires pour la nouvelle logique métier
- [ ] Tests property-based pour les propriétés universelles
- [ ] Tous les tests passent localement
- [ ] Couverture de code maintenue ou améliorée

#### Documentation
- [ ] Commentaires dartdoc sur les classes/méthodes publiques
- [ ] Requirements tracés dans les commentaires
- [ ] README mis à jour si nécessaire
- [ ] CHANGELOG mis à jour

#### UI/UX
- [ ] Conformité avec les maquettes Figma
- [ ] Coins arrondis respectés (12px champs, 16px cartes)
- [ ] Couleurs conformes à la palette
- [ ] Touch targets >= 48px
- [ ] Semantics labels présents

#### Performance
- [ ] `const` utilisé pour les widgets statiques
- [ ] Pas de rebuilds inutiles
- [ ] Images optimisées avec errorBuilder
- [ ] Transitions <= 300ms

#### Sécurité
- [ ] Pas de données sensibles en dur
- [ ] Validation des entrées utilisateur
- [ ] Gestion des erreurs appropriée
