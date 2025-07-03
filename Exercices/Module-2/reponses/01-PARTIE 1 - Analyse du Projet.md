# RÉPONSES - PARTIE 1 : Analyse du Projet

## A) Architecture générale (5 points)

1. **Quel framework JavaScript est utilisé pour ce projet ?**
   Justifiez votre réponse en citant 3 éléments de la structure qui l'indiquent.

```
Le framework utilisé est Next.js. Les 3 éléments qui l'indiquent sont :
1. Le fichier next.config.ts à la racine du projet
2. La structure du dossier src/app/ qui utilise l'App Router de Next.js 13+
3. Les fichiers layout.tsx et page.tsx qui sont spécifiques à Next.js
```

2. **Identifiez les différentes couches de l'application (présentation, logique métier, données) et associez-les aux dossiers correspondants.**

```
• Couche de présentation : src/components/ (composants UI réutilisables)
• Couche logique métier : src/app/ (pages, routes API, logique d'application)
• Couche de données : src/db/ (schéma, connexion base de données) et drizzle/ (migrations)
```

<br/>
<br/>

## B) Routing et Pages (4 points)

3. **Listez toutes les routes disponibles dans cette application en analysant la structure du dossier `src/app/`.**

```
Routes disponibles :
• / (page d'accueil - src/app/page.tsx)
• /dashboard (tableau de bord - src/app/dashboard/page.tsx)
• /invoices (liste des factures - src/app/invoices/page.tsx)
• /invoices/new (création facture - src/app/invoices/new/page.tsx)
• /test (page de test - src/app/test/page.tsx)
• /test-api (test API - src/app/test-api/page.tsx)
• /api/invoices (API REST - src/app/api/invoices/route.ts)
```

4. **Quelle est la différence entre les fichiers `page.tsx` et `route.ts` ? Donnez un exemple concret de chaque.**

```
• page.tsx : définit une page avec interface utilisateur (rendu côté client/serveur)
  Exemple : src/app/dashboard/page.tsx affiche le tableau de bord
• route.ts : définit une route API REST (endpoints HTTP)
  Exemple : src/app/api/invoices/route.ts gère les requêtes GET/POST pour les factures
```

<br/>
<br/>

## C) Base de données et ORM (3 points)

5. **Quel ORM (Object-Relational Mapping) est utilisé ?**
   Quels fichiers/dossiers vous permettent de l'identifier ?

```
L'ORM utilisé est Drizzle ORM. Les éléments qui l'identifient :
• Le fichier drizzle.config.ts (configuration)
• Le dossier drizzle/ (migrations)
• Le fichier src/db/schema.ts (définition du schéma)
```

6. **À quoi sert le dossier `drizzle/` à la racine du projet ?**

```
Le dossier drizzle/ contient les migrations de base de données :
• Les fichiers SQL de migration (0000_military_eternals.sql)
• Le dossier meta/ avec les métadonnées et journaux des migrations
```

<br/>
<br/>

## D) Interface utilisateur (4 points)

7. **Quels outils ou bibliothèques sont utilisés pour le styling ?**
   Citez au moins deux indices dans la structure.

```
• TailwindCSS : fichier tailwind.config.js et postcss.config.mjs
• shadcn/ui : fichier components.json et structure components/ui/
• CSS global : src/app/globals.css
```

8. **Que représente le dossier `components/ui/` ?**
   Quelle approche de développement cela suggère-t-elle ?

```
Le dossier components/ui/ contient des composants UI réutilisables (button, input, label, textarea).
Cela suggère une approche de développement basée sur un système de design avec 
des composants modulaires et réutilisables (Design System).
```

---

## E) Configuration et outillage (4 points)

9. **Identifiez trois fichiers de configuration différents et expliquez brièvement leur rôle.**

```
• next.config.ts : Configuration de Next.js (build, optimisations)
• drizzle.config.ts : Configuration Drizzle ORM (connexion DB, migrations)
• tailwind.config.js : Configuration TailwindCSS (thème, classes personnalisées)
```

10. **Quelle est la fonction du fichier `components.json` ?**

```
Le fichier components.json configure shadcn/ui : il définit les chemins, 
les styles et les utilitaires utilisés par la CLI shadcn/ui pour générer
et installer les composants UI dans le projet.
```

<br/>
<br/>

## F) Composants et gestion des interfaces (4 points)

11. **Quelles sont les commandes utilisées pour ajouter de nouveaux composants UI à partir d'une librairie comme shadcn/ui ?**
    (Exemple : bouton, carte, formulaire...)

```
• npx shadcn@latest add button (ajouter un bouton)
• npx shadcn@latest add card (ajouter une carte)
• npx shadcn@latest add form (ajouter un formulaire)
• npx shadcn@latest add input (ajouter un champ de saisie)
```

12. **À partir de l'arborescence ASCII suivante, repérez les composants suivants : `button.tsx`, `form.tsx`, `card.tsx`.**

**Chemins à écrire :**

* `button.tsx` : src/components/ui/button.tsx
* `form.tsx` : src/components/ui/form/form.tsx
* `card.tsx` : src/components/ui/card.tsx

<br/>
<br/>

## Instructions

* Durée recommandée : 20 à 25 minutes
* Répondez de manière claire et structurée
* Points bonus : mentionnez toute convention ou outil supplémentaire observé



## Points bonus observés :

- **TypeScript** : Tous les fichiers utilisent l'extension .ts/.tsx
- **ESLint** : Configuration avec eslint.config.mjs pour la qualité du code
- **Node.js** : Gestion des dépendances avec package.json et package-lock.json
- **Convention de nommage** : Utilisation de kebab-case pour les dossiers et PascalCase pour les composants
- **Structure modulaire** : Séparation claire entre logique métier, présentation et données
- **API REST** : Implémentation d'une API suivant les standards REST 
