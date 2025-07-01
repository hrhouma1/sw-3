# <h1 id="module-6">Module 6 – Tableau dynamique des factures & page de détail `/invoices/[id]`</h1>

Ces deux écrans terminent le “circuit minimal viable”  :

1. **Dashboard** (`/dashboard`) – lecture en base, affichage dans un tableau cliquable.
2. **Invoice details** (`/invoices/[id]`) – page statique rendue côté serveur à partir de l’ID.

---

## <h2 id="1-arbo">1. Arborescence supplémentaire</h2>

```
src/
└── app/
    ├── dashboard/
    │   └── page.tsx          # tableau dynamique
    └── invoices/
        ├── [id]/
        │   └── page.tsx      # détail d’une facture
        └── new/…             # formulaire (déjà créé)
```

> Rien d’autre à créer côté `db/` : la table **`invoices`** existe, les migrations sont déjà appliquées.

---

## <h2 id="2-dashboard">2. `src/app/dashboard/page.tsx` – tableau dynamique</h2>

```tsx
import { db }          from "@/db";
import { invoices }    from "@/db/schema";
import Link            from "next/link";

/* shadcn/ui */
import {
  Table, TableHeader, TableBody, TableRow, TableHead, TableCell,
} from "@/components/ui/table";

/* icône pour la CTA */
import { CirclePlus } from "lucide-react";

export const metadata = { title: "Invoices • Dashboard" };

export default async function Dashboard() {
  /* 1. Lecture BD ---------------------------------------------------------- */
  const list = await db.select().from(invoices).orderBy(invoices.id.desc());

  /* 2. Rendu --------------------------------------------------------------- */
  return (
    <main className="max-w-5xl mx-auto px-6 py-12">
      <div className="flex items-center justify-between mb-8">
        <h1 className="text-3xl font-bold">Invoices</h1>
        <Link
          href="/invoices/new"
          className="inline-flex items-center gap-2 text-sm font-medium hover:underline"
        >
          <CirclePlus className="w-4 h-4" />
          Create Invoice
        </Link>
      </div>

      <Table>
        <TableHeader>
          <TableRow>
            <TableHead className="w-32">Date</TableHead>
            <TableHead className="w-56">Customer</TableHead>
            <TableHead>Email</TableHead>
            <TableHead className="w-28 text-center">Status</TableHead>
            <TableHead className="w-24 text-right">Value</TableHead>
          </TableRow>
        </TableHeader>

        <TableBody>
          {list.map((inv) => (
            <TableRow /* clé obligatoire */ key={inv.id}>
              {/* Date -------------------------------------------------------- */}
              <TableCell className="p-0">
                <Link
                  href={`/invoices/${inv.id}`}
                  className="block p-4 font-semibold"
                >
                  {new Date(inv.createTs).toLocaleDateString()}
                </Link>
              </TableCell>

              {/* Customer ---------------------------------------------------- */}
              <TableCell className="p-0">
                <Link
                  href={`/invoices/${inv.id}`}
                  className="block p-4"
                >
                  {inv.customer ?? "—"}
                </Link>
              </TableCell>

              {/* Email ------------------------------------------------------- */}
              <TableCell className="p-0">
                <Link
                  href={`/invoices/${inv.id}`}
                  className="block p-4"
                >
                  {inv.email}
                </Link>
              </TableCell>

              {/* Status badge ------------------------------------------------ */}
              <TableCell className="p-0">
                <Link
                  href={`/invoices/${inv.id}`}
                  className="block p-4"
                >
                  <span
                    className="
                      inline-block rounded-full px-3 py-0.5 text-xs
                      font-semibold capitalize
                      bg-slate-900/90 text-white
                    "
                  >
                    {inv.status}
                  </span>
                </Link>
              </TableCell>

              {/* Value ------------------------------------------------------- */}
              <TableCell className="p-0 text-right">
                <Link
                  href={`/invoices/${inv.id}`}
                  className="block p-4"
                >
                  ${(inv.value / 100).toFixed(2)}
                </Link>
              </TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </main>
  );
}
```

### Points clés & didactique

| Problème rencontré                         | Solution appliquée                                                                                                                   |
| ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------ |
| **Cellule non-cliquable** entre les textes | - Padding retiré (`p-0`) sur `<TableCell>`, déplacé sur `<Link className="block p-4">` + `block` permet de remplir toute la cellule. |
| Format monétaire                           | Division par `100` (stockage en centimes) puis `.toFixed(2)`                                                                         |
| Format date international                  | `toLocaleDateString()` sans paramètres → s’adapte à la locale navigateur.                                                            |
| Accessibilité / SEO                        | Lien sur chaque cellule **ou** on peut mettre un seul lien invisible absolu ; ici traitement simple.                                 |

---

## <h2 id="3-detail">3. `src/app/invoices/[id]/page.tsx` – page de détail</h2>

```tsx
import { db }       from "@/db";
import { invoices } from "@/db/schema";
import { notFound } from "next/navigation";

interface Props { params: { id: string } }

export async function generateMetadata({ params }: Props) {
  return { title: `Invoice ${params.id}` };
}

export default async function InvoicePage({ params }: Props) {
  const id = Number(params.id);
  if (Number.isNaN(id)) notFound();

  const inv = await db
    .select()
    .from(invoices)
    .where(invoices.id.eq(id))
    .limit(1)
    .then((r) => r[0]);

  if (!inv) notFound();

  /* -- Affichage ---------------------------------------------------------- */
  return (
    <main className="max-w-3xl mx-auto px-6 py-12 space-y-10">
      <header className="flex items-baseline justify-between">
        <h1 className="text-3xl font-bold">Invoice #{inv.id}</h1>
        <span
          className="
            inline-block rounded-full px-3 py-0.5 text-sm font-semibold
            bg-slate-900/90 text-white capitalize
          "
        >
          {inv.status}
        </span>
      </header>

      <section className="grid grid-cols-2 gap-4 text-sm">
        <p className="text-slate-500">Date:</p>
        <p>{new Date(inv.createTs).toLocaleDateString()}</p>

        <p className="text-slate-500">Customer:</p>
        <p>{inv.customer ?? "—"}</p>

        <p className="text-slate-500">Email:</p>
        <p>{inv.email}</p>

        <p className="text-slate-500">Value:</p>
        <p className="font-semibold">${(inv.value / 100).toFixed(2)}</p>
      </section>

      <section>
        <h2 className="text-lg font-semibold mb-2">Description</h2>
        <p className="whitespace-pre-wrap">{inv.description}</p>
      </section>
    </main>
  );
}
```

---

## <h2 id="4-tests">4. Tests manuels</h2>

| Action                          | Résultat attendu                                             |
| ------------------------------- | ------------------------------------------------------------ |
| `/dashboard` charge             | Toutes les lignes de la table ; les décimales sont visibles. |
| Passer le curseur sur une ligne | Le curseur change sur **toute** la largeur.                  |
| Clic → `/invoices/[id]`         | Affichage détaillé (pas de 404).                             |
| Entrer un ID inexistant         | Redirection auto 404 (Next.js).                              |


