# <h1 id="module-3">Module 3 – Installer et configurer **Xata** + **Drizzle ORM** (PostgreSQL)</h1>

---

## <h2 id="objectif">Objectif pédagogique</h2>

Mettre en place une base PostgreSQL gérée par **Xata**, l’exposer via **Drizzle ORM** et préparer les migrations. À la fin :

1. Un compte Xata gratuit actif, une base `my-invoicing-app`.
2. Les identifiants sécurisés dans `.env.local`.
3. Le dossier `src/db` avec `index.ts` exportant l’instance `db`.
4. `drizzle.config.ts` au racine + première migration exécutable.

---

## <h2 id="etape-0">Étape 0 – Création de la base Xata (interface Web)</h2>

1. Se connecter à `https://xata.io`, cliquer **New database**.
2. Renseigner :

   * **Database name** : `my-invoicing-app`
   * **Region** : N. Virginia (us-east-1) ou la région souhaitée
   * Activer **Enable direct access to Postgres** (obligatoire pour Drizzle) ([xata.io][1])
3. Cliquer **Next**, puis **Get credentials** : copier

   * **PostgreSQL endpoint** (chaîne complète)
   * **API Key** (si proposée)

---

## <h2 id="etape-1">Étape 1 – Stocker les secrets dans `.env.local`</h2>

Créer (ou ouvrir) le fichier `.env.local` à la racine :

```dotenv
# Xata (mode Postgres)
XATA_DATABASE_URL="postgresql://<user>:<password>@<host>/<db>?sslmode=require"
```

> **Nota** : ne versionnez jamais ce fichier ; il est déjà dans le `.gitignore` généré par Next.js.

---

## <h2 id="etape-2">Étape 2 – Installer les dépendances côté projet</h2>

```bash
# dépendances runtime
npm install drizzle-orm pg

# dépendances dev : CLI + typage
npm install -D drizzle-kit @types/pg ts-node
```

* `drizzle-orm` : ORM typé
* `pg` : driver officiel Node/Postgres
* `drizzle-kit` : migrations / introspection ([orm.drizzle.team][2], [orm.drizzle.team][3])

---

## <h2 id="etape-3">Étape 3 – Créer le dossier `src/db`</h2>

```
src/
├── app/
├── components/
└── db/
    └── index.ts
```

### Contenu `src/db/index.ts`

```ts
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";

/* pool Postgres partagé pour les Server Actions */
const pool = new Pool({
  connectionString: process.env.XATA_DATABASE_URL,
  max: 20,              // connexions simultanées
});

export const db = drizzle(pool);
```

> La connexion est instanciée **une seule fois** et peut être importée dans n’importe quel Server Action ou Route API. ([orm.drizzle.team][4])

---

## <h2 id="etape-4">Étape 4 – Créer la configuration Drizzle</h2>

À la racine du dépôt :

```ts
// drizzle.config.ts
import "dotenv/config";
import { defineConfig } from "drizzle-kit";

export default defineConfig({
  schema: "./src/db/schema.ts",     // chemin des tables (à créer)
  out: "./drizzle",                 // dossier de migrations
  driver: "pg",
  dbCredentials: {
    connectionString: process.env.XATA_DATABASE_URL!,
  },
});
```

---

## <h2 id="etape-5">Étape 5 – Définir le schéma initial</h2>

Créer `src/db/schema.ts` :

```ts
import { pgTable, serial, varchar, numeric, timestamp } from "drizzle-orm/pg-core";

export const invoices = pgTable("invoices", {
  id:          serial("id").primaryKey(),
  customer:    varchar("customer", { length: 120 }).notNull(),
  email:       varchar("email",    { length: 160 }).notNull(),
  value:       numeric("value").notNull(),
  description: varchar("description", { length: 255 }),
  status:      varchar("status", { length: 32 }).default("open"),
  createdAt:   timestamp("created_at").defaultNow(),
});
```

---

## <h2 id="etape-6">Étape 6 – Générer et exécuter la première migration</h2>

```bash
# génération SQL dans ./drizzle
npx drizzle-kit generate

# exécution automatique sur la base
npx drizzle-kit push
```

`drizzle-kit` crée la table `invoices` directement dans votre instance Xata (mode Postgres). Le dossier `drizzle/` contient le fichier SQL versionné pour audit. ([orm.drizzle.team][5])

---

## <h2 id="etape-7">Étape 7 – Vérification rapide avec un script</h2>

Créer `scripts/test-db.ts` :

```ts
import { db } from "../src/db";
import { invoices } from "../src/db/schema";

async function main() {
  const rows = await db.select().from(invoices).limit(1);
  console.log("Test : lecture OK →", rows);
  process.exit(0);
}
main();
```

Exécuter :

```bash
npx ts-node scripts/test-db.ts
```

Vous devez obtenir un tableau vide `[]` (aucune facture pour l’instant) sans erreur de connexion.

---

## <h2 id="evaluation">Évaluation (barème partiel)</h2>

| Critère                                                        | Points |
| -------------------------------------------------------------- | :----: |
| `.env.local` présent et non commité                            |    2   |
| `src/db/index.ts` exporte `db` avec `Pool` et connexion `.env` |    2   |
| `schema.ts` conforme (7 colonnes dont `id` PK)                 |    2   |
| Migration générée dans `drizzle/` + push réussi sur Xata       |    2   |
| Script de test lit la table sans erreur                        |    2   |
| **Total**                                                      | **10** |

