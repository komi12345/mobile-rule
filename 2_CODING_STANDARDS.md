# DealAuto - Standards de Code et Conventions

## Principes Généraux

### Philosophie de Code
- **Code en ANGLAIS** (variables, fonctions, classes, commentaires techniques)
- **Commentaires métier en FRANÇAIS** pour expliquer la logique business togolaise
- **Clarté > Concision** : Code lisible plutôt que code "clever"
- **Fail-safe** : Toujours gérer les cas d'erreur
- **Mobile-first** : Penser performance et UX mobile
- **Production-ready** : Chaque ligne doit être prête pour production

### Règle d'Or
> "Si tu dois relire ton code 3 fois pour comprendre, il est trop complexe. Simplifie."

---

## Structure de Fichiers Flutter

### Organisation des Dossiers (OBLIGATOIRE)

```
lib/
├── main.dart
├── app.dart
├── config/
│   ├── app_config.dart
│   ├── route_config.dart
│   └── theme_config.dart
├── core/
│   ├── constants/
│   ├── utils/
│   ├── errors/
│   └── network/
├── data/
│   ├── models/
│   ├── repositories/
│   └── datasources/
│       ├── remote/
│       └── local/
├── domain/
│   ├── entities/
│   ├── repositories/
│   └── usecases/
├── presentation/
│   ├── screens/
│   ├── shared/
│   ├── providers/
│   └── theme/
└── services/
```

---

## Standards UI Basés sur Figma

### RÈGLE ABSOLUE : Pixel-Perfect
> "Le code doit reproduire EXACTEMENT les designs Figma fournis"

### Analyse Systématique des Captures Figma

Quand une image Figma est fournie, Claude doit :

#### 1. IDENTIFIER LES MESURES
```dart
class FigmaSpecs {
  // Border radius (mesurer avec précision)
  static const double buttonBorderRadius = 12.0;
  static const double cardBorderRadius = 16.0;
  static const double inputBorderRadius = 8.0;
  
  // Heights
  static const double buttonHeight = 56.0;
  static const double inputHeight = 48.0;
  
  // Padding & Spacing
  static const double screenPadding = 24.0;
  static const double cardPadding = 16.0;
}
```

#### 2. ANALYSER LES FORMULAIRES
```dart
class FigmaInputStyle {
  static InputDecoration getDecoration({
    required String label,
    String? hint,
    Widget? prefixIcon,
  }) {
    return InputDecoration(
      labelText: label,
      hintText: hint,
      prefixIcon: prefixIcon,
      
      // Border EXACTEMENT comme dans Figma
      border: OutlineInputBorder(
        borderRadius: BorderRadius.circular(8),
        borderSide: const BorderSide(
          color: Color(0xFFE0E0E0),
          width: 1.0,
        ),
      ),
      
      focusedBorder: OutlineInputBorder(
        borderRadius: BorderRadius.circular(8),
        borderSide: const BorderSide(
          color: Color(0xFF0D3B66),
          width: 2.0,
        ),
      ),
      
      contentPadding: const EdgeInsets.symmetric(
        horizontal: 16,
        vertical: 16,
      ),
    );
  }
}
```

#### 3. ANALYSER LES BOUTONS
```dart
class FigmaButtonStyles {
  static ButtonStyle primaryButtonStyle = ElevatedButton.styleFrom(
    backgroundColor: const Color(0xFF0D3B66),
    foregroundColor: Colors.white,
    minimumSize: const Size(double.infinity, 56),
    shape: RoundedRectangleBorder(
      borderRadius: BorderRadius.circular(12),
    ),
    elevation: 2,
    padding: const EdgeInsets.symmetric(
      horizontal: 32,
      vertical: 16,
    ),
  );
}
```

---

## Gestion d'Erreurs (Obligatoire)

```dart
// ✅ TOUJOURS avec try-catch
Future<void> fetchData() async {
  try {
    final data = await api.getData();
    setState(() => _data = data);
  } on NetworkException catch (e) {
    setState(() => _errorMessage = 'Vérifiez votre connexion internet');
  } catch (e) {
    setState(() => _errorMessage = 'Une erreur est survenue');
  } finally {
    setState(() => _isLoading = false);
  }
}
```

---

## Navigation Fluide

```dart
// Transition slide (300ms)
PageRouteBuilder(
  transitionDuration: const Duration(milliseconds: 300),
  transitionsBuilder: (context, animation, secondaryAnimation, child) {
    const begin = Offset(1.0, 0.0);
    const end = Offset.zero;
    const curve = Curves.easeInOut;
    
    var tween = Tween(begin: begin, end: end).chain(
      CurveTween(curve: curve),
    );
    
    return SlideTransition(
      position: animation.drive(tween),
      child: child,
    );
  },
);
```

---

## Validation des Données

```dart
class Validators {
  static String? validatePhone(String? value) {
    if (value == null || value.isEmpty) {
      return 'Le numéro de téléphone est requis';
    }
    
    final phoneRegex = RegExp(r'^\+228\d{8}$');
    if (!phoneRegex.hasMatch(value.replaceAll(' ', ''))) {
      return 'Format invalide. Exemple: +228 90 12 34 56';
    }
    
    return null;
  }
}
```

---

## Formatage des Données

```dart
class Formatters {
  static String formatPrice(double price) {
    return price
        .toStringAsFixed(0)
        .replaceAllMapped(
          RegExp(r'(\d{1,3})(?=(\d{3})+(?!\d))'),
          (Match m) => '${m[1]} ',
        )
        .trim() + ' FCFA';
  }
}
```

---

**Ce document doit être utilisé comme référence pour TOUT le code écrit dans DealAuto.**
