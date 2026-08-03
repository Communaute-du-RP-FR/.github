# Communauté du RP FR

> Écosystème complet de bots Discord et outils pour le serveur discord Communauté du RP FR

Bienvenue dans l'organisation GitHub de la Communauté du RP FR ! Nous développons et maintenons plusieurs bots Discord et outils pour faciliter la gestion du serveur.

## Nos Projets

### Bots Discord

#### [Liste-du-RP-FR](https://github.com/Communaute-du-RP-FR/Liste-du-RP-FR)
Bot principal de gestion de la liste des serveurs RP.

**Fonctionnalités :**
- Inscription et gestion des serveurs RP
- Recherche de serveurs par critères
- Gestion des membres et joueurs
- Système de boost
- Statistiques et classements

**Technologies :** Python, discord.py

---

#### [Moderator](https://github.com/Communaute-du-RP-FR/Moderator)
Bot de modération avancée avec système de vérification et gestion de tickets.

**Fonctionnalités :**
- Outils de modération complets
- Système de tickets
- Vérification automatisée des utilisateurs
- Mini-jeux
- Gestion de listes et configurations
- Tâches automatiques (loops)

**Technologies :** Python, discord.py

---

#### [Modmail](https://github.com/Communaute-du-RP-FR/Modmail)
Système de messagerie privée entre utilisateurs et équipe de modération.

**Fonctionnalités :**
- Conversations privées user ↔️ Administration
- Historique et archivage
- Notifications

**Technologies :** Python, discord.py
**Documentation :** Sphinx

---

#### [Restore](https://github.com/Communaute-du-RP-FR/Restore)
Bot de sauvegarde et restauration de serveurs Discord.

**Fonctionnalités :**
- Restauration partielle de la base de données
- Restauration de configurations
- Base de données SQL

**Technologies :** Python, discord.py, SQL
**Documentation :** Sphinx

---

### Utilitaires

#### [utils](https://github.com/Communaute-du-RP-FR/utils)
Bibliothèque partagée de fonctions utilitaires pour tous les bots.

**Contenu :**
- Client Discord personnalisé
- Gestionnaire de base de données
- Vues génériques et composants UI
- Utilitaires de texte et émotes
- Configuration centralisée

**Technologies :** Python

---

#### [alerts](https://github.com/Communaute-du-RP-FR/alerts)
CLI Python pour envoyer des notifications de statut de service sur Discord.

**Fonctionnalités :**
- Notifications formatées sur Discord via webhooks
- Horodatage automatique
- Mention @everyone pour notifier les Admins
- Interface propre avec composants Discord natifs

**Technologies :** Python, discord.py, requests
**Documentation :** Sphinx

**Installation :**
```bash
uv pip install git+https://github.com/Communaute-du-RP-FR/alerts.git
```

**Usage :**
```bash
export DISCORD_WEBHOOK_URL="https://discord.com/api/webhooks/..."
alerts my_service STARTED
```

---

#### [backups](https://github.com/Communaute-du-RP-FR/backups)
Système de sauvegarde et restauration PostgreSQL avec chiffrement et stockage distant.

**Fonctionnalités :**
- Sauvegardes chiffrées avec [age encryption](https://github.com/FiloSottile/age)
- Dumps PostgreSQL compressés (format custom, niveau 9)
- Stockage distant via SCP
- Nettoyage automatique (conserve les 30 dernières sauvegardes)
- Restauration sécurisée avec options clean et if-exists

**Technologies :** Bash, PostgreSQL, age encryption

**Usage :**
```bash
# Créer une sauvegarde
./scripts/backup.sh

# Restaurer depuis une sauvegarde
./scripts/restore.sh backup-file.dump.age
```

---

## Installation

### Prérequis Généraux

- Python 3.10+ et [uv](https://docs.astral.sh/uv/) pour la gestion des environnements/dépendances des bots
- Node.js 18+ (pour le site web)
- Git
- PostgreSQL et SQLite (selon le bot)

### Installation des Bots Discord

#### Configuration Standard (Liste-du-RP-FR, Moderator)

```bash
# 1. Cloner le repository
git clone https://github.com/Communaute-du-RP-FR/[NOM-DU-BOT].git
cd [NOM-DU-BOT]

# 2. Créer l'environnement avec uv
uv venv .venv

# 3. Installer les dépendances
uv pip install -r requirements.txt

# 4. Configurer le service systemd (voir section "Exécution comme service")
# Les variables d'environnement sont chargées via EnvironmentFile dans le service
```

#### Configuration avec PyProject (Modmail, Restore)

```bash
# 1. Cloner le repository
git clone https://github.com/Communaute-du-RP-FR/[NOM-DU-BOT].git
cd [NOM-DU-BOT]

# 2. Créer l'environnement avec uv
uv venv .venv

# 3. Installer les dépendances depuis pyproject
uv sync  # utilise pyproject.toml et uv.lock si présent
# OU (fallback) installer depuis requirements.txt
uv pip install -r requirements.txt

# 4. Configurer le service systemd (voir section "Exécution comme service")
# Les variables d'environnement sont chargées via EnvironmentFile dans le service
```

### Exécution comme service (systemd)

Les quatre bots (Liste-du-RP-FR, Moderator, Modmail, Restore) tournent en service systemd. Exemple pour Liste-du-RP-FR :

1) Créer le fichier d'environnement `/etc/liste/liste.env` avec les variables nécessaires (voir pour chaque bot dans leur README respectif).

2) Créer le service `/etc/systemd/system/liste.service` :

```ini
[Unit]
Description=Liste du RP FR bot for Communauté du RP FR discord server.
StartLimitInterval=200
StartLimitBurst=5
After=network.target

[Service]
ExecStart=/opt/Liste-du-RP-FR/.venv/bin/python3 /opt/Liste-du-RP-FR/src/main.py
ExecStartPost=/opt/alerts/.venv/bin/alerts liste STARTED
ExecStopPost=/opt/alerts/.venv/bin/alerts liste STOPPED
Restart=always
RestartSec=60
User=debian
StandardOutput=journal
EnvironmentFile=/etc/liste/liste.env
NoNewPrivileges=true
ProtectSystem=full
ProtectHome=true
CapabilityBoundingSet=

[Install]
WantedBy=multi-user.target
```

3) Activer et démarrer :

```bash
sudo systemctl daemon-reload
sudo systemctl enable liste.service
sudo systemctl start liste.service
sudo systemctl status liste.service
```

Adapter pour les autres bots en remplaçant les chemins et noms.

### Installation du Package Utils

```bash
# Option 1 : Installation depuis le repo
uv pip install git+https://github.com/Communaute-du-RP-FR/utils.git

# Option 2 : Installation locale pour développement
git clone https://github.com/Communaute-du-RP-FR/utils.git
cd utils
uv pip install -e .
```

### Installation du Website

```bash
# 1. Cloner le repository
git clone https://github.com/Communaute-du-RP-FR/Website.git
cd Website

# 2. Frontend (Next.js)
npm install
cp .env.example .env.local
# Éditer .env.local avec vos configurations
npm run dev

# 3. Backend (FastAPI) - dans un autre terminal
cd backend
uv venv .venv
uv pip install -r requirements.txt
cp .env.example .env
# Éditer .env
uv run uvicorn app.main:app --reload
```

### Configuration de la Base de Données

**PostgreSQL (Restore) avant `/init-db`**

Pour utiliser `/init-db` (commande du bot Restore), il faut d'abord créer la base et donner les droits à l'utilisateur `debian` pour une connexion socket sans mot de passe :

```bash
sudo -u postgres psql <<'SQL'
CREATE DATABASE listerp_db;
CREATE USER debian;
GRANT ALL PRIVILEGES ON DATABASE listerp_db TO debian;
ALTER DATABASE listerp_db OWNER TO debian;
SQL

# Assurer dans pg_hba.conf une entrée locale trust ou peer pour l'utilisateur debian sur la base listerp_db
# Exemple :
# local   listerp_db   debian   peer

# Recharger PostgreSQL après modification
sudo systemctl reload postgresql
```

Ensuite lancer `/init-db` depuis le bot Restore pour créer les tables et les feed.

---

### Git & GitHub

**Types de commits :**
- `feat`: Nouvelle fonctionnalité
- `fix`: Correction de bug
- `doc`: Documentation
- `style`: Formatage, pas de changement de code
- `refactor`: Refactorisation du code
- `test`: Ajout/modification de tests
- `chore`: Tâches de maintenance

**Branches :**
```bash
# Créer une branche pour chaque fonctionnalité
git checkout -b feature/nom-de-la-fonctionnalite
git checkout -b fix/nom-du-bug
git checkout -b docs/mise-a-jour-readme
```

### Tests

```bash
# Pour les projets avec pytest (Modmail, Restore)
uv run pytest
uv run pytest -v  # mode verbose
uv run pytest --cov  # avec coverage
```

### Documentation

- Maintenir le README à jour
- Documenter les fonctions complexes avec docstrings
- Pour Modmail/Restore : générer la doc Sphinx
  ```bash
  cd docs
  uv run sphinx-build -b html source build/html
  ```

### Mise à Jour des Dépendances

```bash
# Vérifier les dépendances obsolètes
uv pip list --outdated

# Mettre à jour une dépendance spécifique
uv pip install --upgrade nom-package

# Mettre à jour requirements.txt
uv pip freeze > requirements.txt
```

---

## Contact & Support

- Discord : [Rejoindre le serveur](https://discord.gg/commurp)
- Issues : Utiliser le système d'issues de chaque repository
- Email : virgile.devolder2@gmail.com ou martin.devolder2@gmail.com

---

## Licence

Chaque projet a sa propre licence (généralement MIT). Consultez le fichier LICENSE de chaque repository.
