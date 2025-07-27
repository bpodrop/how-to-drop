---
title: Déployer un site statique depuis GitHub via FTP
lang: fr
tags: [ftp, déploiement, github-actions]
created: 2025-07-27
updated: 2025-07-27
---

# 🚀 Déployer un site statique depuis GitHub via FTP

## 🎯 Objectif

Configurer **GitHub Actions** pour publier automatiquement le contenu du dossier `www/` sur un serveur FTP à chaque *push* sur la branche `main`.

---

## 🧰 Prérequis

Avant de commencer, assurez-vous d’avoir :

- Un dépôt GitHub contenant votre site statique (HTML/CSS/JS).
- Un accès FTP à un serveur (nom d’hôte, identifiants).
- Une branche `main` active (ou ajustez selon vos besoins).
- Le dossier à déployer, par exemple : `www/`.

---

## 1. 🔐 Ajouter les secrets FTP dans GitHub

Rendez-vous dans **Settings > Secrets and variables > Actions > New repository secret** de votre dépôt GitHub, et ajoutez les clés suivantes :

| Nom du secret       | Description                   |
|---------------------|-------------------------------|
| `FTP_SERVER`        | Adresse du serveur FTP        |
| `FTP_USERNAME`      | Nom d’utilisateur FTP         |
| `FTP_PASSWORD`      | Mot de passe FTP              |

---

## 2. ⚙️ Créer le workflow GitHub Actions

Dans votre dépôt, créez le fichier suivant :

```
.github/workflows/deploy-ftp.yml
```

Et collez ce contenu :

```yaml
name: Deploy Website via FTP

on:
  push:
    branches:
      - main

jobs:
  ftp-deploy:
    name: Upload to FTP
    runs-on: ubuntu-latest

    steps:
      - name: Checkout repository
        uses: actions/checkout@v3

      - name: Deploy via FTP
        uses: SamKirkland/FTP-Deploy-Action@v4.3.4
        with:
          server: ${{ secrets.FTP_SERVER }}
          username: ${{ secrets.FTP_USERNAME }}
          password: ${{ secrets.FTP_PASSWORD }}
          local-dir: www
```

---

## 3. 🚀 Tester le déploiement

1. Commitez ce fichier dans la branche `main`.
2. Poussez une modification dans le dossier `www`.
3. Consultez l’onglet **Actions** de votre dépôt GitHub pour suivre le déploiement en temps réel.

---

## ✅ Résultat

À chaque mise à jour de la branche `main`, GitHub publiera automatiquement le contenu de votre dossier `www` vers votre serveur FTP.

---

## 🧼 Conseils supplémentaires

- **Limiter le dossier local :** Assurez-vous que seul le contenu à publier est dans `www/`.
- **Ignorez les fichiers sensibles :** Ajoutez un `.ftpignore` si nécessaire.
- **Utilisez une branche dédiée au déploiement** si vous souhaitez séparer développement et publication.

---

## 📚 Ressources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [SamKirkland/FTP-Deploy-Action](https://github.com/SamKirkland/FTP-Deploy-Action)
