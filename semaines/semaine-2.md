# 🔧 Semaine 2 : Backend Spring Boot

## 🎯 Objectif Pédagogique

**Construire une API REST professionnelle avec Spring Boot en comprenant l'architecture en couches, l'injection de dépendances, et les patterns de l'industrie qui rendent le code scalable et testable.**

Après cette semaine, vous ne verrez plus Spring comme une "boîte noire magique" mais comme un outil structuré dont vous maîtrisez la logique.

---

## 🍽️ Comprendre Spring Boot : L'Analogie du Restaurant

Imaginez un restaurant bien organisé, avec une séparation stricte des rôles.

**Le serveur** (votre `@Controller`) reçoit les commandes des clients — les requêtes HTTP. Son rôle est limité : noter la commande, la transmettre au bon chef, et rapporter la réponse au client. Il ne cuisine pas.

**Le chef de cuisine** (votre `@Service`) connaît les recettes. C'est lui qui décide comment préparer le plat — la logique métier. Il ne sait pas si la commande vient d'une table en salle, d'un service en ligne ou d'un drive. Il cuisine, c'est tout.

**Le magasinier** (votre `@Repository`) gère les stocks — les données en base. Le chef lui demande des ingrédients, il les fournit sans se demander pourquoi.

**Le gestionnaire du restaurant** (Spring lui-même) embauche tout le monde au démarrage et s'assure que chacun dispose de ce dont il a besoin. Le serveur n'a pas à chercher le numéro du chef — le gestionnaire le lui a donné d'entrée. C'est **l'injection de dépendances**.

---

## 🏗️ L'Architecture en Couches (Layered Architecture)

Un code professionnel est un code **isolé**. Chaque couche ne connaît que celle qui est située immédiatement en dessous d'elle.

- **API REST LAYER** : `@RestController` (Validation, Mapping, Endpoints)
- **SERVICE LAYER** : `@Service` (Logique métier, Transactionnel)
- **REPOSITORY** : `@Repository` (Accès aux données, Requêtes SQL/JPA)
- **DATABASE** : PostgreSQL / H2 (Stockage physique)

> **Règle d'or :** Ne jamais mélanger le code SQL dans le Controller, et ne jamais faire d'appels HTTP dans le Repository.
---

## ⚡ Les Piliers du Développement Spring Boot

### 1. La gestion des DTOs (Data Transfer Objects)
Ne jamais renvoyer vos objets "Base de données" (`Entity`) directement vers l'extérieur. 
- **Sécurité :** Vous pourriez exposer des champs sensibles (mot de passe, hachage).
- **Découplage :** Si vous changez la table en base, votre API reste identique pour le client.

### 2. La Validation Bean (Jakarta EE)
Utilisez les annotations de validation pour bloquer les données corrompues avant même qu'elles n'atteignent votre code métier.

### 3. La Gestion des Erreurs (`@ControllerAdvice`)
Plus besoin de `try-catch` répétitifs. Centralisez toutes vos erreurs dans une classe unique qui intercepte les exceptions et renvoie un message propre au client.

---

## 🔧 Les Pièges Courants

| Le Bug / Symptôme | La Cause Réelle | La Solution Pro |
| :--- | :--- | :--- |
| `LazyInitializationException` | Accès à une donnée liée en base alors que la transaction est fermée. | Utilisez `@Transactional` correctement ou des DTOs avec Fetching optimisé. |
| Couplage API / BDD | L'API renvoie directement l'objet base de données. | Créez des DTOs de réponse distincts de vos Entities. |
| Pas de gestion du 404 | Retourne 200 OK avec `null` quand la ressource manque. | Utilisez `orElseThrow()` + ExceptionHandler personnalisé. |
| Validation côté client seul | Les données malformées atteignent la BDD. | `@Valid` sur le `@RequestBody` + Bean Validation. |

---

## 🥋 Dojo de Code : L'API de gestion de contenu

### Phase 1 : Configuration
1. Initialisez un projet Spring Boot avec `Spring Data JPA`, `PostgreSQL Driver`, et `Validation`.
2. Configurez votre `application.yml` pour le profil `dev` (H2 en mémoire) et `prod` (PostgreSQL local).

### Phase 2 : Modélisation
1. Créez une `Entity` nommée `Article` avec : `id`, `titre`, `contenu`, `datePublication`.
2. Créez le `Repository` correspondant héritant de `JpaRepository`.

### Phase 3 : Logique Métier (Service)
1. Créez `ArticleService`.
2. Implémentez la méthode `creerArticle(ArticleRequest request)`.

### Phase 4 : Contrôleur REST
1. Créez `ArticleController` avec les endpoints :
   - `GET /api/articles`
   - `POST /api/articles` (avec validation)
   - `GET /api/articles/{id}` (avec gestion du 404)

---

## 🏁 Auto-Évaluation (Diagnostic Rapide)

1. *Pourquoi l'injection de dépendances rend-elle les tests unitaires plus simples ?*
2. *Quelle est la différence entre une `@Entity` et un `DTO` ?*
3. *Si un Service appelle un autre Service, est-ce un problème ? Si oui, pourquoi ?*

---

## 📚 Ressources

- **Livre** : "Spring in Action" — Craig Walls (6e édition)
- **Docs** : spring.io/guides (tutoriels officiels, tous testés)
- **Référence** : Baeldung.com (articles de qualité sur chaque aspect de Spring)
- **Tests** : "Testing Spring Boot Applications" — Baeldung Guide

---

## 🎯 Résumé

| Jour | Focus | Livrable attendu |
|------|-------|-----------------|
| 1 | Structure du projet, configuration | Application qui démarre, H2 Console accessible |
| 2 | Entités JPA, relations, Repositories | Articles et Commentaires persistés en base |
| 3 | DTOs, Services, logique métier | CRUD complet avec transformations DTO ↔ Entity |
| 4 | Controllers, validation, gestion d'erreurs | API testable via Postman avec bons codes HTTP |
| 5 | Tests unitaires et Controller | Tests passants pour Service et Controller |

---

**[← Module 1 (Fondations Java)](./semaine-1.md) | [Retour au README](../README.md) | [Passer au Module 3 (Frontend React) →](./semaine-3.md)**