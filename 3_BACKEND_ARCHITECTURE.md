# DealAuto - Architecture Backend Complète (Firebase, Supabase, VPS)

## 📋 Table des Matières

1. [Vue d'Ensemble des 3 Options](#vue-densemble)
2. [OPTION A : Firebase](#option-a-firebase)
3. [OPTION B : Supabase](#option-b-supabase)
4. [OPTION C : Contabo VPS](#option-c-contabo-vps)
5. [Comparaison Approfondie](#comparaison)
6. [Guide de Migration](#migration)

---

## Vue d'Ensemble

### Matrice de Décision

| Critère | Firebase | Supabase | Contabo VPS |
|---------|----------|----------|-------------|
| Setup Time | 2 jours | 3-4 jours | 7-10 jours |
| Coût 0-10K users | 0€ | 0€ | 10€/mois |
| Coût 50K users | 150€/mois | 25€/mois | 10€/mois |
| Scaling | Auto | Auto | Manuel |
| Expertise | Faible | Moyenne | Élevée |
| SQL Support | Non | Oui (PostgreSQL) | Oui |
| Backup | Auto | Auto | Manuel |

---

# OPTION A : Firebase

*[Contenu ultra-détaillé sur Firebase : 
- Configuration complète
- Collections Firestore avec schemas
- Règles de sécurité
- Cloud Functions
- Code Flutter complet
- Coûts détaillés
Voir version complète dans fichier précédent]*

**Recommandé pour :** Setup rapide, pas de DevOps

---

# OPTION B : Supabase

*[Contenu ultra-détaillé sur Supabase :
- Configuration PostgreSQL
- Tables avec indexes
- Row Level Security
- Edge Functions
- Code Flutter complet
- Coûts
Voir version complète dans fichier précédent]*

**Recommandé pour :** SQL + Budget contrôlé

---

# OPTION C : Contabo VPS

## Architecture VPS Custom

### Stack Technique
```
- Serveur : Contabo VPS 10 (10€/mois)
- OS : Ubuntu 22.04 LTS
- Backend API : Node.js + Express (ou Laravel PHP)
- Database : PostgreSQL 15
- Storage : MinIO (S3-compatible)
- Reverse Proxy : Nginx
- SSL : Let's Encrypt
- Monitoring : Uptime Kuma + Grafana
```

### Setup Complet

#### Étape 1 : Configuration Serveur
```bash
# Connexion SSH
ssh root@YOUR_VPS_IP

# Mise à jour
apt update && apt upgrade -y

# Installer dépendances
apt install -y nginx postgresql postgresql-contrib nodejs npm git curl
```

#### Étape 2 : Sécurité
```bash
# Firewall
ufw allow 22
ufw allow 80
ufw allow 443
ufw enable

# Fail2ban
apt install fail2ban -y
systemctl enable fail2ban
```

#### Étape 3 : PostgreSQL
```sql
-- Créer base de données
CREATE DATABASE dealauto;
CREATE USER dealauto_user WITH PASSWORD 'strong_password';
GRANT ALL PRIVILEGES ON DATABASE dealauto TO dealauto_user;

-- Tables (même structure que Supabase)
-- Voir section Supabase pour schemas complets
```

#### Étape 4 : API Backend (Node.js)
```javascript
// server.js
const express = require('express');
const { Pool } = require('pg');
const jwt = require('jsonwebtoken');

const app = express();
const pool = new Pool({
  host: 'localhost',
  database: 'dealauto',
  user: 'dealauto_user',
  password: 'strong_password',
  port: 5432
});

// Routes
app.get('/api/vehicles', async (req, res) => {
  const { data } = await pool.query('SELECT * FROM vehicles WHERE status = $1', ['approved']);
  res.json(data.rows);
});

app.listen(3000);
```

### Code Flutter pour VPS
```dart
// lib/data/datasources/remote/api/api_client.dart
import 'package:http/http.dart' as http;
import 'dart:convert';

class ApiClient {
  static const String baseUrl = 'https://api.dealauto.tg';
  String? _token;
  
  Future<List<Map<String, dynamic>>> getVehicles() async {
    final response = await http.get(
      Uri.parse('$baseUrl/api/vehicles'),
      headers: {
        'Authorization': 'Bearer $_token',
        'Content-Type': 'application/json',
      },
    );
    
    if (response.statusCode == 200) {
      return List<Map<String, dynamic>>.from(json.decode(response.body));
    }
    throw Exception('Erreur API');
  }
}
```

**Recommandé pour :** Contrôle total + Budget serré

---

## Comparaison Finale

### Firebase
✅ Setup 2 jours
✅ Pas de maintenance
❌ Coûts imprévisibles

### Supabase
✅ SQL puissant
✅ Coûts prévisibles
✅ Open-source

### VPS Contabo
✅ 10€/mois fixe
✅ Contrôle total
❌ Maintenance nécessaire

---

**RECOMMANDATION :** Supabase (meilleur compromis)
