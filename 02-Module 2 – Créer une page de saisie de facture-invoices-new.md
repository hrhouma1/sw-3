# <h1 id="module-2">Module 2 – Création du formulaire « Create Invoice » (`/invoices/new`)</h1>



## <h2 id="objectif">Objectif pédagogique</h2>

À la fin de ce module, l’étudiant doit être capable de :

1. Vérifier ou installer Tailwind CSS dans un projet Next 15.
2. Générer une route **App Router** pour `/invoices/new`.
3. Installer et utiliser des composants **`shadcn/ui`**.
4. Construire un formulaire complet, stylé et fonctionnel.
5. Préparer l’intégration de **React Hook Form** en vue de la validation et de la soumission.

---

## <h2 id="prerequis">Pré-requis techniques</h2>

* **Node ≥ 18** et **npm** (ou pnpm / yarn).
* Projet Next 15 déjà généré :

  ```bash
  npx create-next-app@latest my-invoicing-app
  ```
* Option *Tailwind CSS* cochée **Yes** lors de la génération.

  > Si ce n’est pas le cas, suivre la **section A** (Installation manuelle de Tailwind) avant de passer à la suite.

---

## <h2 id="section-a">Section A – Installation manuelle de Tailwind (si absent)</h2>

> **À exécuter uniquement si `tailwind.config.ts` n’existe pas déjà dans le projet.**

1. Installer les dépendances :

   ```bash
   npm install -D tailwindcss postcss autoprefixer
   npx tailwindcss init -p
   ```

2. Modifier `tailwind.config.ts` :

   ```ts
   /** @type {import('tailwindcss').Config} */
   export default {
     content: ["./src/**/*.{ts,tsx,js,jsx}"],
     theme: { extend: {} },
     plugins: [],
   };
   ```

3. Vérifier / créer `src/styles/globals.css` et y placer :

   ```css
   @tailwind base;
   @tailwind components;
   @tailwind utilities;
   ```

4. Redémarrer le serveur de développement :

   ```bash
   npm run dev
   ```

---

## <h2 id="etape-1">Étape 1 – Création de l’arborescence `/invoices/new`</h2>

1. Dans `src/app`, créer les dossiers :

   ```
   invoices/
     └── new/
         └── page.tsx
   ```

2. Contenu minimal de `page.tsx` (test de la route) :

   ```tsx
   export default function Page() {
     return <h1 className="text-3xl font-bold">Create Invoice</h1>;
   }
   ```

3. Vérification :
   Naviguer vers `http://localhost:3000/invoices/new`.
   Le titre « Create Invoice » doit s’afficher.

---

## <h2 id="etape-2">Étape 2 – Installation des composants `shadcn/ui`</h2>

1. Initialiser le CLI (si ce n’est pas encore fait) :

   ```bash
   npx shadcn@latest init
   # Répondre aux invites : TypeScript ? → yes, Tailwind → yes, Directory → src
   ```

2. Ajouter les composants nécessaires au formulaire :

   ```bash
   npx shadcn@latest add input label textarea button
   ```

   Des fichiers apparaissent dans `src/components/ui/`.

---

## <h2 id="etape-3">Étape 3 – Formulaire statique (version fonctionnelle + styles exacts)</h2>

Copier entièrement ce code dans `src/app/invoices/new/page.tsx` :

```tsx
import { Label } from "@/components/ui/label";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";

export default function Page() {
  return (
    <main className="p-10 max-w-xl mx-auto">
      <h1 className="text-3xl font-bold mb-8">Create a New Invoice</h1>

      <form className="space-y-6">
        {/* Billing Name */}
        <div>
          <Label htmlFor="name" className="block font-semibold text-sm mb-2">
            Billing Name
          </Label>
          <Input id="name" name="name" type="text" className="w-full" />
        </div>

        {/* Billing Email */}
        <div>
          <Label htmlFor="email" className="block font-semibold text-sm mb-2">
            Billing Email
          </Label>
          <Input id="email" name="email" type="email" className="w-full" />
        </div>

        {/* Value */}
        <div>
          <Label htmlFor="value" className="block font-semibold text-sm mb-2">
            Value
          </Label>
          <Input id="value" name="value" type="number" className="w-full" />
        </div>

        {/* Description */}
        <div>
          <Label
            htmlFor="description"
            className="block font-semibold text-sm mb-2"
          >
            Description
          </Label>
          <Textarea
            id="description"
            name="description"
            rows={4}
            className="w-full"
          />
        </div>

        {/* Submit */}
        <div>
          <Button type="submit" className="w-full font-semibold">
            Submit
          </Button>
        </div>
      </form>
    </main>
  );
}
```

### Contrôle de fonctionnement

1. Lancer `npm run dev` (ou laisser tourner).
2. Rafraîchir `http://localhost:3000/invoices/new`.
3. Vérifier :

   * Alignement à gauche du titre et du formulaire.
   * Champs pleine largeur (`w-full`).
   * Bouton noir (`Button` shadcn par défaut) pleine largeur, texte en gras.

---

## <h2 id="etape-4">Étape 4 – Préparation à React Hook Form</h2>

1. Installer :

   ```bash
   npm install react-hook-form
   ```

2. Concepts à maîtriser (lecture obligatoire avant la prochaine séance) :

   | Fonction           | Rôle                                              |
   | ------------------ | ------------------------------------------------- |
   | `useForm()`        | Initialise le contexte du formulaire              |
   | `register()`       | Lie un champ HTML/React au state interne          |
   | `handleSubmit(fn)` | Exécute `fn` si la validation passe               |
   | `formState.errors` | Objet listant les erreurs de validation par champ |

3. Travaux dirigés à préparer :

   * Ajouter `useForm()` dans `page.tsx`.
   * Afficher en console les valeurs au `console.log(data)` après soumission.
   * Rendre le champ **Billing Email** obligatoire et de format e-mail.




