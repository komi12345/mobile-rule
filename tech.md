# DealAuto - Technical Stack

## Framework & Language

- **Framework**: Flutter 3.10+
- **Language**: Dart 3.0+ (SDK ^3.10.3)
- **Platforms**: Android (primary), iOS (planned)

## Dependencies

### Production
```yaml
flutter: sdk
cupertino_icons: ^1.0.8
```

### Development
```yaml
flutter_test: sdk
glados: ^1.1.1          # Property-based testing
flutter_lints: ^6.0.0   # Code linting
```

### Planned Dependencies (Backend)
- Firebase OR Supabase OR custom VPS (decision pending)
- provider/riverpod for state management
- cached_network_image for image caching
- url_launcher for phone/WhatsApp integration

## Common Commands

```bash
# Run the app
flutter run

# Run all tests
flutter test

# Run specific test file
flutter test test/filename_test.dart

# Analyze code
flutter analyze

# Format code
dart format lib/ test/

# Build APK
flutter build apk --release

# Get dependencies
flutter pub get
```

## Testing Strategy

- **Unit tests**: `*_unit_test.dart` - Logic and utilities
- **Property-based tests**: `*_property_test.dart` - Using glados for invariant testing
- **Widget tests**: Standard Flutter widget testing

## Code Quality

- Linting via `flutter_lints` package
- Target: 0 warnings from `flutter analyze`
- Coverage target: ≥70%

## Performance Targets

| Metric | Target |
|--------|--------|
| First Contentful Paint | < 2s |
| Frame rate | 60 FPS |
| Memory usage | < 150MB |
| APK size | < 30MB |
| Touch targets | ≥ 48px |

## Backend Options (Pending Decision)

1. **Firebase** - Fastest setup, native SMS auth
2. **Supabase** - PostgreSQL, predictable costs
3. **Contabo VPS** - Full control, lowest cost
