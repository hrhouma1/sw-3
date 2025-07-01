# Cours Tailwind CSS v3 avec Next.js 15 – Niveau 0

**Objectif** : Apprendre à utiliser Tailwind CSS version 3 pour styliser un site web avec Next.js 15, étape par étape, sans aucune connaissance préalable.



# 1. Créer un projet Next.js 15

Ouvre un terminal et exécute les commandes suivantes :

```bash
npx create-next-app@latest mon-projet-tailwind
cd mon-projet-tailwind
```

Lorsque l’outil de création te pose des questions, réponds ainsi :

* TypeScript ? → Non (`n`)
* ESLint ? → Oui (`y`)
* Tailwind CSS ? → Non (`n`) (on l’installera nous-mêmes)
* App Router ? → Oui (`y`)
* src/ directory ? → Oui (`y`)
* Import alias ? → Non (`n`)



# 2. Installer Tailwind CSS v3

Toujours dans le terminal, installe Tailwind :

```bash
npm install -D tailwindcss@3 postcss autoprefixer
npx tailwindcss init -p
```

Cela crée deux fichiers :

* `tailwind.config.js`
* `postcss.config.js`



# 3. Configurer Tailwind CSS

Ouvre le fichier `tailwind.config.js` et remplace son contenu par :

```js
/** @type {import('tailwindcss').Config} */
module.exports = {
  content: [
    "./src/**/*.{js,ts,jsx,tsx}"
  ],
  theme: {
    extend: {},
  },
  plugins: [],
}
```

Puis, ouvre le fichier `./src/app/globals.css`, supprime tout son contenu et écris ceci :

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```



# 4. Tester que Tailwind fonctionne

Ouvre le fichier `./src/app/page.tsx` (ou `.js` si tu n’as pas choisi TypeScript).

Remplace son contenu par :

```jsx
export default function Home() {
  return (
    <div className="min-h-screen bg-gray-100 flex items-center justify-center">
      <h1 className="text-4xl font-bold text-blue-600">
        Hello Tailwind v3
      </h1>
    </div>
  )
}
```

Ensuite, lance le projet avec la commande suivante :

```bash
npm run dev
```

Ouvre un navigateur et visite l’adresse [http://localhost:3000](http://localhost:3000).
Tu devrais voir un fond gris clair et un titre bleu centré sur l’écran.



# 5. Mini guide des classes Tailwind utiles

| Objectif                 | Classe Tailwind                          |
| ------------------------ | ---------------------------------------- |
| Couleur de fond          | `bg-blue-500`, `bg-gray-100`             |
| Couleur du texte         | `text-white`, `text-blue-600`            |
| Taille du texte          | `text-sm`, `text-xl`, `text-4xl`         |
| Marge interne (padding)  | `p-4`, `px-2`, `py-6`                    |
| Marge externe (margin)   | `m-4`, `mt-2`, `mx-8`                    |
| Centrage horizontal      | `mx-auto`                                |
| Centrage vertical + flex | `flex`, `items-center`, `justify-center` |
| Texte en gras            | `font-bold`                              |
| Bords arrondis           | `rounded`, `rounded-lg`                  |
| Ombres                   | `shadow`, `shadow-lg`                    |



# 6. Exemple de carte avec Tailwind

Composant de démonstration :

```jsx
export default function Carte() {
  return (
    <div className="bg-white shadow-md rounded-lg p-6 max-w-sm mx-auto mt-10">
      <h2 className="text-2xl font-semibold text-gray-800">Bienvenue !</h2>
      <p className="text-gray-600 mt-2">Ceci est une carte stylisée avec Tailwind CSS.</p>
    </div>
  )
}
```

Pour comprendre chaque classe utilisée (`bg-white`, `shadow-md`, etc.), une explication ligne par ligne est fournie séparément. Si tu le souhaites, je peux regrouper toutes les explications techniques dans une fiche dédiée.



# 7. Étapes suivantes recommandées

Lorsque tu seras à l’aise avec les bases, voici les sujets à explorer :

* Layouts responsives (`grid`, `flex`, `sm:`, `md:`)
* Plugins officiels de Tailwind : typographie, formulaires, aspect-ratio
* Création d’une interface complète : en-tête, pied de page, boutons, formulaire




<br/>

---------------------
# Partie 2
---------------------

## Exemple complet : carte centrée et stylisée avec Tailwind v3

### Code React (dans un fichier `.tsx` ou `.jsx`) :

```jsx
export default function Carte() {
  return (
    <div className="bg-white shadow-md rounded-lg p-6 max-w-sm mx-auto mt-10">
      <h2 className="text-2xl font-semibold text-gray-800">Bienvenue !</h2>
      <p className="text-gray-600 mt-2">Ceci est une carte stylée avec Tailwind CSS.</p>
    </div>
  )
}
```


# Explication de chaque classe utilisée (débutant absolu)

### `bg-white`

* Définit la **couleur de fond**.
* `bg-white` signifie que le fond est **blanc**.
* Tu peux changer par exemple en `bg-red-100` pour un fond rouge très pâle.



### `shadow-md`

* Ajoute une **ombre** autour du bloc.
* Cela donne un **effet de profondeur** comme une carte en relief.
* `shadow-sm`, `shadow-lg` existent aussi, pour des ombres plus petites ou plus grandes.



### `rounded-lg`

* Ajoute des **coins arrondis**.
* `lg` signifie que les coins sont **modérément arrondis**.
* Tu peux tester aussi `rounded`, `rounded-md`, `rounded-full`.



### `p-6`

* Signifie **padding = 6**.
* Le padding est l'**espace intérieur** du bloc (entre le bord et le contenu).
* `p` veut dire "padding dans toutes les directions".
* `p-6` correspond à environ `1.5rem = 24px`.



### `max-w-sm`

* Limite la **largeur maximale** de la carte.
* `sm` est une taille prédéfinie par Tailwind, ici environ `24rem = 384px`.
* Cela empêche la carte d’être trop large sur grand écran.



### `mx-auto`

* `m` signifie **marge** (margin).
* `x` veut dire **horizontal** (gauche + droite).
* `auto` centre horizontalement l’élément dans son conteneur.
* Très utilisé pour **centrer un bloc**.



### `mt-10`

* `m` = marge, `t` = top (haut).
* `mt-10` signifie que l'on ajoute une **grande marge au-dessus** (environ `2.5rem = 40px`).
* Cela **espace la carte du haut de la page**.



### `text-2xl`

* Définit la **taille du texte**.
* `2xl` = deux fois plus grand que le texte normal.


### `font-semibold`

* `font` = police de caractères.
* `semibold` = semi-gras.
* Moins lourd que `font-bold`, mais plus visible que `font-normal`.



### `text-gray-800`

* Définit la **couleur du texte** en gris très foncé.
* Plus la valeur est élevée (`800`), plus le gris est **proche du noir**.



### `text-gray-600 mt-2`

* `text-gray-600` = gris un peu plus clair, utilisé pour les textes secondaires.
* `mt-2` = ajoute une **petite marge verticale au-dessus du paragraphe** (environ `0.5rem = 8px`).



## Résultat attendu

Tu dois voir une **boîte blanche**, **centrée horizontalement**, avec du texte bien espacé, une ombre autour et des coins arrondis. C’est la base de **toute mise en page web moderne**.











<br/>

---------------------
# Partie 3
---------------------




#  1. Les couleurs : `bg-red-100`, `text-gray-800`, etc.

### Structure :

```txt
bg-[couleur]-[intensité]
```

### Exemple :

```css
bg-red-100
```

* `bg-` = background (fond)
* `red` = couleur de base (rouge)
* `100` = intensité

### Pourquoi 100 et pas 99 ?

Tailwind propose une **échelle standardisée** d’intensités. Les valeurs autorisées sont :

```
50, 100, 200, 300, 400, 500, 600, 700, 800, 900, 950
```

* Plus le nombre est **petit** → couleur **très claire** (presque blanche)
* Plus le nombre est **grand** → couleur **très foncée**

**Exemples** :

| Classe       | Couleur approximative |
| ------------ | --------------------- |
| `bg-red-50`  | Rouge très pâle       |
| `bg-red-500` | Rouge normal          |
| `bg-red-900` | Rouge très foncé      |

Tu ne peux pas écrire `bg-red-99` ou `bg-red-110`. Tailwind ne reconnaît **que les valeurs définies** dans sa palette. Il faut choisir dans la liste.


# 2. Les espacements : `p-6`, `mt-2`, etc.

### Structure :

```txt
[p | m][t | b | x | y]-[valeur]
```

* `p` = padding (espace **à l’intérieur**)
* `m` = margin (espace **à l’extérieur**)
* `t` = top (haut), `b` = bottom (bas), `x` = horizontal, `y` = vertical

### Exemple :

```css
p-6
```

* `p` = padding
* `6` = unité prédéfinie

### Quelles sont les valeurs autorisées ?

Elles suivent un système basé sur un multiple de **4 pixels**. Voici quelques valeurs utiles :

| Classe | Taille réelle |
| ------ | ------------- |
| `p-0`  | 0px           |
| `p-1`  | 4px           |
| `p-2`  | 8px           |
| `p-3`  | 12px          |
| `p-4`  | 16px          |
| `p-5`  | 20px          |
| `p-6`  | 24px          |
| `p-8`  | 32px          |
| `p-10` | 40px          |
| `p-12` | 48px          |

Tu ne peux pas faire `p-5.5` ou `p-7.3`. Tailwind ne gère **que des valeurs prédéfinies**, pour garder un design cohérent.



# 3. Les largeurs maximales : `max-w-sm`

### Structure :

```txt
max-w-[taille]
```

### Exemples utiles :

| Classe      | Taille maximale |
| ----------- | --------------- |
| `max-w-xs`  | 20rem (320px)   |
| `max-w-sm`  | 24rem (384px)   |
| `max-w-md`  | 28rem (448px)   |
| `max-w-lg`  | 32rem (512px)   |
| `max-w-xl`  | 36rem (576px)   |
| `max-w-2xl` | 42rem (672px)   |

Ces tailles sont pensées pour créer des designs responsives sans avoir à gérer des pixels bruts.



# 4. Les tailles de texte : `text-2xl`, `text-sm`, etc.

| Classe      | Taille réelle        |
| ----------- | -------------------- |
| `text-xs`   | Très petit (0.75rem) |
| `text-sm`   | Petit (0.875rem)     |
| `text-base` | Normal (1rem)        |
| `text-lg`   | Grand (1.125rem)     |
| `text-xl`   | Plus grand           |
| `text-2xl`  | Très grand           |
| `text-4xl`  | Titre principal      |


# 5. Les breakpoints (responsive) : `sm:`, `md:`, etc.

### Structure :

```txt
sm:bg-blue-500
```

Cela signifie : “Applique `bg-blue-500` **à partir du petit écran** (sm)”.

### Liste des points de rupture Tailwind :

| Nom   | Taille d'écran minimale |
| ----- | ----------------------- |
| `sm`  | 640px                   |
| `md`  | 768px                   |
| `lg`  | 1024px                  |
| `xl`  | 1280px                  |
| `2xl` | 1536px                  |

### Exemple pratique :

```html
<div class="bg-red-100 sm:bg-blue-100 md:bg-green-100">
```

* Écran < 640px : rouge clair
* Écran ≥ 640px : bleu clair
* Écran ≥ 768px : vert clair


