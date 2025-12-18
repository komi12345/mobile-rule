# DealAuto - UI/UX Guidelines et Analyse Figma

## 📋 Table des Matières

1. [Philosophie Design](#philosophie-design)
2. [Analyse Systématique des Captures Figma](#analyse-figma)
3. [Design System Complet](#design-system)
4. [Composants UI Standards](#composants)
5. [Navigation et Transitions](#navigation)
6. [Animations et Micro-interactions](#animations)
7. [Responsive Design](#responsive)
8. [Accessibilité](#accessibilite)
9. [Performance UI](#performance)
10. [Checklist Qualité](#checklist)

---

## Philosophie Design

### Principes Fondamentaux DealAuto

1. **Pixel-Perfect Figma**
   - Le code doit reproduire EXACTEMENT les maquettes Figma
   - Chaque pixel, chaque ombre, chaque espacement compte
   - Utiliser les outils d'inspection Figma pour extraire les valeurs précises

2. **Mobile-First Togolais**
   - Optimisé pour connexions 3G lentes
   - Interface simple et claire (pas de fioritures inutiles)
   - Boutons larges et espacés (thumb-friendly)
   - Textes lisibles même en plein soleil

3. **Feedback Immédiat**
   - Chaque action utilisateur doit avoir un feedback visuel < 50ms
   - Loading states sur toutes les actions asynchrones
   - Messages d'erreur clairs en français

4. **Cohérence Absolue**
   - Même style de bouton partout
   - Même espacement entre éléments similaires
   - Même durée d'animation pour toutes les transitions

---

## Analyse Systématique des Captures Figma

### 🔍 Checklist d'Analyse d'une Capture Figma

Quand Claude reçoit une image Figma, il doit systématiquement extraire ces informations :

#### 1. MESURES GLOBALES

**À extraire :**
```dart
class FigmaScreenSpecs {
  // Dimensions écran
  static const double screenWidth = 375.0;   // iPhone SE base
  static const double screenHeight = 667.0;
  
  // Safe areas
  static const double statusBarHeight = 44.0;
  static const double bottomSafeArea = 34.0;  // iPhone avec encoche
  
  // Padding global écran
  static const double screenPaddingHorizontal = 24.0;
  static const double screenPaddingVertical = 16.0;
}
```

**Comment mesurer dans Figma :**
1. Sélectionner le frame principal
2. Panneau de droite → Size (W × H)
3. Vérifier les contraintes (constraints)

---

#### 2. FORMULAIRES (Inputs)

**À extraire de CHAQUE input :**

```dart
class FigmaInputSpecs {
  // Dimensions
  static const double inputHeight = 56.0;
  static const double inputBorderRadius = 12.0;
  static const double inputBorderWidth = 1.0;
  
  // Padding interne
  static const EdgeInsets inputPadding = EdgeInsets.symmetric(
    horizontal: 16.0,
    vertical: 16.0,
  );
  
  // Spacing entre inputs
  static const double inputSpacing = 16.0;
  
  // Couleurs
  static const Color inputBorderColorNormal = Color(0xFFE0E0E0);
  static const Color inputBorderColorFocus = Color(0xFF0D3B66);
  static const Color inputBorderColorError = Color(0xFFD32F2F);
  static const Color inputFillColor = Color(0xFFFFFFFF);
  static const Color inputTextColor = Color(0xFF212121);
  static const Color inputHintColor = Color(0xFF9E9E9E);
  
  // Typographie
  static const double inputTextSize = 16.0;
  static const FontWeight inputTextWeight = FontWeight.w400;
  static const double inputLabelSize = 14.0;
  static const FontWeight inputLabelWeight = FontWeight.w500;
}
```

**Processus d'extraction dans Figma :**

1. **Sélectionner un input**
2. **Panneau Design (droite) :**
   - Size → Height = `inputHeight`
   - Corner Radius → `inputBorderRadius`
   - Stroke → Width = `inputBorderWidth`, Color = `inputBorderColor`
   - Fill → `inputFillColor`
3. **Auto Layout (si présent) :**
   - Horizontal padding = `horizontal` du EdgeInsets
   - Vertical padding = `vertical` du EdgeInsets
4. **Text Layer (à l'intérieur) :**
   - Font size = `inputTextSize`
   - Font weight = `inputTextWeight`
   - Color = `inputTextColor`
5. **Mesurer spacing :**
   - Sélectionner 2 inputs consécutifs
   - Figma affiche automatiquement l'espace entre = `inputSpacing`

**États à identifier :**
- **Normal** : État par défaut (repos)
- **Focus** : Quand input est sélectionné (border plus épaisse/colorée)
- **Error** : Quand validation échoue (border rouge)
- **Disabled** : Quand input est désactivé (opacité réduite)

**Template Flutter basé sur Figma :**
```dart
class DealAutoTextField extends StatelessWidget {
  final String label;
  final String? hint;
  final TextEditingController? controller;
  final String? Function(String?)? validator;
  final Widget? prefixIcon;
  final Widget? suffixIcon;
  final bool enabled;
  
  const DealAutoTextField({
    Key? key,
    required this.label,
    this.hint,
    this.controller,
    this.validator,
    this.prefixIcon,
    this.suffixIcon,
    this.enabled = true,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return TextFormField(
      controller: controller,
      validator: validator,
      enabled: enabled,
      style: const TextStyle(
        fontSize: FigmaInputSpecs.inputTextSize,
        fontWeight: FigmaInputSpecs.inputTextWeight,
        color: FigmaInputSpecs.inputTextColor,
      ),
      decoration: InputDecoration(
        labelText: label,
        hintText: hint,
        prefixIcon: prefixIcon,
        suffixIcon: suffixIcon,
        
        // Border normal
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(
            FigmaInputSpecs.inputBorderRadius,
          ),
          borderSide: const BorderSide(
            color: FigmaInputSpecs.inputBorderColorNormal,
            width: FigmaInputSpecs.inputBorderWidth,
          ),
        ),
        
        // Border en focus
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(
            FigmaInputSpecs.inputBorderRadius,
          ),
          borderSide: const BorderSide(
            color: FigmaInputSpecs.inputBorderColorFocus,
            width: 2.0, // Plus épais en focus
          ),
        ),
        
        // Border en erreur
        errorBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(
            FigmaInputSpecs.inputBorderRadius,
          ),
          borderSide: const BorderSide(
            color: FigmaInputSpecs.inputBorderColorError,
            width: FigmaInputSpecs.inputBorderWidth,
          ),
        ),
        
        // Border en focus + erreur
        focusedErrorBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(
            FigmaInputSpecs.inputBorderRadius,
          ),
          borderSide: const BorderSide(
            color: FigmaInputSpecs.inputBorderColorError,
            width: 2.0,
          ),
        ),
        
        // Fill
        filled: true,
        fillColor: enabled 
            ? FigmaInputSpecs.inputFillColor 
            : FigmaInputSpecs.inputFillColor.withOpacity(0.5),
        
        // Padding interne
        contentPadding: FigmaInputSpecs.inputPadding,
        
        // Style label
        labelStyle: const TextStyle(
          fontSize: FigmaInputSpecs.inputLabelSize,
          fontWeight: FigmaInputSpecs.inputLabelWeight,
          color: FigmaInputSpecs.inputHintColor,
        ),
        
        // Style hint
        hintStyle: const TextStyle(
          fontSize: FigmaInputSpecs.inputTextSize,
          color: FigmaInputSpecs.inputHintColor,
        ),
      ),
    );
  }
}

// Utilisation
DealAutoTextField(
  label: 'Numéro de téléphone',
  hint: '+228 XX XX XX XX',
  prefixIcon: const Icon(Icons.phone),
  validator: Validators.validatePhone,
)
```

---

#### 3. BOUTONS

**À extraire de CHAQUE bouton :**

```dart
class FigmaButtonSpecs {
  // Dimensions
  static const double buttonHeight = 56.0;
  static const double buttonBorderRadius = 12.0;
  
  // Padding interne
  static const EdgeInsets buttonPadding = EdgeInsets.symmetric(
    horizontal: 32.0,
    vertical: 16.0,
  );
  
  // Couleurs - Bouton Primaire
  static const Color primaryButtonBackground = Color(0xFF0D3B66);
  static const Color primaryButtonForeground = Color(0xFFFFFFFF);
  static const Color primaryButtonBackgroundPressed = Color(0xFF082442);
  static const Color primaryButtonBackgroundDisabled = Color(0xFFBDBDBD);
  
  // Couleurs - Bouton Secondaire
  static const Color secondaryButtonBorder = Color(0xFF0D3B66);
  static const Color secondaryButtonForeground = Color(0xFF0D3B66);
  static const Color secondaryButtonBackground = Color(0x00000000); // Transparent
  
  // Couleurs - Bouton Texte
  static const Color textButtonForeground = Color(0xFF0D3B66);
  
  // Shadow (Elevation)
  static const double buttonElevation = 2.0;
  static const Color buttonShadowColor = Color(0x1A000000); // 10% noir
  static const Offset buttonShadowOffset = Offset(0, 2);
  static const double buttonShadowBlurRadius = 8.0;
  static const double buttonShadowSpreadRadius = 0.0;
  
  // Typographie
  static const double buttonTextSize = 16.0;
  static const FontWeight buttonTextWeight = FontWeight.w600;
  static const double buttonLetterSpacing = 0.5;
  
  // Icônes
  static const double buttonIconSize = 20.0;
  static const double buttonIconSpacing = 8.0; // Entre icône et texte
}
```

**Processus d'extraction dans Figma :**

1. **Sélectionner un bouton**
2. **Dimensions :**
   - Size → Height = `buttonHeight`
   - Auto Layout → Horizontal padding = `horizontal`, Vertical = `vertical`
3. **Apparence :**
   - Corner Radius = `buttonBorderRadius`
   - Fill (bouton primaire) = `primaryButtonBackground`
   - Stroke (bouton secondaire) = `secondaryButtonBorder`
4. **Shadow :**
   - Effects → Drop Shadow
   - X = `offset.dx`, Y = `offset.dy`
   - Blur = `blurRadius`
   - Spread = `spreadRadius`
   - Color + Opacity = `shadowColor`
5. **Texte :**
   - Font size = `buttonTextSize`
   - Font weight = `buttonTextWeight`
   - Letter spacing = `buttonLetterSpacing`
   - Color = `buttonForeground`

**États à identifier :**
- **Normal** : État repos
- **Pressed** : Quand appuyé (généralement plus foncé)
- **Disabled** : Non cliquable (grisé)
- **Loading** : En cours d'action (spinner)

**Template Flutter :**
```dart
// Bouton Primaire
class PrimaryButton extends StatelessWidget {
  final String text;
  final VoidCallback? onPressed;
  final bool isLoading;
  final Widget? icon;
  
  const PrimaryButton({
    Key? key,
    required this.text,
    this.onPressed,
    this.isLoading = false,
    this.icon,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return SizedBox(
      height: FigmaButtonSpecs.buttonHeight,
      width: double.infinity,
      child: ElevatedButton(
        onPressed: isLoading ? null : onPressed,
        style: ElevatedButton.styleFrom(
          backgroundColor: FigmaButtonSpecs.primaryButtonBackground,
          foregroundColor: FigmaButtonSpecs.primaryButtonForeground,
          disabledBackgroundColor: FigmaButtonSpecs.primaryButtonBackgroundDisabled,
          disabledForegroundColor: Colors.white,
          
          // Shape
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(
              FigmaButtonSpecs.buttonBorderRadius,
            ),
          ),
          
          // Padding
          padding: FigmaButtonSpecs.buttonPadding,
          
          // Shadow
          elevation: FigmaButtonSpecs.buttonElevation,
          shadowColor: FigmaButtonSpecs.buttonShadowColor,
          
          // Text style
          textStyle: const TextStyle(
            fontSize: FigmaButtonSpecs.buttonTextSize,
            fontWeight: FigmaButtonSpecs.buttonTextWeight,
            letterSpacing: FigmaButtonSpecs.buttonLetterSpacing,
          ),
        ),
        child: isLoading
            ? const SizedBox(
                height: 20,
                width: 20,
                child: CircularProgressIndicator(
                  color: Colors.white,
                  strokeWidth: 2,
                ),
              )
            : icon != null
                ? Row(
                    mainAxisSize: MainAxisSize.min,
                    children: [
                      icon!,
                      SizedBox(width: FigmaButtonSpecs.buttonIconSpacing),
                      Text(text),
                    ],
                  )
                : Text(text),
      ),
    );
  }
}

// Bouton Secondaire
class SecondaryButton extends StatelessWidget {
  final String text;
  final VoidCallback? onPressed;
  final Widget? icon;
  
  const SecondaryButton({
    Key? key,
    required this.text,
    this.onPressed,
    this.icon,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return SizedBox(
      height: FigmaButtonSpecs.buttonHeight,
      width: double.infinity,
      child: OutlinedButton(
        onPressed: onPressed,
        style: OutlinedButton.styleFrom(
          foregroundColor: FigmaButtonSpecs.secondaryButtonForeground,
          backgroundColor: FigmaButtonSpecs.secondaryButtonBackground,
          
          // Border
          side: const BorderSide(
            color: FigmaButtonSpecs.secondaryButtonBorder,
            width: 2.0,
          ),
          
          // Shape
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(
              FigmaButtonSpecs.buttonBorderRadius,
            ),
          ),
          
          // Padding
          padding: FigmaButtonSpecs.buttonPadding,
          
          // Text style
          textStyle: const TextStyle(
            fontSize: FigmaButtonSpecs.buttonTextSize,
            fontWeight: FigmaButtonSpecs.buttonTextWeight,
            letterSpacing: FigmaButtonSpecs.buttonLetterSpacing,
          ),
        ),
        child: icon != null
            ? Row(
                mainAxisSize: MainAxisSize.min,
                children: [
                  icon!,
                  SizedBox(width: FigmaButtonSpecs.buttonIconSpacing),
                  Text(text),
                ],
              )
            : Text(text),
      ),
    );
  }
}

// Utilisation
PrimaryButton(
  text: 'Se connecter',
  onPressed: () => _handleLogin(),
  isLoading: _isLoading,
)

SecondaryButton(
  text: 'Annuler',
  onPressed: () => Navigator.pop(context),
)
```

---

#### 4. CARDS (Cartes)

**À extraire :**

```dart
class FigmaCardSpecs {
  // Dimensions
  static const double cardBorderRadius = 16.0;
  static const double cardMinHeight = 100.0;
  
  // Padding interne
  static const EdgeInsets cardPadding = EdgeInsets.all(16.0);
  
  // Margin externe
  static const EdgeInsets cardMargin = EdgeInsets.only(bottom: 16.0);
  
  // Couleurs
  static const Color cardBackground = Color(0xFFFFFFFF);
  static const Color cardBorder = Color(0xFFE0E0E0);
  static const double cardBorderWidth = 1.0;
  
  // Shadow
  static const List<BoxShadow> cardShadow = [
    BoxShadow(
      color: Color(0x0F000000), // 6% noir
      offset: Offset(0, 2),
      blurRadius: 8.0,
      spreadRadius: 0.0,
    ),
  ];
  
  // Image (pour vehicle cards)
  static const double cardImageHeight = 200.0;
  static const double cardImageBorderRadius = 12.0; // Plus petit que card
  
  // Badge overlay (NOUVEAU, PROMO, etc)
  static const double badgeSize = 60.0;
  static const double badgeFontSize = 12.0;
  static const FontWeight badgeFontWeight = FontWeight.w700;
}
```

**Processus d'extraction dans Figma :**

1. **Sélectionner une card**
2. **Structure :**
   - Corner Radius = `cardBorderRadius`
   - Auto Layout → Padding = `cardPadding`
   - Gap between items (si Auto Layout) = spacing interne
3. **Apparence :**
   - Fill = `cardBackground`
   - Stroke (si présent) = `cardBorder` + `cardBorderWidth`
4. **Shadow :**
   - Effects → Drop Shadow
   - Extraire offset, blur, spread, color
5. **Image (si présente) :**
   - Height de l'image = `cardImageHeight`
   - Corner Radius = `cardImageBorderRadius`
6. **Badges :**
   - Position (top-left, top-right)
   - Size, couleur, texte

**Template Card Véhicule :**
```dart
class VehicleCard extends StatelessWidget {
  final Vehicle vehicle;
  final VoidCallback onTap;
  
  const VehicleCard({
    Key? key,
    required this.vehicle,
    required this.onTap,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: FigmaCardSpecs.cardMargin,
      decoration: BoxDecoration(
        color: FigmaCardSpecs.cardBackground,
        borderRadius: BorderRadius.circular(
          FigmaCardSpecs.cardBorderRadius,
        ),
        border: Border.all(
          color: FigmaCardSpecs.cardBorder,
          width: FigmaCardSpecs.cardBorderWidth,
        ),
        boxShadow: FigmaCardSpecs.cardShadow,
      ),
      child: Material(
        color: Colors.transparent,
        child: InkWell(
          onTap: onTap,
          borderRadius: BorderRadius.circular(
            FigmaCardSpecs.cardBorderRadius,
          ),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              // Image avec badges
              _buildImageSection(),
              
              // Contenu
              Padding(
                padding: FigmaCardSpecs.cardPadding,
                child: Column(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    _buildTitle(),
                    const SizedBox(height: 8),
                    _buildPrice(),
                    const SizedBox(height: 8),
                    _buildMetadata(),
                  ],
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
  
  Widget _buildImageSection() {
    return Stack(
      children: [
        // Image principale
        ClipRRect(
          borderRadius: const BorderRadius.only(
            topLeft: Radius.circular(FigmaCardSpecs.cardImageBorderRadius),
            topRight: Radius.circular(FigmaCardSpecs.cardImageBorderRadius),
          ),
          child: CachedNetworkImage(
            imageUrl: vehicle.mainImageUrl,
            height: FigmaCardSpecs.cardImageHeight,
            width: double.infinity,
            fit: BoxFit.cover,
            placeholder: (context, url) => Container(
              color: Colors.grey[200],
              child: const Center(
                child: CircularProgressIndicator(),
              ),
            ),
            errorWidget: (context, url, error) => Container(
              color: Colors.grey[300],
              child: const Icon(Icons.error),
            ),
          ),
        ),
        
        // Badge "NOUVEAU" (top-left)
        if (vehicle.isNew)
          Positioned(
            top: 12,
            left: 12,
            child: Container(
              padding: const EdgeInsets.symmetric(
                horizontal: 12,
                vertical: 6,
              ),
              decoration: BoxDecoration(
                color: const Color(0xFFFF8C42), // Orange
                borderRadius: BorderRadius.circular(6),
              ),
              child: const Text(
                'NOUVEAU',
                style: TextStyle(
                  color: Colors.white,
                  fontSize: FigmaCardSpecs.badgeFontSize,
                  fontWeight: FigmaCardSpecs.badgeFontWeight,
                ),
              ),
            ),
          ),
        
        // Badge type vendeur (top-right)
        Positioned(
          top: 12,
          right: 12,
          child: Container(
            padding: const EdgeInsets.symmetric(
              horizontal: 10,
              vertical: 4,
            ),
            decoration: BoxDecoration(
              color: _getSellerTypeBadgeColor(),
              borderRadius: BorderRadius.circular(4),
            ),
            child: Text(
              _getSellerTypeLabel(),
              style: const TextStyle(
                color: Colors.white,
                fontSize: 10,
                fontWeight: FontWeight.w600,
              ),
            ),
          ),
        ),
      ],
    );
  }
  
  Widget _buildTitle() {
    return Text(
      '${vehicle.brand} ${vehicle.model}',
      style: const TextStyle(
        fontSize: 18,
        fontWeight: FontWeight.w700,
        color: Color(0xFF212121),
      ),
      maxLines: 1,
      overflow: TextOverflow.ellipsis,
    );
  }
  
  Widget _buildPrice() {
    return Text(
      Formatters.formatPrice(vehicle.price),
      style: const TextStyle(
        fontSize: 20,
        fontWeight: FontWeight.w700,
        color: Color(0xFFFF8C42), // Orange (couleur secondaire)
      ),
    );
  }
  
  Widget _buildMetadata() {
    return Row(
      children: [
        Icon(
          Icons.calendar_today,
          size: 14,
          color: Colors.grey[600],
        ),
        const SizedBox(width: 4),
        Text(
          '${vehicle.year}',
          style: TextStyle(
            fontSize: 14,
            color: Colors.grey[600],
          ),
        ),
        const SizedBox(width: 16),
        Icon(
          Icons.speed,
          size: 14,
          color: Colors.grey[600],
        ),
        const SizedBox(width: 4),
        Text(
          Formatters.formatMileage(vehicle.mileage),
          style: TextStyle(
            fontSize: 14,
            color: Colors.grey[600],
          ),
        ),
      ],
    );
  }
  
  Color _getSellerTypeBadgeColor() {
    switch (vehicle.sellerType) {
      case 'professionnel':
        return const Color(0xFF4CAF50); // Vert
      case 'concessionnaire':
        return const Color(0xFF2196F3); // Bleu
      default:
        return const Color(0xFF9E9E9E); // Gris
    }
  }
  
  String _getSellerTypeLabel() {
    switch (vehicle.sellerType) {
      case 'professionnel':
        return 'PRO';
      case 'concessionnaire':
        return 'CONCESSIONNAIRE';
      default:
        return 'PARTICULIER';
    }
  }
}
```

---

#### 5. ESPACEMENT (Spacing System)

**Système 8dp (Design Tokens) :**

```dart
class FigmaSpacing {
  // Système basé sur 8dp
  static const double xxs = 4.0;   // XXS
  static const double xs = 8.0;    // XS
  static const double sm = 12.0;   // Small
  static const double md = 16.0;   // Medium (DEFAULT)
  static const double lg = 24.0;   // Large
  static const double xl = 32.0;   // XL
  static const double xxl = 48.0;  // XXL
  static const double xxxl = 64.0; // XXXL
  
  // Spacing spécifiques
  static const double screenPadding = 24.0;
  static const double sectionSpacing = 32.0;
  static const double cardSpacing = 16.0;
  static const double elementSpacing = 12.0;
  static const double iconTextSpacing = 8.0;
}
```

**Comment identifier dans Figma :**
1. Sélectionner 2 éléments adjacents
2. Figma affiche automatiquement l'espace entre (ligne rouge)
3. Noter la valeur
4. Vérifier que c'est un multiple de 4 (idéalement 8)

**Utilisation :**
```dart
Column(
  children: [
    Text('Titre'),
    SizedBox(height: FigmaSpacing.md), // 16dp
    TextField(),
    SizedBox(height: FigmaSpacing.lg), // 24dp
    Button(),
  ],
)
```

---

#### 6. TYPOGRAPHIE

**À extraire :**

```dart
class FigmaTypography {
  // Headings (Titres)
  static const TextStyle h1 = TextStyle(
    fontSize: 32.0,
    fontWeight: FontWeight.w700,  // Bold
    height: 1.2,                  // Line height
    color: Color(0xFF212121),
    letterSpacing: -0.5,
  );
  
  static const TextStyle h2 = TextStyle(
    fontSize: 24.0,
    fontWeight: FontWeight.w700,
    height: 1.3,
    color: Color(0xFF212121),
  );
  
  static const TextStyle h3 = TextStyle(
    fontSize: 20.0,
    fontWeight: FontWeight.w600,  // SemiBold
    height: 1.4,
    color: Color(0xFF212121),
  );
  
  static const TextStyle h4 = TextStyle(
    fontSize: 18.0,
    fontWeight: FontWeight.w600,
    height: 1.4,
    color: Color(0xFF212121),
  );
  
  // Body text
  static const TextStyle body1 = TextStyle(
    fontSize: 16.0,
    fontWeight: FontWeight.w400,  // Regular
    height: 1.5,
    color: Color(0xFF212121),
  );
  
  static const TextStyle body2 = TextStyle(
    fontSize: 14.0,
    fontWeight: FontWeight.w400,
    height: 1.5,
    color: Color(0xFF757575),
  );
  
  // Small text
  static const TextStyle caption = TextStyle(
    fontSize: 12.0,
    fontWeight: FontWeight.w400,
    height: 1.4,
    color: Color(0xFF9E9E9E),
  );
  
  // Boutons
  static const TextStyle button = TextStyle(
    fontSize: 16.0,
    fontWeight: FontWeight.w600,
    letterSpacing: 0.5,
    color: Colors.white,
  );
  
  // Prix (spécial DealAuto)
  static const TextStyle price = TextStyle(
    fontSize: 20.0,
    fontWeight: FontWeight.w700,
    color: Color(0xFFFF8C42), // Orange
  );
  
  // Badge
  static const TextStyle badge = TextStyle(
    fontSize: 12.0,
    fontWeight: FontWeight.w700,
    color: Colors.white,
  );
}
```

**Comment extraire dans Figma :**
1. Sélectionner un texte
2. Panneau Design → Text
3. Noter :
   - Font family (Roboto, Inter, etc)
   - Font size
   - Font weight (Light/Regular/Medium/SemiBold/Bold)
   - Line height (Auto ou valeur fixe)
   - Letter spacing
   - Color

---

#### 7. COULEURS (Palette)

**À extraire :**

```dart
class FigmaColors {
  // Couleurs principales
  static const Color primary = Color(0xFF0D3B66);      // Bleu foncé
  static const Color primaryDark = Color(0xFF082442);  // Bleu très foncé
  static const Color primaryLight = Color(0xFF1A5490); // Bleu clair
  
  static const Color secondary = Color(0xFFFF8C42);    // Orange
  static const Color secondaryDark = Color(0xFFE07935);
  static const Color secondaryLight = Color(0xFFFFA05E);
  
  // Couleurs d'arrière-plan
  static const Color background = Color(0xFFF5F5F5);   // Gris très clair
  static const Color surface = Color(0xFFFFFFFF);      // Blanc
  static const Color surfaceVariant = Color(0xFFFAFAFA);
  
  // Couleurs de texte
  static const Color textPrimary = Color(0xFF212121);    // Presque noir
  static const Color textSecondary = Color(0xFF757575);  // Gris moyen
  static const Color textDisabled = Color(0xFFBDBDBD);   // Gris clair
  static const Color textOnPrimary = Color(0xFFFFFFFF);  // Blanc (sur fond primary)
  
  // Couleurs d'état
  static const Color success = Color(0xFF4CAF50);  // Vert
  static const Color error = Color(0xFFD32F2F);    // Rouge
  static const Color warning = Color(0xFFFFC107);  // Jaune/Orange
  static const Color info = Color(0xFF2196F3);     // Bleu
  
  // Bordures et dividers
  static const Color border = Color(0xFFE0E0E0);
  static const Color divider = Color(0xFFEEEEEE);
  
  // Overlays et shadows
  static const Color overlay = Color(0x80000000);    // Noir 50%
  static const Color shadowLight = Color(0x0F000000); // Noir 6%
  static const Color shadowMedium = Color(0x1A000000);// Noir 10%
  static const Color shadowDark = Color(0x33000000);  // Noir 20%
}
```

**Comment extraire dans Figma :**
1. Sélectionner un élément coloré
2. Panneau Design → Fill/Stroke
3. Noter le code HEX (ex: #0D3B66)
4. Convertir en Color Dart : `Color(0xFF0D3B66)`
   - Remplacer # par 0xFF
5. Noter l'opacité si < 100%
   - 50% opacity = 0x80 au lieu de 0xFF
   - Calcul : (opacity% / 100) * 255 en hexadécimal

---

## Design System Complet

### Theme Configuration Flutter

```dart
// lib/presentation/theme/app_theme.dart
import 'package:flutter/material.dart';

class AppTheme {
  static ThemeData get lightTheme {
    return ThemeData(
      // Couleurs principales
      primaryColor: FigmaColors.primary,
      colorScheme: const ColorScheme.light(
        primary: FigmaColors.primary,
        secondary: FigmaColors.secondary,
        surface: FigmaColors.surface,
        background: FigmaColors.background,
        error: FigmaColors.error,
        onPrimary: FigmaColors.textOnPrimary,
        onSecondary: Colors.white,
        onSurface: FigmaColors.textPrimary,
        onBackground: FigmaColors.textPrimary,
        onError: Colors.white,
      ),
      
      // Scaffolds
      scaffoldBackgroundColor: FigmaColors.background,
      
      // AppBar
      appBarTheme: const AppBarTheme(
        backgroundColor: Colors.white,
        foregroundColor: FigmaColors.textPrimary,
        elevation: 0,
        centerTitle: true,
        titleTextStyle: FigmaTypography.h3,
        iconTheme: IconThemeData(
          color: FigmaColors.textPrimary,
          size: 24,
        ),
      ),
      
      // Text Theme
      textTheme: const TextTheme(
        displayLarge: FigmaTypography.h1,
        displayMedium: FigmaTypography.h2,
        displaySmall: FigmaTypography.h3,
        headlineMedium: FigmaTypography.h4,
        bodyLarge: FigmaTypography.body1,
        bodyMedium: FigmaTypography.body2,
        labelLarge: FigmaTypography.button,
        bodySmall: FigmaTypography.caption,
      ),
      
      // ElevatedButton
      elevatedButtonTheme: ElevatedButtonThemeData(
        style: ElevatedButton.styleFrom(
          backgroundColor: FigmaColors.primary,
          foregroundColor: Colors.white,
          minimumSize: const Size(double.infinity, 56),
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(12),
          ),
          elevation: 2,
          textStyle: FigmaTypography.button,
        ),
      ),
      
      // OutlinedButton
      outlinedButtonTheme: OutlinedButtonThemeData(
        style: OutlinedButton.styleFrom(
          foregroundColor: FigmaColors.primary,
          minimumSize: const Size(double.infinity, 56),
          side: const BorderSide(
            color: FigmaColors.primary,
            width: 2,
          ),
          shape: RoundedRectangleBorder(
            borderRadius: BorderRadius.circular(12),
          ),
          textStyle: FigmaTypography.button,
        ),
      ),
      
      // TextButton
      textButtonTheme: TextButtonThemeData(
        style: TextButton.styleFrom(
          foregroundColor: FigmaColors.primary,
          textStyle: FigmaTypography.button.copyWith(
            fontWeight: FontWeight.w600,
          ),
        ),
      ),
      
      // InputDecoration
      inputDecorationTheme: InputDecorationTheme(
        filled: true,
        fillColor: Colors.white,
        border: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(
            color: FigmaColors.border,
          ),
        ),
        enabledBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(
            color: FigmaColors.border,
          ),
        ),
        focusedBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(
            color: FigmaColors.primary,
            width: 2,
          ),
        ),
        errorBorder: OutlineInputBorder(
          borderRadius: BorderRadius.circular(12),
          borderSide: const BorderSide(
            color: FigmaColors.error,
          ),
        ),
        contentPadding: const EdgeInsets.symmetric(
          horizontal: 16,
          vertical: 16,
        ),
        labelStyle: FigmaTypography.body2,
        hintStyle: FigmaTypography.body2.copyWith(
          color: FigmaColors.textDisabled,
        ),
      ),
      
      // Card
      cardTheme: CardTheme(
        color: Colors.white,
        elevation: 0,
        shape: RoundedRectangleBorder(
          borderRadius: BorderRadius.circular(16),
          side: const BorderSide(
            color: FigmaColors.border,
          ),
        ),
        margin: const EdgeInsets.only(bottom: 16),
      ),
      
      // Divider
      dividerTheme: const DividerThemeData(
        color: FigmaColors.divider,
        thickness: 1,
        space: 24,
      ),
      
      // BottomNavigationBar
      bottomNavigationBarTheme: const BottomNavigationBarThemeData(
        backgroundColor: Colors.white,
        selectedItemColor: FigmaColors.primary,
        unselectedItemColor: FigmaColors.textSecondary,
        type: BottomNavigationBarType.fixed,
        elevation: 8,
        selectedLabelStyle: TextStyle(
          fontSize: 12,
          fontWeight: FontWeight.w600,
        ),
        unselectedLabelStyle: TextStyle(
          fontSize: 12,
          fontWeight: FontWeight.w400,
        ),
      ),
    );
  }
}
```

---

## Navigation et Transitions

### Transitions Standards

```dart
class AppTransitions {
  // Durées
  static const Duration fast = Duration(milliseconds: 200);
  static const Duration normal = Duration(milliseconds: 300);
  static const Duration slow = Duration(milliseconds: 400);
  
  // Curves
  static const Curve easeIn = Curves.easeIn;
  static const Curve easeOut = Curves.easeOut;
  static const Curve easeInOut = Curves.easeInOut;
  
  // Slide Right (navigation principale)
  static Route slideRight(Widget page) {
    return PageRouteBuilder(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: normal,
      reverseTransitionDuration: normal,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        const begin = Offset(1.0, 0.0);
        const end = Offset.zero;
        final tween = Tween(begin: begin, end: end);
        final curvedAnimation = CurvedAnimation(
          parent: animation,
          curve: easeInOut,
        );
        
        return SlideTransition(
          position: tween.animate(curvedAnimation),
          child: child,
        );
      },
    );
  }
  
  // Fade (pour modals)
  static Route fade(Widget page) {
    return PageRouteBuilder(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: fast,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        return FadeTransition(
          opacity: animation,
          child: child,
        );
      },
    );
  }
  
  // Scale (pour dialogs)
  static Route scale(Widget page) {
    return PageRouteBuilder(
      pageBuilder: (context, animation, secondaryAnimation) => page,
      transitionDuration: fast,
      transitionsBuilder: (context, animation, secondaryAnimation, child) {
        const begin = 0.8;
        const end = 1.0;
        final tween = Tween(begin: begin, end: end);
        final curvedAnimation = CurvedAnimation(
          parent: animation,
          curve: easeOut,
        );
        
        return ScaleTransition(
          scale: tween.animate(curvedAnimation),
          child: FadeTransition(
            opacity: animation,
            child: child,
          ),
        );
      },
    );
  }
}

// Utilisation
Navigator.push(
  context,
  AppTransitions.slideRight(VehicleDetailScreen()),
);
```

---

## Animations et Micro-interactions

### Haptic Feedback
```dart
import 'package:flutter/services.dart';

class AppHaptics {
  // Feedback léger (tap normal)
  static void light() {
    HapticFeedback.lightImpact();
  }
  
  // Feedback moyen (action importante)
  static void medium() {
    HapticFeedback.mediumImpact();
  }
  
  // Feedback fort (succès/erreur)
  static void heavy() {
    HapticFeedback.heavyImpact();
  }
  
  // Selection
  static void selection() {
    HapticFeedback.selectionClick();
  }
}

// Utilisation dans bouton
onPressed: () {
  AppHaptics.light();
  _handleSubmit();
}
```

### Loading States (Skeleton)
```dart
class SkeletonLoader extends StatelessWidget {
  final double width;
  final double height;
  final double borderRadius;
  
  const SkeletonLoader({
    Key? key,
    required this.width,
    required this.height,
    this.borderRadius = 8.0,
  }) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Shimmer.fromColors(
      baseColor: Colors.grey[300]!,
      highlightColor: Colors.grey[100]!,
      child: Container(
        width: width,
        height: height,
        decoration: BoxDecoration(
          color: Colors.grey[300],
          borderRadius: BorderRadius.circular(borderRadius),
        ),
      ),
    );
  }
}

// Skeleton pour VehicleCard
class VehicleCardSkeleton extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Container(
      margin: FigmaCardSpecs.cardMargin,
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(16),
        border: Border.all(color: FigmaColors.border),
      ),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          SkeletonLoader(
            width: double.infinity,
            height: 200,
            borderRadius: 12,
          ),
          Padding(
            padding: const EdgeInsets.all(16),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                SkeletonLoader(width: 200, height: 20),
                const SizedBox(height: 8),
                SkeletonLoader(width: 150, height: 24),
                const SizedBox(height: 8),
                Row(
                  children: [
                    SkeletonLoader(width: 80, height: 16),
                    const SizedBox(width: 16),
                    SkeletonLoader(width: 100, height: 16),
                  ],
                ),
              ],
            ),
          ),
        ],
      ),
    );
  }
}
```

---

## Checklist Qualité UI

### ✅ Avant Chaque Commit

- [ ] Toutes les valeurs Figma respectées (border-radius, padding, colors)
- [ ] Transitions fluides (300ms, 60 FPS)
- [ ] Haptic feedback sur actions importantes
- [ ] Loading states présents partout
- [ ] Messages d'erreur en français
- [ ] Responsive sur différentes tailles d'écran
- [ ] Textes lisibles (contraste suffisant)
- [ ] Boutons suffisamment grands (min 44×44dp)
- [ ] Espacement cohérent (système 8dp)
- [ ] Ombres et élévations correctes

---

**FIN DU DOCUMENT UI/UX FIGMA GUIDELINES**
