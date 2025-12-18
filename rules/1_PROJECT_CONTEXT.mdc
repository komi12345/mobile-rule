# DealAuto - Contexte du Projet

## Vue d'ensemble
DealAuto est une marketplace automobile mobile pour le marché togolais, développée avec Flutter. L'application connecte vendeurs et acheteurs de véhicules au Togo avec une interface moderne et fluide basée sur des designs Figma professionnels.

## Objectif Principal
Créer un MVP fonctionnel de haute qualité permettant :
- Publication d'annonces de véhicules avec photos (filigrane automatique)
- Recherche et découverte de véhicules avec filtres avancés
- Système d'abonnements payants (Particulier, Professionnel, Concessionnaire)
- Authentification par numéro de téléphone (SMS OTP)
- Validation administrative des annonces
- Paiements via Fedapay (Mobile Money : TMoney, Flooz)
- Navigation fluide et intuitive entre tous les écrans
- Interface conforme aux maquettes Figma fournies

## Marché Cible
- **Géographie :** Togo (principalement Lomé au lancement)
- **Utilisateurs :** Particuliers, professionnels, concessionnaires automobiles
- **Contraintes :** Connexions internet souvent lentes (3G), téléphones Android variés (budget à haut de gamme)
- **Langue :** Français uniquement
- **Devise :** Franc CFA (FCFA)
- **Comportement utilisateur :** Habitués aux interfaces simples et rapides comme WhatsApp

## Valeur Ajoutée
- Première marketplace mobile-first spécialisée automobile au Togo
- Intégration native Mobile Money (TMoney, Flooz)
- Filigrane automatique sur photos pour protection
- Vérification des vendeurs professionnels
- Interface moderne inspirée des meilleures apps du marché
- Navigation fluide et rapide même sur 3G

## Stack Technique - 3 OPTIONS BACKEND

### ⚠️ DÉCISION BACKEND EN ATTENTE

Le projet DealAuto peut être développé avec **3 solutions backend différentes**. Chacune a ses avantages et contraintes. Les Steering Docs couvrent les 3 options pour permettre une transition rapide selon le choix final.

---

### OPTION A : Firebase (Google)

**Stack complète :**
- Frontend : Flutter (Dart)
- Authentication : Firebase Authentication
- Database : Cloud Firestore (NoSQL)
- Storage : Firebase Storage
- Functions : Cloud Functions (Node.js)
- Notifications : Firebase Cloud Messaging (FCM)
- Analytics : Firebase Analytics
- Monitoring : Firebase Crashlytics
- Paiements : Fedapay (intégration externe)

**Avantages :**
- ✅ Setup ultra-rapide (MVP en 7 jours possible)
- ✅ Pas de gestion serveur (serverless)
- ✅ Scaling automatique
- ✅ Gratuit jusqu'à 50K utilisateurs/jour
- ✅ SDK Flutter natif et mature
- ✅ Temps réel natif (Firestore)
- ✅ Sécurité gérée par Google
- ✅ Backup automatique
- ✅ Documentation excellente

**Inconvénients :**
- ❌ Coûts peuvent exploser après 100K+ utilisateurs
- ❌ Vendor lock-in (difficile de migrer)
- ❌ Requêtes complexes limitées
- ❌ Pas de SQL natif
- ❌ Moins de contrôle sur l'infrastructure

**Coûts estimés (après gratuit) :**
- 0-10K utilisateurs actifs/mois : **0-20€/mois**
- 10K-50K utilisateurs : **20-150€/mois**
- 50K-200K utilisateurs : **150-800€/mois**

**Recommandé si :**
- Vous voulez lancer rapidement
- Budget limité au départ
- Pas d'expertise DevOps
- Besoin de scaling automatique

---

### OPTION B : Supabase (Alternative open-source à Firebase)

**Stack complète :**
- Frontend : Flutter (Dart)
- Authentication : Supabase Auth
- Database : PostgreSQL (SQL relationnel)
- Storage : Supabase Storage (S3-compatible)
- Functions : Supabase Edge Functions (Deno)
- Realtime : Supabase Realtime (WebSockets)
- Notifications : Firebase Cloud Messaging ou OneSignal
- Analytics : Supabase Analytics + Google Analytics
- Monitoring : Sentry ou custom
- Paiements : Fedapay (intégration externe)

**Avantages :**
- ✅ Open-source (peut auto-héberger plus tard)
- ✅ PostgreSQL = SQL puissant et standard
- ✅ Gratuit jusqu'à 500MB DB + 1GB storage
- ✅ Coûts prévisibles et transparents
- ✅ Row Level Security natif (sécurité fine)
- ✅ Dashboard admin intégré
- ✅ Pas de vendor lock-in (migration facile)
- ✅ SDK Flutter officiel et actif
- ✅ Moins cher que Firebase à long terme

**Inconvénients :**
- ❌ Plus jeune que Firebase (moins mature)
- ❌ Communauté plus petite
- ❌ Quelques features en beta
- ❌ Setup légèrement plus complexe
- ❌ Moins de services intégrés (analytics, crash)

**Coûts estimés :**
- 0-10K utilisateurs : **0€/mois** (tier gratuit)
- 10K-50K utilisateurs : **25€/mois** (Pro plan)
- 50K-200K utilisateurs : **25-100€/mois** (Pro + addons)

**Recommandé si :**
- Vous voulez du SQL et des requêtes complexes
- Préférez l'open-source
- Budget contrôlé à long terme
- Voulez éviter le vendor lock-in

---

### OPTION C : Contabo Cloud VPS 10 (Serveur dédié virtuel)

**Stack complète :**
- Frontend : Flutter (Dart)
- Backend : API REST custom (Node.js + Express OU Laravel PHP)
- Authentication : JWT + bcrypt (custom)
- Database : PostgreSQL OU MySQL (auto-hébergé)
- Storage : MinIO (S3-compatible) OU dossier serveur
- Functions : Routes API custom
- Realtime : Socket.io ou Pusher
- Notifications : Firebase Cloud Messaging (FCM)
- Analytics : Matomo (auto-hébergé) ou Google Analytics
- Monitoring : Uptime Kuma + Grafana
- Paiements : Fedapay (intégration directe)
- Serveur : Ubuntu 22.04 LTS
- Reverse Proxy : Nginx
- SSL : Let's Encrypt (gratuit)
- Backup : Scripts cron + Rsync

**Avantages :**
- ✅ Contrôle total de l'infrastructure
- ✅ Coûts ultra-prévisibles (4.99€-9.99€/mois fixe)
- ✅ Pas de surprises de facturation
- ✅ Performance garantie (vCPU dédiés)
- ✅ Stockage illimité (dans les limites du VPS)
- ✅ Pas de vendor lock-in
- ✅ Peut héberger d'autres services
- ✅ Apprentissage DevOps complet

**Inconvénients :**
- ❌ Setup complexe (3-7 jours de configuration)
- ❌ Maintenance serveur nécessaire
- ❌ Sécurité à gérer manuellement
- ❌ Backup à configurer soi-même
- ❌ Pas de scaling automatique
- ❌ Requiert compétences DevOps
- ❌ Single point of failure (1 seul serveur)
- ❌ Vous gérez les mises à jour

**Coûts estimés (Contabo VPS 10) :**
- VPS 10 : **9.99€/mois** (12 vCPU, 60GB RAM, 1.6TB SSD)
- Domaine : **10€/an**
- **TOTAL : ~10€/mois fixe** pour 0-500K utilisateurs

**Recommandé si :**
- Vous avez des compétences DevOps ou êtes prêt à apprendre
- Budget très limité à long terme
- Voulez contrôle total
- Pas peur de gérer un serveur
- Prévoyez d'autres services sur le même serveur

---

### 🎯 RECOMMANDATION SELON PROFIL

#### Profil Débutant + Besoin de rapidité
➡️ **Firebase** (Option A)
- Lancez en 1 semaine
- Changez plus tard si nécessaire

#### Profil Intermédiaire + Budget contrôlé
➡️ **Supabase** (Option B)
- Meilleur compromis qualité/prix
- SQL pour requêtes complexes

#### Profil Avancé + Budget ultra-serré
➡️ **Contabo VPS** (Option C)
- Maximum de contrôle
- Coûts minimaux long terme

---

## Contraintes Critiques (Communes aux 3 options)

### Techniques
- JAMAIS utiliser localStorage ou sessionStorage (non supporté dans Flutter)
- Toujours utiliser state management approprié (Provider/Riverpod/Bloc)
- Optimisation images obligatoire (compression + filigrane)
- Support Android 8.0+ minimum
- Application doit fonctionner sur connexions 3G lentes (< 1Mbps)
- Taille APK < 50MB (sans images utilisateur)

### UI/UX
- **Respect strict des maquettes Figma** fournies par le client
- Analyse systématique des screenshots Figma pour :
  - Détection du border-radius des composants (arrondi, carré, circulaire)
  - Style des formulaires (padding, spacing, alignement)
  - Style des boutons (hauteur, largeur, ombres, états)
  - Palette de couleurs exacte
  - Typographie et tailles de texte
- Navigation entre écrans doit être **fluide et rapide** (< 300ms)
- Transitions animées cohérentes partout
- Aucun écran blanc/vide pendant chargement (skeleton screens)
- Feedback visuel immédiat sur chaque action utilisateur

### Qualité de Code
- Code production-ready dès le départ
- Gestion d'erreurs complète et utilisateur-friendly
- Logs structurés pour debugging
- Tests unitaires sur logique critique
- Validation de toutes les entrées utilisateur
- Pas de données hardcodées (utiliser constantes/config)

---

## Personas Principaux

### 1. Vendeur Particulier (Kofi)
- **Âge :** 32 ans
- **Profession :** Commerçant
- **Besoin :** Vendre sa voiture rapidement
- **Compétence tech :** Moyenne
- **Priorités :** Simplicité, rapidité, bon prix

### 2. Vendeur Professionnel (Ama)
- **Âge :** 45 ans
- **Profession :** Garagiste
- **Besoin :** Gérer 15-30 véhicules en ligne
- **Compétence tech :** Moyenne
- **Priorités :** Visibilité, crédibilité, outils de gestion

### 3. Concessionnaire (TransMotors)
- **Type :** Entreprise 50+ employés
- **Besoin :** Catalogue en ligne, statistiques
- **Compétence tech :** Élevée (équipe marketing)
- **Priorités :** Branding, analytics, lead generation

### 4. Acheteur (Edem)
- **Âge :** 28 ans
- **Profession :** Salarié
- **Besoin :** Trouver une voiture fiable < 5M FCFA
- **Compétence tech :** Élevée
- **Priorités :** Bon rapport qualité/prix, confiance, comparaison facile

---

## KPIs Critiques

### Performance
- Temps de chargement app : **< 2 secondes**
- Temps d'affichage liste véhicules : **< 1 seconde**
- Publication d'annonce : **< 10 minutes** (photos incluses)
- Hot reload développement : **< 2 secondes**
- Taille APK release : **< 50MB**

### Business
- Taux de conversion inscription → publication : **> 40%**
- Taux de rétention J7 : **> 60%**
- Temps moyen par session : **> 5 minutes**
- Taux de paiement abonnement : **> 15%**

### Qualité
- Crash rate : **< 0.5%**
- Note Play Store : **> 4.5/5**
- Temps de résolution bugs critiques : **< 24h**

---

## Principes de Design (Basés sur Figma)

### Analyse Systématique des Maquettes
Lorsqu'une image Figma est fournie, Claude doit systématiquement analyser :

1. **Formulaires :**
   - Border radius des champs input (exemple : 8px, 12px, 16px ?)
   - Padding interne (vertical et horizontal)
   - Hauteur des champs (exemple : 48dp, 56dp ?)
   - Style des labels (au-dessus, flottant, inline ?)
   - Couleur des bordures (normal, focus, erreur)
   - Présence d'icônes (gauche, droite, les deux ?)

2. **Boutons :**
   - Border radius (rectangulaire, arrondi, pill-shape ?)
   - Hauteur et padding
   - Style de texte (taille, poids, casse)
   - Ombres ou élévation
   - États (normal, hover, pressed, disabled)
   - Icônes présentes ?

3. **Cards/Containers :**
   - Border radius des coins
   - Ombres et élévation
   - Padding interne
   - Espacement entre éléments

4. **Navigation :**
   - Type de transition entre écrans
   - Présence d'animations
   - Durée des transitions
   - Direction (slide, fade, scale ?)

5. **Palette de Couleurs :**
   - Couleur primaire (hex code)
   - Couleur secondaire
   - Couleurs d'état (succès, erreur, warning, info)
   - Couleurs de texte (primary, secondary, disabled)
   - Couleurs de background

### Règles de Cohérence UI
- **Si un bouton est arrondi à 8px dans un écran, TOUS les boutons primaires doivent être à 8px**
- **Si les inputs ont 16dp de padding vertical, TOUS doivent avoir 16dp**
- **Les transitions doivent toutes avoir la même durée (exemple : 300ms)**
- **Même style de shadow partout pour les cards**
- **Spacing cohérent (système 8dp : 8, 16, 24, 32, 40, 48)**

---

## Exigences de Navigation Fluide

### Transitions Obligatoires
- **Entre écrans principaux :** Slide horizontal (300ms, easeInOut)
- **Modals/Bottom sheets :** Slide vertical + fade (250ms)
- **Retour arrière :** Slide inverse (300ms)
- **Navigation bottom bar :** Fade + scale subtle (200ms)

### Performance
- Pré-chargement des écrans suivants probables
- Lazy loading des images
- Cache intelligent des données
- Skeleton screens pendant chargement
- Animations 60 FPS minimum (pas de lag)

### Feedback Utilisateur
- Ripple effect sur tous les éléments cliquables
- Haptic feedback sur actions importantes
- Changement visuel immédiat (< 50ms)
- Messages de confirmation clairs
- Indicateurs de chargement contextuels

---

## Philosophie de Développement

### Pour ce Projet
- **Design First :** Le code doit correspondre pixel-perfect aux maquettes Figma
- **Quality Over Speed :** Mieux prendre 1 jour de plus et avoir du code propre
- **User Experience :** Chaque interaction doit être fluide et agréable
- **Mobile First :** Penser mobile avant tout (thumb-friendly, gestures)
- **Local Context :** Adapter à la réalité togolaise (3G, Android variés, français)

---

## Contexte Togolais Important

### Connectivité
- Connexion moyenne : **3G (0.5-2 Mbps)**
- WiFi rare en mobilité
- Coût data : élevé (500 FCFA = ~50MB)
- Solution : Optimisation maximale, mode offline partiel

### Devices
- Majorité : Android mid-range (2-4GB RAM)
- Prix moyen téléphone : 50K-150K FCFA
- Écrans : 5.5"-6.5" majoritairement
- Solution : App légère et performante

### Paiements
- Mobile Money ultra-dominant : **TMoney (95%), Flooz (80%)**
- Cartes bancaires rares (< 5%)
- Cash à la livraison apprécié
- Solution : Intégration Fedapay (TMoney + Flooz)

### Langue et Culture
- Français seule langue officielle
- Utilisation WhatsApp massive
- Confiance difficile à établir en ligne
- Photos et descriptions détaillées essentielles
- Solution : Interface française claire, vérification vendeurs, photos multiples

---

## Livrables MVP (Ordre de Priorité)

### Phase 1 : Core (Semaine 1-2)
1. Authentification téléphone + OTP
2. Écran liste véhicules (avec recherche basique)
3. Écran détail véhicule
4. Écran ajout véhicule (formulaire complet)
5. Upload photos avec compression et filigrane

### Phase 2 : Business (Semaine 3)
6. Système d'abonnements (3 tiers)
7. Intégration paiement Fedapay
8. Validation admin des annonces
9. Tableau de bord vendeur

### Phase 3 : Polish (Semaine 4)
10. Recherche avancée + filtres
11. Favoris/Wishlist
12. Notifications push
13. Chat vendeur-acheteur (optionnel MVP)

---

## Success Criteria

### MVP sera considéré réussi si :
- ✅ 100% des maquettes Figma respectées (pixel-perfect sur 1 device test)
- ✅ Navigation fluide sans lag visible
- ✅ App fonctionne correctement sur 3G lente
- ✅ Aucun crash sur fonctionnalités principales
- ✅ Publication annonce possible en < 10 minutes
- ✅ Paiement Fedapay fonctionnel en production
- ✅ 10 utilisateurs beta peuvent utiliser sans aide
- ✅ Code propre et maintenable (prêt pour évolution)

---

**Ce document doit être utilisé comme référence principale pour toutes les décisions techniques et design du projet DealAuto.**
