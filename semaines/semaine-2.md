# 🔩 Semaine 2 — Backend Spring Boot : Construire une API REST Professionnelle

> **Objectif de la semaine :** Transformer le code Java de la Semaine 1 en une API REST complète avec Spring Boot. Comprendre pourquoi et comment Spring gère l'architecture à votre place, pour que vous puissiez vous concentrer sur la logique métier.

**Durée estimée :** 35 à 40 heures  
**Prérequis :** Semaine 1 validée — le projet `dojo-bibliotheque` doit fonctionner localement

---

## 🧭 Table des matières

1. [Le concept clé — Quand le cadre travaille pour vous](#-le-concept-clé--quand-le-cadre-travaille-pour-vous)
2. [Spring — Ce que c'est vraiment](#-spring--ce-que-cest-vraiment)
3. [Notions techniques incontournables](#-notions-techniques-incontournables)
   - [Spring Boot & l'IoC / Injection de Dépendances](#1--spring-boot--lioc--injection-de-dépendances)
   - [Architecture en couches](#2--architecture-en-couches)
   - [API REST — Le contrat HTTP](#3--api-rest--le-contrat-http)
   - [JPA / Hibernate — La persistance sans SQL à la main](#4--jpa--hibernate--la-persistance-sans-sql-à-la-main)
4. [Dojo de Code](#-dojo-de-code--la-bibliothèque-passe-en-production)
5. [Critères de validation](#-critères-de-validation)
6. [Conseils de recruteur](#-conseils-de-recruteur)

---

## 🧠 Le concept clé — Quand le cadre travaille pour vous

### Spring comme une agence de recrutement interne

Imaginez une grande entreprise qui ouvre un nouveau département.

Sans organisation, chaque chef de projet recrute lui-même ses assistants, achète lui-même ses fournitures, gère lui-même ses factures. Résultat : chaos, doublons, chaque chef fait les mêmes tâches administratives à la place de son vrai travail.

Maintenant, cette entreprise crée **une agence interne centralisée**. Dès qu'un chef de projet a besoin d'un comptable, d'un juriste ou d'un développeur, il le *demande* à l'agence. L'agence recrute, forme, et *livre* la ressource prête à l'emploi. Le chef de projet n'a plus qu'à travailler avec elle.

**C'est exactement ce que fait Spring.**

- Votre code ne crée plus ses dépendances lui-même (`new ServiceX()`).
- Il *déclare* ce dont il a besoin (`@Autowired`, ou mieux : injection par constructeur).
- Spring — le **conteneur IoC** — crée les objets, les configure, et les fournit au bon endroit, au bon moment.

> Le bénéfice immédiat : vos classes sont découplées. Vous pouvez remplacer une implémentation sans toucher au code qui l'utilise. Vous pouvez tester chaque pièce séparément. C'est la fondation de tout code maintenable.

---

### L'API REST comme un guichet de service public

Un guichet de mairie a des règles précises :
- Vous arrivez au bon **guichet** (URL = `/livres`, `/adherents`).
- Vous formulez votre **demande** avec un verbe clair : *"Je veux consulter"* (GET), *"Je veux déposer un dossier"* (POST), *"Je veux corriger une erreur"* (PUT), *"Je veux annuler"* (DELETE).
- L'agent vous répond avec un **code standard** : *200* (tout va bien), *404* (dossier introuvable), *400* (votre dossier est incomplet), *500* (erreur interne — revenez plus tard).

Votre API REST est ce guichet. Elle reçoit des requêtes HTTP structurées, les traite, et répond de manière prévisible. Côté client — qu'il soit React, mobile ou Postman — on n'a besoin de rien connaître de votre code interne. Juste le guichet et les règles.

---

## 🌱 Spring — Ce que c'est vraiment

### Le problème que Spring a résolu

Avant Spring, créer une application web Java demandait des semaines de configuration. Il fallait configurer manuellement un serveur (Tomcat, JBoss), écrire des fichiers XML pour tout relier, gérer soi-même la création et le cycle de vie de chaque objet. Un projet vide pesait déjà plusieurs centaines de lignes de configuration avant d'écrire une seule ligne de logique métier.

Spring Framework est né en 2003 avec une promesse simple : *laissez le framework gérer la plomberie, concentrez-vous sur ce qui a de la valeur — le code métier.*

### Spring Framework vs Spring Boot — la distinction cruciale

Beaucoup de débutants confondent les deux. Ce sont des choses différentes.

| | Spring Framework | Spring Boot |
| :--- | :--- | :--- |
| **Créé en** | 2003 | 2014 |
| **C'est quoi** | Un ensemble de bibliothèques Java | Une surcouche sur Spring Framework |
| **Configuration** | Manuelle et explicite | Automatique par convention |
| **Serveur web** | À installer et configurer séparément | Intégré (Tomcat embarqué) |
| **Démarrage** | Long et complexe | `mvn spring-boot:run` — c'est tout |

> **En pratique :** vous utilisez **Spring Boot** tous les jours. Spring Boot utilise Spring Framework en dessous. Vous bénéficiez de la puissance de Spring Framework sans sa complexité de configuration.

### Ce que Spring Boot fait pour vous automatiquement

Quand vous ajoutez une dépendance à votre `pom.xml`, Spring Boot **détecte ce que vous voulez faire** et configure tout à votre place. C'est ce qu'on appelle l'**auto-configuration**.

Exemple concret : vous ajoutez `spring-boot-starter-data-jpa` et une base de données H2.

Sans que vous écriviez une seule ligne de configuration, Spring Boot :
- Crée automatiquement le pool de connexions à la base de données
- Configure Hibernate comme implémentation JPA
- Crée les tables en base à partir de vos entités Java
- Rend disponibles tous les repositories que vous déclarez

Vous n'avez pas à comprendre comment tout ça fonctionne en détail pour commencer. Vous devez juste savoir **que ça existe** et **ce que ça fait**. La compréhension profonde vient avec la pratique.

### Le point d'entrée — votre première classe Spring Boot

Toute application Spring Boot commence par une seule classe avec une seule annotation :

```java
package fr.dojo.bibliotheque;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication  // ← Cette annotation fait BEAUCOUP de choses (voir ci-dessous)
public class BibliothequeApplication {

    public static void main(String[] args) {
        SpringApplication.run(BibliothequeApplication.class, args);
        // ↑ Lance le conteneur Spring, démarre le serveur Tomcat intégré,
        //   scanne les packages, configure tout automatiquement
    }
}
```

`@SpringBootApplication` est un raccourci pour trois annotations :
- `@SpringBootConfiguration` — déclare cette classe comme source de configuration
- `@EnableAutoConfiguration` — active la magie de l'auto-configuration
- `@ComponentScan` — dit à Spring de scanner tous les sous-packages pour trouver vos beans

### Ce qui se passe quand vous lancez l'application

Quand vous exécutez `main()`, voici ce qui se passe en coulisses, dans l'ordre :

```
1. La JVM exécute main()
        ↓
2. Spring démarre le conteneur IoC (ApplicationContext)
        ↓
3. Spring scanne tous les packages sous fr.dojo.bibliotheque/
        ↓
4. Spring trouve toutes les classes annotées (@Controller, @Service, @Repository...)
        ↓
5. Spring crée ces objets ("beans") dans le bon ordre
        ↓
6. Spring injecte les dépendances entre beans
        ↓
7. Spring Boot démarre le serveur Tomcat intégré sur le port 8080
        ↓
8. Votre application est prête : http://localhost:8080
```

Tout ça se passe en moins de 3 secondes. Vous verrez ce message dans la console :

```
  .   ____          _            __ _ _
 /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
 \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
  '  |____| .__|_| |_|_| |_\__, | / / / /
 =========|_|==============|___/=/_/_/_/
 :: Spring Boot ::               (v3.x.x)

Started BibliothequeApplication in 2.347 seconds
```

Ce logo ASCII — le "Spring banner" — signifie que tout s'est bien passé. Votre serveur tourne.

### Les "starters" — la clé du système de dépendances

Spring Boot organise ses fonctionnalités en **starters** : des paquets de dépendances prêts à l'emploi.

| Starter | Ce qu'il apporte | Quand l'utiliser |
| :--- | :--- | :--- |
| `spring-boot-starter-web` | Serveur Tomcat + Spring MVC + Jackson (JSON) | Toujours, pour une API REST |
| `spring-boot-starter-data-jpa` | JPA + Hibernate + pool de connexions | Quand vous avez une base de données |
| `spring-boot-starter-validation` | Bean Validation (annotations `@NotNull`, `@Size`...) | Pour valider les données entrantes |
| `spring-boot-starter-test` | JUnit 5 + Mockito + Spring Test | Pour les tests (inclus par défaut) |

Vous n'ajoutez pas des dizaines de dépendances séparées. Vous ajoutez **un starter**, et il tire tout ce qu'il faut.

---

## 📚 Notions techniques incontournables

### 1 · Spring Boot & l'IoC / Injection de Dépendances

#### Le conteneur IoC (Inversion of Control)

Sans Spring, vos classes construisent leurs dépendances elles-mêmes :

```java
// ❌ Couplage fort — difficile à tester, difficile à remplacer
public class LivreController {
    private LivreService service = new LivreService(); // vous gérez la création
}
```

Avec Spring, vous renversez le contrôle :

```java
// ✅ Spring crée et injecte le service — vous déclarez le besoin, pas la solution
@RestController
public class LivreController {

    private final LivreService livreService;

    // Injection par constructeur — la meilleure pratique
    public LivreController(LivreService livreService) {
        this.livreService = livreService;
    }
}
```

#### Les annotations fondamentales

| Annotation | Rôle | Où l'utiliser |
| :--- | :--- | :--- |
| `@SpringBootApplication` | Point d'entrée — lance le conteneur | Classe `main` |
| `@RestController` | Déclare un bean qui expose des endpoints REST | Couche Controller |
| `@Service` | Déclare un bean de logique métier | Couche Service |
| `@Repository` | Déclare un bean d'accès aux données | Couche Repository |
| `@Autowired` | Injection automatique (préférer le constructeur) | Partout où besoin |
| `@Component` | Bean générique géré par Spring | Classes utilitaires |

> **À retenir :** `@RestController`, `@Service`, `@Repository` sont tous des `@Component` spécialisés. Spring les détecte automatiquement au démarrage grâce au **component scan**.

#### Le cycle de vie d'un bean Spring

```
Démarrage de l'application
        ↓
Spring scanne les packages (@SpringBootApplication)
        ↓
Spring détecte tous les @Component (et ses dérivés)
        ↓
Spring crée les instances dans le bon ordre (résout les dépendances)
        ↓
Spring injecte les dépendances
        ↓
L'application est prête à recevoir des requêtes
```

---

### 2 · Architecture en couches

Une API Spring Boot professionnelle est toujours structurée en **trois couches distinctes**. Chaque couche a une responsabilité unique et ne communique qu'avec la couche adjacente.

```
Requête HTTP entrante
        ↓
┌─────────────────────────────────────┐
│         CONTROLLER                  │  ← Reçoit la requête, valide le format,
│  (@RestController)                  │    délègue, renvoie la réponse HTTP
└─────────────────┬───────────────────┘
                  ↓
┌─────────────────────────────────────┐
│           SERVICE                   │  ← Contient toute la logique métier.
│  (@Service)                         │    Ne sait pas d'où vient la requête.
└─────────────────┬───────────────────┘
                  ↓
┌─────────────────────────────────────┐
│         REPOSITORY                  │  ← Parle à la base de données.
│  (@Repository / JpaRepository)      │    Ne sait pas ce que fait le service.
└─────────────────┬───────────────────┘
                  ↓
            Base de données
```

**Pourquoi cette séparation ?**

- Le Controller peut changer (REST → GraphQL) sans toucher au Service.
- La base de données peut changer (H2 → PostgreSQL) sans toucher au Service.
- Le Service peut être testé unitairement sans base de données ni HTTP.

> **Piège à éviter :** Ne jamais mettre de logique métier dans le Controller, et ne jamais faire d'appels directs à la base de données depuis le Controller. Si vous voyez `@Autowired Repository` dans un Controller, c'est un signal d'alarme.

---

### 3 · API REST — Le contrat HTTP

#### Les verbes HTTP et leur sémantique

| Verbe | Signification | Idempotent | Exemple |
| :--- | :--- | :--- | :--- |
| `GET` | Lire une ressource | ✅ Oui | `GET /livres` — liste tous les livres |
| `POST` | Créer une ressource | ❌ Non | `POST /livres` — crée un nouveau livre |
| `PUT` | Remplacer une ressource entière | ✅ Oui | `PUT /livres/1` — remplace le livre 1 |
| `PATCH` | Modifier partiellement | ❌ Non | `PATCH /livres/1` — modifie le titre |
| `DELETE` | Supprimer une ressource | ✅ Oui | `DELETE /livres/1` — supprime le livre 1 |

> **Idempotent** : appeler la même requête 10 fois produit le même résultat qu'une seule fois. Un `GET` sur `/livres/1` renvoie toujours le même livre. Un `POST` crée un nouveau livre à chaque appel.

#### Les codes de statut HTTP essentiels

| Code | Signification | Quand l'utiliser |
| :--- | :--- | :--- |
| `200 OK` | Succès | GET, PUT, PATCH réussis |
| `201 Created` | Ressource créée | POST réussi |
| `204 No Content` | Succès sans corps | DELETE réussi |
| `400 Bad Request` | Requête malformée | Données manquantes ou invalides |
| `404 Not Found` | Ressource introuvable | ID inexistant |
| `409 Conflict` | Conflit d'état | ISBN déjà existant |
| `500 Internal Server Error` | Erreur serveur | Exception non gérée |

#### Structure d'un Controller REST

```java
@RestController
@RequestMapping("/api/livres")  // préfixe commun à tous les endpoints de ce Controller
public class LivreController {

    private final LivreService livreService;

    public LivreController(LivreService livreService) {
        this.livreService = livreService;
    }

    // GET /api/livres → liste tous les livres
    @GetMapping
    public List<LivreDto> getAll() {
        return livreService.findAll();
    }

    // GET /api/livres/{isbn} → un livre par ISBN
    @GetMapping("/{isbn}")
    public ResponseEntity<LivreDto> getByIsbn(@PathVariable String isbn) {
        return livreService.findByIsbn(isbn)
            .map(ResponseEntity::ok)
            .orElse(ResponseEntity.notFound().build());
    }

    // POST /api/livres → créer un livre
    @PostMapping
    public ResponseEntity<LivreDto> create(@RequestBody @Valid LivreCreationRequest request) {
        LivreDto cree = livreService.creer(request);
        return ResponseEntity.status(HttpStatus.CREATED).body(cree);
    }

    // DELETE /api/livres/{isbn} → supprimer un livre
    @DeleteMapping("/{isbn}")
    public ResponseEntity<Void> delete(@PathVariable String isbn) {
        livreService.supprimer(isbn);
        return ResponseEntity.noContent().build();
    }
}
```

#### DTOs — Ne jamais exposer vos entités directement

Un **DTO (Data Transfer Object)** est la forme que prend votre donnée côté API. Il découple la représentation externe de la structure interne.

```java
// L'entité (structure interne, liée à la BDD)
@Entity
public class Livre {
    @Id
    private String isbn;
    private String titre;
    private String auteur;
    private int anneePublication;
    private boolean disponible;
    // ...
}

// Le DTO (ce que l'API expose — vous choisissez ce qui sort)
public record LivreDto(
    String isbn,
    String titre,
    String auteur,
    int anneePublication,
    boolean disponible
) {}

// Le DTO de création (ce que le client envoie — pas d'isbn généré côté client)
public record LivreCreationRequest(
    @NotBlank String isbn,
    @NotBlank String titre,
    @NotBlank String auteur,
    @Min(1000) int anneePublication
) {}
```

> **Règle d'or :** Exposer une entité JPA directement dans un Controller, c'est comme montrer vos plans d'architecte à tous les clients. Le DTO est la façade propre et maîtrisée de votre données.

---

### 4 · JPA / Hibernate — La persistance sans SQL à la main

#### Qu'est-ce que JPA ?

JPA (Jakarta Persistence API) est un **traducteur** entre votre code Java et la base de données relationnelle. Vous écrivez des objets Java. JPA génère le SQL correspondant.

```java
// Vous écrivez ça :
@Entity
@Table(name = "livres")
public class Livre {

    @Id
    @Column(name = "isbn", nullable = false, unique = true)
    private String isbn;

    @Column(name = "titre", nullable = false)
    private String titre;

    @Column(name = "auteur", nullable = false)
    private String auteur;

    @Column(name = "annee_publication")
    private int anneePublication;

    @Column(name = "disponible")
    private boolean disponible = true;

    // Constructeur sans argument obligatoire pour JPA
    protected Livre() {}

    public Livre(String isbn, String titre, String auteur, int anneePublication) {
        this.isbn = isbn;
        this.titre = titre;
        this.auteur = auteur;
        this.anneePublication = anneePublication;
    }

    // Getters...
}
```

#### Spring Data JPA — Le repository magique

```java
// En étendant JpaRepository, vous obtenez GRATUITEMENT :
// findAll(), findById(), save(), delete(), count(), existsById()...
public interface LivreRepository extends JpaRepository<Livre, String> {

    // Spring génère automatiquement la requête SQL à partir du nom de la méthode
    List<Livre> findByAuteur(String auteur);
    List<Livre> findByDisponibleTrue();
    Optional<Livre> findByIsbn(String isbn);

    // Pour des requêtes plus complexes : JPQL (Java Persistence Query Language)
    @Query("SELECT l FROM Livre l WHERE l.anneePublication >= :annee ORDER BY l.titre")
    List<Livre> findRecentsOrderedByTitre(@Param("annee") int annee);
}
```

#### Configuration de la base de données (H2 en mémoire pour le développement)

Dans `src/main/resources/application.properties` :

```properties
# Base de données H2 en mémoire (disparaît à chaque redémarrage — parfait pour le dev)
spring.datasource.url=jdbc:h2:mem:bibliotheque
spring.datasource.driver-class-name=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=

# JPA / Hibernate
spring.jpa.hibernate.ddl-auto=create-drop
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true

# Console H2 accessible sur http://localhost:8080/h2-console
spring.h2.console.enabled=true
```

---

## 🥋 Dojo de Code — La Bibliothèque passe en Production

> Vous allez encapsuler la logique métier de la Semaine 1 dans une API REST Spring Boot. À la fin, votre bibliothèque sera accessible via des requêtes HTTP testées avec Postman.

### Avant de commencer — Les 3 questions

Appliquez systématiquement les 3 questions avant d'ouvrir IntelliJ :

```
✅ Business  : Qui utilise cette API ? Le frontend React (S3) et Postman aujourd'hui.
               Quelle valeur ? Permettre la gestion d'une bibliothèque sans accès direct à la BDD.

✅ Dev       : Quelles contraintes ? Un ISBN est unique. Un livre déjà emprunté ne peut pas
               être réemprunté. Un adhérent ne peut pas avoir plus de 3 emprunts simultanés.
               Que fait-on si la ressource n'existe pas ? → 404. Si conflit ? → 409.

✅ QA        : Scénarios à valider :
               - POST avec ISBN existant → 409
               - GET avec ISBN inexistant → 404
               - POST sans champ obligatoire → 400
               - Emprunt d'un livre déjà emprunté → 409
               - DELETE réussi → 204 sans corps
```

Ces scénarios deviennent vos cas de test Postman de l'Étape 6.

### Structure cible du projet

```
dojo-bibliotheque/
├── pom.xml
└── src/
    └── main/
        ├── java/
        │   └── fr/dojo/bibliotheque/
        │       ├── BibliothequeApplication.java
        │       ├── controller/
        │       │   ├── LivreController.java
        │       │   └── EmpruntController.java
        │       ├── service/
        │       │   ├── LivreService.java
        │       │   └── EmpruntService.java
        │       ├── repository/
        │       │   ├── LivreRepository.java
        │       │   └── EmpruntRepository.java
        │       ├── entity/
        │       │   ├── Livre.java
        │       │   ├── Adherent.java
        │       │   └── Emprunt.java
        │       ├── dto/
        │       │   ├── LivreDto.java
        │       │   ├── LivreCreationRequest.java
        │       │   └── EmpruntDto.java
        │       └── exception/
        │           ├── LivreNotFoundException.java
        │           ├── LivreIndisponibleException.java
        │           └── GlobalExceptionHandler.java
        └── resources/
            └── application.properties
```

### Étape 1 — Initialiser le projet Spring Boot (Jour 1)

Allez sur [start.spring.io](https://start.spring.io) et configurez :
- **Project :** Maven
- **Language :** Java
- **Spring Boot :** 3.x
- **Group :** `fr.dojo`
- **Artifact :** `bibliotheque`
- **Java :** 21
- **Dépendances :** Spring Web, Spring Data JPA, H2 Database, Validation

Téléchargez, dézippez, ouvrez dans IntelliJ.

**`pom.xml` — vérifiez que vous avez ces dépendances :**

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-jpa</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-validation</artifactId>
    </dependency>
    <dependency>
        <groupId>com.h2database</groupId>
        <artifactId>h2</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

### Étape 2 — Créer les entités JPA (Jour 1-2)

Convertissez vos classes S1 en entités JPA. Exemple pour `Livre` :

```java
@Entity
@Table(name = "livres")
public class Livre {

    @Id
    private String isbn;

    @Column(nullable = false)
    private String titre;

    @Column(nullable = false)
    private String auteur;

    private int anneePublication;
    private boolean disponible = true;

    protected Livre() {}

    public Livre(String isbn, String titre, String auteur, int anneePublication) {
        this.isbn = isbn;
        this.titre = titre;
        this.auteur = auteur;
        this.anneePublication = anneePublication;
    }

    // Getters + setters (nécessaires pour JPA)
    // ...
}
```

Pour `Emprunt`, utilisez les relations JPA :

```java
@Entity
@Table(name = "emprunts")
public class Emprunt {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "livre_isbn", nullable = false)
    private Livre livre;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "adherent_id", nullable = false)
    private Adherent adherent;

    @Column(nullable = false)
    private LocalDate dateEmprunt;

    @Column(nullable = false)
    private LocalDate dateRetourPrevue;

    private boolean estRendu = false;

    // ...
}
```

### Étape 3 — Créer les repositories (Jour 2)

```java
public interface LivreRepository extends JpaRepository<Livre, String> {
    List<Livre> findByAuteurContainingIgnoreCase(String auteur);
    List<Livre> findByDisponibleTrue();
}

public interface EmpruntRepository extends JpaRepository<Emprunt, Long> {
    List<Emprunt> findByAdherentIdAndEstRenduFalse(Long adherentId);
    List<Emprunt> findByEstRenduFalseAndDateRetourPrevueBefore(LocalDate date);
}
```

### Étape 4 — Implémenter les Services (Jour 3)

Portez la logique métier de S1 dans des `@Service` Spring.

```java
@Service
public class LivreService {

    private final LivreRepository livreRepository;

    public LivreService(LivreRepository livreRepository) {
        this.livreRepository = livreRepository;
    }

    public List<LivreDto> findAll() {
        return livreRepository.findAll().stream()
            .map(this::toDto)
            .toList();
    }

    public Optional<LivreDto> findByIsbn(String isbn) {
        return livreRepository.findById(isbn).map(this::toDto);
    }

    public LivreDto creer(LivreCreationRequest request) {
        if (livreRepository.existsById(request.isbn())) {
            throw new LivreDejaExistantException("ISBN déjà enregistré : " + request.isbn());
        }
        Livre livre = new Livre(request.isbn(), request.titre(), request.auteur(), request.anneePublication());
        return toDto(livreRepository.save(livre));
    }

    public void supprimer(String isbn) {
        if (!livreRepository.existsById(isbn)) {
            throw new LivreNotFoundException("Livre introuvable : " + isbn);
        }
        livreRepository.deleteById(isbn);
    }

    private LivreDto toDto(Livre livre) {
        return new LivreDto(livre.getIsbn(), livre.getTitre(), livre.getAuteur(),
                            livre.getAnneePublication(), livre.isDisponible());
    }
}
```

### Étape 5 — Gérer les erreurs proprement (Jour 4)

Ne laissez jamais une exception se propager brute jusqu'au client. Créez un gestionnaire global :

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(LivreNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(LivreNotFoundException ex) {
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("NOT_FOUND", ex.getMessage()));
    }

    @ExceptionHandler(LivreIndisponibleException.class)
    public ResponseEntity<ErrorResponse> handleIndisponible(LivreIndisponibleException ex) {
        return ResponseEntity.status(HttpStatus.CONFLICT)
            .body(new ErrorResponse("CONFLICT", ex.getMessage()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
            .map(e -> e.getField() + " : " + e.getDefaultMessage())
            .collect(Collectors.joining(", "));
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse("VALIDATION_ERROR", message));
    }
}

public record ErrorResponse(String code, String message) {}
```

### Étape 6 — Tester avec Postman (Jour 5)

Créez une **collection Postman** `Bibliothèque API` avec les requêtes suivantes :

| Requête | Méthode | URL | Corps |
| :--- | :--- | :--- | :--- |
| Lister tous les livres | GET | `localhost:8080/api/livres` | — |
| Obtenir un livre | GET | `localhost:8080/api/livres/978-0-13-468599-1` | — |
| Créer un livre | POST | `localhost:8080/api/livres` | `{"isbn":"978-0-13-468599-1","titre":"Clean Code","auteur":"Martin","anneePublication":2008}` |
| Supprimer un livre | DELETE | `localhost:8080/api/livres/978-0-13-468599-1` | — |
| Créer un emprunt | POST | `localhost:8080/api/emprunts` | `{"isbn":"978-0-13-468599-1","adherentId":1}` |

**Vérifications attendues :**
- POST avec un ISBN déjà existant → `409 Conflict`
- GET avec un ISBN inexistant → `404 Not Found`
- POST avec un champ manquant → `400 Bad Request` avec message explicite
- DELETE réussi → `204 No Content` (corps vide)

### Étape 7 — Versionner avec Git Flow (Jour 5)

```bash
# Démarrer depuis develop (jamais depuis main)
git checkout develop
git pull

# Une feature branch = un bloc fonctionnel cohérent
git checkout -b feature/spring-boot-init
git commit -m "feat: initialiser le projet Spring Boot avec les dépendances"
git push -u origin feature/spring-boot-init
# → Pull Request : feature/spring-boot-init → develop

git checkout develop ; git pull
git checkout -b feature/entites-jpa
git commit -m "feat: ajouter les entités JPA Livre, Adherent, Emprunt"
git commit -m "feat: implémenter LivreRepository et EmpruntRepository"
git push -u origin feature/entites-jpa
# → Pull Request : feature/entites-jpa → develop

git checkout develop ; git pull
git checkout -b feature/api-rest-livres
git commit -m "feat: implémenter LivreService avec logique métier"
git commit -m "feat: exposer l'API REST via LivreController"
git commit -m "feat: ajouter la gestion globale des erreurs"
git push -u origin feature/api-rest-livres
# → Pull Request : feature/api-rest-livres → develop
```

> Chaque Pull Request correspond à une **User Story complète** qui peut être démontrée et validée indépendamment. C'est la définition du "Done" en Scrum.

---

## ✅ Critères de validation

Fermez ce document. Ouvrez une page blanche. Expliquez à voix haute ou par écrit, sans aide :

- [ ] **L'IoC en une phrase** — Que signifie "inversion of control" concrètement ? Qui crée quoi ?
- [ ] **L'injection par constructeur vs `@Autowired` sur le champ** — Pourquoi préfère-t-on le constructeur ?
- [ ] **Les trois couches** — Que fait chacune ? Quelle règle ne doit jamais être violée entre elles ?
- [ ] **GET vs POST** — Quelle est la différence sémantique ? Laquelle est idempotente, et qu'est-ce que ça veut dire ?
- [ ] **Les codes 200, 201, 204, 400, 404, 409** — Citez un scénario concret pour chacun dans le contexte de la bibliothèque.
- [ ] **Le rôle du DTO** — Pourquoi ne pas exposer l'entité JPA directement ?
- [ ] **Spring Data JPA** — Qu'est-ce que `findByAuteurContainingIgnoreCase` génère comme SQL ? Comment le savez-vous ?
- [ ] **Le `@RestControllerAdvice`** — Quel problème résout-il ? Que se passe-t-il si on ne l'a pas ?
- [ ] **Git Flow** — Depuis quelle branche avez-vous créé vos feature branches dans ce Dojo ? Pourquoi pas depuis `main` ?

---

## 🎯 Conseils de recruteur

> Spring Boot est **le** framework Java en entreprise. Ces questions reviennent dans presque tous les entretiens Java Backend. Soyez précis.

### Questions fréquentes et ce que le recruteur attend

**"Expliquez-moi la différence entre `@Controller` et `@RestController`."**
> `@RestController` = `@Controller` + `@ResponseBody`. Avec `@RestController`, chaque méthode retourne directement un objet sérialisé en JSON. Avec `@Controller` seul, on retourne des noms de vues (templates HTML). Pour une API REST, on utilise toujours `@RestController`.

**"Qu'est-ce que l'injection de dépendances ?"**
> Ne dites pas "Spring crée les objets". Dites : *"C'est le principe selon lequel une classe ne crée pas ses dépendances — elle les reçoit. Spring implémente ce principe via son conteneur IoC, qui instancie les beans et les injecte là où ils sont déclarés nécessaires. Cela rend le code testable et découplé."*

**"Pourquoi utilise-t-on des DTOs ?"**
> Trois raisons à citer : (1) découplage entre la structure interne et le contrat API — on peut refactorer sans casser les clients, (2) sécurité — on contrôle ce qu'on expose (pas de champ `motDePasse` dans le JSON de réponse), (3) validation — on valide les données entrantes sur le DTO avant qu'elles n'atteignent le domaine.

**"Que se passe-t-il si vous faites `GET /livres/999` et que l'id n'existe pas ?"**
> Le mauvais candidat : *"Ça retourne null."*  
> Le bon candidat : *"Sans gestion explicite, Spring retourne 500. La bonne pratique est de lever une exception métier (`LivreNotFoundException`), capturée par un `@RestControllerAdvice` qui retourne un `404 Not Found` avec un corps d'erreur structuré."*

**"Quelle est la différence entre `@Transactional` sur le service et sur le repository ?"**
> Spring Data JPA met `@Transactional` par défaut sur les méthodes des repositories. Sur le service, on l'ajoute quand une opération métier couvre **plusieurs appels** au repository qui doivent réussir ou échouer ensemble — c'est l'atomicité. Si `créerEmprunt` met à jour le livre ET crée l'emprunt, les deux doivent être dans la même transaction.

### Ce que votre GitHub doit montrer

Un recruteur qui ouvre votre dépôt regarde :
1. **La structure des packages** — voit-il les trois couches clairement séparées ?
2. **Les commits** — racontent-ils une histoire de construction progressive ?
3. **Le `GlobalExceptionHandler`** — signe que vous pensez aux cas d'erreur, pas seulement au happy path
4. **La collection Postman** — idéalement committée dans le dépôt (`/postman/bibliotheque.json`)

Un recruteur qui ne voit que `Main.java` avec 200 lignes de Spring mélangé dedans a déjà sa réponse.

---

## 🔗 Ressources complémentaires

| Ressource | Type | Pourquoi |
| :--- | :--- | :--- |
| [Spring Initializr](https://start.spring.io) | Outil | Générer le projet Spring Boot |
| [Spring Boot Reference Docs](https://docs.spring.io/spring-boot/docs/current/reference/html/) | Documentation | La référence officielle |
| [Baeldung — Spring REST Tutorial](https://www.baeldung.com/rest-with-spring-series) | Tutoriel | Série complète et pratique |
| [HTTP Status Codes](https://developer.mozilla.org/fr/docs/Web/HTTP/Status) | Référence | Comprendre chaque code |
| [Spring Data JPA — Query Methods](https://docs.spring.io/spring-data/jpa/docs/current/reference/html/#jpa.query-methods) | Documentation | Maîtriser les méthodes dérivées |

---

**[⬅ Semaine 1 — Fondations Java](./semaine-1.md)** · **[Semaine 3 — Frontend React ➔](./semaine-3.md)**
 