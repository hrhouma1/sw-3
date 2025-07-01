# <h1 id="module-7">Module 7 – Gérer les « vrais » 404 & autres erreurs pour `/invoices/[id]`</h1>

Dans le module précédent nous avons créé la page dynamique `/invoices/[id]`.
Si l’ID n’existe pas, Next.js lève actuellement une erreur serveur.
Nous allons :

1. **Retourner un 404 propre** (`notFound()` + `not-found.tsx`)
2. Prévoir un **error boundary** (`error.tsx`) pour toute autre exception (ex. connexion BD)
3. Tester le tout.

---

## <h2 id="1-notFound">1. Utiliser `notFound()` dans `page.tsx`</h2>

Ajoutez la vérification *juste après* la requête BD :

```tsx
import { notFound } from "next/navigation";

/* … code existant … */

const invoice = await db
  .select()
  .from(invoices)
  .where(eq(invoices.id, invoiceId))
  .limit(1)
  .then((r) => r[0]);

if (!invoice) notFound();        // ⇢ déroute vers not-found.tsx
```

> ⚠️ Ne supprimez pas le `.limit(1)` : c’est plus sûr et plus rapide qu’un `select()` nu.

---

## <h2 id="2-notfound-file">2. Créer un rendu 404 spécifique à l’espace « Invoices »</h2>

### Option A — 404 locale à la feature

```
src/app/invoices/not-found.tsx
```

```tsx
export default function NotFound() {
  return (
    <main className="max-w-md mx-auto py-24 text-center space-y-6">
      <h1 className="text-4xl font-bold">Invoice introuvable</h1>
      <p className="text-slate-600">
        Cette facture n’existe pas ou a été supprimée.
      </p>

      <a
        href="/dashboard"
        className="inline-block px-5 py-2 bg-slate-900 text-white rounded-md"
      >
        Retour au tableau de bord
      </a>
    </main>
  );
}
```

### Option B — 404 globale

Placez simplement le même fichier à la racine d’`app/` si vous voulez un écran 404 identique pour tout le site.

---

## <h2 id="3-error-boundary">3. Boundary d’erreur — `error.tsx`</h2>

Un SQL malformé, un time-out réseau Xata … on veut un écran sobre plutôt qu’un crash.

```
src/app/error.tsx           # (ou src/app/invoices/error.tsx pour limiter au scope)
```

```tsx
"use client";               // un error boundary est *toujours* client

import { useEffect } from "react";

export default function GlobalError({ error, reset }: {
  error: Error & { digest?: string };
  reset: () => void;
}) {
  useEffect(() => {
    console.error(error);   // log côté navigateur + console serveur
  }, [error]);

  return (
    <main className="h-screen flex flex-col items-center justify-center gap-6">
      <h1 className="text-3xl font-bold">Une erreur est survenue</h1>
      <p className="text-slate-600">Impossible d’afficher la page demandée.</p>

      <button
        onClick={() => reset()}               // tentative de reload soft
        className="px-4 py-2 bg-slate-900 text-white rounded-md"
      >
        Réessayer
      </button>
    </main>
  );
}
```

---

## <h2 id="4-tests">4. Scénarios de vérification manuelle</h2>

| URL testée                                | Résultat attendu                                     |
| ----------------------------------------- | ---------------------------------------------------- |
| `/invoices/99999` (id inexistant)         | Affichage de la page 404 personnalisée               |
| `/invoices/abc` (id non numérique)        | 404 (le `parseInt()` → `NaN` déclenche `notFound()`) |
| Débrancher le Wi-Fi, appeler une vraie ID | Écran *error boundary* avec bouton « Réessayer »     |

---

## <h2 id="5-good-to-know">5. Bon à savoir</h2>

| Point                  | Détail                                                                                                                                           |
| ---------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ |
| **Static vs. Dynamic** | Comme on appelle `db.select()` à chaque requête, la page reste *dynamique* (`cache: "no-store"` implicite). Pas besoin de config supplémentaire. |
| **Pré-rendu SSG**      | Si vous souhaitiez pré-générer certaines factures (rare), créez `generateStaticParams()` dans `[id]/page.tsx`.                                   |
| **Propagation 404**    | `notFound()` remonte au premier `not-found.tsx` ancêtre (local > global).                                                                        |
| **Logging**            | `error.tsx` s’exécute côté client ; pour log côté serveur, logguez avant de lancer `notFound()` ou laissez la stack-trace apparaître en local.   |


