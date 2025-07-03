# Cours sur les Tests API avec VS Code REST Client et en-têtes HTTP et Validation des APIs REST

*Concevoir, exécuter et valider des tests d'intégration pour une API Next.js avec REST Client et contrôle des en-têtes HTTP*



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





## 7. Code api-tests.http




```json
# 🧪 TESTS API - Application de Facturation Next.js 15
# Fichier de tests HTTP pour VSCode REST Client
# Installation : Installer l'extension "REST Client" dans VSCode
# Usage : Cliquer sur "Send Request" au-dessus de chaque requête

### Variables globales
@baseUrl = http://localhost:3000
@apiUrl = {{baseUrl}}/api/invoices

# ═══════════════════════════════════════════════════════════════════
# 📋 TESTS GET - RÉCUPÉRATION DES FACTURES
# ═══════════════════════════════════════════════════════════════════

### GET - Récupérer toutes les factures (liste vide initialement)
GET {{apiUrl}}
Accept: application/json

### GET - Test avec en-têtes personnalisés
GET {{apiUrl}}
Accept: application/json
User-Agent: Test-Client/1.0
X-Test-Type: API-Testing

# ═══════════════════════════════════════════════════════════════════
# ✅ TESTS POST - CRÉATION DE FACTURES VALIDES
# ═══════════════════════════════════════════════════════════════════

### POST - Créer une facture simple et valide
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Entreprise ABC",
  "email": "contact@entreprise-abc.com",
  "value": "1500.00",
  "description": "Développement site web e-commerce"
}

### POST - Créer une facture avec montant décimal
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Société XYZ",
  "email": "admin@societe-xyz.fr",
  "value": "2750.99",
  "description": "Consulting technique et formation React"
}

### POST - Créer une facture sans description (optionnelle)
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "StartUp Innovation",
  "email": "hello@startup-innovation.com",
  "value": "850.50"
}

### POST - Créer une facture avec description longue
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Grande Corporation SARL",
  "email": "facturation@grande-corporation.fr",
  "value": "5000.00",
  "description": "Audit complet de sécurité, mise en place d'une architecture microservices, formation équipe développement sur Next.js 15 et intégration CI/CD avec Docker et Kubernetes"
}

### POST - Facture avec montant minimal
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Micro Entreprise",
  "email": "micro@example.com",
  "value": "0.01"
}

### POST - Facture avec gros montant
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Mega Project Corp",
  "email": "finance@megaproject.com",
  "value": "50000.00",
  "description": "Projet de transformation digitale complète"
}

# ═══════════════════════════════════════════════════════════════════
# ❌ TESTS POST - VALIDATION DES ERREURS
# ═══════════════════════════════════════════════════════════════════

### POST - Erreur : Customer manquant
POST {{apiUrl}}
Content-Type: application/json

{
  "email": "test@example.com",
  "value": "100.00"
}

### POST - Erreur : Email manquant
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Customer",
  "value": "100.00"
}

### POST - Erreur : Value manquant
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Customer",
  "email": "test@example.com"
}

### POST - Erreur : Tous les champs manquants
POST {{apiUrl}}
Content-Type: application/json

{
}

### POST - Erreur : Email invalide (format incorrect)
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Customer",
  "email": "email-invalide",
  "value": "100.00"
}

### POST - Erreur : Value comme string vide
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Customer", 
  "email": "test@example.com",
  "value": ""
}

### POST - Erreur : Value négative
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Customer",
  "email": "test@example.com", 
  "value": "-100.00"
}

# ═══════════════════════════════════════════════════════════════════
# 🔍 TESTS AVANCÉS - CAS LIMITES
# ═══════════════════════════════════════════════════════════════════

### POST - Customer avec caractères spéciaux
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Société & Cie - L'Innovation (2024)",
  "email": "contact@societe-innovation.com",
  "value": "1200.00",
  "description": "Projet avec caractères spéciaux : à, é, è, ç, ù, €, @, #"
}

### POST - Email avec domaine international
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "International Corp",
  "email": "contact@société-français.fr",
  "value": "2000.00"
}

### POST - Description avec emoji et caractères Unicode
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Modern Agency",
  "email": "hello@modern-agency.com",
  "value": "999.99",
  "description": "🚀 Projet innovant avec émojis et caractères spéciaux : ñ, ü, ß, 中文, русский"
}

### POST - Customer nom très long (test limite 120 chars)
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Nom d'entreprise extrêmement long pour tester la limite de 120 caractères définie dans le schéma Drizzle ORM",
  "email": "very-long-name@example.com",
  "value": "500.00"
}

### POST - Email long (test limite 160 chars)  
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Long Email",
  "email": "utilisateur-avec-nom-tres-long-pour-tester-les-limites@domaine-avec-nom-extremement-long-pour-validation.com",
  "value": "300.00"
}

### POST - Description longue (test limite 255 chars)
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Description",
  "email": "test@example.com", 
  "value": "400.00",
  "description": "Description très longue pour tester la limite de 255 caractères définie dans le schéma. Lorem ipsum dolor sit amet, consectetur adipiscing elit. Sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad."
}

# ═══════════════════════════════════════════════════════════════════
# 🔧 TESTS TECHNIQUES - HEADERS ET FORMATS
# ═══════════════════════════════════════════════════════════════════

### POST - Test avec Content-Type incorrect
POST {{apiUrl}}
Content-Type: text/plain

{
  "customer": "Test Wrong Content-Type",
  "email": "test@example.com",
  "value": "100.00"
}

### POST - Test sans Content-Type
POST {{apiUrl}}

{
  "customer": "Test No Content-Type",
  "email": "test@example.com", 
  "value": "100.00"
}

### POST - Test avec JSON malformé
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Malformed JSON"
  "email": "test@example.com",
  "value": "100.00"
}

### POST - Test avec propriétés supplémentaires (non validées)
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test Extra Props",
  "email": "test@example.com",
  "value": "100.00",
  "description": "Test propriétés supplémentaires",
  "extraField": "Cette propriété ne devrait pas être traitée",
  "anotherField": 12345,
  "booleanField": true
}

# ═══════════════════════════════════════════════════════════════════
# 📊 TESTS EN SÉRIE - CRÉATION MULTIPLE
# ═══════════════════════════════════════════════════════════════════

### POST - Facture 1/5 - Série de tests
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Client Série 1",
  "email": "client1@serie.com",
  "value": "100.00",
  "description": "Première facture de la série"
}

### POST - Facture 2/5 - Série de tests  
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Client Série 2", 
  "email": "client2@serie.com",
  "value": "200.00",
  "description": "Deuxième facture de la série"
}

### POST - Facture 3/5 - Série de tests
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Client Série 3",
  "email": "client3@serie.com",
  "value": "300.00",
  "description": "Troisième facture de la série"
}

### POST - Facture 4/5 - Série de tests
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Client Série 4",
  "email": "client4@serie.com", 
  "value": "400.00",
  "description": "Quatrième facture de la série"
}

### POST - Facture 5/5 - Série de tests
POST {{apiUrl}}
Content-Type: application/json

{
  "customer": "Client Série 5",
  "email": "client5@serie.com",
  "value": "500.00", 
  "description": "Cinquième facture de la série"
}

### GET - Vérification après création en série
GET {{apiUrl}}
Accept: application/json

# ═══════════════════════════════════════════════════════════════════
# 🎯 TESTS DE PERFORMANCE - REQUÊTES SIMULTANÉES
# ═══════════════════════════════════════════════════════════════════

### GET - Test performance 1
GET {{apiUrl}}

### GET - Test performance 2  
GET {{apiUrl}}

### GET - Test performance 3
GET {{apiUrl}}

### GET - Test performance 4
GET {{apiUrl}}

### GET - Test performance 5
GET {{apiUrl}}

# ═══════════════════════════════════════════════════════════════════
# 🔄 TESTS ENDPOINTS INEXISTANTS (404)
# ═══════════════════════════════════════════════════════════════════

### GET - Test route inexistante
GET {{baseUrl}}/api/invoices/123

### POST - Test route inexistante
POST {{baseUrl}}/api/invoices/create
Content-Type: application/json

{
  "customer": "Test 404",
  "email": "test@example.com",
  "value": "100.00"
}

### PUT - Méthode non supportée
PUT {{apiUrl}}
Content-Type: application/json

{
  "customer": "Test PUT", 
  "email": "test@example.com",
  "value": "100.00"
}

### DELETE - Méthode non supportée  
DELETE {{apiUrl}}

# ═══════════════════════════════════════════════════════════════════
# 📝 NOTES ET DOCUMENTATION
# ═══════════════════════════════════════════════════════════════════

###
# 🔍 COMMENT UTILISER CE FICHIER :
# 
# 1. Installer l'extension VSCode "REST Client" 
# 2. Démarrer votre serveur Next.js : npm run dev
# 3. Cliquer sur "Send Request" au-dessus de chaque requête
# 4. Observer les réponses dans le panel de droite
# 
# 🎯 TESTS RECOMMANDÉS DANS L'ORDRE :
# 
# 1. GET initial (base vide)
# 2. POST factures valides (plusieurs)  
# 3. GET pour vérifier la création
# 4. POST avec erreurs de validation
# 5. Tests cas limites et caractères spéciaux
# 6. Tests techniques (headers, JSON malformé)
# 
# 📊 CODES DE STATUT ATTENDUS :
# 
# - 200 : GET successful
# - 201 : POST successful (création) 
# - 400 : Erreurs de validation
# - 404 : Routes inexistantes
# - 405 : Méthodes non supportées
# - 500 : Erreurs serveur
# 
# 🔧 DEBUGGING :
# 
# - Vérifiez la console serveur Next.js pour les logs détaillés
# - Utilisez Drizzle Studio : npm run db:studio
# - Inspectez les réponses JSON dans VSCode REST Client
#
# 🎉 HAPPY TESTING ! 
```















## 8. Exercices pratiques guidés

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
- A. Accept
- B. Content-Type
- C. User-Agent
- D. Authorization

**Réponse : B**
Explication : `Content-Type` précise la nature du corps envoyé (ici `application/json`). Sans lui le serveur ne sait pas comment parser la requête.



### Question 2

Quel code de statut est adéquat si la route `/api/invoices/create` n’existe pas ?
- A. 200
- B. 201
- C. 404
- D. 405

**Réponse : C**
Explication : Ressource introuvable, donc `404 Not Found`.



### Question 3

Vous envoyez un JSON mal formé. Quel code devez-vous attendre ?
- A. 400
- B. 401
- C. 403
- D. 500

**Réponse : A**
Explication : La requête est invalide côté client (`Bad Request`).



### Question 4

Quel en-tête de réponse liste les méthodes autorisées lorsque le serveur retourne `405 Method Not Allowed` ?
- A. Allow
- B. Accept
- C. Vary
- D. Retry-After

**Réponse : A**
Explication : Le RFC 7231 impose d’inclure `Allow: GET, POST`.



### Question 5

Dans le plan de tests, pourquoi utiliser `@baseUrl` et `@apiUrl` ?
- A. Pour masquer l’URL réelle dans les logs.
- B. Pour faciliter un changement d’environnement sans modifier chaque requête.
- C. Pour augmenter la sécurité TLS.
- D. Pour éviter d’utiliser `localhost`.

**Réponse : B**
Explication : Les variables globales rendent le fichier portable entre développement, staging et production.



### Question 6

Quel est le rôle de `Accept: application/json` dans une requête ?
- A. Indiquer au serveur quel type de réponse est souhaité.
- B. Indiquer au client quel type de contenu il envoie.
- C. Forcer l’authentification.
- D. Configurer le cache.

**Réponse : A**
Explication : `Accept` décrit les formats que le client peut accepter.



### Question 7

`drizzle-kit` génère des migrations ; quel est l’équivalent HTTP de la migration appliquée ?
- A. 200
- B. 201
- C. 204
- D. Ce n’est pas relié à HTTP.

**Réponse : D**
Explication : Les migrations sont côté base de données ; elles ne renvoient pas de code HTTP.













### Question 8

Quel en-tête est requis pour qu’un serveur REST accepte des données JSON envoyées en `POST` ?

* A. Accept
* B. X-Content
* C. Content-Type
* D. Authorization

**Réponse : C**
Explication : `Content-Type: application/json` informe le serveur du format du corps de la requête.



### Question 9

Quelle réponse est correcte si l’utilisateur envoie un champ obligatoire manquant ?

* A. 201
* B. 500
* C. 400
* D. 204

**Réponse : C**
Explication : L’absence de champs requis entraîne une erreur côté client : `400 Bad Request`.


### Question 10

Quelle est la conséquence d’un `POST` sans en-tête `Content-Type` ?

* A. Le corps est interprété comme du texte brut.
* B. La requête est automatiquement convertie en XML.
* C. Le serveur renvoie 401.
* D. Le serveur ne peut pas parser le corps et renvoie une erreur.

**Réponse : D**
Explication : Sans `Content-Type`, le serveur ne sait pas comment interpréter le corps de la requête.

---
### Question 11

Quel code le serveur doit-il renvoyer lorsqu’un client tente d’utiliser une méthode HTTP non supportée sur une route ?

* A. 403
* B. 405
* C. 409
* D. 501

**Réponse : B**
Explication : `405 Method Not Allowed` indique que la méthode n’est pas autorisée pour cette route.



### Question 12

Quelle méthode HTTP est utilisée pour créer une ressource sur le serveur ?

* A. GET
* B. POST
* C. PUT
* D. DELETE

**Réponse : B**
Explication : `POST` est la méthode standard pour créer une ressource via un formulaire ou un corps JSON.


### Question 13

Que signifie un code HTTP 201 ?

* A. Ressource supprimée avec succès
* B. Requête valide mais aucune réponse
* C. Ressource créée avec succès
* D. Erreur d’authentification

**Réponse : C**
Explication : Le code `201 Created` est renvoyé quand une ressource a bien été créée sur le serveur.

---

### Question 14

Dans le test suivant, pourquoi cette requête est-elle invalide ?

```http
POST /api/invoices
{
  "customer": "ACME Inc."
}
```

* A. L’email est manquant
* B. Le champ `Content-Type` est absent
* C. La méthode est incorrecte
* D. Le body est mal formé

**Réponse : B**
Explication : Il manque l’en-tête `Content-Type: application/json`, nécessaire pour que le serveur comprenne le corps.


### Question 15

Que signifie le code HTTP 415 ?

* A. Erreur dans la syntaxe du JSON
* B. Type MIME non pris en charge
* C. Conflit dans la requête
* D. Erreur serveur inconnue

**Réponse : B**
Explication : `415 Unsupported Media Type` indique que le format du corps n’est pas reconnu par le serveur.



### Question 16

Pourquoi est-il utile d’ajouter un en-tête `X-Request-ID` ?

* A. Pour éviter le cache
* B. Pour tracer une requête dans les logs
* C. Pour authentifier l’utilisateur
* D. Pour indiquer le type MIME

**Réponse : B**
Explication : L’en-tête personnalisé `X-Request-ID` est souvent utilisé pour tracer les requêtes dans un système distribué.



### Question 17

Que doit faire un serveur quand il reçoit un JSON mal formé ?

* A. Retourner `200` et ignorer le corps
* B. Interpréter comme une chaîne vide
* C. Retourner `400 Bad Request`
* D. Retourner `500 Internal Server Error`

**Réponse : C**
Explication : Le JSON mal formé est une erreur côté client, donc `400` est la réponse appropriée.









### Question 18

Quel code doit renvoyer le serveur si tous les champs requis sont absents dans une requête `POST` ?

* A. 404
* B. 200
* C. 400
* D. 204

**Réponse : C**
Explication : Une requête invalide, même sans champ, est une faute du client. Le code correct est `400 Bad Request`.



### Question 19

Quelle est la conséquence d’un envoi avec `Content-Type: text/plain` au lieu de `application/json` ?

* A. Le serveur acceptera la requête sans problème
* B. Le corps sera ignoré
* C. Le serveur retournera probablement `415`
* D. Le serveur retournera `200`

**Réponse : C**
Explication : Le type `text/plain` n’est pas compatible avec un `POST` JSON. Le serveur renverra `415 Unsupported Media Type`.



### Question 20

Quel champ JSON est **optionnel** dans la plupart des tests de factures valides de votre fichier ?

* A. customer
* B. email
* C. value
* D. description

**Réponse : D**
Explication : Le champ `description` n'est pas toujours obligatoire selon les règles métier.



### Question 21

Quel type de test est illustré par une requête avec un champ `extraField` non attendu par le schéma ?

* A. Test de performance
* B. Test de surcharge
* C. Test de robustesse / tolérance aux champs supplémentaires
* D. Test de sécurité

**Réponse : C**
Explication : Ce test vérifie si l’API ignore proprement les propriétés non déclarées dans le schéma.



### Question 22

Quel en-tête est indispensable pour que le client indique qu’il veut une réponse JSON ?

* A. Content-Type
* B. Accept
* C. Authorization
* D. Referer

**Réponse : B**
Explication : `Accept: application/json` demande explicitement une réponse au format JSON.


### Question 23

Quel est le rôle de la méthode `PUT` en HTTP ?

* A. Créer une ressource
* B. Lire une ressource
* C. Remplacer complètement une ressource existante
* D. Supprimer une ressource

**Réponse : C**
Explication : `PUT` remplace intégralement une ressource, contrairement à `PATCH` qui la modifie partiellement.


### Question 24

Quel test démontre un **cas limite** dans votre fichier ?

* A. Test avec `User-Agent`
* B. Envoi d’une valeur négative
* C. Création de 5 factures en série
* D. Envoi d’un email invalide

**Réponse : B**
Explication : Une valeur négative est un cas limite qui teste la validation métier au-delà de la simple présence des champs.



### Question 25

Quel code est attendu après une création réussie de ressource via `POST` ?

* A. 200
* B. 201
* C. 204
* D. 301

**Réponse : B**
Explication : La création réussie d’une ressource avec `POST` renvoie `201 Created`.


### Question 26

Pourquoi une requête `POST` vers `/api/invoices/create` renvoie-t-elle `404` ?

* A. Le corps est vide
* B. Le serveur est mal configuré
* C. La route `/api/invoices/create` n’existe pas
* D. Le client utilise un mauvais port

**Réponse : C**
Explication : La route `/api/invoices/create` n’est pas définie dans votre API, seule `/api/invoices` est valide.



### Question 27

Que signifie `405 Method Not Allowed` ?

* A. Le serveur ne peut pas comprendre la requête
* B. La méthode HTTP utilisée n’est pas supportée pour cette ressource
* C. L’authentification a échoué
* D. Le client n’a pas les droits suffisants

**Réponse : B**
Explication : Ce code indique que la méthode (`PUT`, `DELETE`, etc.) n’est pas autorisée sur cette route.



### Question 28

Quelle est l’utilité du test `GET` après l’insertion de 5 factures ?

* A. Vérifier les statuts HTTP
* B. Vérifier le bon fonctionnement de la suppression
* C. Vérifier la persistance et la création des données
* D. Réinitialiser l’état de la base

**Réponse : C**
Explication : Le `GET` après insertion permet de confirmer que les données ont bien été enregistrées.


### Question 29

Quelle est la fonction de l'en-tête `User-Agent` ?

* A. Indiquer le type de client effectuant la requête
* B. Authentifier la requête
* C. Encoder le corps
* D. Remplacer `Accept`

**Réponse : A**
Explication : `User-Agent` permet d’identifier le type d’application cliente (navigateur, script, testeur API, etc.).



### Question 30

Que signifie une réponse avec `500 Internal Server Error` ?

* A. Problème côté client
* B. Problème d’en-tête `Accept`
* C. Bug ou exception côté serveur
* D. Requête vers une route interdite

**Réponse : C**
Explication : Le code `500` indique que le serveur a rencontré une erreur qu’il n’a pas pu gérer proprement.




