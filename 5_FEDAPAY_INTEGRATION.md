# DealAuto - Intégration Fedapay Complète

## 📋 Table des Matières

1. [Introduction Fedapay](#introduction)
2. [Configuration Compte Fedapay](#configuration)
3. [Architecture Paiement](#architecture)
4. [Implémentation Flutter](#implementation-flutter)
5. [Webhooks et Callbacks](#webhooks)
6. [Gestion des Erreurs](#erreurs)
7. [Tests et Validation](#tests)
8. [Sécurité](#securite)
9. [Go-Live Production](#production)

---

## Introduction Fedapay

### Qu'est-ce que Fedapay ?

Fedapay est la plateforme de paiement leader en Afrique francophone permettant d'accepter :
- **TMoney** (Togocom) - 95% du marché togolais
- **Flooz** (Moov Africa) - 80% du marché togolais  
- **Cartes bancaires** (Visa, Mastercard) - minoritaire au Togo

### Pourquoi Fedapay pour DealAuto ?

✅ Intégration Mobile Money native (TMoney + Flooz)
✅ API REST simple et documentée
✅ Webhooks pour notifications temps réel
✅ Dashboard admin complet
✅ Support technique réactif
✅ Conformité réglementaire BCEAO
✅ Frais compétitifs (3-5%)

### Flux Utilisateur DealAuto

```
User choisit abonnement
    ↓
Sélectionne méthode paiement (TMoney/Flooz/Card)
    ↓
Entre son numéro Mobile Money
    ↓
Reçoit popup de confirmation sur téléphone
    ↓
Entre PIN Mobile Money
    ↓
Paiement validé
    ↓
Abonnement activé automatiquement
```

---

## Configuration Compte Fedapay

### Étape 1 : Création Compte

1. **Aller sur** : https://fedapay.com
2. **S'inscrire** avec email professionnel
3. **Vérifier email**
4. **Compléter profil** :
   - Nom entreprise : DealAuto SARL
   - Pays : Togo
   - Secteur : Marketplace / E-commerce
   - RCCM : [Votre numéro]
   - IFU : [Votre numéro fiscal]

### Étape 2 : Activation Méthodes de Paiement

Dans le dashboard Fedapay :
1. **Settings → Payment Methods**
2. **Activer** :
   - ✅ TMoney
   - ✅ Flooz  
   - ✅ Cartes bancaires (optionnel)
3. **Soumettre documents** :
   - RCCM (registre commerce)
   - IFU (identifiant fiscal)
   - Pièce d'identité gérant
   - Justificatif domicile

**Délai activation** : 24-48h ouvrées

### Étape 3 : Obtenir Clés API

1. **Settings → API Keys**
2. **Mode Sandbox** (tests) :
   - Public Key : `pk_sandbox_xxxxxxxxxxxxxx`
   - Secret Key : `sk_sandbox_xxxxxxxxxxxxxx`
3. **Mode Live** (production) :
   - Public Key : `pk_live_xxxxxxxxxxxxxx`
   - Secret Key : `sk_live_xxxxxxxxxxxxxx`

⚠️ **CRITIQUE** : Ne JAMAIS commit les Secret Keys dans Git !

### Étape 4 : Configurer Webhook

1. **Settings → Webhooks**
2. **Add Webhook URL** :
   - URL : `https://api.dealauto.tg/webhooks/fedapay`
   - Events : `transaction.approved`, `transaction.declined`, `transaction.failed`
3. **Récupérer Webhook Secret** : `whsec_xxxxxxxxxxxxxx`

---

## Architecture Paiement

### Schéma Complet

```
┌─────────────────────────────────────────────────────┐
│              Flutter App (DealAuto)                  │
│                                                      │
│  1. User choisit abonnement (Professionnel 15K)    │
│  2. Clique "Payer"                                  │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│         Backend (Firebase/Supabase/VPS)              │
│                                                      │
│  3. Crée transaction Fedapay via API                │
│  4. Retourne transaction_id + checkout_url          │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│              Fedapay Checkout Page                   │
│                                                      │
│  5. User entre numéro Mobile Money                  │
│  6. Choisit TMoney ou Flooz                         │
│  7. Confirme montant                                │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│         Mobile Money Provider (Togocom/Moov)         │
│                                                      │
│  8. Envoie popup confirmation sur téléphone         │
│  9. User entre PIN                                  │
│ 10. Débite compte                                   │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│                  Fedapay API                         │
│                                                      │
│ 11. Transaction approved                            │
│ 12. Envoie webhook à backend                        │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│         Backend Webhook Handler                      │
│                                                      │
│ 13. Vérifie signature webhook                       │
│ 14. Active abonnement user                          │
│ 15. Envoie notification push                        │
└──────────────────┬──────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────────────────┐
│              Flutter App                             │
│                                                      │
│ 16. Affiche succès                                  │
│ 17. Redirige vers dashboard                         │
└──────────────────────────────────────────────────────┘
```

---

## Implémentation Flutter

### Installation Package

```yaml
# pubspec.yaml
dependencies:
  http: ^1.1.0
  url_launcher: ^6.2.2  # Pour ouvrir checkout Fedapay
```

### Structure Code

```
lib/
├── services/
│   └── fedapay_service.dart       # Service principal
├── models/
│   ├── transaction_model.dart     # Modèle transaction
│   └── subscription_plan.dart     # Plans d'abonnement
├── screens/
│   ├── subscription_plans_screen.dart
│   ├── payment_screen.dart
│   └── payment_success_screen.dart
└── config/
    └── fedapay_config.dart        # Configuration clés
```

### Configuration

```dart
// lib/config/fedapay_config.dart
class FedapayConfig {
  // Mode (sandbox ou live)
  static const bool isProduction = false; // Changer en true pour production
  
  // Clés API (NE JAMAIS commit les vraies clés)
  static const String publicKeySandbox = 'pk_sandbox_xxxxxxxxxxxxxx';
  static const String secretKeySandbox = 'sk_sandbox_xxxxxxxxxxxxxx';
  
  static const String publicKeyLive = 'pk_live_xxxxxxxxxxxxxx';
  static const String secretKeyLive = 'sk_live_xxxxxxxxxxxxxx';
  
  // Clés actives selon mode
  static String get publicKey => isProduction ? publicKeyLive : publicKeySandbox;
  static String get secretKey => isProduction ? secretKeyLive : secretKeySandbox;
  
  // URLs API
  static const String baseUrlSandbox = 'https://sandbox-api.fedapay.com/v1';
  static const String baseUrlLive = 'https://api.fedapay.com/v1';
  
  static String get baseUrl => isProduction ? baseUrlLive : baseUrlSandbox;
  
  // Montants plans (en FCFA)
  static const Map<String, int> planPrices = {
    'particulier': 10000,     // 10 000 FCFA
    'professionnel': 15000,   // 15 000 FCFA
    'concessionnaire': 50000, // 50 000 FCFA
  };
  
  // Limites plans
  static const Map<String, Map<String, dynamic>> planLimits = {
    'particulier': {
      'maxVehicles': 5,
      'maxPhotos': 8,
      'canFeature': false,
    },
    'professionnel': {
      'maxVehicles': 20,
      'maxPhotos': 10,
      'canFeature': true,
    },
    'concessionnaire': {
      'maxVehicles': 100,
      'maxPhotos': 15,
      'canFeature': true,
    },
  };
}
```

### Service Fedapay

```dart
// lib/services/fedapay_service.dart
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'package:firebase_auth/firebase_auth.dart'; // ou Supabase

class FedapayService {
  final String _baseUrl = FedapayConfig.baseUrl;
  final String _secretKey = FedapayConfig.secretKey;
  
  // Headers pour authentification
  Map<String, String> get _headers => {
    'Authorization': 'Bearer $_secretKey',
    'Content-Type': 'application/json',
  };
  
  /// Créer une transaction Fedapay
  /// 
  /// [planType] : 'particulier', 'professionnel', ou 'concessionnaire'
  /// [phoneNumber] : Numéro Mobile Money (+228XXXXXXXX)
  /// [paymentMethod] : 'mobile_money' ou 'card'
  /// 
  /// Returns : Map contenant transaction_id et checkout_url
  Future<Map<String, dynamic>> createTransaction({
    required String planType,
    required String phoneNumber,
    String paymentMethod = 'mobile_money',
  }) async {
    try {
      // Récupérer user actuel
      final user = FirebaseAuth.instance.currentUser;
      if (user == null) throw Exception('Utilisateur non connecté');
      
      // Récupérer montant du plan
      final amount = FedapayConfig.planPrices[planType];
      if (amount == null) throw Exception('Plan invalide');
      
      // Préparer données transaction
      final body = {
        'description': 'Abonnement DealAuto - $planType',
        'amount': amount,
        'currency': {
          'iso': 'XOF', // Franc CFA
        },
        'customer': {
          'firstname': user.displayName ?? '',
          'lastname': '',
          'email': user.email ?? '',
          'phone_number': {
            'number': phoneNumber,
            'country': 'tg', // Togo
          },
        },
        'callback_url': 'dealauto://payment-success', // Deep link
        'custom_metadata': {
          'userId': user.uid,
          'subscriptionType': planType,
          'planName': _getPlanName(planType),
          'app': 'dealauto',
        },
      };
      
      // Appel API Fedapay
      final response = await http.post(
        Uri.parse('$_baseUrl/transactions'),
        headers: _headers,
        body: json.encode(body),
      );
      
      if (response.statusCode == 200 || response.statusCode == 201) {
        final data = json.decode(response.body);
        
        return {
          'success': true,
          'transaction_id': data['v1']['id'],
          'transaction_reference': data['v1']['reference'],
          'checkout_url': data['v1']['url'], // URL pour payer
          'amount': amount,
          'currency': 'XOF',
        };
      } else {
        throw Exception('Erreur Fedapay: ${response.body}');
      }
    } catch (e) {
      print('Erreur createTransaction: $e');
      throw Exception('Impossible de créer la transaction: $e');
    }
  }
  
  /// Vérifier le statut d'une transaction
  /// 
  /// [transactionId] : ID de la transaction Fedapay
  /// 
  /// Returns : Status ('approved', 'pending', 'declined', 'failed')
  Future<String> checkTransactionStatus(String transactionId) async {
    try {
      final response = await http.get(
        Uri.parse('$_baseUrl/transactions/$transactionId'),
        headers: _headers,
      );
      
      if (response.statusCode == 200) {
        final data = json.decode(response.body);
        return data['v1']['status'] as String;
      } else {
        throw Exception('Erreur vérification: ${response.body}');
      }
    } catch (e) {
      print('Erreur checkTransactionStatus: $e');
      throw Exception('Impossible de vérifier le statut');
    }
  }
  
  /// Récupérer détails complets d'une transaction
  Future<Map<String, dynamic>> getTransactionDetails(String transactionId) async {
    try {
      final response = await http.get(
        Uri.parse('$_baseUrl/transactions/$transactionId'),
        headers: _headers,
      );
      
      if (response.statusCode == 200) {
        final data = json.decode(response.body);
        final transaction = data['v1'];
        
        return {
          'id': transaction['id'],
          'reference': transaction['reference'],
          'status': transaction['status'],
          'amount': transaction['amount'],
          'description': transaction['description'],
          'created_at': transaction['created_at'],
          'approved_at': transaction['approved_at'],
          'customer': transaction['customer'],
          'custom_metadata': transaction['custom_metadata'],
        };
      } else {
        throw Exception('Erreur récupération détails');
      }
    } catch (e) {
      print('Erreur getTransactionDetails: $e');
      rethrow;
    }
  }
  
  /// Ouvrir page de paiement Fedapay
  /// 
  /// Lance le navigateur avec l'URL checkout Fedapay
  Future<void> openCheckoutPage(String checkoutUrl) async {
    try {
      final uri = Uri.parse(checkoutUrl);
      
      if (await canLaunchUrl(uri)) {
        await launchUrl(
          uri,
          mode: LaunchMode.externalApplication, // Ouvre dans navigateur externe
        );
      } else {
        throw Exception('Impossible d\'ouvrir le lien de paiement');
      }
    } catch (e) {
      print('Erreur openCheckoutPage: $e');
      throw Exception('Erreur ouverture page paiement: $e');
    }
  }
  
  // Helper : nom du plan pour affichage
  String _getPlanName(String planType) {
    switch (planType) {
      case 'particulier':
        return 'Pack Particulier';
      case 'professionnel':
        return 'Pack Professionnel';
      case 'concessionnaire':
        return 'Pack Concessionnaire';
      default:
        return 'Pack Inconnu';
    }
  }
}
```

### Screen de Sélection Plan

```dart
// lib/screens/subscription_plans_screen.dart
import 'package:flutter/material.dart';

class SubscriptionPlansScreen extends StatelessWidget {
  const SubscriptionPlansScreen({Key? key}) : super(key: key);
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Choisir un abonnement'),
      ),
      body: SafeArea(
        child: Padding(
          padding: const EdgeInsets.all(24.0),
          child: Column(
            children: [
              // Header
              const Text(
                'Publiez plus d\'annonces',
                style: TextStyle(
                  fontSize: 24,
                  fontWeight: FontWeight.w700,
                ),
                textAlign: TextAlign.center,
              ),
              const SizedBox(height: 8),
              Text(
                'Choisissez l\'abonnement adapté à vos besoins',
                style: TextStyle(
                  fontSize: 16,
                  color: Colors.grey[600],
                ),
                textAlign: TextAlign.center,
              ),
              const SizedBox(height: 32),
              
              // Plans
              Expanded(
                child: ListView(
                  children: [
                    _buildPlanCard(
                      context,
                      planType: 'particulier',
                      title: 'Particulier',
                      price: 10000,
                      features: [
                        '5 annonces actives',
                        '8 photos par annonce',
                        'Publication immédiate',
                        'Support standard',
                      ],
                      isPopular: false,
                    ),
                    const SizedBox(height: 16),
                    _buildPlanCard(
                      context,
                      planType: 'professionnel',
                      title: 'Professionnel',
                      price: 15000,
                      features: [
                        '20 annonces actives',
                        '10 photos par annonce',
                        'Badge "PRO"',
                        'Mise en avant 5 jours',
                        'Support prioritaire',
                      ],
                      isPopular: true,
                    ),
                    const SizedBox(height: 16),
                    _buildPlanCard(
                      context,
                      planType: 'concessionnaire',
                      title: 'Concessionnaire',
                      price: 50000,
                      features: [
                        '100 annonces actives',
                        '15 photos par annonce',
                        'Badge "CONCESSIONNAIRE"',
                        'Mise en avant illimitée',
                        'Statistiques détaillées',
                        'Support dédié 24/7',
                      ],
                      isPopular: false,
                    ),
                  ],
                ),
              ),
            ],
          ),
        ),
      ),
    );
  }
  
  Widget _buildPlanCard(
    BuildContext context, {
    required String planType,
    required String title,
    required int price,
    required List<String> features,
    required bool isPopular,
  }) {
    return Container(
      decoration: BoxDecoration(
        color: Colors.white,
        borderRadius: BorderRadius.circular(16),
        border: Border.all(
          color: isPopular 
              ? const Color(0xFFFF8C42) 
              : const Color(0xFFE0E0E0),
          width: isPopular ? 2 : 1,
        ),
        boxShadow: [
          BoxShadow(
            color: Colors.black.withOpacity(0.06),
            offset: const Offset(0, 2),
            blurRadius: 8,
          ),
        ],
      ),
      child: Stack(
        children: [
          Padding(
            padding: const EdgeInsets.all(24),
            child: Column(
              crossAxisAlignment: CrossAxisAlignment.start,
              children: [
                // Titre + Badge populaire
                Row(
                  mainAxisAlignment: MainAxisAlignment.spaceBetween,
                  children: [
                    Text(
                      title,
                      style: const TextStyle(
                        fontSize: 20,
                        fontWeight: FontWeight.w700,
                      ),
                    ),
                    if (isPopular)
                      Container(
                        padding: const EdgeInsets.symmetric(
                          horizontal: 12,
                          vertical: 4,
                        ),
                        decoration: BoxDecoration(
                          color: const Color(0xFFFF8C42),
                          borderRadius: BorderRadius.circular(20),
                        ),
                        child: const Text(
                          'POPULAIRE',
                          style: TextStyle(
                            color: Colors.white,
                            fontSize: 11,
                            fontWeight: FontWeight.w700,
                          ),
                        ),
                      ),
                  ],
                ),
                const SizedBox(height: 16),
                
                // Prix
                Row(
                  crossAxisAlignment: CrossAxisAlignment.start,
                  children: [
                    Text(
                      Formatters.formatPrice(price.toDouble()),
                      style: const TextStyle(
                        fontSize: 28,
                        fontWeight: FontWeight.w700,
                        color: Color(0xFF0D3B66),
                      ),
                    ),
                    const SizedBox(width: 4),
                    const Padding(
                      padding: EdgeInsets.only(top: 8),
                      child: Text(
                        '/mois',
                        style: TextStyle(
                          fontSize: 14,
                          color: Colors.grey,
                        ),
                      ),
                    ),
                  ],
                ),
                const SizedBox(height: 24),
                
                // Features
                ...features.map((feature) => Padding(
                  padding: const EdgeInsets.only(bottom: 12),
                  child: Row(
                    children: [
                      const Icon(
                        Icons.check_circle,
                        color: Color(0xFF4CAF50),
                        size: 20,
                      ),
                      const SizedBox(width: 12),
                      Expanded(
                        child: Text(
                          feature,
                          style: const TextStyle(
                            fontSize: 15,
                            color: Color(0xFF212121),
                          ),
                        ),
                      ),
                    ],
                  ),
                )).toList(),
                
                const SizedBox(height: 24),
                
                // Bouton
                SizedBox(
                  width: double.infinity,
                  height: 56,
                  child: ElevatedButton(
                    onPressed: () {
                      Navigator.push(
                        context,
                        MaterialPageRoute(
                          builder: (context) => PaymentScreen(
                            planType: planType,
                            planName: title,
                            amount: price,
                          ),
                        ),
                      );
                    },
                    style: ElevatedButton.styleFrom(
                      backgroundColor: isPopular
                          ? const Color(0xFFFF8C42)
                          : const Color(0xFF0D3B66),
                    ),
                    child: const Text(
                      'Choisir ce plan',
                      style: TextStyle(
                        fontSize: 16,
                        fontWeight: FontWeight.w600,
                      ),
                    ),
                  ),
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

### Screen de Paiement

```dart
// lib/screens/payment_screen.dart
import 'package:flutter/material.dart';
import 'package:url_launcher/url_launcher.dart';

class PaymentScreen extends StatefulWidget {
  final String planType;
  final String planName;
  final int amount;
  
  const PaymentScreen({
    Key? key,
    required this.planType,
    required this.planName,
    required this.amount,
  }) : super(key: key);
  
  @override
  State<PaymentScreen> createState() => _PaymentScreenState();
}

class _PaymentScreenState extends State<PaymentScreen> {
  final FedapayService _fedapayService = FedapayService();
  final TextEditingController _phoneController = TextEditingController();
  
  bool _isLoading = false;
  String _selectedMethod = 'mobile_money'; // mobile_money ou card
  String _selectedProvider = 'tmoney'; // tmoney ou flooz
  
  @override
  void dispose() {
    _phoneController.dispose();
    super.dispose();
  }
  
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(
        title: const Text('Paiement'),
      ),
      body: SafeArea(
        child: SingleChildScrollView(
          padding: const EdgeInsets.all(24),
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              // Récapitulatif
              _buildSummaryCard(),
              const SizedBox(height: 32),
              
              // Méthode de paiement
              const Text(
                'Méthode de paiement',
                style: TextStyle(
                  fontSize: 18,
                  fontWeight: FontWeight.w700,
                ),
              ),
              const SizedBox(height: 16),
              
              // Mobile Money
              _buildPaymentMethodCard(
                icon: Icons.phone_android,
                title: 'Mobile Money',
                subtitle: 'TMoney ou Flooz',
                value: 'mobile_money',
              ),
              const SizedBox(height: 12),
              
              // Carte bancaire
              _buildPaymentMethodCard(
                icon: Icons.credit_card,
                title: 'Carte bancaire',
                subtitle: 'Visa ou Mastercard',
                value: 'card',
              ),
              
              // Si Mobile Money sélectionné
              if (_selectedMethod == 'mobile_money') ...[
                const SizedBox(height: 32),
                const Text(
                  'Opérateur',
                  style: TextStyle(
                    fontSize: 18,
                    fontWeight: FontWeight.w700,
                  ),
                ),
                const SizedBox(height: 16),
                
                Row(
                  children: [
                    Expanded(
                      child: _buildProviderCard(
                        logo: 'assets/images/tmoney_logo.png',
                        name: 'TMoney',
                        value: 'tmoney',
                      ),
                    ),
                    const SizedBox(width: 12),
                    Expanded(
                      child: _buildProviderCard(
                        logo: 'assets/images/flooz_logo.png',
                        name: 'Flooz',
                        value: 'flooz',
                      ),
                    ),
                  ],
                ),
                
                const SizedBox(height: 24),
                
                // Numéro de téléphone
                const Text(
                  'Numéro de téléphone',
                  style: TextStyle(
                    fontSize: 16,
                    fontWeight: FontWeight.w600,
                  ),
                ),
                const SizedBox(height: 8),
                TextFormField(
                  controller: _phoneController,
                  keyboardType: TextInputType.phone,
                  decoration: InputDecoration(
                    hintText: '+228 90 12 34 56',
                    prefixIcon: const Icon(Icons.phone),
                    border: OutlineInputBorder(
                      borderRadius: BorderRadius.circular(12),
                    ),
                  ),
                ),
              ],
              
              const SizedBox(height: 32),
              
              // Bouton payer
              SizedBox(
                width: double.infinity,
                height: 56,
                child: ElevatedButton(
                  onPressed: _isLoading ? null : _handlePayment,
                  child: _isLoading
                      ? const SizedBox(
                          height: 20,
                          width: 20,
                          child: CircularProgressIndicator(
                            color: Colors.white,
                            strokeWidth: 2,
                          ),
                        )
                      : Text(
                          'Payer ${Formatters.formatPrice(widget.amount.toDouble())}',
                        ),
                ),
              ),
              
              const SizedBox(height: 16),
              
              // Sécurité
              Row(
                mainAxisAlignment: MainAxisAlignment.center,
                children: [
                  Icon(
                    Icons.lock,
                    size: 16,
                    color: Colors.grey[600],
                  ),
                  const SizedBox(width: 4),
                  Text(
                    'Paiement 100% sécurisé par Fedapay',
                    style: TextStyle(
                      fontSize: 12,
                      color: Colors.grey[600],
                    ),
                  ),
                ],
              ),
            ],
          ),
        ),
      ),
    );
  }
  
  Widget _buildSummaryCard() {
    return Container(
      padding: const EdgeInsets.all(20),
      decoration: BoxDecoration(
        color: const Color(0xFFF5F5F5),
        borderRadius: BorderRadius.circular(12),
      ),
      child: Column(
        crossAxisAlignment: CrossAxisAlignment.start,
        children: [
          const Text(
            'Récapitulatif',
            style: TextStyle(
              fontSize: 16,
              fontWeight: FontWeight.w600,
            ),
          ),
          const SizedBox(height: 12),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              const Text('Abonnement'),
              Text(
                widget.planName,
                style: const TextStyle(fontWeight: FontWeight.w600),
              ),
            ],
          ),
          const SizedBox(height: 8),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              const Text('Durée'),
              const Text(
                '30 jours',
                style: TextStyle(fontWeight: FontWeight.w600),
              ),
            ],
          ),
          const Divider(height: 24),
          Row(
            mainAxisAlignment: MainAxisAlignment.spaceBetween,
            children: [
              const Text(
                'Total',
                style: TextStyle(
                  fontSize: 18,
                  fontWeight: FontWeight.w700,
                ),
              ),
              Text(
                Formatters.formatPrice(widget.amount.toDouble()),
                style: const TextStyle(
                  fontSize: 20,
                  fontWeight: FontWeight.w700,
                  color: Color(0xFFFF8C42),
                ),
              ),
            ],
          ),
        ],
      ),
    );
  }
  
  Widget _buildPaymentMethodCard({
    required IconData icon,
    required String title,
    required String subtitle,
    required String value,
  }) {
    final isSelected = _selectedMethod == value;
    
    return GestureDetector(
      onTap: () {
        setState(() => _selectedMethod = value);
        AppHaptics.light();
      },
      child: Container(
        padding: const EdgeInsets.all(16),
        decoration: BoxDecoration(
          color: Colors.white,
          borderRadius: BorderRadius.circular(12),
          border: Border.all(
            color: isSelected 
                ? const Color(0xFF0D3B66) 
                : const Color(0xFFE0E0E0),
            width: isSelected ? 2 : 1,
          ),
        ),
        child: Row(
          children: [
            Container(
              padding: const EdgeInsets.all(12),
              decoration: BoxDecoration(
                color: isSelected
                    ? const Color(0xFF0D3B66).withOpacity(0.1)
                    : Colors.grey[100],
                borderRadius: BorderRadius.circular(8),
              ),
              child: Icon(
                icon,
                color: isSelected 
                    ? const Color(0xFF0D3B66) 
                    : Colors.grey[600],
              ),
            ),
            const SizedBox(width: 16),
            Expanded(
              child: Column(
                crossAxisAlignment: CrossAxisAlignment.start,
                children: [
                  Text(
                    title,
                    style: const TextStyle(
                      fontSize: 16,
                      fontWeight: FontWeight.w600,
                    ),
                  ),
                  Text(
                    subtitle,
                    style: TextStyle(
                      fontSize: 14,
                      color: Colors.grey[600],
                    ),
                  ),
                ],
              ),
            ),
            if (isSelected)
              const Icon(
                Icons.check_circle,
                color: Color(0xFF0D3B66),
              ),
          ],
        ),
      ),
    );
  }
  
  Widget _buildProviderCard({
    required String logo,
    required String name,
    required String value,
  }) {
    final isSelected = _selectedProvider == value;
    
    return GestureDetector(
      onTap: () {
        setState(() => _selectedProvider = value);
        AppHaptics.light();
      },
      child: Container(
        padding: const EdgeInsets.all(16),
        decoration: BoxDecoration(
          color: Colors.white,
          borderRadius: BorderRadius.circular(12),
          border: Border.all(
            color: isSelected 
                ? const Color(0xFF0D3B66) 
                : const Color(0xFFE0E0E0),
            width: isSelected ? 2 : 1,
          ),
        ),
        child: Column(
          children: [
            Image.asset(
              logo,
              height: 40,
            ),
            const SizedBox(height: 8),
            Text(
              name,
              style: TextStyle(
                fontSize: 14,
                fontWeight: isSelected ? FontWeight.w600 : FontWeight.w400,
              ),
            ),
          ],
        ),
      ),
    );
  }
  
  Future<void> _handlePayment() async {
    // Validation
    if (_selectedMethod == 'mobile_money') {
      if (_phoneController.text.isEmpty) {
        _showError('Veuillez entrer votre numéro de téléphone');
        return;
      }
      
      final validation = Validators.validatePhone(_phoneController.text);
      if (validation != null) {
        _showError(validation);
        return;
      }
    }
    
    setState(() => _isLoading = true);
    AppHaptics.light();
    
    try {
      // Créer transaction Fedapay
      final result = await _fedapayService.createTransaction(
        planType: widget.planType,
        phoneNumber: _phoneController.text,
        paymentMethod: _selectedMethod,
      );
      
      if (result['success']) {
        // Ouvrir page checkout Fedapay
        await _fedapayService.openCheckoutPage(result['checkout_url']);
        
        // Montrer dialog d'attente
        _showWaitingDialog(result['transaction_id']);
      } else {
        throw Exception('Échec création transaction');
      }
    } catch (e) {
      _showError('Erreur lors du paiement: $e');
    } finally {
      setState(() => _isLoading = false);
    }
  }
  
  void _showWaitingDialog(String transactionId) {
    showDialog(
      context: context,
      barrierDismissible: false,
      builder: (context) => WillPopScope(
        onWillPop: () async => false,
        child: AlertDialog(
          title: const Text('Paiement en cours'),
          content: Column(
            mainAxisSize: MainAxisSize.min,
            children: const [
              CircularProgressIndicator(),
              SizedBox(height: 16),
              Text(
                'Confirmez le paiement sur votre téléphone',
                textAlign: TextAlign.center,
              ),
            ],
          ),
        ),
      ),
    );
    
    // Polling du statut
    _pollTransactionStatus(transactionId);
  }
  
  Future<void> _pollTransactionStatus(String transactionId) async {
    int attempts = 0;
    const maxAttempts = 60; // 5 minutes max (5s * 60)
    
    while (attempts < maxAttempts) {
      await Future.delayed(const Duration(seconds: 5));
      
      try {
        final status = await _fedapayService.checkTransactionStatus(transactionId);
        
        if (status == 'approved') {
          Navigator.pop(context); // Fermer dialog
          _navigateToSuccess();
          return;
        } else if (status == 'declined' || status == 'failed') {
          Navigator.pop(context);
          _showError('Paiement refusé ou échoué');
          return;
        }
      } catch (e) {
        print('Erreur polling: $e');
      }
      
      attempts++;
    }
    
    // Timeout
    Navigator.pop(context);
    _showError('Délai d\'attente dépassé. Vérifiez votre abonnement plus tard.');
  }
  
  void _navigateToSuccess() {
    Navigator.pushReplacement(
      context,
      MaterialPageRoute(
        builder: (context) => PaymentSuccessScreen(
          planName: widget.planName,
          amount: widget.amount,
        ),
      ),
    );
  }
  
  void _showError(String message) {
    AppHaptics.heavy();
    ScaffoldMessenger.of(context).showSnackBar(
      SnackBar(
        content: Text(message),
        backgroundColor: Colors.red,
      ),
    );
  }
}
```

---

## Webhooks et Callbacks

### Backend Webhook Handler (Cloud Function Firebase)

```javascript
// functions/src/api/fedapayWebhook.js
const functions = require('firebase-functions');
const admin = require('firebase-admin');
const crypto = require('crypto');

exports.fedapayWebhook = functions.https.onRequest(async (req, res) => {
  // Vérifier méthode POST
  if (req.method !== 'POST') {
    return res.status(405).send('Method Not Allowed');
  }
  
  try {
    // 1. Vérifier signature Fedapay pour sécurité
    const signature = req.headers['x-fedapay-signature'];
    const webhookSecret = functions.config().fedapay.webhook_secret;
    
    const hash = crypto
      .createHmac('sha256', webhookSecret)
      .update(JSON.stringify(req.body))
      .digest('hex');
    
    if (`sha256=${hash}` !== signature) {
      console.error('Signature webhook invalide');
      return res.status(401).send('Unauthorized');
    }
    
    // 2. Extraire données
    const {
      event,
      entity,
      transaction_id,
      status,
      amount,
      custom_metadata
    } = req.body;
    
    console.log('Webhook reçu:', event, status);
    
    // 3. Traiter selon événement
    if (event === 'transaction.approved' && status === 'approved') {
      const { userId, subscriptionType, planName } = custom_metadata;
      
      const db = admin.firestore();
      const batch = db.batch();
      
      // Calculer dates
      const now = new Date();
      const endDate = new Date(now.getTime() + 30 * 24 * 60 * 60 * 1000);
      
      // Mettre à jour utilisateur
      const userRef = db.collection('users').doc(userId);
      batch.update(userRef, {
        subscriptionType: subscriptionType,
        subscriptionStatus: 'active',
        subscriptionStartDate: admin.firestore.Timestamp.fromDate(now),
        subscriptionEndDate: admin.firestore.Timestamp.fromDate(endDate),
        updatedAt: admin.firestore.FieldValue.serverTimestamp()
      });
      
      // Créer subscription
      const subscriptionRef = db.collection('subscriptions').doc();
      batch.set(subscriptionRef, {
        subscriptionId: subscriptionRef.id,
        userId: userId,
        planType: subscriptionType,
        planName: planName,
        amount: amount,
        currency: 'FCFA',
        duration: 30,
        startDate: admin.firestore.Timestamp.fromDate(now),
        endDate: admin.firestore.Timestamp.fromDate(endDate),
        paymentMethod: entity.mode,
        paymentProvider: 'fedapay',
        transactionId: transaction_id,
        transactionReference: entity.reference,
        paymentStatus: 'completed',
        paidAt: admin.firestore.FieldValue.serverTimestamp(),
        status: 'active',
        createdAt: admin.firestore.FieldValue.serverTimestamp()
      });
      
      // Créer notification
      const notificationRef = db.collection('notifications').doc();
      batch.set(notificationRef, {
        userId: userId,
        title: 'Paiement réussi !',
        body: `Votre abonnement ${planName} est maintenant actif`,
        type: 'payment_success',
        data: {
          subscriptionId: subscriptionRef.id
        },
        isRead: false,
        createdAt: admin.firestore.FieldValue.serverTimestamp()
      });
      
      await batch.commit();
      
      // Envoyer notification push
      const userDoc = await db.collection('users').doc(userId).get();
      const userData = userDoc.data();
      
      if (userData.deviceTokens && userData.deviceTokens.length > 0) {
        await admin.messaging().sendMulticast({
          notification: {
            title: 'Paiement réussi !',
            body: `Votre abonnement ${planName} est actif`
          },
          data: {
            type: 'payment_success',
            subscriptionId: subscriptionRef.id
          },
          tokens: userData.deviceTokens
        });
      }
      
      console.log(`Abonnement créé pour user ${userId}`);
    } else if (event === 'transaction.declined' || event === 'transaction.failed') {
      // Log l'échec
      console.error(`Transaction ${transaction_id} échouée:`, status);
    }
    
    // Répondre 200 OK à Fedapay
    return res.status(200).json({ received: true });
    
  } catch (error) {
    console.error('Erreur webhook Fedapay:', error);
    return res.status(500).json({ error: 'Internal Server Error' });
  }
});
```

---

## Tests et Validation

### Numéros de Test Fedapay (Sandbox)

```
# TMoney Test
+228 90 00 00 01  → Transaction APPROVED
+228 90 00 00 02  → Transaction DECLINED
+228 90 00 00 03  → Transaction FAILED

# Flooz Test
+228 96 00 00 01  → Transaction APPROVED
+228 96 00 00 02  → Transaction DECLINED

# Carte Bancaire Test
Card Number: 4242 4242 4242 4242
Expiry: 12/25
CVV: 123
→ Transaction APPROVED

Card Number: 4000 0000 0000 0002
→ Transaction DECLINED
```

### Tests à Effectuer

```dart
// test/fedapay_service_test.dart
import 'package:flutter_test/flutter_test.dart';

void main() {
  group('FedapayService Tests', () {
    test('Créer transaction avec numéro valide', () async {
      final service = FedapayService();
      
      final result = await service.createTransaction(
        planType: 'professionnel',
        phoneNumber: '+22890000001', // Numéro test
      );
      
      expect(result['success'], true);
      expect(result['transaction_id'], isNotNull);
      expect(result['checkout_url'], isNotNull);
    });
    
    test('Rejeter transaction avec numéro invalide', () async {
      final service = FedapayService();
      
      expect(
        () => service.createTransaction(
          planType: 'professionnel',
          phoneNumber: '123', // Invalide
        ),
        throwsException,
      );
    });
  });
}
```

---

## Sécurité

### Règles Critiques

1. **JAMAIS stocker Secret Key côté client**
   - Toujours appeler API depuis backend sécurisé
   - Ne commit JAMAIS les clés dans Git

2. **Vérifier signature webhook**
   - Utiliser HMAC SHA256
   - Comparer avec signature Fedapay

3. **HTTPS obligatoire**
   - Webhook URL doit être en HTTPS
   - Certificat SSL valide requis

4. **Validation montants**
   - Toujours vérifier montant côté serveur
   - Ne jamais faire confiance au client

5. **Idempotence**
   - Gérer les webhooks dupliqués
   - Utiliser transaction_id comme clé unique

---

## Go-Live Production

### Checklist Avant Production

- [ ] Changer FedapayConfig.isProduction = true
- [ ] Remplacer clés sandbox par clés live
- [ ] Configurer webhook URL production
- [ ] Tester avec vrais numéros TMoney/Flooz
- [ ] Vérifier certificat SSL
- [ ] Activer logs serveur
- [ ] Configurer alertes échecs paiement
- [ ] Documenter procédure remboursement
- [ ] Former équipe support
- [ ] Préparer FAQ paiements

---

**FIN DU DOCUMENT FEDAPAY INTEGRATION**
