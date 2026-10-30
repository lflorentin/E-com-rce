# E-com&rce

**Socle full stack pour une application e-commerce avec Angular, Node.js et Express.**

![Angular](https://img.shields.io/badge/Angular-13.3-DD0031?logo=angular&logoColor=white)
![TypeScript](https://img.shields.io/badge/TypeScript-4.6-3178C6?logo=typescript&logoColor=white)
![Express](https://img.shields.io/badge/Express-4.16-000000?logo=express&logoColor=white)
![Docker](https://img.shields.io/badge/Backend-Docker-2496ED?logo=docker&logoColor=white)
![Status](https://img.shields.io/badge/Statut-Initialisation-orange)

## Présentation

E-com&rce est un projet de plateforme e-commerce organisé autour de deux applications indépendantes : une interface Angular et un serveur Node.js/Express.

Cette première version pose les fondations du projet : structure frontend, routage modulaire côté backend, traitement des requêtes HTTP, gestion des erreurs et conteneurisation du serveur.

**Le dépôt est au stade de l’initialisation technique.** L’interface affiche encore la page d’accueil générée par Angular. Les fonctionnalités de catalogue, de panier et de commande constituent les prochaines étapes du développement.

## Éléments présents

| Partie | Contenu |
| --- | --- |
| Frontend | Application Angular 13 avec TypeScript, SCSS et module de routage. |
| Backend | Serveur Express avec traitement JSON, formulaires et cookies. |
| Routes | Chargement automatique des routeurs depuis `backend/routes/core/`. |
| Vues serveur | Templates Pug et gestion des erreurs HTTP. |
| Logs | Journalisation des requêtes avec Morgan. |
| Docker | Dockerfile, configuration Compose et variante de débogage. |
| Tests | Base de tests frontend Jasmine/Karma et backend Mocha/Supertest. |

Le pilote MongoDB est déclaré dans les dépendances du backend. La connexion à une base, les modèles de données et les opérations de persistance ne sont pas encore implémentés. Les deux applications ne communiquent pas encore entre elles.

## Stack technique

| Couche | Technologies |
| --- | --- |
| Interface | Angular `13.3`, TypeScript `4.6`, SCSS |
| Navigation | Angular Router |
| Serveur | Node.js, Express `4.16` |
| Templates serveur | Pug |
| Middlewares | Morgan, cookie-parser, http-errors |
| Chargement des routes | require-dir |
| Pilote de base de données déclaré | mongodb `5.3` |
| Tests frontend | Jasmine, Karma |
| Tests backend | Mocha, Supertest |
| Conteneurisation | Docker, Docker Compose |

## Organisation du dépôt

| Chemin | Rôle |
| --- | --- |
| `backend/app.js` | Configuration Express, middlewares et montage des routes. |
| `backend/bin/www` | Création et démarrage du serveur HTTP. |
| `backend/routes/core/` | Routeurs chargés automatiquement. |
| `backend/views/` | Templates Pug. |
| `backend/public/` | Ressources statiques du serveur. |
| `backend/test/` | Tests HTTP avec Supertest. |
| `backend/Dockerfile` | Image du backend exécutée avec un utilisateur non privilégié. |
| `backend/docker-compose.yml` | Lancement du backend sur le port `3000`. |
| `backend/docker-compose.debug.yml` | Variante avec inspecteur Node.js sur le port `9229`. |
| `frontend/e-com-mrc-frontend/` | Application Angular et configuration de compilation. |

## Installation et démarrage

### Prérequis

- Node.js et npm compatibles avec les dépendances de chaque application.
- Docker et Docker Compose pour le lancement conteneurisé du backend, facultatif.

Le fichier `backend/.node-version` indique `8.11.1`, une valeur historique à réaligner avec les dépendances réellement déclarées. Le Dockerfile utilise `node:lts-alpine` sans version fixe. L’environnement Node.js reste donc à harmoniser et à verrouiller.

### 1. Corriger l’import de route du backend

Dans `backend/app.js`, un import référence un fichier absent :

```javascript
var usersRouter = require('./routes/users');
```

Le fichier existe dans `routes/core/`. Supprimer cet import inutilisé, ou corriger son chemin :

```javascript
var usersRouter = require('./routes/core/users');
```

Les routeurs sont déjà montés par le chargeur automatique `require-dir`. Cette correction est nécessaire avant le démarrage, y compris avec Docker.

### 2. Lancer le backend

Depuis la racine du dépôt :

```bash
cd backend
npm install
npm start
```

Le serveur écoute par défaut sur le port **3000**. La variable d’environnement `PORT` permet de choisir un autre port.

### 3. Lancer le frontend

Dans un autre terminal, depuis la racine du dépôt :

```bash
cd frontend/e-com-mrc-frontend
npm install
npm start
```

Ouvrir [l’application Angular](http://localhost:4200/). À ce stade, elle présente l’écran initial Angular, sans pages métier.

## Routes du backend

Le chargement automatique associe chaque nom de fichier de `routes/core/` à un préfixe HTTP.

| Méthode | Route | Réponse définie |
| --- | --- | --- |
| GET | `/index` | Page Pug d’accueil Express. |
| GET | `/users` | Texte de démonstration `respond with a resource`. |

Après correction de l’import et lancement du serveur :

```bash
curl http://localhost:3000/index
curl http://localhost:3000/users
```

La route `/` n’est pas montée dans la configuration actuelle et renvoie une erreur 404. Aucun CRUD utilisateur n’est encore disponible.

## Lancement avec Docker

Après correction de l’import dans `app.js`, depuis `backend/` :

```bash
docker compose up --build
```

Le service `demo` publie le port **3000**. Pour l’arrêter :

```bash
docker compose down
```

Pour utiliser la configuration de débogage à la place de la configuration standard :

```bash
docker compose -f docker-compose.debug.yml up --build
```

Cette variante expose également l’inspecteur Node.js sur le port **9229**. Le frontend et une éventuelle base MongoDB ne sont pas inclus dans les fichiers Compose actuels.

## Commandes utiles

| Répertoire | Commande | Usage |
| --- | --- | --- |
| `backend/` | `npm start` | Démarrer le serveur Express. |
| `backend/` | `npm test` | Exécuter les tests Mocha/Supertest. |
| `frontend/e-com-mrc-frontend/` | `npm start` | Démarrer le serveur de développement Angular. |
| `frontend/e-com-mrc-frontend/` | `npm run build` | Compiler le frontend. |
| `frontend/e-com-mrc-frontend/` | `npm run watch` | Compiler en continu en mode développement. |
| `frontend/e-com-mrc-frontend/` | `npm test` | Lancer les tests Jasmine/Karma. |

Le test backend fourni interroge `/` et attend la page Express. Il doit être aligné sur `/index`, ou accompagné du rétablissement de la route `/`. Les tests frontend couvrent le composant racine généré. Les commandes de lancement et les tests n’ont pas été exécutés lors de la rédaction de cette documentation.

## Feuille de route

- [ ] Corriger l’import de route et aligner le test backend sur le routage.
- [ ] Harmoniser et verrouiller les versions de Node.js.
- [ ] Définir les modèles produit, utilisateur, panier et commande.
- [ ] Connecter le backend à MongoDB.
- [ ] Exposer une API de gestion du catalogue.
- [ ] Développer les pages Angular et leurs services HTTP.
- [ ] Configurer les échanges frontend/backend avec un proxy de développement ou CORS.
- [ ] Implémenter l’authentification et la validation des données.
- [ ] Ajouter le panier et le suivi des commandes.
- [ ] Étendre Docker Compose au frontend et à la base de données.
- [ ] Ajouter des tests métier et une pipeline CI/CD.
