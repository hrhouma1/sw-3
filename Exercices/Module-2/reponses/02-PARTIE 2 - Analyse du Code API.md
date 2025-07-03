# RÉPONSES - PARTIE 2 : Analyse du Code API

## A) Architecture API (5 points)

11. **Quelles sont les deux méthodes HTTP implémentées dans ce fichier ?**
    Expliquez brièvement le rôle de chacune.

```
Les deux méthodes HTTP implémentées sont :
• POST : Créer une nouvelle facture (fonction POST)
  - Reçoit les données de la facture dans le body
  - Valide les données, génère un ID unique
  - Insère la facture en base de données
• GET : Récupérer toutes les factures (fonction GET)
  - Aucun paramètre requis
  - Retourne toutes les factures triées par date de création
```

12. **Pourquoi utilise-t-on `async/await` dans ces fonctions ?**
    Donnez un exemple concret de son utilité ici.

```
On utilise async/await car les opérations sont asynchrones (non-bloquantes) :
• Lecture du body JSON : await request.json()
• Requêtes base de données : await db.execute() et await db.select()
• Ces opérations prennent du temps et ne doivent pas bloquer le serveur
Exemple : await db.insert(invoices).values(insertData).returning()
Sans await, on obtiendrait une Promise au lieu des données réelles
```

<br/>
<br/>

## B) Gestion des données (6 points)

13. **Analysez la ligne suivante :**

```javascript
const { customer, email, value, description } = body;
```

* Comment s'appelle cette syntaxe JavaScript ?
* Quel est son avantage par rapport à `const customer = body.customer;` ?

```
• Cette syntaxe s'appelle la "destructuration" (destructuring assignment)
• Avantages par rapport à const customer = body.customer :
  - Plus concis : 1 ligne au lieu de 4 lignes
  - Plus lisible : on voit immédiatement quelles propriétés on extrait
  - Moins de répétition : on n'écrit pas "body." à chaque fois
  - Plus maintenable : facile d'ajouter/retirer des propriétés
```

14. **Expliquez cette requête SQL complexe :**

```sql
SELECT COALESCE(MAX(id), 0) + 1 as next_id FROM invoices
```

* Que fait `COALESCE` ?
* Pourquoi ajoute-t-on `+ 1` ?
* Quel problème pourrait survenir avec cette approche en production ?

```
• COALESCE : Retourne la première valeur non-null de la liste
  - Si MAX(id) existe, retourne MAX(id)
  - Si la table est vide, MAX(id) = NULL, donc retourne 0
• +1 : Incrémente pour obtenir le prochain ID disponible
  - Si MAX(id) = 5, alors next_id = 6
  - Si table vide, next_id = 0 + 1 = 1
• Problème en production : Race condition !
  - Si deux requêtes arrivent simultanément, elles peuvent obtenir le même ID
  - Solution : utiliser une séquence/auto-increment de la base de données
```

<br/>
<br/>

## C) Validation et sécurité (4 points)

15. **La validation actuelle est-elle suffisante ?**
    Proposez 3 améliorations possibles.

```
La validation actuelle n'est pas suffisante. Améliorations proposées :
1. Validation du format email : utiliser regex ou bibliothèque comme Zod
   - Vérifier que l'email contient @ et un domaine valide
2. Validation du montant (value) :
   - Vérifier que c'est un nombre positif
   - Limiter le nombre de décimales
3. Validation de la longueur des champs :
   - Limiter customer à 100 caractères
   - Limiter description à 500 caractères
   - Prévenir les attaques par déni de service
```

16. **Qu'est-ce que l'opérateur `?.` dans `maxIdResult.rows[0]?.next_id` ?**
    Pourquoi est-il important ici ?

```
• L'opérateur ?. est le "optional chaining" (chaînage optionnel)
• Il évite les erreurs si maxIdResult.rows[0] est undefined/null
• Sans ?. : si rows[0] n'existe pas, on aurait une erreur "Cannot read property 'next_id' of undefined"
• Avec ?. : si rows[0] n'existe pas, l'expression retourne undefined
• Sécurité : évite les crashes de l'application
```

<br/>
<br/>

## D) Gestion d'erreurs (3 points)

17. **Identifiez les différents types d'erreurs gérées**
    et leurs codes de statut HTTP correspondants.

```
Types d'erreurs et codes de statut :
• Erreur de validation (champs manquants) : 400 Bad Request
  - customer, email ou value manquants
• Erreur serveur/base de données : 500 Internal Server Error
  - Problème d'insertion, de connexion DB, erreur système
• Erreur de parsing JSON : 500 Internal Server Error
  - Body malformé, non-JSON
```

18. **Pourquoi fait-on `error instanceof Error` ?**
    Que se passe-t-il si on ne le fait pas ?

```
• instanceof Error vérifie si l'erreur est une instance de la classe Error
• JavaScript peut "throw" n'importe quoi : string, number, object, etc.
• Si on ne le fait pas :
  - error.message pourrait être undefined (si error est un string)
  - error.stack pourrait ne pas exister
  - Risque d'erreur "Cannot read property 'message' of undefined"
• Avec instanceof : on s'assure que error a les propriétés message et stack
```

<br/>
<br/>

## E) ORM et base de données (2 points)

19. **Comparez ces deux approches dans le code :**

* `db.execute(sql\`SELECT...\`)\`
* `db.select().from(invoices)`
  Quand utiliser l'une ou l'autre ?

```
• db.execute(sql`SELECT...`) : SQL brut
  - Utilisé pour des requêtes complexes (COALESCE, MAX, +1)
  - Plus de contrôle, syntaxe SQL native
  - Moins de sécurité de type (pas de TypeScript)
  
• db.select().from(invoices) : Query Builder Drizzle
  - Utilisé pour des requêtes simples et courantes
  - Type-safe, auto-complétion, moins d'erreurs
  - Plus maintenable, plus lisible
  
Règle : Query Builder pour les cas simples, SQL brut pour les cas complexes
```

<br/>
<br/>

## Instructions

* Durée recommandée : 25 minutes
* Vous pouvez naviguer dans le code pour trouver les explications
* Rédigez des réponses structurées, concises et argumentées

---

## Points bonus observés :

- **Logging détaillé** : console.log à chaque étape pour le debugging
- **Gestion robuste des erreurs** : try-catch avec messages explicites
- **Réponses structurées** : format JSON cohérent avec success/error
- **Sécurité basique** : validation des champs obligatoires
- **TypeScript** : types NextRequest/NextResponse pour la sécurité
- **Conversion de types** : Number(nextAvailableId), value.toString()
- **Fallback values** : || 1 en cas d'erreur, || null pour description
- **Const assertion** : 'open' as const pour TypeScript
- **Destructuration moderne** : extraction propre des propriétés
- **API RESTful** : respect des conventions HTTP (GET, POST, codes statut) 
