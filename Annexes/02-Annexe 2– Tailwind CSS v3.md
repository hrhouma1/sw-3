# Cours Tailwind CSS v3 avec Next.js 15 – Niveau 0

**Objectif** : Apprendre à utiliser Tailwind CSS version 3 pour styliser un site web avec Next.js 15, étape par étape, sans aucune connaissance préalable.


# Avant de commencer


## <h1 id="definition">1. Définition</h1>

**Tailwind CSS** est un **framework CSS utilitaire**.
Il fournit une large collection de **classes CSS prédéfinies** que tu peux appliquer directement dans ton HTML pour styliser rapidement des éléments, sans écrire de CSS personnalisé.

Au lieu d'utiliser des classes abstraites comme `.card`, `.header`, ou `.button`, tu composes ton design en utilisant des **petites classes très spécifiques** comme `text-lg`, `bg-red-500`, `p-4`, etc.



## <h2 id="exemple">2. Exemple comparatif</h2>

### Sans Tailwind CSS (avec un fichier CSS personnalisé) :

```html
<!-- HTML -->
<button class="btn">Envoyer</button>
```

```css
/* CSS */
.btn {
  background-color: #3b82f6;
  color: white;
  padding: 0.5rem 1rem;
  border-radius: 0.375rem;
  font-weight: bold;
}
```

### Avec Tailwind CSS :

```html
<button class="bg-blue-500 text-white py-2 px-4 rounded font-bold">
  Envoyer
</button>
```

Tu n’écris plus de règles CSS. Tu utilises les classes Tailwind directement.



## <h2 id="avantages">3. Avantages</h2>

* **Productivité élevée** : tu développes des interfaces rapidement.
* **Pas besoin de fichier CSS séparé**.
* **Contrôle précis** sur chaque propriété CSS.
* **Design responsive facile** avec des préfixes (`sm:`, `md:`, `lg:`…).
* **Personnalisable** via un fichier de configuration (`tailwind.config.js`).



## <h2 id="inconvenients">4. Inconvénients</h2>

* Le HTML devient parfois **verbeux** (beaucoup de classes à la suite).
* Courbe d’apprentissage pour comprendre les conventions (`bg-gray-100`, `px-6`, `space-y-4`, etc.).
* Nécessite un système de **compilation CSS** avec PostCSS, Vite, Webpack ou autre.



## <h2 id="comment-ca-marche">5. Comment ça fonctionne</h2>

Tu crées un fichier CSS principal comme ceci :

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Puis tu compiles ce fichier avec un outil (Vite, Webpack, CLI Tailwind).

Enfin, tu écris ton HTML comme ceci :

```html
<div class="max-w-xl mx-auto p-4 bg-white shadow rounded">
  <h1 class="text-2xl font-bold mb-4">Bienvenue</h1>
  <p class="text-gray-700">Ceci est un exemple de contenu.</p>
</div>
```


## <h2 id="ressources">6. Ressource officielle</h2>

* Documentation complète : [https://tailwindcss.com/docs](https://tailwindcss.com/docs)


<br/>

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















<br/>

# Partie 4 - exercices

# Exercices Tailwind CSS – Débutants absolus

> **Prérequis** : Projet Next.js 15 configuré avec Tailwind v3, comme vu précédemment.

---

## Exercice 1 – Changer la couleur de fond et du texte

**Objectif** : Modifier les couleurs en utilisant `bg-*` et `text-*`.

### Étapes :

1. Ouvre le fichier `src/app/page.tsx`
2. Crée un bloc `<div>` avec cette structure :

```jsx
<div className="min-h-screen bg-gray-50 flex items-center justify-center">
  <p className="text-blue-700 text-xl">Bienvenue dans le monde Tailwind</p>
</div>
```

3. Change `bg-gray-50` par `bg-yellow-100`, `bg-red-200`, etc.
4. Change `text-blue-700` par `text-green-800`, `text-red-600`, etc.

---

## Exercice 2 – Ajouter du padding et des marges

**Objectif** : Utiliser `p-*`, `m-*`, `mt-*`, `mb-*`.

### Étapes :

1. Ajoute ce bloc dans la page :

```jsx
<div className="bg-white p-6 m-4 shadow rounded">
  <p className="text-gray-700">Ce bloc a du padding et de la marge.</p>
</div>
```

2. Essaie différentes valeurs :

   * `p-2`, `p-8`
   * `m-0`, `mt-10`, `mx-auto`

---

## Exercice 3 – Créer un bouton stylisé

**Objectif** : Utiliser `bg-*`, `hover:*`, `rounded`, `text-*`, `px-*`, `py-*`.

### Étapes :

1. Ajoute ce bouton :

```jsx
<button className="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">
  Cliquez ici
</button>
```

2. Expérimente :

   * Changer les couleurs
   * Modifier la taille (`px-*`, `py-*`)
   * Ajouter une bordure (`border`, `border-black`)

---

## Exercice 4 – Centrer un bloc avec `flex`

**Objectif** : Apprendre `flex`, `justify-center`, `items-center`.

### Étapes :

1. Ajoute ceci dans la page :

```jsx
<div className="min-h-screen flex items-center justify-center bg-gray-100">
  <div className="bg-white p-8 rounded shadow">
    <p className="text-lg font-medium">Contenu centré</p>
  </div>
</div>
```

2. Change `justify-center` par `justify-start`, `justify-end` pour observer les effets.

---

## Exercice 5 – Créer une carte responsive

**Objectif** : Utiliser `max-w-*`, `text-*`, `mt-*`, `rounded`, `shadow`, `sm:`.

### Étapes :

1. Colle ce code :

```jsx
<div className="max-w-sm mx-auto mt-10 bg-white rounded-lg shadow p-6">
  <h2 className="text-2xl font-bold text-gray-800">Titre de la carte</h2>
  <p className="text-gray-600 mt-2">Texte de démonstration dans une carte responsive.</p>
</div>
```

2. Ajoute une règle responsive :

```jsx
<div className="max-w-sm sm:max-w-md md:max-w-lg lg:max-w-xl mx-auto mt-10 bg-white rounded-lg shadow p-6">
```

Observe comment la carte s’adapte à la largeur de l’écran.

---

## Exercice 6 – Créer un formulaire basique

**Objectif** : Appliquer Tailwind à des éléments de formulaire.

### Étapes :

```jsx
<form className="max-w-md mx-auto p-4 bg-gray-50 rounded shadow mt-10">
  <label className="block mb-2 text-sm font-medium text-gray-700">Nom</label>
  <input className="w-full p-2 border border-gray-300 rounded mb-4" type="text" />

  <label className="block mb-2 text-sm font-medium text-gray-700">Email</label>
  <input className="w-full p-2 border border-gray-300 rounded mb-4" type="email" />

  <button className="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700">Envoyer</button>
</form>
```

---

## Exercice 7 – Utiliser un layout de colonne avec `grid`

**Objectif** : Disposer plusieurs blocs côte à côte.

```jsx
<div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4 p-6">
  <div className="bg-red-100 p-4">Bloc 1</div>
  <div className="bg-blue-100 p-4">Bloc 2</div>
  <div className="bg-green-100 p-4">Bloc 3</div>
</div>
```



<br/>


# Partie 5 - Quiz Tailwind CSS v3

**Instructions** : Choisis la bonne réponse parmi les propositions. Il n’y a qu’une seule bonne réponse par question.



### 1. Que signifie la classe `bg-blue-100` ?

A) Un texte bleu pâle
B) Une bordure bleue
C) Un fond bleu clair
D) Un fond bleu foncé

---

### 2. Que fait `text-center` ?

A) Centre un bloc horizontalement
B) Centre le texte à l’intérieur d’un élément
C) Centre verticalement
D) Ne fait rien

---

### 3. Que signifie `p-4` ?

A) Padding horizontal de 4px
B) Padding vertical de 4px
C) Padding de 16px dans toutes les directions
D) Margin de 4px

---

### 4. Que signifie `mt-8` ?

A) Margin top = 8px
B) Margin top = 32px
C) Margin total = 8px
D) Padding top = 8px

---

### 5. Que fait `rounded-lg` ?

A) Ajoute un ombrage
B) Rend le texte en italique
C) Rend les coins arrondis
D) Centre le texte

---

### 6. Quelle est la classe pour rendre un texte gras ?

A) `text-bold`
B) `font-weight`
C) `font-bold`
D) `bold-text`

---

### 7. `mx-auto` est utilisé pour :

A) Ajouter une marge verticale
B) Centrer un élément horizontalement
C) Mettre un élément en plein écran
D) Ajouter un padding horizontal

---

### 8. Quelle classe crée une ombre moyenne ?

A) `shadow`
B) `shadow-md`
C) `shadow-sm`
D) `shadow-lg`

---

### 9. Quelle classe rend un fond blanc ?

A) `bg-white`
B) `bg-100`
C) `bg-gray-0`
D) `background-white`

---

### 10. `flex` est utilisé pour :

A) Ajouter une bordure
B) Appliquer une animation
C) Disposer des éléments en ligne ou en colonne
D) Rendre le texte flexible

---

### 11. Quelle classe augmente la taille du texte ?

A) `text-big`
B) `font-size-lg`
C) `text-lg`
D) `large-text`

---

### 12. Pour centrer à la fois horizontalement et verticalement :

A) `center-all`
B) `flex items-center justify-center`
C) `text-center`
D) `mx-auto my-auto`

---

### 13. Que fait `grid-cols-3` ?

A) Crée 3 rangées
B) Crée 3 colonnes
C) Crée une marge de 3px
D) Applique 3 paddings

---

### 14. Que fait `w-full` ?

A) Largeur automatique
B) Largeur de 100% du parent
C) Largeur de 100px
D) Largeur fixe

---

### 15. `hover:bg-blue-700` signifie :

A) Changer de texte au survol
B) Appliquer une animation
C) Changer le fond en bleu foncé au survol
D) Ajouter un bouton

---

### 16. `text-gray-600` signifie :

A) Texte invisible
B) Texte en gris clair
C) Texte en gris foncé
D) Texte en gras

---

### 17. Que fait `py-2` ?

A) Padding horizontal de 2px
B) Padding vertical de 8px
C) Margin vertical de 2px
D) Padding vertical de 2px

---

### 18. Pour faire un bouton de base, on peut utiliser :

A) `button`
B) `bg-blue-500 text-white px-4 py-2 rounded`
C) `text-bold shadow-full`
D) `btn btn-primary`

---

### 19. Quelle classe applique une bordure grise ?

A) `border-gray-300`
B) `border:gray-300`
C) `border-300-gray`
D) `gray-border-300`

---

### 20. Quelle est la classe Tailwind pour afficher un bloc caché ?

A) `display: none`
B) `d-none`
C) `hidden`
D) `invisible`

---

### 21. À quoi sert `gap-4` dans une grille ou un flex ?

A) Ajoute un padding entre les éléments
B) Ajoute un espacement entre les colonnes ou lignes
C) Modifie la couleur
D) Aligne les éléments

---

### 22. `sm:bg-red-100` signifie :

A) Applique un fond rouge sur tous les écrans
B) Applique un fond rouge uniquement sur petits écrans
C) Applique un fond rouge dès 640px et plus
D) Ne fait rien

---

### 23. `text-sm` correspond à :

A) Très grand texte
B) Texte en majuscules
C) Petite taille de police
D) Texte barré

---

### 24. Pour ajouter une bordure arrondie complète :

A) `rounded`
B) `rounded-xl`
C) `rounded-full`
D) `rounded-999`

---

### 25. `max-w-md` signifie :

A) Largeur maximale = moyen
B) Largeur de l’écran
C) Pleine largeur
D) Marge externe moyenne

---

### 26. `overflow-hidden` sert à :

A) Masquer ce qui dépasse d’un bloc
B) Ajouter un ascenseur
C) Centrer un élément
D) Afficher tout le contenu

---

### 27. `aspect-square` signifie :

A) Rendre le bloc circulaire
B) Appliquer une hauteur fixe
C) Appliquer un rapport largeur/hauteur de 1/1
D) Appliquer un padding carré

---

### 28. Pour appliquer une animation sur survol :

A) `hover:` + une classe
B) `@hover`
C) `hovering-class`
D) `transition-on-hover`

---

### 29. `divide-y` dans une liste sert à :

A) Diviser le texte
B) Ajouter une séparation horizontale entre les enfants
C) Supprimer les marges
D) Ajouter une animation

---

### 30. Quelle est la meilleure méthode pour créer une interface responsive ?

A) Ajouter des media queries manuellement
B) Utiliser les classes `sm:`, `md:`, `lg:` de Tailwind
C) Utiliser JavaScript
D) Utiliser uniquement `flex`


