---
title: Deploy a Static Website from GitHub via FTP
lang: en
tags: [git, ftp, deployment, github-actions]
created: 2024-10-12
updated: 2025-07-27
---

# 🚀 How to Deploy a Static Website from GitHub via FTP

## 🎯 Goal

Automatically deploy a static website to a remote server via FTP on every push to GitHub.


---

## 🧰 Requirements

Before getting started, make sure you have:

- A GitHub repository with your static site (HTML/CSS/JS).
- FTP access to a remote server (host, username, password).
- An active `main` branch (or adapt as needed).
- A local folder to deploy (e.g., `www/`).

---

## 1. 🔐 Add FTP Secrets to GitHub

Go to **Settings > Secrets and variables > Actions > New repository secret** in your GitHub repository, and add the following:

| Secret Name         | Description                     |
|---------------------|---------------------------------|
| `FTP_SERVER`        | Your FTP server address         |
| `FTP_USERNAME`      | Your FTP username               |
| `FTP_PASSWORD`      | Your FTP password               |

---

## 2. ⚙️ Create the GitHub Actions Workflow

In your repository, create the file:

```
.github/workflows/deploy-ftp.yml
```

Paste the following content:

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

## 3. 🚀 Test the Deployment

1. Commit the workflow file to the `main` branch.
2. Push a change inside the `www` folder.
3. Go to the **Actions** tab in your repository to track the deployment.

---

## ✅ Result

Every time you push to the `main` branch, GitHub will automatically deploy the contents of your `www` folder to your FTP server.

---

## 🧼 Extra Tips

- **Limit what you upload:** Make sure only deployable files are in `www/`.
- **Ignore unwanted files:** Use a `.ftpignore` file if needed.
- **Consider a dedicated deployment branch** to separate dev and production.

---

## 📚 Resources

- [GitHub Actions Documentation](https://docs.github.com/en/actions)
- [SamKirkland/FTP-Deploy-Action](https://github.com/SamKirkland/FTP-Deploy-Action)
