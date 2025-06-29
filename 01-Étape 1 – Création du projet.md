# <h1 id="etape-1-creation-du-projet">Étape 1 – Création du projet</h1>

Commande à exécuter dans le terminal :

```bash
npx create-next-app@latest my-invoicing-app
```

Répondez aux questions comme suit :

| Question                                                       | Réponse |
| -------------------------------------------------------------- | ------- |
| Would you like to use TypeScript?                              | Yes     |
| Would you like to use ESLint?                                  | Yes     |
| Would you like to use Tailwind CSS?                            | Yes     |
| Would you like to use `src/` directory?                        | Yes     |
| Would you like to use App Router? (recommended)                | Yes     |
| Would you like to customize the default import alias (`@/*`) ? | No      |

---

# <h1 id="etape-2-demarrage">Étape 2 – Démarrage du serveur de développement</h1>

Une fois le projet généré :

```bash
cd my-invoicing-app
npm run dev
```

Un message comme celui-ci doit s’afficher :

```
Next.js 14.2.10 ou 15.x.x
Local: http://localhost:3000
```

Ouvrez votre navigateur et accédez à l’adresse :
`http://localhost:3000`

---

# <h1 id="etape-3-arborescence">Étape 3 – Structure initiale du projet</h1>

L’arborescence générée est la suivante :

```
my-invoicing-app/
├── src/
│   ├── app/                # App Router activé (pages et layout)
│   │   ├── page.tsx        # Page d'accueil (à nettoyer)
│   │   ├── layout.tsx      # Mise en page générale
│   │   └── fonts/
│   ├── styles/
│   │   └── globals.css     # CSS global Tailwind
├── .eslintrc.json
├── tailwind.config.ts
├── tsconfig.json
├── next.config.mjs
```

---

# <h1 id="etape-4-nettoyage-du-fichier-page">Étape 4 – Nettoyage du fichier `page.tsx`</h1>

Ouvrir le fichier `src/app/page.tsx` et **supprimer tout le contenu existant**.
Le remplacer par le composant suivant :

```tsx
export default function Home() {
  return (
    <main className="min-h-screen p-8 bg-white text-black">
      <h1 className="text-3xl font-bold">Bienvenue sur My Invoicing App</h1>
    </main>
  );
}
```

Ce fichier remplace le contenu de démonstration par défaut par une base propre avec classes Tailwind.



