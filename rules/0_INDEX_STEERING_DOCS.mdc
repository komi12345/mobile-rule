# DealAuto - Index des Steering Docs

## 📚 Documents Créés pour Kiro

Voici la liste complète des **6 Steering Docs** créés pour guider l'IA (Claude Opus) dans Kiro lors du développement de DealAuto.

---

## 🗂️ Liste des Documents

### 1. **PROJECT_CONTEXT.md** (Contexte du Projet)
**Taille :** ~500 lignes  
**Usage :** Contexte global, vision, contraintes

**Contenu :**
- Vue d'ensemble DealAuto
- Marché togolais (3G, Mobile Money, culture)
- **3 options backend** (Firebase, Supabase, Contabo VPS) avec comparaison détaillée
- Stack technique pour chaque option
- Personas utilisateurs
- KPIs critiques
- Livrables MVP
- Success criteria

**Quand le lire :**
- Au début de chaque nouvelle feature
- Quand on doute sur une décision technique
- Pour comprendre le "pourquoi" du projet

---

### 2. **CODING_STANDARDS.md** (Standards de Code)
**Taille :** ~400 lignes  
**Usage :** Conventions, architecture, qualité code

**Contenu :**
- Structure dossiers Flutter (lib/, screens/, services/, etc.)
- Conventions de nommage (fichiers, classes, variables)
- Template de Screen obligatoire
- Gestion d'état (Provider)
- Gestion d'erreurs (try-catch obligatoire)
- Navigation et transitions
- Validation des données
- Formatage (prix FCFA, téléphone togolais)
- Packages autorisés/interdits

**Quand le lire :**
- Avant de créer tout nouveau fichier
- Pour vérifier les conventions
- Quand on écrit du code Flutter

---

### 3. **BACKEND_ARCHITECTURE.md** (Architecture Backend)
**Taille :** ~600 lignes  
**Usage :** Backend, BDD, APIs, authentification

**Contenu :**
- **Firebase** : Collections Firestore complètes, règles sécurité, Cloud Functions, code Flutter
- **Supabase** : Tables PostgreSQL, Row Level Security, Edge Functions, code Flutter
- **Contabo VPS** : Setup Ubuntu, PostgreSQL, API REST custom, code Flutter
- Comparaison approfondie des 3 options
- Coûts détaillés pour chaque solution
- Guide de migration

**Quand le lire :**
- Avant toute opération backend
- Pour créer/modifier des collections/tables
- Pour implémenter l'authentification
- Pour configurer les règles de sécurité

---

### 4. **UI_UX_FIGMA_GUIDELINES.md** (Guidelines UI/UX)
**Taille :** ~700 lignes  
**Usage :** Design, Figma, composants UI

**Contenu :**
- **Analyse systématique des captures Figma** (checklist complète)
- Extraction précise :
  - Formulaires (border-radius, padding, couleurs états)
  - Boutons (3 types avec tous les états)
  - Cards (shadows, spacing, badges)
  - Espacement (système 8dp)
  - Typographie (tous les text styles)
  - Couleurs (palette complète)
- Design System Flutter (ThemeData complet)
- Navigation et transitions (slide, fade, scale)
- Animations (haptic feedback, skeleton loaders)
- Checklist qualité UI

**Quand le lire :**
- Avant de créer TOUT composant UI
- Quand on reçoit une capture Figma
- Pour extraire les specs (border-radius, padding, colors)
- Pour garantir le pixel-perfect

---

### 5. **FEDAPAY_INTEGRATION.md** (Intégration Paiements)
**Taille :** ~800 lignes  
**Usage :** Paiements Mobile Money, abonnements

**Contenu :**
- Introduction Fedapay (TMoney, Flooz, Cartes)
- Configuration compte étape par étape
- Obtention clés API (sandbox + live)
- Schéma complet du flow paiement (16 étapes)
- **Code Flutter complet** :
  - Service Fedapay avec toutes les méthodes
  - Screen sélection plans (3 cartes abonnement)
  - Screen paiement (sélection méthode, opérateur, validation)
  - Polling du statut transaction
- **Webhooks backend** :
  - Cloud Function Firebase complète
  - Vérification signature HMAC
  - Activation abonnement automatique
- Tests (numéros test sandbox)
- Sécurité (5 règles critiques)
- Checklist go-live production

**Quand le lire :**
- Pour implémenter les abonnements
- Pour intégrer Fedapay
- Pour gérer les webhooks
- Pour tester les paiements

---

### 6. **DEVELOPMENT_RULES.md** (Règles de Développement)
**Taille :** ~900 lignes  
**Usage :** Règles absolues, comportements, qualité

**Contenu :**
- **Principe fondamental** : Ne jamais deviner, toujours être sûr à 100%
- **10 Interdictions absolues** :
  1. Halluciner des APIs/packages
  2. Code non testé/non compilable
  3. Réponses vagues
  4. Ignorer les Steering Docs
  5. Copier-coller sans adapter
  6. Hardcoder des valeurs
  7. Oublier gestion d'erreurs
  8. Code non commenté
  9. Ignorer la performance
  10. Ne pas tester son code
- **10 Obligations** :
  1. Lire Steering Docs avant toute action
  2. Demander clarification si ambigu
  3. Respecter l'architecture
  4. Fournir code complet et fonctionnel
  5. Expliquer décisions techniques
  6. Vérifier compatibilité versions
  7. Localisation en français
  8. Optimisation pour 3G
  9. Dispose des controllers
  10. Accessibilité mobile
- **Processus de travail strict** (6 étapes)
- **Standards qualité non-négociables** (checklist 6 critères)
- **Gestion erreurs et debugging**
- **Communication avec développeur** (templates réponse)
- **Checklist universelle** (15 points avant chaque réponse)
- **Cas d'usage spécifiques** (créer screen, intégrer API, débugger)
- **Exemples concrets** (bon/mauvais code)
- **10 Commandements du développeur Kiro**

**Quand le lire :**
- **TOUJOURS** avant de commencer à coder
- Au début de chaque session Kiro
- Quand on doute sur une approche
- Pour vérifier la qualité de son travail

---

## 🎯 Comment Utiliser ces Documents dans Kiro

### Étape 1 : Upload dans Kiro

1. Ouvrir **Kiro Code**
2. Aller dans **"Agent Steering"** (panneau latéral)
3. Cliquer **"Add Steering Doc"**
4. Pour chaque document (1 à 6) :
   - Upload le fichier .md
   - Donner un nom clair (ex: "PROJECT_CONTEXT")
   - Cocher "Active"

### Étape 2 : Ordre de Lecture Recommandé

Pour **toute nouvelle feature**, Kiro devrait lire dans cet ordre :

```
1️⃣ DEVELOPMENT_RULES.md (OBLIGATOIRE - règles de base)
2️⃣ PROJECT_CONTEXT.md (comprendre le contexte)
3️⃣ CODING_STANDARDS.md (conventions)
4️⃣ [Document spécifique selon la tâche]
   - UI → UI_UX_FIGMA_GUIDELINES.md
   - Backend → BACKEND_ARCHITECTURE.md
   - Paiement → FEDAPAY_INTEGRATION.md
```

### Étape 3 : Prompt Type à Utiliser

**Exemple de prompt efficace :**

```
@DEVELOPMENT_RULES @PROJECT_CONTEXT @UI_UX_FIGMA_GUIDELINES

Crée l'écran de liste des véhicules avec :
- Grid de 2 colonnes
- Cards selon specs Figma (border-radius 16dp)
- Lazy loading avec pagination
- Pull-to-refresh
- Skeleton loaders pendant chargement

Respecte STRICTEMENT les règles de DEVELOPMENT_RULES 
et les specs de UI_UX_FIGMA_GUIDELINES.
```

---

## 📊 Statistiques Globales

**Total documents :** 6  
**Total lignes :** ~3,900 lignes  
**Total pages (estimé) :** ~95 pages  
**Temps lecture complet :** ~3-4 heures  
**Temps création :** ~8 heures  

**Couverture :**
- ✅ Contexte et vision
- ✅ Standards de code Flutter
- ✅ 3 options backend détaillées
- ✅ UI/UX et analyse Figma
- ✅ Intégration paiements
- ✅ Règles anti-hallucination

---

## 🎓 Formation Recommandée pour l'Équipe

### Jour 1 : Fondations (2h)
- Lecture PROJECT_CONTEXT.md (30 min)
- Lecture DEVELOPMENT_RULES.md (1h)
- Discussion choix backend (30 min)

### Jour 2 : Code & UI (2h)
- Lecture CODING_STANDARDS.md (45 min)
- Lecture UI_UX_FIGMA_GUIDELINES.md (1h15)

### Jour 3 : Backend & Paiements (2h)
- Lecture BACKEND_ARCHITECTURE.md (1h)
- Lecture FEDAPAY_INTEGRATION.md (1h)

### Jour 4 : Pratique (4h)
- Créer 1 screen simple avec Kiro
- Vérifier conformité avec les docs
- Débugger si nécessaire
- Itérer jusqu'à qualité parfaite

---

## 🔄 Maintenance des Documents

### Quand Mettre à Jour ?

- ✅ Changement de stack technique
- ✅ Nouvelles features majeures
- ✅ Nouvelles règles/contraintes
- ✅ Feedback d'utilisation (problèmes récurrents)
- ✅ Mise à jour Flutter/packages

### Comment Mettre à Jour ?

1. Modifier le fichier .md concerné
2. Incrémenter le numéro de version
3. Ajouter date de mise à jour
4. Uploader la nouvelle version dans Kiro
5. Notifier l'équipe des changements

---

## ✅ Checklist de Démarrage Projet

Avant de commencer à coder avec Kiro :

- [ ] Les 6 Steering Docs sont uploadés dans Kiro
- [ ] Tous les docs sont marqués "Active"
- [ ] L'équipe a lu au moins DEVELOPMENT_RULES.md
- [ ] Le choix backend est décidé (Firebase/Supabase/VPS)
- [ ] Les clés API sont configurées (Fedapay, Firebase, etc.)
- [ ] Le repository Git est créé
- [ ] Le projet Flutter est initialisé
- [ ] Les maquettes Figma sont disponibles
- [ ] Les numéros de test sont notés (TMoney sandbox)

---

## 🚀 Promesse de Qualité

Avec ces 6 Steering Docs, **Kiro (Claude Opus) est capable de :**

✅ Générer du code Flutter **production-ready**  
✅ Respecter **pixel-perfect** les designs Figma  
✅ Implémenter backend (Firebase/Supabase/VPS) **sans halluciner**  
✅ Intégrer Fedapay **correctement et sécurisé**  
✅ Suivre **toutes les best practices** Flutter  
✅ Gérer les erreurs **de façon professionnelle**  
✅ Optimiser pour **connexions 3G togolaises**  
✅ Communiquer **clairement et honnêtement**  

**Résultat attendu :** Code de qualité senior, prêt pour production, sans bugs majeurs.

---

## 📞 Support et Questions

Si vous rencontrez des problèmes avec les Steering Docs ou avec Kiro :

1. Vérifier que les docs sont bien activés dans Kiro
2. Vérifier que le prompt mentionne explicitement les docs (@nom_doc)
3. Relire DEVELOPMENT_RULES.md pour les cas spécifiques
4. Consulter les exemples dans chaque document

---

**Version :** 1.0  
**Date de création :** 18 Décembre 2024  
**Prochaine révision :** Après feedback équipe ou changement majeur  
**Statut :** ✅ Complet et prêt à l'emploi

---

**Bonne chance avec DealAuto ! 🚗🇹🇬**
