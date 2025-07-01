## Gestion complète des erreurs : 404, paramètre invalide et *error boundary* Next 15

*(à mettre en place **avant** d’ajouter l’authentification)*

---

### 1. Page dynamique `src/app/invoices/[invoiceId]/page.tsx`

```tsx
import { notFound }        from "next/navigation";
import { eq }              from "drizzle-orm";
import { db }              from "@/db";
import { Invoices }        from "@/db/schema";

export default async function InvoicePage(
  { params }: { params: { invoiceId: string } },
) {
  /* 1. Vérifier que l’ID est un entier positif  */
  const invoiceId = Number(params.invoiceId);
  if (!Number.isInteger(invoiceId) || invoiceId <= 0) {
    // On lève une ERREUR 500 (sera capturée par l’error boundary)
    throw new Error("Invalid invoice ID");
  }

  /* 2. Chercher la facture ; on sait qu’il n’y en aura qu’une */
  const [invoice] = await db
    .select()
    .from(Invoices)
    .where(eq(Invoices.id, invoiceId))
    .limit(1);

  /* 3. Si aucune facture → 404 automatiquement géré par Next */
  if (!invoice) notFound();

  /* 4. Affichage normal */
  return (
    <main className="max-w-5xl mx-auto my-12">
      {/* …contenu de la facture… */}
    </main>
  );
}
```

---

### 2. *Error boundary* globale `src/app/error.tsx`

> * Le fichier doit être placé **à la racine** de `app` (ou dans n’importe quel sous-arbre si vous souhaitez un découpage plus fin).
> * Il **doit** être marqué `use client` : c’est un composant React côté client.

```tsx
"use client";

import { useEffect } from "react";
import NextError from "next/error";

/** Props imposées par Next >= 13.4 */
type GlobalErrorProps = {
  error: Error & { digest?: string };
  reset: () => void;            // permet de tenter un *retry*
};

export default function GlobalError({ error, reset }: GlobalErrorProps) {
  /* Log dans la console (ou envoyez à Sentry, LogRocket…) */
  useEffect(() => {
    console.error("💥 Global error boundary :", error);
  }, [error]);

  /* 
   * - On réutilise la page d’erreur « par défaut » de Next 
   *   pour garder un style cohérent. 
   * - On force un status 500 (erreur interne).
   * - Le titre reprend le `message` que vous avez lancé dans la page.
   */
  return (
    <NextError
      statusCode={500}
      title={error.message || "Something went wrong"}
    />
  );
}
```

*Comportement obtenu*

| Scénario                                      | Résultat                                                                  |
| --------------------------------------------- | ------------------------------------------------------------------------- |
| `/invoices/42` alors que l’ID 42 n’existe pas | **404** (mise en page *built-in* de Next)                                 |
| `/invoices/asdf` (ID non numérique)           | **500** + message « Invalid invoice ID » rendu par votre *error boundary* |
| Toute autre exception dans le composant       | **500** avec message correspondant                                        |

---

### 3. Pourquoi deux traitements ?

| Type de problème                       | Raisonnement                                                               | Action                                       |
| -------------------------------------- | -------------------------------------------------------------------------- | -------------------------------------------- |
| **Ressource inconnue** (query vide)    | Le client a bien demandé une route valide, mais la donnée n’existe pas.    | `notFound()` → 404                           |
| **Paramètre malformé** (ID non entier) | La requête est invalide : on ne veut pas divulguer si la ressource existe. | On lève une `Error` → *error boundary* → 500 |

*(Adaptez la stratégie selon vos besoins de sécurité : on peut décider de renvoyer 404 dans les deux cas pour ne rien révéler.)*

---

### 4. Tests rapides

```bash
# Doit renvoyer 404
curl -I http://localhost:3000/invoices/9999

# Doit renvoyer 500 + "Invalid invoice ID"
curl -I http://localhost:3000/invoices/notanumber
```

### 5. Étape suivante

Les fondations de gestion d’erreurs sont maintenant solides.
Vous pouvez passer sereinement au **module Auth** (NextAuth.js + middleware) : toutes les erreurs « UNAUTHENTICATED », « FORBIDDEN », etc. seront capturées proprement par le même *error boundary*.

