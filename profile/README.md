# Communauté du RP FR

> Complete ecosystem of Discord bots and tools for the `Communauté du RP FR` Discord server

Welcome to the `Communauté du RP FR` GitHub organization! We develop and maintain several Discord bots and tools to help manage the server.

## Our Projects

### Discord Bots

#### [Liste-du-RP-FR](https://github.com/Communaute-du-RP-FR/Liste-du-RP-FR)
Main bot for managing the roleplay server listing.

**Features:**
- Roleplay server registration and management
- Server search by criteria
- Member and player management
- Boost system
- Statistics and leaderboards

**Technologies:** Python, discord.py
**Documentation:** Sphinx

---

#### [Moderator](https://github.com/Communaute-du-RP-FR/Moderator)
Advanced moderation bot with a verification system and ticket management.

**Features:**
- Comprehensive moderation tools
- Ticket system
- Automated user verification
- Mini-games
- List and configuration management
- Automated tasks (loops)

**Technologies:** Python, discord.py
**Documentation:** Sphinx

---

#### [Modmail](https://github.com/Communaute-du-RP-FR/Modmail)
Private messaging system between users and the moderation team.

**Features:**
- Private conversations between a user and staff
- History and archiving
- Notifications

**Technologies:** Python, discord.py
**Documentation:** Sphinx

---

#### [Restore](https://github.com/Communaute-du-RP-FR/Restore)
Discord server backup and restoration bot.

**Features:**
- Partial database restoration
- Configuration restoration
- SQL database

**Technologies:** Python, discord.py, SQL
**Documentation:** Sphinx

---

### Utilities

#### [utils](https://github.com/Communaute-du-RP-FR/utils)
Shared library of utility functions for all bots.

**Contents:**
- Custom Discord client
- Database manager
- Generic views and UI components
- Text and emote utilities
- Centralized configuration

**Technologies:** Python
**Documentation:** Sphinx

---

#### [policies](https://github.com/Communaute-du-RP-FR/policies)
Privacy policy and terms of use for each bot.

**Contents:**
- Data privacy policy for all four bots
- Terms of use for all four bots
- Links to published versions

---

#### [alerts](https://github.com/Communaute-du-RP-FR/alerts)
Python CLI for sending service status notifications to Discord.

**Features:**
- Formatted Discord notifications via webhooks
- Automatic timestamping
- @everyone mention to notify Admins
- Clean interface using native Discord components

**Technologies:** Python, discord.py, requests
**Documentation:** Sphinx

**Installation:**

```bash
uv pip install git+https://github.com/Communaute-du-RP-FR/alerts.git
```

**Usage:**

```bash
export DISCORD_WEBHOOK_URL="https://discord.com/api/webhooks/..."
alerts my_service STARTED
```

---

#### [backups](https://github.com/Communaute-du-RP-FR/backups)
PostgreSQL backup and restoration system with encryption and remote storage.

**Features:**
- Encrypted backups with [age encryption](https://github.com/FiloSottile/age)
- Compressed PostgreSQL dumps (custom format, level 9)
- Remote storage via SCP
- Automatic cleanup (keeps the last 30 backups)
- Secure restoration with clean and if-exists options

**Technologies:** Bash, PostgreSQL, age encryption

**Usage:**

```bash
# Create a backup
./scripts/backup.sh

# Restore from a backup
./scripts/restore.sh backup-file.dump.age
```

---

## Installation

### General Prerequisites

- Python 3.10+ and [uv](https://docs.astral.sh/uv/) for managing the bots' environments/dependencies
- Git
- PostgreSQL

### Installing the Discord Bots

```bash
# 1. Clone the repository
git clone https://github.com/Communaute-du-RP-FR/[BOT-NAME].git
cd [BOT-NAME]

# 2. Create the environment with uv
uv venv .venv

# 3. Install dependencies from pyproject
uv sync  # uses pyproject.toml and uv.lock if present

# 4. Configure the systemd service (see "Running as a service" section)
# Environment variables are loaded via EnvironmentFile in the service
```

### Running as a Service (systemd)

All four bots (Liste-du-RP-FR, Moderator, Modmail, Restore) run as systemd services. Example for Liste-du-RP-FR:

1) Create the environment file `/etc/liste/liste.env` with the required variables (see each bot's own README for details).

2) Create the service file `/etc/systemd/system/liste.service`:

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

3) Enable and start:

```bash
sudo systemctl daemon-reload
sudo systemctl enable liste.service
sudo systemctl start liste.service
sudo systemctl status liste.service
```

Each bot's specifics are detailed in its own README.

### Installing the Utils Package

```bash
# Option 1: Install from the repo
uv pip install git+https://github.com/Communaute-du-RP-FR/utils.git

# Option 2: Local install for development
git clone https://github.com/Communaute-du-RP-FR/utils.git
cd utils
uv pip install -e .
```

### Database Configuration

**PostgreSQL (Restore) before `/init-db`**

To use `/init-db` (a Restore bot command), you first need to create the database and grant the `debian` user passwordless socket-connection privileges:

```bash
sudo -u postgres psql <<'SQL'
CREATE DATABASE listerp_db;
CREATE USER debian;
GRANT ALL PRIVILEGES ON DATABASE listerp_db TO debian;
ALTER DATABASE listerp_db OWNER TO debian;
SQL

# Make sure pg_hba.conf has a local trust or peer entry for the debian user on the listerp_db database
# Example:
# local   listerp_db   debian   peer

# Reload PostgreSQL after making changes
sudo systemctl reload postgresql
```

Then run `/init-db` from the Restore bot to create the tables and seed data.

---

### Git & GitHub

**Commit types:**
- `feat`: New feature
- `fix`: Bug fix
- `doc`: Documentation
- `style`: Formatting, no code change
- `refactor`: Code refactoring
- `test`: Adding/modifying tests
- `chore`: Maintenance tasks
---

## Contact & Support

- Discord: [Join the server](https://discord.gg/commurp)
- Issues: Use each repository's issue tracker
- Email: virgile.devolder2@gmail.com or martin.devolder2@gmail.com

---

## License

Each project has its own license (generally MIT). Check the LICENSE file in each repository.
