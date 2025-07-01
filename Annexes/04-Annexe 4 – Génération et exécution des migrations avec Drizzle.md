# <h1 id="module-3-suite">Module 4 – Génération et exécution des migrations avec **Drizzle Kit**</h1>

Les fichiers de schéma (`src/db/schema.ts`) et de configuration (`drizzle.config.ts`) sont désormais en place. Cette section couvre :

1. Configuration définitive de **`drizzle.config.ts`**.
2. Ajout des scripts `generate` et `migrate` à **`package.json`**.
3. Exécution des commandes & vérifications.
4. Contrôle d’évaluation rapide.

---

## <h2 id="1-drizzleconfig-final">1. `drizzle.config.ts` – version finale</h2>

```ts
import { defineConfig } from "drizzle-kit";
import * as dotenv from "dotenv";

dotenv.config({ path: "./.env.local" });

if (typeof process.env.XATA_DATABASE_URL !== "string") {
  throw new Error("Please set XATA_DATABASE_URL in .env.local");
}

export default defineConfig({
  dialect: "postgresql",
  schema: "./src/db/schema.ts",
  out: "./src/db/migrations",
  dbCredentials: {
    url: process.env.XATA_DATABASE_URL,
  },
});
```

Points clés :

| Ligne           | Rôle                                                                  |
| --------------- | --------------------------------------------------------------------- |
| `dotenv.config` | Charge les variables de `.env.local` lorsqu’on lance le CLI.          |
| Bloc `if (…)`   | Sécurise l’exécution : erreur explicite si la variable est manquante. |
| `schema`        | Chemin absolu vers le fichier des tables.                             |
| `out`           | Les fichiers `.sql` seront produits dans `src/db/migrations`.         |

---

## <h2 id="2-packagejson">2. Scripts à ajouter dans `package.json`</h2>

```jsonc
{
  "scripts": {
    "dev":    "next dev",
    "build":  "next build",
    "start":  "next start",
    "lint":   "next lint",

    /* Drizzle Kit */
    "generate": "drizzle-kit generate",
    "migrate":  "drizzle-kit migrate"
  }
}
```

> *`generate`* = construit les fichiers SQL à partir du schéma.
> *`migrate`* = exécute les fichiers SQL sur la base définie.

---

## <h2 id="3-execution">3. Exécution des commandes</h2>

### 3.1 – Génération des migrations

```bash
npm run generate
```

Sortie attendue :

```
Reading config file drizzle.config.ts
1 tables
invoices 7 columns …
✓ Your SQL migration file → src/db/migrations/0000_<slug>.sql
```

### 3.2 – Migration de la base

```bash
npm run migrate
```

Sortie attendue :

```
Connecting to Postgres …
Applied migration 0000_<slug>.sql ✔
```

### 3.3 – Vérification visuelle

*Option 1 :* depuis la console Xata : la table **invoices** et l’énum **status** doivent apparaître.
*Option 2 :* requête rapide dans n’importe quel Server Component :

```ts
import { sql } from "drizzle-orm";
import { db } from "@/db";

const tables = await db.execute(sql`SELECT relname FROM pg_class WHERE relname = 'invoices'`);
console.log(tables.rows); // doit contenir "invoices"
```


