# <h1 id="module-2">Module 2 – Créer une page de saisie de facture (`/invoices/new`)</h1>

---

## <h2 id="objectif">Objectif pédagogique</h2>

À la fin de ce module, l’étudiant sera capable de :

* Créer une route dynamique dans l’arborescence App Router (`/invoices/new`)
* Utiliser des composants visuels issus de `shadcn/ui`
* Créer un formulaire simple et réutilisable pour soumettre des données
* Préparer l’intégration future avec un hook de formulaire (`react-hook-form`)

---

## <h2 id="etape-1-arborescence">Étape 1 – Créer l’arborescence `app/invoices/new/page.tsx`</h2>

Créer les dossiers suivants dans `src/app` :

```
src/
└── app/
    └── invoices/
        └── new/
            └── page.tsx
```

Le fichier `page.tsx` contiendra la page de création d'une nouvelle facture.

---

## <h2 id="etape-2-base-page-tsx">Étape 2 – Contenu initial de `page.tsx`</h2>

```tsx
export default function Page() {
  return (
    <main className="p-10">
      <h1 className="text-3xl font-bold mb-6">Create a New Invoice</h1>
    </main>
  );
}
```

Tester dans le navigateur à l’adresse :
[http://localhost:3000/invoices/new](http://localhost:3000/invoices/new)

---

## <h2 id="etape-3-ajout-de-shadcn-ui">Étape 3 – Installer les composants UI nécessaires (`shadcn/ui`)</h2>

Dans le terminal :

```bash
npx shadcn@latest add input label textarea button
```

Cela va générer les composants réutilisables suivants dans `src/components/ui/` :

* `input.tsx`
* `label.tsx`
* `textarea.tsx`
* `button.tsx`

---

## <h2 id="etape-4-formulaire-brut">Étape 4 – Ajouter le formulaire dans `page.tsx`</h2>

Voici une version fonctionnelle **statique** sans hook de validation pour l’instant :

```tsx
import { Label } from "@/components/ui/label";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";

export default function Page() {
  return (
    <main className="p-10 max-w-xl mx-auto">
      <h1 className="text-3xl font-bold mb-6">Create a New Invoice</h1>
      <form className="space-y-4">
        <div>
          <Label htmlFor="name">Billing Name</Label>
          <Input id="name" name="name" />
        </div>
        <div>
          <Label htmlFor="email">Billing Email</Label>
          <Input id="email" name="email" type="email" />
        </div>
        <div>
          <Label htmlFor="value">Value</Label>
          <Input id="value" name="value" type="number" />
        </div>
        <div>
          <Label htmlFor="description">Description</Label>
          <Textarea id="description" name="description" />
        </div>
        <Button type="submit">Submit</Button>
      </form>
    </main>
  );
}
```

---

## <h2 id="etape-5-exploration-react-hook-form">Étape 5 – Préparer la suite : React Hook Form</h2>

Les étudiants doivent maintenant :

1. Lire la documentation officielle :
   [https://react-hook-form.com/get-started](https://react-hook-form.com/get-started)

2. Comprendre les concepts suivants :

   * `useForm()`
   * `register()`
   * `handleSubmit()`
   * `formState.errors`

3. Installer la dépendance dans le projet :

```bash
npm install react-hook-form
```

