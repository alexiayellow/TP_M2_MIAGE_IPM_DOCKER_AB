# TP_DEVOPS_M2_MIAGE_DOCKER_AB

TP Docker, Alexia B

## Application de départ

Application Node.js

- `server.js` : serveur Express qui écoute sur le port 3000, avec les routes `/` et `/big`.
- `package.json` et `package-lock.json` : dépendances (`express`, `mongodb`, `nodemon` en dev).
- `dockerfile` : Dockerfile de départ, à optimiser.

## Analyse du Dockerfile de départ

Mauvaises pratiques identifiées :

- Image de base `node:latest`, très lourde (1.88 Go).
- Copie du dossier `node_modules` de la machine hôte, puis `npm install` : les dépendances ne sont pas gérées proprement dans l'image.
- Pas de `.dockerignore` : tout le contexte est envoyé au build, `node_modules` compris.
- `ENV NODE_ENV=development` : l'application tourne en mode développement.
- `RUN npm run build` : le script `build` ne fait qu'un `echo`, l'étape ne sert à rien.
- `apt-get install build-essential ca-certificates locales` : outils de compilation inutiles à l'exécution.
- `EXPOSE 3000 4000 5000` : l'application n'écoute que sur le port 3000.
- `USER root` en fin de fichier : le conteneur tourne en root.
- `server.js` : middleware qui journalise chaque requête. Le code le signale lui-même comme verbeux et inutile. Ce point n'est pas une optimisation de l'image, il n'a pas été modifié.

## Étapes d'optimisation

### 1. Image de base allégée

- Problème : `node:latest` pèse 1.88 Go et contient des outils inutiles à l'exécution.
- Correction : `FROM node:20-alpine`.
- Taille obtenue : 216 Mo 

### 2. Installation des dépendances

- Problème : copie de `node_modules` depuis l'hôte, puis `npm install` classique. Cette version pèse 226 Mo (`tpdockeropti:node`).
- Correction : copier d'abord `package*.json`, puis `RUN npm ci --only=production`. 
- Taille obtenue : nc

### 3. Contexte de build

- Problème : tout le dossier est envoyé au build, dont `node_modules`.
- Correction : ajout d'un `.dockerignore` qui exclut `node_modules`, `.git`, `npm-debug.log`, `.DS_Store`, `.env`, `README.md`, `dockerfile`.
- Taille obtenue : 209 Mo

### 4. Ports

- Problème : `EXPOSE 3000 4000 5000`, alors que le code n'utilise que le port 3000.
- Correction : `EXPOSE 3000`.
- Taille : inchangée, `EXPOSE` ne modifie pas la taille de l'image.

### 5. Utilisateur non root

- Problème : le conteneur tourne en root.
- Correction : `USER node`, utilisateur fourni par l'image officielle Node.
- Taille finale : 209 Mo `USER` ne modifie pas la taille de l'image.

## Résumé des tailles

| Étape | Taille |
|---|---|---|
| Départ | 1.88 Go |
| Image alpine | 216 Mo |
| npm install classique | 226 Mo |
| Contexte de build | 209 Mo |
| Version finale | 209 Mo |

## Performances : temps

 Résultats de l'application de base

 - Temps total : 45 s, sans cache.
 - Étape la plus longue : apt-get install avec 16 s (environ un tiers du temps)
 - Téléchargement et décompression de l'image de base : 8,3 s + 4,7 s
 - Export de l'image : 9,6 s.
 - npm install : 3,0 s. npm run build : 1,0 s 

Resultats de l'application finale : 
- Temps total : 1.1s avec cache
- Étape la plus longue : docker.io/library/node:20-alpine avec 0.5 s

Résultats du Build relancé sans cache : 4,3 s
 - Image node:20-alpine déjà en cache
 - npm ci --only=production : 2,2 s
 - Export de l'image : 0,7 s.


## Historique des commits

- `208a6be` chore: ajout de l'application tpdockeroptimisation (application de base)
- `1ddd442` feat: ajout du gitignore et du dockerignore
- `14b0931` feat : dockerfile modification du FROM pour node:20-alpine
- `c18f692` feat : dockerfile modification pour supprimer COPY de l'app et ajout de l'ENV
- `2e2ab58` feat : dockerfile modification commande RUN, COPY et EXPOSE (3000 seulement)
- `9a9542b` feat : dockerfile modification USER
