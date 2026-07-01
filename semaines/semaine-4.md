# 🔗 Semaine 4 — Intégration Full-Stack : Assembler, Livrer, Défendre

> **Objectif de la semaine :** Connecter le frontend React (S3) au backend Spring Boot (S2), résoudre les frictions d'intégration, conteneuriser l'application avec Docker, et se préparer à défendre ses choix techniques en entretien.

**Durée estimée :** 35 à 40 heures  
**Prérequis :** Semaines 1, 2 et 3 validées — l'API tourne sur `localhost:8080`, le frontend sur `localhost:5173`

---

## 🧭 Table des matières

1. [Le concept clé — L'heure de vérité](#-le-concept-clé--lheure-de-vérité)
2. [Ce que vous allez résoudre cette semaine](#-ce-que-vous-allez-résoudre-cette-semaine)
3. [Notions techniques incontournables](#-notions-techniques-incontournables)
   - [CORS — Pourquoi le navigateur bloque votre API](#1--cors--pourquoi-le-navigateur-bloque-votre-api)
   - [Variables d'environnement — Ne jamais coder en dur](#2--variables-denvironnement--ne-jamais-coder-en-dur)
   - [Docker — Expédier votre code comme un colis](#3--docker--expédier-votre-code-comme-un-colis)
   - [Docker Compose — Orchestrer plusieurs conteneurs](#4--docker-compose--orchestrer-plusieurs-conteneurs)
   - [Git Flow — Les branches release et hotfix](#5--git-flow--les-branches-release-et-hotfix)
4. [Dojo de Code](#-dojo-de-code--lapplication-complète-en-production-locale)
5. [Simulation d'entretien technique](#-simulation-dentretien-technique)
6. [Critères de validation — Bilan des 4 semaines](#-critères-de-validation--bilan-des-4-semaines)
7. [Conseils de recruteur](#-conseils-de-recruteur)

---

## 🧠 Le concept clé — L'heure de vérité

### Quand les pièces doivent s'assembler

Pendant trois semaines, vous avez construit trois choses séparément :
- Un moteur (la logique Java de S1)
- Une carrosserie et un tableau de bord (l'API Spring Boot de S2)
- Un volant et des vitres (l'interface React de S3)

Jusqu'ici, chaque pièce fonctionnait dans son propre atelier.

Cette semaine, vous les posez côte à côte pour la première fois. Et c'est là que les vraies questions émergent :

*"Pourquoi mon frontend ne peut pas appeler mon API ?"*  
*"Comment l'application saura quelle URL utiliser en production ?"*  
*"Comment je livre tout ça sur une autre machine sans que rien ne casse ?"*

Ces questions n'ont pas de réponse théorique. Elles ont des solutions concrètes — et les maîtriser vous distingue d'un apprenant qui sait coder d'un développeur qui sait **livrer**.

> La différence entre coder et livrer, c'est exactement ce que cette semaine vous apprend.

---

## 🗺️ Ce que vous allez résoudre cette semaine

Voici les quatre obstacles concrets que tout développeur Full-Stack rencontre lors de sa première intégration :

| Obstacle | Symptôme | Solution |
| :--- | :--- | :--- |
| **CORS** | `Access to fetch blocked by CORS policy` dans la console | Configurer Spring Boot pour autoriser le frontend |
| **URL codée en dur** | `http://localhost:8080` dans le code React → cassé en production | Variables d'environnement `.env` |
| **"Ça marche chez moi"** | L'app tourne sur votre machine mais pas sur celle du collègue | Docker — standardiser l'environnement d'exécution |
| **Livraison manuelle** | Lancer 3 terminaux pour démarrer l'app → pas professionnel | Docker Compose — tout démarrer avec une commande |

Chacun de ces obstacles a une section dédiée ci-dessous.

---

## 📚 Notions techniques incontournables

### 1 · CORS — Pourquoi le navigateur bloque votre API

#### Le problème

Vous lancez votre frontend React sur `localhost:5173`. Il essaie d'appeler votre API sur `localhost:8080`. Et la console affiche :

```
Access to fetch at 'http://localhost:8080/api/livres'
from origin 'http://localhost:5173' has been blocked by CORS policy:
No 'Access-Control-Allow-Origin' header is present on the requested resource.
```

Beaucoup de débutants pensent que le serveur bloque la requête. **C'est faux.** C'est le **navigateur** qui bloque la réponse — par mesure de sécurité.

#### Comprendre par l'analogie

Imaginez un service de livraison qui n'accepte de déposer des colis que dans les immeubles qu'il connaît. Si vous habitez au 5173 et commandez depuis le bâtiment 8080, le livreur refusera de monter chez vous — même si le colis vous est bien destiné.

Le navigateur est ce livreur. **CORS (Cross-Origin Resource Sharing)** est le système qui dit au navigateur quelles origines sont autorisées à recevoir les réponses.

#### La solution côté Spring Boot

```java
// Option 1 : annotation par Controller (précis)
@CrossOrigin(origins = "http://localhost:5173")
@RestController
@RequestMapping("/api/livres")
public class LivreController { ... }

// Option 2 : configuration globale (recommandée en production)
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                    .allowedOrigins("http://localhost:5173")
                    .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                    .allowedHeaders("*");
            }
        };
    }
}
```

#### La solution alternative côté Vite (proxy)

Au lieu de modifier le backend, on peut demander à Vite de proxyer les requêtes :

```javascript
// vite.config.js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:8080',
        changeOrigin: true,
      }
    }
  }
})
```

Avec ce proxy, votre frontend appelle `/api/livres` (sans domaine) et Vite transfère la requête à `http://localhost:8080/api/livres`. Le navigateur ne voit jamais de cross-origin.

> **En développement** : le proxy Vite est pratique. **En production** : configurez CORS côté Spring Boot — Vite ne sera pas là.

---

### 2 · Variables d'environnement — Ne jamais coder en dur

#### Le problème

Vous avez écrit ceci dans votre service React :

```javascript
const BASE_URL = 'http://localhost:8080/api';
```

En local, ça fonctionne. En production, votre API sera sur `https://api.votredomaine.fr`. Vous devrez modifier le code, recompiler, redéployer. Et si vous oubliez une occurrence, l'app est cassée.

C'est ce qu'on appelle **coder en dur** (hardcoding) — une pratique à bannir dès maintenant.

#### Les variables d'environnement React (Vite)

Créez un fichier `.env` à la racine du projet frontend :

```bash
# .env (valeurs de développement — ne jamais committer en production)
VITE_API_URL=http://localhost:8080/api
```

> **Important :** avec Vite, les variables doivent commencer par `VITE_` pour être accessibles dans le code.

Utilisez-la dans votre service :

```javascript
// src/services/livreService.js
const BASE_URL = import.meta.env.VITE_API_URL;

export const livreService = {
  async getAll() {
    const reponse = await fetch(`${BASE_URL}/livres`);
    // ...
  }
};
```

Pour la production, créez un `.env.production` :

```bash
# .env.production
VITE_API_URL=https://api.votredomaine.fr/api
```

Vite utilisera automatiquement le bon fichier selon l'environnement.

#### Côté Spring Boot

```properties
# application.properties (développement)
spring.datasource.url=jdbc:h2:mem:bibliotheque
server.port=8080

# application-prod.properties (production — chargé avec --spring.profiles.active=prod)
spring.datasource.url=jdbc:postgresql://db:5432/bibliotheque
server.port=8080
```

**Règle absolue :** Ajoutez `.env` et `application-prod.properties` dans `.gitignore`. Ne committez jamais de mots de passe ou d'URLs de production.

```bash
# .gitignore
.env
.env.production
application-prod.properties
```

---

### 3 · Docker — Expédier votre code comme un colis

#### Le problème que Docker résout

Vous avez terminé votre application. Elle fonctionne parfaitement sur votre machine. Vous l'envoyez à un collègue. Il essaie de la lancer. Ça ne démarre pas.

*"Tu as Java 21 ?"* — *"Non, j'ai Java 17."*  
*"Tu as Node 20 ?"* — *"Non, j'ai Node 18."*  
*"Tu as le bon `application.properties` ?"* — *"C'est quoi ça ?"*

Ce problème a un nom dans le métier : **"It works on my machine"**. Il coûte des heures à chaque équipe qui ne l'a pas résolu.

Docker résout ce problème en empaquetant **le code ET son environnement d'exécution** dans une boîte standardisée appelée **conteneur**.

#### L'analogie du container maritime

Avant les containers maritimes, chaque marchandise s'emballait différemment. Un bateau transportait du bois, du tissu, de la porcelaine — chacun avec ses propres contraintes. Charger et décharger prenait des jours.

Les containers standardisés ont tout changé : **peu importe ce qu'il y a dedans**, la boîte a toujours la même forme. Elle se pose sur n'importe quel bateau, n'importe quel camion, n'importe quel port.

Docker fait pareil pour le code. Peu importe l'OS de votre collègue — Linux, Windows, Mac — le conteneur tourne de la même façon partout.

#### Les concepts fondamentaux

| Concept | Analogie | Définition technique |
| :--- | :--- | :--- |
| **Image** | La recette du plat | Un modèle immuable qui décrit l'environnement |
| **Conteneur** | Le plat préparé | Une instance en cours d'exécution d'une image |
| **Dockerfile** | La liste d'ingrédients et les étapes | Le fichier qui décrit comment construire l'image |
| **Registry** | Le livre de recettes partagé | Dépôt d'images (Docker Hub, GitHub Registry) |

```
Dockerfile  ──(build)──▶  Image  ──(run)──▶  Conteneur
(recette)                  (boîte vide)        (boîte active)
```

#### Installer Docker

Téléchargez **Docker Desktop** sur [docker.com](https://www.docker.com/products/docker-desktop/). Il inclut Docker Engine + Docker Compose.

Vérifiez l'installation :

```bash
docker --version        # Docker version 25.x.x
docker compose version  # Docker Compose version v2.x.x
```

#### Dockerfile pour le backend Spring Boot

Créez `Dockerfile` à la racine du projet Spring Boot :

```dockerfile
# Étape 1 — Build : compiler le JAR avec Maven
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN ./mvnw package -DskipTests

# Étape 2 — Run : exécuter uniquement le JAR (image plus légère)
FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

> **Multi-stage build** : on utilise deux images. La première (avec JDK) compile le code. La seconde (avec seulement JRE, plus légère) exécute le JAR. L'image finale ne contient pas le code source, seulement le binaire compilé.

Construire et tester l'image :

```bash
# Construire l'image (depuis le dossier du projet Spring Boot)
docker build -t bibliotheque-api:1.0 .

# Lancer le conteneur
docker run -p 8080:8080 bibliotheque-api:1.0

# L'API est maintenant sur http://localhost:8080
```

**Comprendre `-p 8080:8080`** : le premier `8080` est le port de votre machine. Le second est le port à l'intérieur du conteneur. Vous pouvez écrire `-p 9090:8080` pour accéder à l'API sur le port 9090 de votre machine.

#### Dockerfile pour le frontend React

Créez `Dockerfile` à la racine du projet React :

```dockerfile
# Étape 1 — Build : compiler React en fichiers statiques
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Étape 2 — Run : servir les fichiers avec Nginx (serveur web léger)
FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

Créez `nginx.conf` à côté du Dockerfile React :

```nginx
server {
    listen 80;

    location / {
        root   /usr/share/nginx/html;
        index  index.html;
        try_files $uri $uri/ /index.html;  # Gère le routing React (SPA)
    }

    location /api {
        proxy_pass http://api:8080;  # "api" = nom du service dans Docker Compose
        proxy_set_header Host $host;
    }
}
```

> **`try_files $uri $uri/ /index.html`** : sans cette ligne, un refresh sur `/livres` retournerait un 404 — Nginx cherche un fichier `/livres` qui n'existe pas. Cette règle dit : si le fichier n'existe pas, sers toujours `index.html` et laisse React gérer le routing.

---

### 4 · Docker Compose — Orchestrer plusieurs conteneurs

Votre application a maintenant deux conteneurs : l'API et le frontend. Les lancer séparément à la main n'est pas pratique. **Docker Compose** permet de les décrire et de les lancer ensemble avec **une seule commande**.

#### Comprendre par l'analogie

Docker Compose est le chef d'orchestre. Chaque musicien (conteneur) sait jouer sa partition (Dockerfile). Le chef d'orchestre s'assure que tout le monde joue ensemble, dans le bon ordre, sur la même scène (réseau).

#### `docker-compose.yml` — Le fichier de coordination

Créez `docker-compose.yml` à la racine d'un dossier `dojo-bibliotheque/` qui contient les deux projets :

```yaml
version: '3.9'

services:

  # Service 1 : le backend Spring Boot
  api:
    build: ./bibliotheque-backend
    container_name: bibliotheque-api
    ports:
      - "8080:8080"
    environment:
      - SPRING_PROFILES_ACTIVE=docker
    networks:
      - bibliotheque-network
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/actuator/health"]
      interval: 30s
      timeout: 10s
      retries: 3

  # Service 2 : le frontend React
  frontend:
    build: ./bibliotheque-frontend
    container_name: bibliotheque-frontend
    ports:
      - "80:80"
    depends_on:
      - api          # Démarre après l'API
    networks:
      - bibliotheque-network

networks:
  bibliotheque-network:
    driver: bridge   # Réseau interne — les conteneurs se voient par leur nom de service
```

#### Les commandes Docker Compose essentielles

```bash
# Construire les images et démarrer tous les services
docker compose up --build

# Démarrer en arrière-plan (mode détaché)
docker compose up --build -d

# Voir les logs de tous les services
docker compose logs -f

# Voir les logs d'un service spécifique
docker compose logs -f api

# Arrêter tous les services
docker compose down

# Arrêter et supprimer les volumes (base de données remise à zéro)
docker compose down -v

# Vérifier l'état des services
docker compose ps
```

Après `docker compose up --build` :
- Frontend accessible sur `http://localhost:80` (ou juste `http://localhost`)
- API accessible sur `http://localhost:8080`

> **Le réseau `bibliotheque-network`** : à l'intérieur de ce réseau, les conteneurs se joignent par leur **nom de service**. C'est pourquoi Nginx peut écrire `proxy_pass http://api:8080` — `api` est le nom du service Docker Compose, pas une URL externe.

---

### 5 · Git Flow — Les branches release et hotfix

En S1, vous avez appris `main`, `develop` et `feature/*`. Ce sont les 3 branches du quotidien. Voici les 2 restantes que vous utiliserez à la fin de chaque sprint.

#### La branche `release` — Préparer une livraison

Quand `develop` contient tout ce qui doit partir en production pour un sprint, on crée une branche `release` :

```bash
# Créer la release depuis develop
git checkout develop
git pull
git checkout -b release/1.0.0

# Sur cette branche : corrections mineures, mise à jour du numéro de version
# PAS de nouvelles fonctionnalités

# Quand la release est prête :
# 1. Fusionner dans main (c'est le code qui part en production)
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"
git push origin main --tags

# 2. Fusionner dans develop (pour récupérer les corrections faites sur la release)
git checkout develop
git merge --no-ff release/1.0.0
git push origin develop

# 3. Supprimer la branche release
git branch -d release/1.0.0
git push origin --delete release/1.0.0
```

#### La branche `hotfix` — Corriger un bug en production

Un bug critique est découvert sur `main` (en production). On ne peut pas attendre la prochaine release. On crée un `hotfix` :

```bash
# Créer le hotfix depuis main (PAS depuis develop)
git checkout main
git pull
git checkout -b hotfix/correction-emprunt-isbn-null

# Corriger le bug
git commit -m "fix: corriger NPE lors de l'emprunt avec ISBN null"

# Fusionner dans main
git checkout main
git merge --no-ff hotfix/correction-emprunt-isbn-null
git tag -a v1.0.1 -m "Hotfix: correction emprunt ISBN null"
git push origin main --tags

# Fusionner dans develop (le fix doit aussi aller dans la prochaine release)
git checkout develop
git merge --no-ff hotfix/correction-emprunt-isbn-null
git push origin develop

# Supprimer la branche hotfix
git branch -d hotfix/correction-emprunt-isbn-null
```

#### Schéma complet du modèle Git Flow

```
main        ●────────────────────●──────────●
            │ (v1.0.0)           │ (v1.0.1) │ (v1.1.0)
            │                   hotfix      │
develop   ──●──●──●──────────────●──────────●──
              │  │           release/1.0.0  │
feature/A  ───●──┘                          │
feature/B        ●──────────────────────────┘
```

#### Les tags — Marquer les versions

Un tag Git est un pointeur permanent sur un commit. Il marque les versions livrées :

```bash
# Lister les tags
git tag

# Créer un tag annoté (avec message — à préférer)
git tag -a v1.0.0 -m "Première version stable de la bibliothèque"

# Pousser les tags sur GitHub
git push origin --tags

# Revenir à une version précise (lecture seule)
git checkout v1.0.0
```

> En entreprise, les tags sont souvent utilisés par le pipeline CI/CD pour déclencher un déploiement automatique. Quand vous poussez un tag `v*`, le pipeline se lance.

---

## 🥋 Dojo de Code — L'application complète en production locale

> Vous allez assembler l'API Spring Boot et le frontend React dans un stack Docker Compose opérationnel. À la fin, une seule commande suffit pour démarrer l'intégralité de l'application.

### Avant de commencer — Les 3 questions

```
✅ Business  : Qui utilise ce livrable ? N'importe qui ayant Docker installé.
               Valeur : démarrer l'application complète sans configurer Java,
               Node ou Maven. "Clone + docker compose up" — c'est tout.

✅ Dev       : L'API et le frontend tournent sur des ports différents → CORS.
               L'URL de l'API ne doit pas être codée en dur dans React.
               La base de données H2 en mémoire disparaît à chaque restart
               → acceptable pour ce dojo, noter la limitation.

✅ QA        : Scénarios à valider après docker compose up :
               - http://localhost affiche la liste des livres (données de l'API)
               - La recherche filtre en temps réel
               - L'ajout d'un livre via le formulaire fonctionne sans CORS error
               - docker compose down puis up → l'app redémarre proprement
               - Un seul terminal, zéro commande manuelle supplémentaire
```

---

### Étape 1 — Corriger CORS dans Spring Boot (Jour 1)

Ouvrez le projet Spring Boot. Ajoutez la configuration CORS globale :

```java
// src/main/java/fr/dojo/bibliotheque/config/CorsConfig.java
package fr.dojo.bibliotheque.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                    .allowedOrigins(
                        "http://localhost:5173",  // Vite dev
                        "http://localhost"         // Nginx en Docker
                    )
                    .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                    .allowedHeaders("*");
            }
        };
    }
}
```

Testez : relancez Spring Boot, rouvrez le frontend React (`npm run dev`), vérifiez qu'il n'y a plus d'erreur CORS dans la console navigateur.

---

### Étape 2 — Externaliser les URLs dans React (Jour 1)

Créez `.env` à la racine du projet React :

```bash
VITE_API_URL=http://localhost:8080/api
```

Créez `.env.production` (utilisé par Docker lors du build) :

```bash
VITE_API_URL=/api
```

> En production Docker, le frontend et l'API sont sur le même domaine — Nginx proxye `/api` vers le conteneur `api`. Pas besoin d'URL absolue.

Mettez à jour `livreService.js` :

```javascript
const BASE_URL = import.meta.env.VITE_API_URL;
```

Ajoutez au `.gitignore` du frontend :

```
.env.production
```

---

### Étape 3 — Dockeriser le backend Spring Boot (Jour 2)

À la racine du projet Spring Boot, créez le `Dockerfile` :

```dockerfile
FROM eclipse-temurin:21-jdk-alpine AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN ./mvnw package -DskipTests

FROM eclipse-temurin:21-jre-alpine
WORKDIR /app
COPY --from=build /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Vérifiez que le `mvnw` est exécutable :

```bash
# Windows PowerShell — s'assurer que mvnw est dans le projet
# Le fichier est créé automatiquement par Spring Initializr
ls mvnw
```

Testez l'image seule :

```bash
docker build -t bibliotheque-api:1.0 .
docker run -p 8080:8080 bibliotheque-api:1.0
# → http://localhost:8080/api/livres doit répondre
docker stop $(docker ps -q)
```

---

### Étape 4 — Dockeriser le frontend React (Jour 3)

À la racine du projet React, créez `nginx.conf` :

```nginx
server {
    listen 80;

    location / {
        root   /usr/share/nginx/html;
        index  index.html;
        try_files $uri $uri/ /index.html;
    }

    location /api {
        proxy_pass         http://api:8080;
        proxy_set_header   Host $host;
        proxy_set_header   X-Real-IP $remote_addr;
    }
}
```

Créez le `Dockerfile` React :

```dockerfile
FROM node:20-alpine AS build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/conf.d/default.conf
EXPOSE 80
```

Testez l'image seule :

```bash
docker build -t bibliotheque-frontend:1.0 .
docker run -p 80:80 bibliotheque-frontend:1.0
# → http://localhost doit afficher l'interface (l'API ne répondra pas encore — c'est normal)
docker stop $(docker ps -q)
```

---

### Étape 5 — Assembler avec Docker Compose (Jour 4)

Créez un dossier racine `dojo-complet/` qui contient les deux projets :

```
dojo-complet/
├── docker-compose.yml
├── bibliotheque-backend/   ← votre projet Spring Boot
└── bibliotheque-frontend/  ← votre projet React
```

Créez `docker-compose.yml` :

```yaml
version: '3.9'

services:

  api:
    build: ./bibliotheque-backend
    container_name: bibliotheque-api
    ports:
      - "8080:8080"
    networks:
      - reseau-bibliotheque

  frontend:
    build: ./bibliotheque-frontend
    container_name: bibliotheque-frontend
    ports:
      - "80:80"
    depends_on:
      - api
    networks:
      - reseau-bibliotheque

networks:
  reseau-bibliotheque:
    driver: bridge
```

Lancez le tout :

```bash
cd dojo-complet
docker compose up --build
```

**Checklist de validation :**
- [ ] `http://localhost` affiche l'interface bibliothèque
- [ ] La liste des livres se charge (données de l'API)
- [ ] La recherche filtre en temps réel
- [ ] L'ajout d'un livre via le formulaire fonctionne
- [ ] Aucune erreur CORS dans la console navigateur
- [ ] `docker compose down` puis `docker compose up` redémarre proprement

---

### Étape 6 — Créer la release v1.0.0 avec Git Flow (Jour 5)

```bash
# Vérifier que develop est à jour
git checkout develop
git pull

# Créer la branche release
git checkout -b release/1.0.0

# Committer les fichiers d'intégration
git add .
git commit -m "feat: ajouter la configuration CORS globale"
git commit -m "feat: externaliser l'URL API dans les variables d'environnement"
git commit -m "feat: ajouter le Dockerfile pour le backend Spring Boot"
git commit -m "feat: ajouter le Dockerfile et nginx.conf pour le frontend React"
git commit -m "feat: ajouter docker-compose.yml pour l'orchestration complète"
git commit -m "docs: mettre à jour le README avec les instructions de démarrage"

# Fusionner dans main
git checkout main
git merge --no-ff release/1.0.0
git tag -a v1.0.0 -m "Version 1.0.0 — Application bibliothèque Full-Stack complète"
git push origin main --tags

# Fusionner dans develop
git checkout develop
git merge --no-ff release/1.0.0
git push origin develop

# Nettoyer
git branch -d release/1.0.0
git push origin --delete release/1.0.0
```

Résultat final sur GitHub :
- `main` — tagué `v1.0.0` — contient l'application complète dockerisée
- `develop` — prêt pour la prochaine itération
- Historique propre avec des PR par fonctionnalité

---

## 🎤 Simulation d'entretien technique

> Cette section vous prépare à l'exercice réel. Lisez chaque question, fermez le document, répondez à voix haute pendant 2 minutes. Enregistrez-vous si possible — c'est inconfortable, c'est exactement pour ça que ça fonctionne.

### Les formats d'entretien que vous rencontrerez

| Format | Durée typique | Ce qu'on évalue |
| :--- | :--- | :--- |
| **Questions techniques** | 30–60 min | Profondeur des connaissances, justification des choix |
| **Live coding** | 45–90 min | Raisonnement en temps réel, gestion du stress, communication |
| **Code review** | 30 min | Lecture de code, identification de problèmes, bonnes pratiques |
| **Présentation de projet** | 20–30 min | Communication technique, capacité à défendre ses choix |

### Questions transversales — bilan des 4 semaines

**Java (S1)**

*"Expliquez-moi la différence entre une interface et une classe abstraite. Quand choisissez-vous l'un ou l'autre ?"*
> L'interface définit un contrat pur — elle est préférable quand plusieurs classes non liées doivent partager un comportement. La classe abstraite partage du code commun entre classes liées par héritage. Depuis Java 8, les interfaces peuvent avoir des méthodes `default`, ce qui réduit encore le besoin de classes abstraites. En cas de doute : interface.

*"Qu'est-ce que le Garbage Collector et pourquoi est-ce important en entretien ?"*
> C'est le mécanisme automatique de libération mémoire de la JVM. Une réponse solide inclut : le fait que le GC ne s'exécute pas en temps déterministe, la notion de memory leak (références retenues inutilement), et le fait qu'on ne contrôle pas *quand* le GC s'exécute — seulement qu'il finira par le faire.

**Spring Boot (S2)**

*"Décrivez le flux complet d'une requête GET /api/livres dans votre application."*
> Réponse attendue : le client HTTP envoie une requête → Tomcat (serveur embarqué) la reçoit → Spring MVC la route vers `LivreController.getAll()` → le Controller délègue à `LivreService.findAll()` → le Service appelle `LivreRepository.findAll()` → Spring Data JPA génère le SQL → la BDD retourne les lignes → Hibernate mappe les lignes en objets `Livre` → le Service mappe en `LivreDto` → le Controller retourne la liste → Spring sérialise en JSON via Jackson → le client reçoit `200 OK` avec le JSON.

*"Pourquoi utilisez-vous des DTOs plutôt que d'exposer directement vos entités JPA ?"*
> Trois raisons : découplage (l'entité peut évoluer sans casser le contrat API), sécurité (on choisit ce qu'on expose — pas de champ `motDePasse`), validation (on valide les données entrantes sur le DTO avant qu'elles n'atteignent le domaine).

**React (S3)**

*"Qu'est-ce que le Virtual DOM et pourquoi React l'utilise-t-il ?"*
> Le Virtual DOM est une représentation en mémoire du DOM réel. Quand le state change, React calcule la différence entre l'ancien et le nouveau Virtual DOM (diff algorithm), puis applique uniquement les changements strictement nécessaires au vrai DOM. Manipuler le DOM réel est coûteux — le Virtual DOM minimise ces opérations.

*"Expliquez `useEffect(() => {...}, [])`. Que se passerait-il sans le tableau vide ?"*
> Avec `[]` : l'effet s'exécute une seule fois, au montage du composant. Sans tableau : l'effet s'exécute à chaque rendu. Si l'effet met à jour le state (comme un appel API), cela provoque un nouveau rendu, qui relance l'effet → boucle infinie.

**Intégration (S4)**

*"Qu'est-ce que CORS et qui le contrôle ?"*
> CORS est une politique de sécurité imposée par le **navigateur** (pas le serveur). Il bloque les réponses d'origines différentes de l'origine de la page. La solution est côté serveur : ajouter des headers `Access-Control-Allow-Origin` dans la réponse pour autoriser certaines origines. Spring Boot gère ça via `@CrossOrigin` ou `WebMvcConfigurer`.

*"Expliquez la différence entre une image Docker et un conteneur."*
> L'image est un modèle immuable — la recette. Le conteneur est une instance en cours d'exécution de cette image. On peut créer 10 conteneurs à partir de la même image. Modifier un conteneur n'affecte pas l'image.

### Le live coding — comment se comporter

**Avant d'écrire la moindre ligne :**
1. Reformulez le problème à voix haute pour valider que vous avez compris
2. Posez une question sur les cas limites (entrée vide ? valeurs négatives ?)
3. Décrivez votre approche en 30 secondes avant de coder

**Pendant le coding :**
- Pensez à voix haute en permanence — le recruteur évalue votre raisonnement, pas seulement votre code
- Commencez par le cas nominal, ajoutez les cas limites ensuite
- Si vous êtes bloqué : *"Je pense que je peux utiliser X ici — est-ce que je peux vérifier la syntaxe ?"* — un recruteur honnête vous laissera chercher

**Si vous ne savez pas :**
> *"Je ne connais pas cette API spécifique, mais voici comment je raisonnerais..."* vaut infiniment mieux que le silence ou l'invention d'une réponse fausse.

### Présenter votre projet dojo-bibliotheque

Préparez une présentation de 5 minutes de votre projet fil rouge :

```
1. Contexte (30 sec)
   "J'ai construit une application de gestion de bibliothèque Full-Stack
    sur 4 semaines, de Java pur jusqu'au déploiement Docker."

2. Architecture (1 min)
   Montrez le schéma : React → API Spring Boot → H2
   Expliquez pourquoi chaque couche existe.

3. Un choix technique défendu (2 min)
   "J'ai utilisé des DTOs pour découpler l'API de la persistance parce que..."
   "J'ai choisi HashMap<String, Livre> pour l'indexation par ISBN parce que..."

4. Ce que vous feriez ensuite (1 min)
   "En production, je remplacerais H2 par PostgreSQL, j'ajouterais
    Spring Security pour l'authentification, et un pipeline CI/CD."

5. Votre GitHub (30 sec)
   Montrez les branches Git Flow, les commits conventionnels, les PRs.
```

> La capacité à expliquer *pourquoi* un choix plutôt que *quoi* vous avez fait est ce qui transforme un candidat Junior en candidat recruté.

---

## ✅ Critères de validation — Bilan des 4 semaines

Fermez ce document. Pour chaque point, expliquez à voix haute sans aide. Si vous bloquez sur un point, retournez à la semaine correspondante.

### Java — Semaine 1
- [ ] Expliquer les 4 piliers de la POO avec un exemple de votre Dojo
- [ ] Justifier le choix de `HashMap` vs `ArrayList` pour l'indexation des livres par ISBN
- [ ] Décrire ce qui se passe en mémoire (Stack/Heap) lors d'un `new Emprunt(...)`
- [ ] Expliquer quand un objet devient éligible au Garbage Collector

### Spring Boot — Semaine 2
- [ ] Décrire le flux complet d'une requête `POST /api/livres` de bout en bout
- [ ] Expliquer la différence entre `@Service`, `@Repository` et `@Controller` au-delà des noms
- [ ] Justifier l'usage de DTOs dans votre API avec trois arguments distincts
- [ ] Expliquer ce que fait `@RestControllerAdvice` et ce qui se passe sans lui

### React — Semaine 3
- [ ] Expliquer la différence entre props et state avec un exemple du Dojo
- [ ] Expliquer pourquoi l'état du filtre vit dans `App` et non dans `RechercheBar`
- [ ] Décrire les 3 états d'un appel API et comment vous les gérez visuellement
- [ ] Expliquer `useEffect(() => {...}, [])` et la conséquence d'omettre le tableau

### Intégration & DevOps — Semaine 4
- [ ] Expliquer ce qu'est CORS, qui l'applique, et comment vous l'avez résolu
- [ ] Expliquer pourquoi `VITE_API_URL=/api` en production et `http://localhost:8080/api` en dev
- [ ] Expliquer la différence entre une image Docker et un conteneur
- [ ] Décrire ce que fait `docker compose up --build` étape par étape

### Git Flow & Pratiques d'équipe
- [ ] Reproduire de mémoire le schéma complet Git Flow (5 types de branches)
- [ ] Expliquer quand on crée une branche `release` vs un `hotfix`
- [ ] Écrire de mémoire la User Story de l'emprunt d'un livre avec ses critères d'acceptation
- [ ] Expliquer le rôle de chacun des 3 Amigos et la question qu'il apporte

---

## 🎯 Conseils de recruteur

### Ce que les recruteurs regardent en 2025

**Votre GitHub avant l'entretien**

Avant de vous recevoir, 80 % des recruteurs techniques ouvrent votre GitHub. Ils vérifient :
- Des commits réguliers (pas un push unique de 200 fichiers)
- Des messages de commit lisibles (`feat: ...`, `fix: ...`)
- Une structure de branches Git Flow visible
- Un `README.md` clair qui explique comment lancer le projet

**Le `README.md` de votre dojo-bibliotheque doit contenir :**

```markdown
## Lancer l'application

### Avec Docker (recommandé)
docker compose up --build
# → Frontend : http://localhost
# → API      : http://localhost:8080/api/livres

### En développement
# Backend
cd bibliotheque-backend && ./mvnw spring-boot:run

# Frontend
cd bibliotheque-frontend && npm install && npm run dev
```

Un candidat dont le README permet à n'importe qui de lancer l'app en 2 minutes a déjà un avantage.

### Ce qui élimine un candidat en 10 minutes

| Signal | Ce que le recruteur pense |
| :--- | :--- |
| Logique métier dans le Controller | Ne comprend pas la séparation des responsabilités |
| `fetch` directement dans le composant React | Pas de notion d'architecture frontend |
| Un seul commit "first commit" avec tout le code | Ne sait pas versionner progressivement |
| Incapable d'expliquer pourquoi un choix technique | A copié sans comprendre |
| Aucune gestion des erreurs | Ne pense pas aux cas réels |

### Ce qui fait la différence entre deux candidats Junior

Deux candidats ont fait le même projet. L'un dit :

> *"J'ai utilisé Spring Boot pour faire une API REST avec une base de données."*

L'autre dit :

> *"J'ai structuré l'API en trois couches — Controller, Service, Repository — pour respecter le principe de responsabilité unique. J'ai utilisé des DTOs pour découpler le contrat API de ma structure de persistance, ce qui me permettrait de changer de base de données sans modifier les endpoints. Pour le frontend React, j'ai fait remonter l'état de recherche dans le composant `App` parce qu'il était partagé entre la barre de recherche et la liste — c'est le pattern de lift state up."*

Le premier a fait un projet. Le second a **réfléchi** à ce qu'il faisait. C'est lui qu'on recrute.

---

## 🔗 Ressources complémentaires

| Ressource | Type | Pourquoi |
| :--- | :--- | :--- |
| [Docker — Get Started](https://docs.docker.com/get-started/) | Documentation | Le tutoriel officiel Docker, clair pour débutants |
| [Docker Compose — Reference](https://docs.docker.com/compose/compose-file/) | Documentation | Toutes les options du fichier `docker-compose.yml` |
| [Nginx Beginner's Guide](https://nginx.org/en/docs/beginners_guide.html) | Documentation | Comprendre votre serveur web de production |
| [Git Flow — Vincent Driessen](https://nvie.com/posts/a-successful-git-branching-model/) | Article | L'article original qui a défini Git Flow en 2010 |
| [Conventional Commits](https://www.conventionalcommits.org/fr/) | Standard | Rappel de la convention de messages de commit |
| [LeetCode — Easy](https://leetcode.com/problemset/?difficulty=EASY) | Exercices | Live coding — commencez par les Easy pour le réflexe |
| [System Design Primer](https://github.com/donnemartin/system-design-primer) | Guide | Pour les questions d'architecture en entretien Senior (aperçu) |

---

**[⬅ Semaine 3 — Frontend React](./semaine-3.md)** · **[⬆ Retour au README](../README.md)**

---

*Félicitations — vous avez parcouru les 4 semaines du Dojo Full-Stack.*  
*Vous êtes passé de zéro à une application complète, dockerisée, versionnée avec Git Flow, et défendable en entretien.*  
*La suite appartient à la pratique : contribuez à des projets open source, passez des entretiens, construisez.*
