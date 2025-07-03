# Cours : Tests API avec VS Code REST Client et en-têtes HTTP

## Table des matières

1. Introduction
2. Rappels sur HTTP
3. Les en-têtes (headers) les plus courants
4. Statuts de réponse et bonnes pratiques de validation
5. Utiliser REST Client dans VS Code
6. Concevoir un plan de tests complet
7. Exercices pratiques guidés
8. Quiz de vérification des acquis (30 questions)



## 1. Introduction

Le fichier que vous avez fourni est un **plan de tests HTTP** destiné à l’extension **REST Client** de VS Code.
Objectifs :

* Automatiser et documenter les appels à l’API `/api/invoices` d’une application Next.js 15.
* Vérifier la création, la validation et la robustesse des endpoints.
* Contrôler les erreurs liées aux en-têtes (`Content-Type`, `Accept`, etc.) et aux formats JSON.


## 2. Rappels sur HTTP

| Élément            | Rôle                                                                           |
| ------------------ | ------------------------------------------------------------------------------ |
| Méthode            | Décrit l’action : `GET`, `POST`, `PUT`, `DELETE`, etc.                         |
| URL                | Identifie la ressource.                                                        |
| En-têtes (headers) | Métadonnées de la requête ou de la réponse (format, authentification, cache…). |
| Corps (body)       | Données transmises, généralement JSON pour une API REST.                       |
| Code de statut     | Résultat de la requête (2xx succès, 4xx erreur client, 5xx erreur serveur).    |



## 3. Les en-têtes les plus courants

| En-tête             | Contexte requête                                  | Exemples de valeurs                        |
| ------------------- | ------------------------------------------------- | ------------------------------------------ |
| `Content-Type`      | Type du corps envoyé                              | `application/json`, `text/plain`           |
| `Accept`            | Types que le client accepte en réponse            | `application/json`, `application/xml`      |
| `Authorization`     | Portage du jeton d’authentification               | `Bearer <token>`                           |
| `User-Agent`        | Identification du client                          | `Test-Client/1.0`                          |
| `X-*` personnalisés | Transport d’informations métier ou de traçabilité | `X-Request-ID`, `X-Test-Type: API-Testing` |

**Règle** : si `Content-Type` est incorrect ou absent, l’API doit retourner au minimum `400 Bad Request`.



## 4. Statuts de réponse et bonnes pratiques de validation

| Code | Signification                                   | Bonnes pratiques                                                                                               |
| ---- | ----------------------------------------------- | -------------------------------------------------------------------------------------------------------------- |
| 200  | Requête réussie, réponse avec contenu           | Pour `GET` ou `DELETE` sur ressource existante.                                                                |
| 201  | Ressource créée                                 | Pour `POST` réussi. Inutile de renvoyer le corps complet si l’URL de la ressource est fournie dans `Location`. |
| 400  | Erreur de validation                            | Toujours décrire l’erreur dans le corps (`message`, `field`).                                                  |
| 404  | Ressource ou route inexistante                  | Retourner un JSON ou texte expliquant la route manquante.                                                      |
| 405  | Méthode non autorisée sur la route              | Spécifier `Allow` dans les en-têtes de réponse.                                                                |
| 415  | Unsupported Media Type (mauvais `Content-Type`) | Souvent pertinent pour JSON mal formé ou mauvais MIME type.                                                    |
| 500  | Erreur interne serveur                          | Ne jamais révéler de stack trace en production.                                                                |



## 5. Utiliser REST Client dans VS Code

1. **Installation** : `Ext > REST Client`
2. **Variables globales** :

   ```http
   @baseUrl = http://localhost:3000
   @apiUrl  = {{baseUrl}}/api/invoices
   ```
3. **Sections de tests** : séparer les scénarios par commentaires `###` pour garder un historique clair.
4. **Exécution** : cliquer sur “Send Request” ou `Ctrl+Alt+R`.
5. **Assertions** : REST Client permet d’ajouter des tests simple avec `@assert` ; pour des assertions avancées, utiliser un runner (Vitest, Jest) ou un outil dédié (k6, Newman).



## 6. Concevoir un plan de tests complet

| Étape | Objectif                                                  | Exemple dans votre fichier             |
| ----- | --------------------------------------------------------- | -------------------------------------- |
| 1     | Sanity check (`GET` liste vide)                           | `GET {{apiUrl}}`                       |
| 2     | Tests de création valides (`POST`)                        | Plusieurs payloads valides             |
| 3     | Validation erreurs (`POST` champs manquants)              | Section “VALIDATION DES ERREURS”       |
| 4     | Tests limites : chaînes très longues, caractères spéciaux | Section “CAS LIMITES”                  |
| 5     | Tests techniques sur les en-têtes                         | Mauvais `Content-Type`, JSON mal formé |
| 6     | Conformité aux méthodes (`PUT`, `DELETE` non supportées)  | Section “ENDPOINTS INEXISTANTS (404)”  |
| 7     | Performance rudimentaire                                  | Plusieurs `GET` en rafale              |


## 7. Exercices pratiques guidés

### Exercice 1 : contrôler `Content-Type`

1. Créez une requête `POST` sans `Content-Type`.
2. Vérifiez que le serveur répond `415` ou `400`.
3. Ajoutez un test `@assert` pour vérifier le code.

### Exercice 2 : simuler un client mobile

1. Ajoutez `User-Agent: Mobile-Test/2.0` à une requête valide.
2. Assurez-vous que la réponse est toujours `201`.

### Exercice 3 : injection d’en-tête personnalisé

1. Envoyez `X-Request-ID: 123456`.
2. Implémentez dans le middleware Next.js la remontée de ce même ID dans la réponse.


## 8. Quiz de vérification des acquis

> Format : question, choix A-D, réponse, explication.

### Question 1

Quel en-tête doit obligatoirement être fourni lors d’un `POST` JSON vers une API REST ?
A. Accept
B. Content-Type
C. User-Agent
D. Authorization

**Réponse : B**
Explication : `Content-Type` précise la nature du corps envoyé (ici `application/json`). Sans lui le serveur ne sait pas comment parser la requête.



### Question 2

Quel code de statut est adéquat si la route `/api/invoices/create` n’existe pas ?
A. 200
B. 201
C. 404
D. 405

**Réponse : C**
Explication : Ressource introuvable, donc `404 Not Found`.



### Question 3

Vous envoyez un JSON mal formé. Quel code devez-vous attendre ?
A. 400
B. 401
C. 403
D. 500

**Réponse : A**
Explication : La requête est invalide côté client (`Bad Request`).



### Question 4

Quel en-tête de réponse liste les méthodes autorisées lorsque le serveur retourne `405 Method Not Allowed` ?
A. Allow
B. Accept
C. Vary
D. Retry-After

**Réponse : A**
Explication : Le RFC 7231 impose d’inclure `Allow: GET, POST`.



### Question 5

Dans le plan de tests, pourquoi utiliser `@baseUrl` et `@apiUrl` ?
A. Pour masquer l’URL réelle dans les logs.
B. Pour faciliter un changement d’environnement sans modifier chaque requête.
C. Pour augmenter la sécurité TLS.
D. Pour éviter d’utiliser `localhost`.

**Réponse : B**
Explication : Les variables globales rendent le fichier portable entre développement, staging et production.



### Question 6

Quel est le rôle de `Accept: application/json` dans une requête ?
A. Indiquer au serveur quel type de réponse est souhaité.
B. Indiquer au client quel type de contenu il envoie.
C. Forcer l’authentification.
D. Configurer le cache.

**Réponse : A**
Explication : `Accept` décrit les formats que le client peut accepter.



### Question 7

`drizzle-kit` génère des migrations ; quel est l’équivalent HTTP de la migration appliquée ?
A. 200
B. 201
C. 204
D. Ce n’est pas relié à HTTP.

**Réponse : D**
Explication : Les migrations sont côté base de données ; elles ne renvoient pas de code HTTP.


