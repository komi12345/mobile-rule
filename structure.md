# DealAuto - Project Structure

## Directory Layout

```
lib/
├── main.dart                 # App entry point, SplashScreen, WelcomeScreen
├── data/
│   └── country_data.dart     # Country list (240+ countries, ISO 3166-1)
├── models/
│   ├── country.dart          # Country model
│   ├── profile_type.dart     # User profile enum (5 types)
│   ├── profile_card_data.dart
│   ├── vehicle.dart          # Vehicle model
│   └── vehicle_category.dart # Vehicle category enum (6 types)
├── screens/
│   ├── login_screen.dart
│   ├── registration_screen.dart
│   ├── registration_otp_screen.dart
│   ├── password_recovery_screen.dart
│   ├── otp_verification_screen.dart
│   ├── set_new_password_screen.dart
│   ├── profile_selection_screen.dart
│   ├── congratulations_screen.dart
│   └── home_dashboard_screen.dart
├── services/
│   ├── country_data_service.dart      # Async country loading
│   ├── image_optimization_service.dart # Image caching/optimization
│   └── navigation_service.dart         # Route transitions
├── utils/
│   ├── accessibility_utils.dart  # WCAG contrast calculations
│   ├── debouncer.dart            # Search debouncing
│   ├── error_text_utils.dart     # Error text overflow handling
│   ├── image_error_handler.dart  # Image placeholder/error handling
│   ├── responsive_utils.dart     # Breakpoints, touch targets
│   └── safe_state_mixin.dart     # Safe setState after dispose
└── widgets/
    ├── deal_auto_logo.dart
    ├── decorative_dots.dart
    ├── success_indicator.dart
    ├── phone_input_field.dart
    ├── password_text_field.dart
    ├── name_text_field.dart
    ├── country_picker_dialog.dart
    ├── country_dropdown_field.dart
    ├── profile_card.dart
    ├── vehicle_card.dart
    ├── category_icon.dart
    ├── search_bar_widget.dart
    ├── contact_banner_widget.dart
    └── scoped_state_builder.dart

test/
├── *_unit_test.dart      # Unit tests (~17 files)
└── *_property_test.dart  # Property-based tests (~35 files)

assets/
└── images/               # App images

.kiro/
├── steering/             # AI guidance documents
└── specs/                # Feature specifications (15 specs)
```

## Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Files | snake_case | `login_screen.dart` |
| Classes | PascalCase | `LoginScreen` |
| Variables | camelCase | `selectedCountry` |
| Private | _camelCase | `_selectedCountry` |
| Constants | static const | `static const darkBlue` |

## File Suffixes

| Suffix | Purpose |
|--------|---------|
| `_screen.dart` | Full-page screens |
| `_widget.dart` | Reusable widgets |
| `_service.dart` | Business logic services |
| `_utils.dart` | Utility functions |
| `_unit_test.dart` | Unit tests |
| `_property_test.dart` | Property-based tests |

## Import Order

1. Dart core (`dart:async`, `dart:io`)
2. Flutter (`package:flutter/material.dart`)
3. External packages (`package:provider/...`)
4. Local imports (alphabetically)
