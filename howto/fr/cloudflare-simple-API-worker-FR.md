---
title: API REST de compteur avec Cloudflare Workers
lang: fr
tags: [cloudflare, api, rest]
created: 2025-07-27
updated: 2025-07-27
---

# 🚀 Mini Guide — API REST de compteur avec Cloudflare Workers

## 🎯 Objectif

Créer une API REST Cloudflare Worker avec :

- Un endpoint `POST /api/counter/:name` pour incrémenter un compteur par nom et date
- Stockage dans `KV`
- Spécification OpenAPI générée automatiquement
- Support complet CORS pour utilisation côté navigateur (SPA, mini-jeu...)

## 🛠️ Stack utilisée

| Élément | Description |
|--------|-------------|
| **Cloudflare Workers** | Exécution serverless de l’API |
| **KV Namespace** | Stockage persistant clé/valeur pour les compteurs |
| **[Hono](https://hono.dev/)** | Micro-framework TypeScript/JS rapide et léger |
| **[Chanfana](https://chanfana.pages.dev/)** | Générateur automatique de documentation OpenAPI |
| **Wrangler** | CLI de gestion Cloudflare Workers |

## 📦 Structure du projet

```
/
├── src/
│   ├── endpoints/
│   │   └── increment.ts
│   └── index.ts
├── wrangler.jsonc
```

## ⚙️ Initialisation du projet

```bash
npm create cloudflare@latest
cd mon-projet
npm install
```

## 🗂️ Configuration `wrangler.jsonc`

```jsonc
{
  "name": "compteur-kv",
  "main": "src/index.ts",
  "compatibility_date": "2025-04-16",
  "kv_namespaces": [
    {
      "binding": "COUNTER_KV",
      "id": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
    }
  ]
}
```

## 🧩 API : `POST /api/counter/:name`

Fichier `src/endpoints/increment.ts` :
...

## 🌐 Setup `src/index.ts`

```ts
import { fromHono } from "chanfana";
import { Hono } from "hono";
import { cors } from "hono/cors";
import { CounterIncrement } from "./endpoints/increment";

const app = new Hono();
app.use("/api/*", cors({
  origin: "*",
  allowMethods: ["GET", "POST", "OPTIONS"],
  allowHeaders: ["Content-Type"],
}));

const openapi = fromHono(app, { docs_url: "/" });
openapi.post("/api/counter/:name", CounterIncrement);
export default app;
```

## 🧪 Tests

```bash
curl -X POST https://<ton-worker>.workers.dev/api/counter/launch
```

Et depuis un navigateur :
```js
fetch("https://<ton-worker>.workers.dev/api/counter/launch", {
  method: "POST",
  headers: { "Content-Type": "application/json" }
}).then(res => res.json()).then(console.log);
```

## 🔍 Détails de l’implémentation `increment.ts`

Le fichier `src/endpoints/increment.ts` contient une classe `CounterIncrement` qui :
- Récupère le nom du compteur depuis l’URL (`:name`)
- Génère une clé au format `YYYY-MM-DD:name`
- Utilise `await kv.get(...)` pour lire la valeur actuelle depuis le namespace KV
- Incrémente cette valeur, ou initialise à 1 si elle n’existe pas
- Sauvegarde la nouvelle valeur avec `await kv.put(...)`
- Retourne une réponse JSON contenant la clé et la nouvelle valeur

La réponse contient également les en-têtes nécessaires pour CORS.

## 🧾 Comment générer l’ID de namespace KV

1. Créer le namespace :
```bash
wrangler kv:namespace create "COUNTER_KV"
```

2. Copier l’`id` retourné dans le terminal
3. Mettre à jour le `wrangler.jsonc` :
```jsonc
"kv_namespaces": [
  {
    "binding": "COUNTER_KV",
    "id": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
]
```

## 📘 Où consulter la documentation OpenAPI

Grâce à `chanfana`, la documentation OpenAPI est générée automatiquement à partir du code.

Elle est disponible à l’URL définie dans `docs_url` :

- Par défaut : [`/`](https://<ton-worker>.workers.dev/)
- Elle propose une interface Swagger UI interactive
