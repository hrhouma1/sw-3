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


# Annexe : 


# <h1 id="correctif-module-3">Correctif complet – Dossier `src/db`, schéma Drizzle et déclaration dans `index.ts`</h1>

---

## <h2 id="1-rappel-des-fichiers-obligatoires">1. Rappel des fichiers obligatoires</h2>

```
src/
└── db/
    ├── index.ts     ← instance unique `db`
    └── schema.ts    ← définition des tables + énumérations
drizzle.config.ts    ← configuration CLI
.env.local           ← XATA_DATABASE_URL
```

---

## <h2 id="2-schema-ts-detaille">2. Contenu exhaustif de `src/db/schema.ts`</h2>

```ts
import {
  pgTable,
  pgEnum,
  serial,
  varchar,
  numeric,
  timestamp,
  text,
} from "drizzle-orm/pg-core";

/* 2.1 – Enumération SQL pour le statut d’une facture */
export const statusEnum = pgEnum("status", [
  "open",
  "paid",
  "void",
  "uncollectible",
]);

/* 2.2 – Table `invoices` */
export const invoices = pgTable("invoices", {
  id:          serial("id").primaryKey(),
  createdAt:   timestamp("created_at").defaultNow().notNull(),
  customer:    varchar("customer", { length: 120 }).notNull(),
  email:       varchar("email",    { length: 160 }).notNull(),
  value:       numeric("value").notNull(),
  description: text("description"),
  status:      statusEnum("status").notNull().default("open"),
});
```

Points clés :

* `statusEnum` doit être déclaré **avant** la table qui l’utilise.
* Nom de table en minuscule (convention PostgreSQL).
* Champ `created_at` pour l’horodatage.

---

## <h2 id="3-index-ts">3. Contenu exhaustif de `src/db/index.ts`</h2>

```ts
import { drizzle } from "drizzle-orm/node-postgres";
import { Pool } from "pg";
import * as schema from "@/db/schema";   // << regroupe tout le schéma

const pool = new Pool({
  connectionString: process.env.XATA_DATABASE_URL,
  max: 20,
});

/**
 * Instance unique de Drizzle, exportée pour tout le projet.
 * Le schéma est passé afin de bénéficier du typage complet.
 */
export const db = drizzle(pool, { schema });
```

Notes :

1. L’import `* as schema` capture **tous** les exports de `schema.ts` (tables + enums).
2. Le paramètre `{ schema }` active l’auto-complétion typée dans VS Code pour toutes les requêtes.

---

## <h2 id="4-drizzle-config">4. `drizzle.config.ts` à la racine</h2>

```ts
import "dotenv/config";
import { defineConfig } from "drizzle-kit";

export default defineConfig({
  schema: "./src/db/schema.ts",
  out: "./drizzle",
  driver: "pg",
  dbCredentials: {
    connectionString: process.env.XATA_DATABASE_URL!,
  },
});
```

---

## <h2 id="5-generer-et-pousser-migration">5. Générer puis pousser la migration</h2>

```bash
npx drizzle-kit generate     # crée 001_invoices.sql dans ./drizzle
npx drizzle-kit push         # exécute le SQL sur Xata
```

> Les deux commandes doivent se terminer sans erreur.
> Vérifier dans la console Xata que la table `invoices` et l’énum `status` apparaissent.

---

## <h2 id="6-test-rapide">6. Test rapide de connexion (Server Action simplifiée)</h2>

Ajoutez dans `src/app/invoices/new/page.tsx` juste au-dessus du `return` :

```tsx
/* Test lecture synchrone (Server Component) */
const dbTest = await db.execute(sql`SELECT current_database()`);
console.log(dbTest);            // Visible dans la console serveur
```

Rafraîchir `/invoices/new`.

* Dans le terminal, la ligne `current_database` doit afficher `my-invoicing-app:main`.
* La page reste fonctionnelle.

---

## <h2 id="7-points-de-controle-pour-lexamen">7. Points de contrôle pour l’examen</h2>

| Item à vérifier                                        | Commentaire |
| ------------------------------------------------------ | ----------- |
| `schema.ts` contient `statusEnum` **et** `invoices`    | oui/non     |
| `index.ts` importe `* as schema` et passe `{ schema }` | oui/non     |
| Migration générée dans `./drizzle`                     | oui/non     |
| `drizzle-kit push` exécuté sans erreur                 | oui/non     |
| Requête test `SELECT current_database()` fonctionne    | oui/non     |



