# 09 - Liste complète des commandes `drizzle-kit`

## Commandes CLI à connaître

| Commande                        | Description                                                                                       |
| ------------------------------- | ------------------------------------------------------------------------------------------------- |
| `drizzle-kit generate`          | Génère les fichiers de migration depuis les fichiers de schéma (schema.ts).                       |
| `drizzle-kit push`              | Applique automatiquement le schéma à la base de données (sans création de fichiers de migration). |
| `drizzle-kit up`                | Applique les migrations générées (fichiers dans `drizzle/migrations`).                            |
| `drizzle-kit studio`            | Lance une interface web locale pour explorer la base de données.                                  |
| `drizzle-kit introspect`        | Génère automatiquement les fichiers de schéma à partir d'une base de données existante.           |
| `drizzle-kit build`             | Compile les fichiers de configuration.                                                            |
| `drizzle-kit introspect:pg`     | Même que `introspect`, mais spécifiquement pour PostgreSQL.                                       |
| `drizzle-kit introspect:mysql`  | Même que `introspect`, mais spécifiquement pour MySQL.                                            |
| `drizzle-kit introspect:sqlite` | Même que `introspect`, mais spécifiquement pour SQLite.                                           |
| `drizzle-kit generate:pg`       | Génère des migrations pour PostgreSQL.                                                            |
| `drizzle-kit generate:mysql`    | Génère des migrations pour MySQL.                                                                 |
| `drizzle-kit generate:sqlite`   | Génère des migrations pour SQLite.                                                                |
| `drizzle-kit push:pg`           | Applique le schéma à PostgreSQL directement (sans migration).                                     |
| `drizzle-kit push:mysql`        | Applique le schéma à MySQL directement (sans migration).                                          |
| `drizzle-kit push:sqlite`       | Applique le schéma à SQLite directement (sans migration).                                         |

<br/>
<br/>

# Scripts à insérer dans `package.json`

Voici une section `"scripts"` complète à copier dans ton `package.json` :

```json
"scripts": {
  "generate": "drizzle-kit generate",
  "generate:pg": "drizzle-kit generate:pg",
  "generate:mysql": "drizzle-kit generate:mysql",
  "generate:sqlite": "drizzle-kit generate:sqlite",

  "push": "drizzle-kit push",
  "push:pg": "drizzle-kit push:pg",
  "push:mysql": "drizzle-kit push:mysql",
  "push:sqlite": "drizzle-kit push:sqlite",

  "migrate": "drizzle-kit up",
  "studio": "drizzle-kit studio",
  "build": "drizzle-kit build",

  "introspect": "drizzle-kit introspect",
  "introspect:pg": "drizzle-kit introspect:pg",
  "introspect:mysql": "drizzle-kit introspect:mysql",
  "introspect:sqlite": "drizzle-kit introspect:sqlite"
}
```

<br/>
<br/>

# Exemple de fichier `drizzle.config.ts`

À placer à la racine du projet :

```ts
import type { Config } from "drizzle-kit";

export default {
  schema: "./src/db/schema.ts",
  out: "./drizzle/migrations",
  driver: "pg",
  dbCredentials: {
    connectionString: process.env.DATABASE_URL!,
  },
} satisfies Config;
```

<br/>
<br/>


# Fichier `.env.local` ou `.env`

Exemple de variable à définir :

```
DATABASE_URL=postgres://user:password@localhost:5432/ma_base
```

<br/>
<br/>


# Exemple de cycle complet

1. Modifier ou créer des entités dans `src/db/schema.ts`
2. Générer les migrations

```bash
npm run generate
```

3. Appliquer les migrations

```bash
npm run migrate
```

4. Visualiser la base (optionnel)

```bash
npm run studio
```

5. Réinitialiser depuis une base existante

```bash
npm run introspect
```
