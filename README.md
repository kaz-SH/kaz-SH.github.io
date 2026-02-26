# KORE Launcher - Glass Edition

![KORE Banner](https://placehold.co/1200x400/0f172a/ffffff?text=KORE+LAUNCHER)

**KORE Launcher** est une solution "Host Anywhere" pour Minecraft moddé. Il permet à n'importe quel membre d'une communauté d'héberger la session de jeu sur sa propre machine, tout en garantissant que la progression (monde, inventaires) est synchronisée et sécurisée sur un serveur de stockage centralisé via SFTP.

Plus besoin de louer un serveur de jeu 24/7 coûteux. La puissance de calcul est fournie par le joueur hôte, le stockage par un simple VPS ou NAS.

## 🚀 Fonctionnalités Clés

*   **Architecture Hybride** : Le serveur tourne en local (sur votre PC puissant) mais les sauvegardes sont dans le cloud.
*   **Zéro Configuration Réseau** : Intégration native de **Playit.gg** (Tunneling) pour inviter vos amis sans ouvrir de ports.
*   **Synchronisation Intelligente** : Seuls les fichiers modifiés sont transférés. Verrouillage de session pour éviter les conflits.
*   **Multi-Modpacks** : Gérez plusieurs aventures (Survie, Créatif, Hardcore) avec des instances isolées.
*   **Interface Glassmorphism** : Une UI moderne, transparente et fluide.

---

## 🎮 Guide Utilisateur

### 1. Installation
1.  Téléchargez l'installateur `KORE-Setup-X.X.X.exe`.
2.  Lancez l'installation (Windows uniquement).
3.  Au premier démarrage, le launcher vous demandera un fichier de configuration.

### 2. Configuration (`config.json`)
Le launcher a besoin d'une "clé" pour accéder au stockage distant.
*   L'administrateur de votre serveur doit vous fournir un fichier `.json`.
*   Cliquez sur "Importer une configuration" et sélectionnez ce fichier.
*   Une fois validé, le fichier est chiffré et stocké en sécurité. L'original est supprimé.

### 3. Jouer (Client)
*   Connectez-vous avec votre compte Microsoft.
*   Sélectionnez votre Modpack en haut à gauche.
*   Cliquez sur **"LANCER LE JEU"**.
*   Le launcher va télécharger les mods, Forge et Java automatiquement.

### 4. Héberger la partie (Host)
Si vous avez un bon PC et une bonne connexion fibre :
1.  Allez dans l'onglet **"Serveur"** (à gauche).
2.  Cliquez sur **"Héberger la Session"**.
3.  Le launcher va :
    *   Verrouiller le serveur distant (personne d'autre ne peut lancer en même temps).
    *   Télécharger la dernière sauvegarde.
    *   Lancer le serveur Minecraft localement.
    *   Lancer un tunnel **Playit.gg** et vous donner l'adresse IP à partager.
4.  Une fois fini, cliquez sur **"Arrêter & Sync"** pour renvoyer la sauvegarde sur le cloud.

---

## 🛠️ Guide Administrateur (Technique)

Cette section est destinée à ceux qui mettent en place l'infrastructure KORE.

### Architecture du Serveur Distant (SFTP)
Le launcher a besoin d'un accès SFTP à un dossier racine sur un serveur Linux.
Voici l'arborescence requise à la racine du dossier utilisateur :

```text
/home/user/kore_storage/
├── modpacks/              # Dossier racine des modpacks
│   ├── PVP/               # Nom du modpack (identique au menu)
│   │   ├── mods/          # Tous les .jar du modpack
│   │   ├── server/        # Sauvegardes du serveur (zippées ou dossiers)
│   │   ├── version.txt    # Contient le numéro de version MC (ex: 1.20.1)
│   │   ├── f_ver.txt      # Contient la version précise de Forge (ex: 47.1.0)
│   │   ├── modpack.txt    # Version du modpack (ex: 1.0.0) pour trigger les mises à jour
│   │   └── lock.txt       # (Créé automatiquement par le launcher quand utilisé)
│   └── CREATIF/           # Autre modpack...
└── admin_pass.txt         # (Optionnel) Mot de passe global pour le panel admin
```

### Fichier `config.json` (Template)
Distribuez ce fichier à vos joueurs.

```json
{
  "host": "ip.de.votre.vps",
  "port": 22,
  "username": "sftp_user",
  "password": "sftp_password",
  "remoteRoot": "/home/sftp_user/kore_storage",
  "javaPath": ""  // Optionnel : chemin vers un Java spécifique
}
```

### Compilation (Build)

Pour modifier le code source et recompiler l'exécutable :

**Pré-requis :**
*   Node.js v16+
*   Python (pour certaines dépendances de build)

**Commandes :**

```bash
# 1. Installer les dépendances
npm install

# 2. Lancer en mode développement
npm start

# 3. Compiler l'installateur Windows (NSIS)
npm run build
```

> **Note :** L'exécutable `playit.exe` doit être présent à la racine du projet pour être inclus dans le build final.

### Sécurité & Bonnes Pratiques

*   **Ne jamais commiter** de `config.json` ou de clés privées dans le code source.
*   Le launcher utilise `app.getPath('userData')` pour stocker les données sensibles.
*   L'accès au panel Admin (dans le launcher) est protégé par mot de passe (défini dans `admin_pass.txt` sur le serveur ou localement).

---

## 🏗️ Stack Technique

*   **Electron** : Framework d'application bureau.
*   **Node.js** : Backend local.
*   **SSH2 / SFTP** : Protocole de transfert sécurisé.
*   **TailwindCSS** : Design de l'interface.
*   **Glassmorphism** : Style visuel.
