
# 🚀 Mini Guide — Counter API with Cloudflare Workers

## 🎯 Goal

Build a REST API using Cloudflare Workers with:

- A `POST /api/counter/:name` endpoint that increments a named/date-based counter
- KV storage backend
- Auto-generated OpenAPI spec
- Full CORS support for frontend usage (SPA, mini-game...)

## 🛠️ Tech Stack

| Component | Description |
|----------|-------------|
| **Cloudflare Workers** | Serverless API runtime |
| **KV Namespace** | Persistent key-value counter storage |
| **[Hono](https://hono.dev/)** | Lightweight TypeScript/JS web framework |
| **[Chanfana](https://chanfana.pages.dev/)** | Auto OpenAPI generator |
| **Wrangler** | CLI to manage Cloudflare Workers |

## 📦 Project Structure

```
/
├── src/
│   ├── endpoints/
│   │   └── increment.ts
│   └── index.ts
├── wrangler.jsonc
```

## ⚙️ Project Setup

```bash
npm create cloudflare@latest
cd my-project
npm install
```

## 🗂️ `wrangler.jsonc` Configuration

```jsonc
{
  "name": "counter-kv",
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

## 🧩 API: `POST /api/counter/:name`

File `src/endpoints/increment.ts`:
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
curl -X POST https://<your-worker>.workers.dev/api/counter/launch
```

From browser:
```js
fetch("https://<your-worker>.workers.dev/api/counter/launch", {
  method: "POST",
  headers: { "Content-Type": "application/json" }
}).then(res => res.json()).then(console.log);
```

## 🔍 Details on `increment.ts` logic

The file `src/endpoints/increment.ts` contains a `CounterIncrement` class that:
- Retrieves the counter name from the URL (`:name`)
- Creates a key in the format `YYYY-MM-DD:name`
- Uses `await kv.get(...)` to read the current value from KV
- Increments it, or starts from 1 if not set
- Persists the value using `await kv.put(...)`
- Returns the updated key and value in a JSON response

The response also includes required CORS headers.

## 🧾 How to generate and configure the KV namespace ID

1. Create the KV namespace:
```bash
wrangler kv:namespace create "COUNTER_KV"
```

2. Copy the returned `id`
3. Update your `wrangler.jsonc` like this:
```jsonc
"kv_namespaces": [
  {
    "binding": "COUNTER_KV",
    "id": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
  }
]
```

## 📘 OpenAPI documentation access

Using `chanfana`, the OpenAPI spec is auto-generated from the code.

It is accessible at the URL set via `docs_url`:

- Default: [`/`](https://<your-worker>.workers.dev/)
- Provides an interactive Swagger UI
