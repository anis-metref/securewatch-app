# SecureWatch

## Présentation
![dashboard](./screens/1.jpg)


**SecureWatch** est une application que j’ai développée pour offrir une vue centralisée et intuitive de l’état de sécurité d’un serveur. Elle permet de surveiller en temps réel les connexions SSH, de lister les adresses IP bloquées par fail2ban, et de visualiser les menaces sur une carte interactive. Son objectif est de fournir aux administrateurs système un outil clair, rapide et efficace pour détecter les comportements suspects et renforcer la sécurité de leurs infrastructures.

## Architecture technique

- **SecureWatch** repose sur une stack moderne et légère :
- **Backend** : Node.js et SQLite
- **Frontend** : React avec Vite
- **Serveur web** : Nginx
- **Conteneurisation** : Docker + Docker Compose

## Fonctionnalités principales

- **Dashboard** : stats SSH, IP bloquées, chart, carte geo des tops attack par pays, événements récents.
- **Carte des menaces** : géolocalisation des IP malveillantes.
- **Gestion des IP** : bannissement/débannissement automatique et manuel.
- **Notifications** : envoi de  vers discord et slack.
- **Exports** : CSV et PDF des logs ssh et ip bloquées.
- **Fail2ban** : lecture/modification des fichiers de config, reload intégré.
- **Sécurité** : authentification JWT, admin par défaut au premier démarrage.
- **Agent** : collecte des événements SSH et synchronisation fail2ban avec le serveur.

## Installation rapide

### 1. Prérequis

- Docker + Docker Compose
- fail2ban et iptables installé et actif
- Accès aux fichiers suivants sur l’hôte :
  - `/var/log/auth.log`
  - `/var/log/fail2ban.log`
  - `/etc/fail2ban/{jail.d,filter.d,fail2ban.d,action.d}`

### 2. Mise en place

```bash
sudo apt update
sudo apt install -y docker.io docker-compose-plugin fail2ban iptables
sudo systemctl enable --now docker
sudo usermod -aG docker "$USER"
```

Reconnectez-vous pour que les droits Docker soient pris en compte.


```bash
sudo cp jail-sshd.conf /etc/fail2ban/jail.d/jail-sshd,conf
```

Si un jail `sshd` est déjà défini ailleurs, désactivez-le pour que `jail-sshd.conf` soit pris en compte.

### 3. Configuration

- Copiez `.env.example` en `.env`

Le `.env` vous permet de configurer les secrets de sécurité, la connexion avec les agents, et les intégrations externes (discord, slack, géolocalisation), ainsi que les paramètres de cache et de protection contre le brute-force.

- Modifiez les secrets :
  ```env
  JWT_SECRET=<change_me_min_32_chara>
  # secret doit corresspondre au meme secret que l'agent 
  AGENT_REGISTRATION_SECRET=<change_me_agent_secret_key>
  ```

### 4. Lancement

```bash
docker compose up -d
```

### 5. Accès

- Frontend : [http://localhost:5173](http://localhost:5173)
- Admin par défaut : `admin / admin` (à changer au premier login)
- API backend : [http://localhost:8787/api/health](http://localhost:8787/api/health)



## Commandes utiles

```bash
docker compose logs -f backend   # Voir les logs
docker compose down              # Arrêter l’app
docker compose up -d            # Démarrer en arrière-plan
```

