# <h1 id="module-4-5">Module 4 & 5 – Créer et insérer une facture avec **Server Actions**, puis générer / exécuter la migration</h1>

> Module 4 = formulaire terminé côté UI.
> Module 5 = logique serveur : insertion dans la table `invoices`.
> Étapes 6-7 ci-dessous : génération / exécution de la migration puis contrôle.

---

## <h2 id="1-arborescence">1. Fichiers ajoutés / modifiés</h2>

```
src/
├── app/
│   └── invoices/
│       ├── new/
│       │   └── page.tsx        # formulaire (client + server hybrid)
│       └── actions.ts          # <— NOUVEAU : server actions
└── db/
    ├── schema.ts               # table invoices / enum status
    └── index.ts                # instance db
drizzle.config.ts               # config CLI (déjà en place)
package.json                    # scripts generate / migrate
```

---

## <h2 id="2-actions-ts">2. `src/app/invoices/actions.ts` – action serveur</h2>

```ts
"use server";

import { db } from "@/db";
import { invoices } from "@/db/schema";
import { redirect } from "next/navigation";
import { sql } from "drizzle-orm";

/* 2.1 – Création d’une nouvelle facture
   ------------------------------------ */
export async function createInvoiceAction(formData: FormData) {
  /* 1. Extraction + typage */
  const customer = String(formData.get("name") ?? "").trim();
  const email    = String(formData.get("email") ?? "").trim().toLowerCase();
  const valueRaw = String(formData.get("value") ?? "0").replace(",", ".");
  const valueInt = Math.floor(parseFloat(valueRaw) * 100);   // 12.34  →  1234
  const description = String(formData.get("description") ?? "").trim();

  /* 2. Insertion */
  const result = await db
    .insert(invoices)
    .values({
      customer,
      email,
      value: valueInt,
      description,
      status: "open",
    })
    .returning({ id: invoices.id });

  /* 3. Redirection vers la page dynamique future */
  const newId = result[0]?.id;
  redirect(`/invoices/${newId}`);
}
```

---

## <h2 id="3-page-tsx">3. `src/app/invoices/new/page.tsx` – formulaire hybride</h2>

```tsx
/* -------------------------------------------------------------------------- */
/*  CLIENT + SERVER (progressive enhancement)                                 */
/* -------------------------------------------------------------------------- */
"use client";

import { useState, useTransition, FormEvent, SyntheticEvent } from "react";
import { useFormStatus } from "react-dom";
import { createInvoiceAction } from "@/app/invoices/actions";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";
import { Textarea } from "@/components/ui/textarea";
import { Button } from "@/components/ui/button";

/* -------------------------------------------------------------------------- */
/*  Bouton de soumission avec loader                                          */
/* -------------------------------------------------------------------------- */
function SubmitButton() {
  const { pending } = useFormStatus();
  return (
    <Button
      type="submit"
      aria-disabled={pending}
      className="w-full font-semibold relative"
    >
      <span className={pending ? "text-transparent" : ""}>Submit</span>
      {pending && (
        <span className="absolute inset-0 flex items-center justify-center">
          <svg
            className="animate-spin w-5 h-5 text-gray-400"
            viewBox="0 0 24 24"
          >
            <circle
              className="opacity-25"
              cx="12"
              cy="12"
              r="10"
              stroke="currentColor"
              strokeWidth="4"
            />
            <path
              className="opacity-75"
              fill="currentColor"
              d="M4 12a8 8 0 018-8v4l3-3-3-3v4A8 8 0 104 12z"
            />
          </svg>
        </span>
      )}
    </Button>
  );
}

/* -------------------------------------------------------------------------- */
/*  Formulaire                                                                */
/* -------------------------------------------------------------------------- */
export default function Page() {
  const [state, setState] = useState<"ready" | "pending">("ready");
  const [startTransition] = useTransition();

  async function handleSubmit(e: FormEvent<HTMLFormElement>) {
    if (state === "pending") return;          // anti-spam
    e.preventDefault();
    const form = e.currentTarget;
    const fd = new FormData(form);

    setState("pending");
    startTransition(async () => {
      await createInvoiceAction(fd);          // redirection interne
    });
  }

  return (
    <main className="p-10 max-w-xl mx-auto">
      <h1 className="text-3xl font-bold mb-8">Create Invoice</h1>

      <form action={createInvoiceAction} onSubmit={handleSubmit} className="space-y-6">
        <div>
          <Label htmlFor="name" className="block font-semibold text-sm mb-2">
            Billing Name
          </Label>
          <Input id="name" name="name" className="w-full" required />
        </div>

        <div>
          <Label htmlFor="email" className="block font-semibold text-sm mb-2">
            Billing Email
          </Label>
          <Input id="email" name="email" type="email" className="w-full" required />
        </div>

        <div>
          <Label htmlFor="value" className="block font-semibold text-sm mb-2">
            Value
          </Label>
          <Input id="value" name="value" type="number" min="0" step="0.01" className="w-full" required />
        </div>

        <div>
          <Label htmlFor="description" className="block font-semibold text-sm mb-2">
            Description
          </Label>
          <Textarea id="description" name="description" rows={4} className="w-full" />
        </div>

        <SubmitButton />
      </form>
    </main>
  );
}
```

### Comportement obtenu

| Scénario     | Résultat                                                                                                                                        |
| ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| JS activé    | Formulaire → appel `createInvoiceAction` ➜ insertion DB ➜ redirection `/invoices/:id` ➜ bouton animé (loader) ; clics répétés ignorés.          |
| JS désactivé | Formulaire natif : la balise `action={createInvoiceAction}` permet la soumission côté serveur sans JavaScript ; UX dégradée mais fonctionnelle. |

---

## <h2 id="4-scripts">4. Scripts de migration déjà en place</h2>

```jsonc
"scripts": {
  "generate": "drizzle-kit generate",
  "migrate":  "drizzle-kit migrate"
}
```

1. **Génération SQL**

   ```bash
   npm run generate
   ```
2. **Push vers Xata**

   ```bash
   npm run migrate
   ```

> Assurez-vous d’avoir `XATA_DATABASE_URL` dans `.env.local` et un réseau ouvert (port 443) vers Xata.

