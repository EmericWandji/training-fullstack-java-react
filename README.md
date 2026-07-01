# 🎯 Dojo Full-Stack — De Zéro à Junior Opérationnel

> **Un parcours intensif de 4 semaines pour acquérir les fondations d'un développeur Fullstack Java / Spring Boot / React employable dès la fin de la formation.**

---

## 🧭 Table des matières

1. [Le contrat pédagogique](#-le-contrat-pédagogique)
2. [Vue d'ensemble du parcours](#-vue-densemble-du-parcours)
3. [Détail des semaines](#-détail-des-semaines)
   - [Semaine 1 — Fondations Java](#semaine-1--fondations-java)
   - [Semaine 2 — Backend Spring Boot](#semaine-2--backend-spring-boot)
   - [Semaine 3 — Frontend React](#semaine-3--frontend-react)
   - [Semaine 4 — Intégration & Test Technique](#semaine-4--intégration--test-technique)
4. [Référentiel technologique](#-référentiel-technologique)
5. [Prérequis & outillage](#-prérequis--outillage)
6. [Principes pédagogiques](#-principes-pédagogiques)
7. [Profil à l'arrivée](#-profil-à-larrivée)

---

## 📜 Le contrat pédagogique

Ce dépôt n'est pas un cours magistral. C'est un **terrain d'entraînement**.

Chaque semaine suit le même rituel en cinq étapes :

| Étape | Ce que vous faites |
| :--- | :--- |
| **1 — Comprendre** | Lire l'analogie Feynman jusqu'à pouvoir l'expliquer à voix haute |
| **2 — Identifier** | Lister les notions techniques et les relier à l'analogie |
| **3 — Pratiquer** | Réaliser le Dojo de Code, seul, sans copier-coller |
| **4 — Valider** | Vérifier les critères de validation point par point |
| **5 — Défendre** | Simuler une question d'entretien à voix haute ou par écrit |

> **Règle d'or :** Si vous ne pouvez pas expliquer un concept avec vos propres mots, vous ne le maîtrisez pas encore. Recommencez l'étape 1.

---

## 🗺️ Vue d'ensemble du parcours

```
Semaine 1          Semaine 2          Semaine 3          Semaine 4
──────────         ──────────         ──────────         ──────────
⚙️ Java            🔩 Spring Boot     🎨 React            🔗 Full-Stack
  Fondations         API REST           Composants          Connexion
  POO / Git Flow     Architecture       State / Hooks       Test Technique
  Scrum / 3 Amigos   JPA / DI           Fetch API           Docker
     │                  │                  │                  │
     └──────────────────┴──────────────────┴──────────────────┘
                   Junior Opérationnel ✅
```

**Durée estimée :** 4 semaines × 35–40 h = **~150 heures** de travail effectif.

---

## 📅 Détail des semaines

### Semaine 1 — Fondations Java

> *Maître le langage, penser en objets, travailler en équipe dès le premier jour.*

| | |
| :--- | :--- |
| **Thèmes** | POO, Collections, Gestion mémoire (Stack/Heap), Git, Git Flow, Scrum (intro), 3 Amigos |
| **Dojo** | Mini-système de gestion d'une bibliothèque en Java pur, versionné avec Git Flow |
| **Compétence cible** | Écrire du Java lisible, committer proprement, organiser son travail avec les pratiques d'équipe |

**👉 [Accéder à la Semaine 1](./semaines/semaine-1.md)**

---

### Semaine 2 — Backend Spring Boot

> *Construire une API REST robuste, comprendre l'injection de dépendances.*

| | |
| :--- | :--- |
| **Thèmes** | Spring Boot, REST, IoC/DI, Couches (Controller / Service / Repository), JPA |
| **Dojo** | API CRUD complète pour gérer des livres (suite du Dojo S1) |
| **Compétence cible** | Exposer des endpoints REST, lire/écrire en base de données, tester avec Postman |

**👉 [Accéder à la Semaine 2](./semaines/semaine-2.md)**

---

### Semaine 3 — Frontend React

> *Construire des interfaces dynamiques, gérer l'état, réagir aux interactions.*

| | |
| :--- | :--- |
| **Thèmes** | Composants, Props, State, Hooks (`useState`, `useEffect`), Fetch API |
| **Dojo** | Interface React affichant et filtrant la liste des livres de l'API S2 |
| **Compétence cible** | Créer une SPA fonctionnelle, consommer une API REST, gérer les erreurs UI |

**👉 [Accéder à la Semaine 3](./semaines/semaine-3.md)**

---

### Semaine 4 — Intégration & Test Technique

> *Assembler le système complet, corriger les frictions, se préparer à l'entretien.*

| | |
| :--- | :--- |
| **Thèmes** | CORS, Variables d'environnement, Docker (intro), Git Flow, Simulation d'entretien |
| **Dojo** | Application Full-Stack déployée localement avec Docker Compose |
| **Compétence cible** | Livrer une application fonctionnelle de bout en bout, défendre ses choix techniques |

**👉 [Accéder à la Semaine 4](./semaines/semaine-4.md)**

---

## 🗂️ Référentiel technologique

| Concept / Technologie | Semaine(s) |
| :--- | :--- |
| **Java — POO (Classes, Héritage, Interfaces)** | S1 |
| **Java — Collections (List, Map, Set)** | S1 |
| **Java — Gestion mémoire (Stack / Heap)** | S1 |
| **Git — Commits, Branches, Pull Requests** | S1 |
| **Git Flow — Branches `main`, `develop`, `feature/*`** | S1, S4 |
| **Scrum — Sprints, User Stories, Backlog (intro)** | S1 |
| **3 Amigos — Alignement Business / Dev / QA** | S1 |
| **Spring Boot — IoC, DI, Annotations** | S2 |
| **Spring Boot — API REST (GET, POST, PUT, DELETE)** | S2 |
| **Spring Boot — Architecture en couches** | S2 |
| **JPA / Hibernate — Entités, Repositories** | S2 |
| **Postman — Tests d'API** | S2 |
| **React — Composants fonctionnels** | S3 |
| **React — Props & State** | S3 |
| **React — Hooks (`useState`, `useEffect`)** | S3 |
| **Fetch API / Axios** | S3 |
| **CORS & Variables d'environnement** | S4 |
| **Docker & Docker Compose (intro)** | S4 |
| **Git Flow — Approfondissement & bonnes pratiques** | S4 |
| **Simulation d'entretien technique** | S4 |

---

## 🔧 Prérequis & outillage

Installez ces outils **avant de commencer la Semaine 1** :

| Outil | Version recommandée | Rôle |
| :--- | :--- | :--- |
| **JDK** | 21 LTS | Compiler et exécuter le Java |
| **Maven** | 3.9+ | Gérer les dépendances Java |
| **Node.js** | 20 LTS | Exécuter React / npm |
| **Git** | Dernière stable | Contrôle de version |
| **Docker Desktop** | Dernière stable | Conteneurisation (S4) |
| **IntelliJ IDEA** | Community (gratuit) | IDE Java / Spring Boot |
| **VS Code** | Dernière stable | IDE React / TypeScript |
| **Postman** | Dernière stable | Tester les APIs REST |

---

## 💡 Principes pédagogiques

### 1. Comprendre avant de mémoriser
Chaque concept est d'abord ancré dans une situation concrète du quotidien, choisie pour être immédiatement parlante. L'image mentale précède toujours la définition formelle : une fois que vous *voyez* le concept, la syntaxe n'est plus qu'un détail.

### 2. Une image par concept, choisie pour vous
L'image utilisée varie selon ce qu'elle doit illustrer. Le seul critère : *un débutant complet doit comprendre sans avoir besoin d'un mot de jargon.* Si l'image a déjà été utilisée et peut créer un pont vers le nouveau concept, elle est réutilisée. Sinon, une nouvelle est choisie.

### 3. Le ratio 20/80
20 % de lecture, 80 % d'écriture active. Chaque notion lue doit être immédiatement mise en pratique dans le Dojo.

### 4. La progression en spirale
Les Dojos sont cumulatifs. La mini-bibliothèque du Dojo S1 devient l'API REST du Dojo S2, puis l'interface React du Dojo S3, puis l'application complète du Dojo S4. **Vous construisez un vrai projet de bout en bout.**

### 5. Le repos fait partie du travail
Le cerveau consolide ce qu'il a appris pendant les phases de repos. Une session de 90 minutes pleinement concentrée vaut plus que 4 heures d'attention dispersée. Respectez les pauses — elles ne sont pas du temps perdu.

---

## 🎯 Profil à l'arrivée

À l'issue de ces 4 semaines, vous serez capable de :

- [ ] Écrire du Java moderne, orienté objet, proprement structuré
- [ ] Construire une API REST avec Spring Boot, connectée à une base de données
- [ ] Créer une interface React fonctionnelle qui consomme cette API
- [ ] Versionner votre travail avec Git de manière professionnelle
- [ ] Livrer une application tournant localement avec Docker Compose
- [ ] Défendre vos choix techniques face à un recruteur ou un tech lead

---

**[Commencer maintenant — Semaine 1 ➔](./semaines/semaine-1.md)**

*4 Semaines · ~150 heures · Un projet fil rouge · Un Junior Opérationnel en sortie*