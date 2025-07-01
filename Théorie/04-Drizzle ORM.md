## Qu'est-ce que **Drizzle ORM** ?

**Drizzle ORM** est un ORM écrit en **TypeScript**, conçu pour être **léger**, **typé de bout en bout**, et facile à utiliser dans des environnements modernes comme **Next.js (App Router)**, **Edge Functions**, **Vite**, ou **Bun**.

Il permet de manipuler des bases de données relationnelles (**PostgreSQL, MySQL, SQLite**) directement dans du code TypeScript, tout en gardant une forte sécurité de type et une syntaxe SQL explicite.

---

## Principales caractéristiques

| Caractéristique      | Détail                                                                 |
| -------------------- | ---------------------------------------------------------------------- |
| Langage principal    | TypeScript                                                             |
| Bases supportées     | PostgreSQL, MySQL, SQLite                                              |
| Typage               | Très strict, intégration complète avec TypeScript                      |
| Génération SQL       | SQL clair et explicite, visible dans le code                           |
| Objectif             | Simplicité, sécurité, performances                                     |
| Cas d’usage typiques | Next.js App Router, Edge Functions, environnements serverless modernes |

---

## Exemple simple (SQLite)

```ts
import { sqliteTable, text, integer } from "drizzle-orm/sqlite-core";
import { drizzle } from "drizzle-orm/sqlite";
import sqlite3 from "sqlite3";

// Définition de la table
const users = sqliteTable("users", {
  id: integer("id").primaryKey(),
  name: text("name"),
  age: integer("age"),
});

// Connexion à une base SQLite en mémoire
const db = drizzle(new sqlite3.Database(":memory:"));

// Insertion d'un utilisateur
await db.insert(users).values({ name: "Alice", age: 25 });

// Récupération des utilisateurs
const result = await db.select().from(users);
console.log(result);
```

---

## Avantages

* Lisibilité du SQL dans le code TypeScript
* Sécurité de type très forte : les erreurs sont détectées dès le développement
* Compatible avec les runtimes modernes (Edge, Bun, Deno)
* Pas besoin de fichier de schéma externe : le schéma est écrit dans le code

---

## Comparaison avec d'autres ORMs TypeScript

| ORM     | Typage TypeScript | SQL explicite | Génération automatique de types | Poids |
| ------- | ----------------- | ------------- | ------------------------------- | ----- |
| Prisma  | Bon               | Non           | Oui                             | Moyen |
| Drizzle | Excellent         | Oui           | Oui                             | Léger |
| TypeORM | Moyen             | Non           | Non                             | Moyen |


