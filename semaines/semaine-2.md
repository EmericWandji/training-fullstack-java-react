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

Résultat : si vous remplacez le chef (changez d'implémentation de service), le serveur ne le sait même pas. Si vous voulez tester la cuisine sans ouvrir le restaurant, vous simulez un magasinier (mock). Chaque partie est testable indépendamment.

---

## 📚 Notions Techniques Incontournables

### Jour 1 : Architecture Spring Boot Fondamentale

- [ ] **Structure d'un projet Spring Boot** : `pom.xml`, `application.properties`, organisation des packages (`controller`, `service`, `repository`, `model`, `dto`, `exception`)
- [ ] **Dépendances essentielles** : `spring-boot-starter-web` (REST + Tomcat embarqué), `spring-boot-starter-data-jpa` (persistance), `h2` (base en mémoire pour le développement)
- [ ] **Architecture en couches** : Controller → Service → Repository. Chaque couche ne connaît que la couche immédiatement en dessous.
- [ ] **`@SpringBootApplication`** : combinaison de `@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan` — Spring scanne tous les beans dans le package et ses sous-packages
- [ ] **Démarrage de l'application** : la méthode `main()` lance un serveur Tomcat embarqué sur le port 8080 par défaut
- [ ] **`application.properties`** : configuration du port, de la base de données, des niveaux de log, des profils d'environnement

### Jour 2 : API REST et HTTP

- [ ] **Verbes HTTP et leur sémantique** : `GET` (lire, idempotent), `POST` (créer), `PUT` (remplacer complètement), `PATCH` (modifier partiellement), `DELETE` (supprimer)
- [ ] **Codes de réponse HTTP** : 200 OK, 201 Created, 204 No Content, 400 Bad Request, 401 Unauthorized, 403 Forbidden, 404 Not Found, 409 Conflict, 500 Internal Server Error
- [ ] **`@RestController`** : combinaison de `@Controller` et `@ResponseBody`, sérialise automatiquement les retours en JSON
- [ ] **`@RequestMapping`** : définit le chemin de base du contrôleur
- [ ] **`@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`** : routes déclaratives sur les méthodes
- [ ] **Paramètres des endpoints** : `@PathVariable` (dans l'URL), `@RequestParam` (query string), `@RequestBody` (corps de la requête JSON)
- [ ] **`ResponseEntity<T>`** : contrôle total sur le code HTTP retourné et les headers

### Jour 3 : Injection de Dépendances et Beans

- [ ] **Inversion de Contrôle (IoC)** : ce n'est plus votre code qui crée ses dépendances avec `new`, c'est Spring qui les crée et les injecte
- [ ] **Stéréotypes** : `@Component` (bean générique), `@Service` (logique métier), `@Repository` (accès aux données — Spring gère automatiquement la traduction des exceptions JPA), `@Controller` / `@RestController` (couche HTTP)
- [ ] **Injection par constructeur** : la façon recommandée — les dépendances sont `final`, l'objet est toujours dans un état cohérent, et les tests sont plus simples
- [ ] **`@Autowired`** : peut être omis sur les constructeurs à partir de Spring 4.3 si la classe n'a qu'un seul constructeur
- [ ] **`@Qualifier` et `@Primary`** : résoudre l'ambiguïté quand plusieurs beans implémentent la même interface
- [ ] **Scopes** : `singleton` (un seul bean partagé — défaut), `prototype` (nouvelle instance à chaque injection), `request` / `session` (pour les applications web)
- [ ] **`@PostConstruct` et `@PreDestroy`** : callbacks de cycle de vie pour initialisation et nettoyage

### Jour 4 : Accès aux Données avec JPA

- [ ] **Annotations JPA fondamentales** : `@Entity`, `@Table(name="...")`, `@Id`, `@GeneratedValue`, `@Column`, `@Transient`
- [ ] **Relations entre entités** : `@OneToMany`, `@ManyToOne`, `@ManyToMany`, `@OneToOne` — avec le `mappedBy` côté propriétaire
- [ ] **Fetch types** : `LAZY` (chargement à la demande — défaut pour les collections), `EAGER` (chargé avec l'entité). Toujours préférer LAZY et charger explicitement si besoin.
- [ ] **Spring Data JPA** : hériter de `JpaRepository<Entity, IdType>` donne automatiquement `save()`, `findById()`, `findAll()`, `delete()`, `count()`
- [ ] **Dérivation de méthodes** : Spring génère la requête SQL à partir du nom de méthode — `findByAuteurIgnoreCase(String auteur)`, `findByTitreContaining(String terme)`
- [ ] **`@Query`** : requêtes JPQL ou SQL natives pour les cas complexes
- [ ] **`@Transactional`** : encapsule une méthode dans une transaction — tout réussit ou tout est annulé (rollback). À mettre sur les méthodes de Service qui modifient des données.
- [ ] **DTOs (Data Transfer Objects)** : des classes simples pour recevoir les données de la requête ou envoyer des données en réponse, sans exposer les entités JPA directement

### Jour 5 : Gestion des Erreurs, Validation et Tests

- [ ] **Exceptions personnalisées** : créer des classes héritant de `RuntimeException` avec un message métier clair
- [ ] **`@RestControllerAdvice`** : intercepte les exceptions au niveau global et retourne des réponses JSON structurées avec les bons codes HTTP
- [ ] **`@ExceptionHandler`** : méthode dans le ControllerAdvice qui gère un type d'exception spécifique
- [ ] **Validation avec Bean Validation** : `@NotNull`, `@NotBlank`, `@Email`, `@Size`, `@Min`, `@Max` sur les DTOs, déclenchés avec `@Valid` dans le contrôleur
- [ ] **`BindingResult`** : récupérer les erreurs de validation pour les inclure dans la réponse
- [ ] **Logging avec SLF4J** : `LoggerFactory.getLogger(MaClasse.class)`, niveaux DEBUG, INFO, WARN, ERROR — ce qu'on logue à chaque niveau
- [ ] **Tests unitaires avec JUnit 5 et Mockito** : `@ExtendWith(MockitoExtension.class)`, `@Mock`, `@InjectMocks`, `when(...).thenReturn(...)`, `verify(...)`
- [ ] **`@WebMvcTest`** : teste un contrôleur en isolation avec `MockMvc`, sans démarrer le contexte Spring complet

---

## 🥋 Dojo de Code : API REST Blog Complète

### Contexte

Vous construisez le backend d'une plateforme de blog. Les utilisateurs créent des articles, les consultent, les modifient, les suppriment, et les commentent. C'est l'API que vous consommerez depuis React en Semaine 3.

---

### Phase 1 : Structure du Projet (Jour 1)

```java
// 1. Créez le projet via Spring Initializr (start.spring.io) ou votre IDE
//    Groupe: com.formation | Artifact: blog-api
//    Dépendances: Spring Web, Spring Data JPA, H2 Database, Validation

// 2. application.properties
spring.datasource.url=jdbc:h2:mem:blogdb
spring.datasource.driver-class-name=org.h2.Driver
spring.jpa.hibernate.ddl-auto=create-drop
spring.h2.console.enabled=true
spring.h2.console.path=/h2-console

logging.level.root=WARN
logging.level.com.formation=DEBUG
logging.level.org.springframework.web=INFO

// 3. Organisation des packages
com.formation.blogapi
  ├── controller/
  │     ├── ArticleController.java
  │     └── CommentaireController.java
  ├── service/
  │     ├── ArticleService.java
  │     └── CommentaireService.java
  ├── repository/
  │     ├── ArticleRepository.java
  │     └── CommentaireRepository.java
  ├── model/         // Entités JPA
  │     ├── Article.java
  │     └── Commentaire.java
  ├── dto/           // Objets d'entrée/sortie API
  │     ├── ArticleRequest.java
  │     ├── ArticleResponse.java
  │     └── CommentaireRequest.java
  ├── exception/
  │     ├── ArticleNotFoundException.java
  │     ├── InvalidArticleException.java
  │     └── GlobalExceptionHandler.java
  └── BlogApiApplication.java
```

---

### Phase 2 : Entités et Repositories (Jour 2)

```java
// TODO: Entité Article
@Entity
@Table(name = "articles")
public class Article {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, length = 200)
    private String titre;

    @Column(nullable = false, columnDefinition = "TEXT")
    private String contenu;

    @Column(nullable = false)
    private String auteur;

    @Column(nullable = false)
    private LocalDateTime dateCreation;

    @OneToMany(mappedBy = "article", cascade = CascadeType.ALL, orphanRemoval = true, fetch = FetchType.LAZY)
    private List<Commentaire> commentaires = new ArrayList<>();

    @PrePersist
    public void prePersist() {
        this.dateCreation = LocalDateTime.now();
    }

    // Constructeurs, getters, setters
}

// TODO: Entité Commentaire
@Entity
@Table(name = "commentaires")
public class Commentaire {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    @Column(nullable = false, columnDefinition = "TEXT")
    private String contenu;

    @Column(nullable = false)
    private String auteur;

    @Column(nullable = false)
    private LocalDateTime dateCreation;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "article_id", nullable = false)
    private Article article;

    @PrePersist
    public void prePersist() {
        this.dateCreation = LocalDateTime.now();
    }

    // Constructeurs, getters, setters
}

// TODO: Repositories
public interface ArticleRepository extends JpaRepository<Article, Long> {

    // Spring génère la requête à partir du nom
    List<Article> findByAuteurIgnoreCase(String auteur);
    List<Article> findByTitreContainingIgnoreCase(String terme);
    List<Article> findAllByOrderByDateCreationDesc();

    // Requête personnalisée pour les statistiques
    @Query("SELECT COUNT(a) FROM Article a WHERE a.auteur = :auteur")
    long countByAuteur(@Param("auteur") String auteur);
}

public interface CommentaireRepository extends JpaRepository<Commentaire, Long> {
    List<Commentaire> findByArticleIdOrderByDateCreationAsc(Long articleId);
    long countByArticleId(Long articleId);
}
```

---

### Phase 3 : DTOs et Services (Jour 3)

```java
// TODO: DTOs — jamais l'entité directement dans l'API

// Entrée (ce que le client envoie)
public class ArticleRequest {
    @NotBlank(message = "Le titre est obligatoire")
    @Size(min = 3, max = 200, message = "Le titre doit faire entre 3 et 200 caractères")
    private String titre;

    @NotBlank(message = "Le contenu est obligatoire")
    @Size(min = 10, message = "Le contenu doit faire au moins 10 caractères")
    private String contenu;

    @NotBlank(message = "L'auteur est obligatoire")
    private String auteur;

    // Getters, setters
}

// Sortie (ce que le serveur retourne)
public class ArticleResponse {
    private Long id;
    private String titre;
    private String contenu;
    private String auteur;
    private LocalDateTime dateCreation;
    private int nombreCommentaires;

    // Constructeur à partir d'une entité Article
    public static ArticleResponse from(Article article) {
        ArticleResponse response = new ArticleResponse();
        response.setId(article.getId());
        response.setTitre(article.getTitre());
        response.setContenu(article.getContenu());
        response.setAuteur(article.getAuteur());
        response.setDateCreation(article.getDateCreation());
        response.setNombreCommentaires(article.getCommentaires().size());
        return response;
    }
}

// TODO: Service avec toute la logique métier
@Service
@Transactional
public class ArticleService {

    private static final Logger log = LoggerFactory.getLogger(ArticleService.class);
    private final ArticleRepository articleRepository;

    public ArticleService(ArticleRepository articleRepository) {
        this.articleRepository = articleRepository;
    }

    public ArticleResponse creerArticle(ArticleRequest request) {
        log.info("Création d'un article par {}", request.getAuteur());
        Article article = new Article();
        article.setTitre(request.getTitre());
        article.setContenu(request.getContenu());
        article.setAuteur(request.getAuteur());
        Article saved = articleRepository.save(article);
        log.debug("Article créé avec l'id {}", saved.getId());
        return ArticleResponse.from(saved);
    }

    @Transactional(readOnly = true)
    public ArticleResponse getArticleParId(Long id) {
        Article article = articleRepository.findById(id)
            .orElseThrow(() -> new ArticleNotFoundException(id));
        return ArticleResponse.from(article);
    }

    @Transactional(readOnly = true)
    public List<ArticleResponse> getTousLesArticles() {
        return articleRepository.findAllByOrderByDateCreationDesc()
            .stream()
            .map(ArticleResponse::from)
            .collect(Collectors.toList());
    }

    public ArticleResponse mettreAJour(Long id, ArticleRequest request) {
        Article article = articleRepository.findById(id)
            .orElseThrow(() -> new ArticleNotFoundException(id));
        article.setTitre(request.getTitre());
        article.setContenu(request.getContenu());
        // L'auteur ne change pas
        return ArticleResponse.from(articleRepository.save(article));
    }

    public void supprimer(Long id) {
        if (!articleRepository.existsById(id)) {
            throw new ArticleNotFoundException(id);
        }
        articleRepository.deleteById(id);
        log.info("Article {} supprimé", id);
    }

    @Transactional(readOnly = true)
    public List<ArticleResponse> rechercherParTitre(String terme) {
        return articleRepository.findByTitreContainingIgnoreCase(terme)
            .stream()
            .map(ArticleResponse::from)
            .collect(Collectors.toList());
    }
}
```

---

### Phase 4 : Controllers et Endpoints REST (Jour 3-4)

```java
// TODO: Controller — uniquement du routing, aucune logique métier ici
@RestController
@RequestMapping("/api/articles")
public class ArticleController {

    private final ArticleService articleService;

    public ArticleController(ArticleService articleService) {
        this.articleService = articleService;
    }

    // GET /api/articles
    @GetMapping
    public ResponseEntity<List<ArticleResponse>> listerArticles() {
        return ResponseEntity.ok(articleService.getTousLesArticles());
    }

    // GET /api/articles/recherche?terme=java
    @GetMapping("/recherche")
    public ResponseEntity<List<ArticleResponse>> rechercher(@RequestParam String terme) {
        return ResponseEntity.ok(articleService.rechercherParTitre(terme));
    }

    // GET /api/articles/{id}
    @GetMapping("/{id}")
    public ResponseEntity<ArticleResponse> getArticle(@PathVariable Long id) {
        return ResponseEntity.ok(articleService.getArticleParId(id));
    }

    // POST /api/articles
    @PostMapping
    public ResponseEntity<ArticleResponse> creerArticle(@Valid @RequestBody ArticleRequest request) {
        ArticleResponse created = articleService.creerArticle(request);
        URI location = URI.create("/api/articles/" + created.getId());
        return ResponseEntity.created(location).body(created);
    }

    // PUT /api/articles/{id}
    @PutMapping("/{id}")
    public ResponseEntity<ArticleResponse> mettreAJour(
            @PathVariable Long id,
            @Valid @RequestBody ArticleRequest request) {
        return ResponseEntity.ok(articleService.mettreAJour(id, request));
    }

    // DELETE /api/articles/{id}
    @DeleteMapping("/{id}")
    public ResponseEntity<Void> supprimer(@PathVariable Long id) {
        articleService.supprimer(id);
        return ResponseEntity.noContent().build();
    }
}

// TODO: Controller pour les commentaires (URL imbriquée)
@RestController
@RequestMapping("/api/articles/{articleId}/commentaires")
public class CommentaireController {
    // Implémentez GET (liste), POST (créer), DELETE (supprimer)
    // Le @PathVariable articleId est accessible dans chaque méthode
}
```

---

### Phase 5 : Gestion des Erreurs et Tests (Jour 4-5)

```java
// TODO: Exceptions personnalisées
public class ArticleNotFoundException extends RuntimeException {
    public ArticleNotFoundException(Long id) {
        super("Article introuvable avec l'id : " + id);
    }
}

public class InvalidArticleException extends RuntimeException {
    public InvalidArticleException(String raison) {
        super("Article invalide : " + raison);
    }
}

// TODO: Gestion globale — une seule classe gère toutes les erreurs
@RestControllerAdvice
public class GlobalExceptionHandler {

    private static final Logger log = LoggerFactory.getLogger(GlobalExceptionHandler.class);

    @ExceptionHandler(ArticleNotFoundException.class)
    public ResponseEntity<ErrorResponse> handleNotFound(ArticleNotFoundException ex) {
        log.warn("Article non trouvé : {}", ex.getMessage());
        return ResponseEntity.status(HttpStatus.NOT_FOUND)
            .body(new ErrorResponse("ARTICLE_NOT_FOUND", ex.getMessage()));
    }

    @ExceptionHandler(MethodArgumentNotValidException.class)
    public ResponseEntity<ErrorResponse> handleValidation(MethodArgumentNotValidException ex) {
        String message = ex.getBindingResult().getFieldErrors().stream()
            .map(e -> e.getField() + " : " + e.getDefaultMessage())
            .collect(Collectors.joining(", "));
        return ResponseEntity.status(HttpStatus.BAD_REQUEST)
            .body(new ErrorResponse("VALIDATION_ERROR", message));
    }

    @ExceptionHandler(Exception.class)
    public ResponseEntity<ErrorResponse> handleGeneric(Exception ex) {
        log.error("Erreur inattendue", ex);
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR)
            .body(new ErrorResponse("INTERNAL_ERROR", "Une erreur inattendue s'est produite"));
    }
}

// Structure de réponse d'erreur uniforme
public record ErrorResponse(String code, String message) {}

// TODO: Tests unitaires du Service
@ExtendWith(MockitoExtension.class)
class ArticleServiceTest {

    @Mock
    private ArticleRepository articleRepository;

    @InjectMocks
    private ArticleService articleService;

    @Test
    void getArticleParId_existant_retourneArticle() {
        // Arrange
        Article article = new Article();
        article.setId(1L);
        article.setTitre("Test");
        article.setContenu("Contenu de test suffisamment long");
        article.setAuteur("Auteur Test");
        when(articleRepository.findById(1L)).thenReturn(Optional.of(article));

        // Act
        ArticleResponse result = articleService.getArticleParId(1L);

        // Assert
        assertThat(result.getTitre()).isEqualTo("Test");
        assertThat(result.getAuteur()).isEqualTo("Auteur Test");
        verify(articleRepository).findById(1L);
    }

    @Test
    void getArticleParId_inexistant_leveException() {
        when(articleRepository.findById(99L)).thenReturn(Optional.empty());

        assertThatThrownBy(() -> articleService.getArticleParId(99L))
            .isInstanceOf(ArticleNotFoundException.class)
            .hasMessageContaining("99");
    }

    @Test
    void creerArticle_champsValides_sauvegarde() {
        // TODO: Implémentez ce test
    }
}

// TODO: Test d'intégration du Controller avec MockMvc
@WebMvcTest(ArticleController.class)
class ArticleControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private ArticleService articleService;

    @Autowired
    private ObjectMapper objectMapper;

    @Test
    void getArticle_existant_retourne200() throws Exception {
        ArticleResponse response = new ArticleResponse();
        response.setId(1L);
        response.setTitre("Mon Article");
        when(articleService.getArticleParId(1L)).thenReturn(response);

        mockMvc.perform(get("/api/articles/1"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.titre").value("Mon Article"))
            .andExpect(jsonPath("$.id").value(1));
    }

    @Test
    void creerArticle_titreVide_retourne400() throws Exception {
        ArticleRequest request = new ArticleRequest();
        request.setTitre(""); // invalide
        request.setContenu("Contenu suffisant");
        request.setAuteur("Auteur");

        mockMvc.perform(post("/api/articles")
            .contentType(MediaType.APPLICATION_JSON)
            .content(objectMapper.writeValueAsString(request)))
            .andExpect(status().isBadRequest())
            .andExpect(jsonPath("$.code").value("VALIDATION_ERROR"));
    }
}
```

---

### Critères de Validation du Dojo

- [ ] Application démarre sur le port 8080 sans erreurs dans les logs
- [ ] Architecture en 3 couches correctement organisée — aucun `new` d'un Repository dans un Controller
- [ ] Tous les verbes HTTP implémentés avec les bons codes de retour (201 Created, 204 No Content...)
- [ ] Les DTOs sont utilisés en entrée et en sortie — aucune entité JPA exposée directement
- [ ] Gestion globale des erreurs avec `@RestControllerAdvice` et réponses JSON structurées
- [ ] Validation des inputs avec `@Valid` + annotations Bean Validation sur les DTOs
- [ ] Injection par constructeur partout (pas de `@Autowired` sur les champs)
- [ ] Logging pertinent : INFO pour les opérations importantes, DEBUG pour le détail, ERROR pour les exceptions inattendues
- [ ] Tests unitaires couvrant les cas nominaux ET les cas d'erreur du Service
- [ ] Tests de Controller avec MockMvc pour les routes GET et POST

---

## ✅ Critères de Validation : Êtes-vous Prêt pour la Semaine 3 ?

### Expliquer Oralement (sans hésiter)

- [ ] "Comment Spring crée les objets et les injecte ?" — au moins 2 minutes d'explication claire
- [ ] "Différence entre `@Service` et `@Repository`" — pas juste le nom, la sémantique
- [ ] "Pourquoi utiliser des DTOs au lieu de passer l'entité directement dans l'API ?"
- [ ] "Qu'est-ce qu'une transaction et dans quels cas en avez-vous besoin ?"
- [ ] "Comment testeriez-vous un Service sans base de données réelle ?"

### Coder Sans Référence

- [ ] Créer un endpoint GET qui retourne du JSON avec `ResponseEntity`
- [ ] Implémenter un POST qui valide les inputs et retourne 201 avec l'URI de la ressource créée
- [ ] Injecter un Repository dans un Service sans utiliser `@Autowired` sur le champ
- [ ] Lever une exception personnalisée et la capturer dans un `@RestControllerAdvice`
- [ ] Écrire un test unitaire avec Mockito pour un Service

### Déboguer Rapidement

- [ ] "Mon endpoint retourne 404 mais la route existe" — trouver pourquoi (package scan, mapping incorrect...)
- [ ] `LazyInitializationException` sur une relation — comprendre et résoudre (EAGER, @Transactional, fetch join)
- [ ] "Injection fails: No bean of type X found" — diagnostic Spring et solution

---

## 💼 Ce qui est Testé en Entretien

### Questions Orales Fréquentes

**"Expliquez l'architecture en couches"**
- ✅ Attendu : chaque couche a une responsabilité unique, les couches ne sautent pas de niveau, la testabilité en est grandement améliorée
- ❌ À éviter : "C'est ce qu'on fait avec Spring" sans justification

**"Pourquoi l'injection de dépendances ?"**
- ✅ Attendu : testabilité (on peut injecter des mocks), découplage (changer l'implémentation sans toucher au Service), respect du principe d'inversion de dépendances
- ❌ À éviter : "Spring le fait automatiquement, je sais pas trop pourquoi"

**Code Review — Trouvez les problèmes :**

```java
@RestController
public class UserController {
    private UserRepository repo = new UserRepository(); // Problème 1

    @GetMapping("/user/{id}")
    public User getUser(@PathVariable String id) { // Problème 2
        return repo.findById(id); // Problème 3
    }
}
```

- ✅ Attendu : (1) instanciation directe → doit être injecté, (2) l'id d'une entité est probablement un `Long`, (3) pas de gestion du cas "non trouvé" → NPE ou 200 avec null
- ❌ À éviter : "Ça a l'air correct"

### Red Flags qui vous éliminent

🚩 Vous créez des dépendances avec `new` dans un Service ou Controller  
🚩 Vous exposez vos entités JPA directement dans les réponses API  
🚩 Vous n'avez aucun test unitaire  
🚩 Vous confondez `@Controller` et `@RestController`  
🚩 Votre gestion des erreurs se résume à `try { ... } catch (Exception e) { e.printStackTrace(); }`

---

## 🔧 Pièges Courants

| Piège | Symptôme | Solution |
|-------|----------|----------|
| **Bean non trouvé** | `No qualifying bean of type X` | Vérifiez l'annotation stéréotype et que la classe est dans le bon package |
| **LazyInitializationException** | Exception en accédant à une collection hors transaction | Annotez la méthode Service avec `@Transactional` ou utilisez `FetchType.EAGER` |
| **N+1 queries** | 100 articles = 101 requêtes SQL | Utilisez `@Query` avec `JOIN FETCH` ou les projections JPA |
| **Entité exposée directement** | Données sensibles dans la réponse, couplage API/BDD | Créez des DTOs de réponse distincts |
| **Pas de gestion du 404** | Retourne 200 avec `null` quand la ressource n'existe pas | `orElseThrow()` + exception capturée dans le ControllerAdvice |
| **Validation côté client seulement** | Les données malformées atteignent la base | `@Valid` sur le `@RequestBody` + annotations Bean Validation sur le DTO |

---

## 📚 Ressources

- **Livre** : "Spring in Action" — Craig Walls (6e édition)
- **Docs** : spring.io/guides (tutoriels officiels, tous testés)
- **Référence** : Baeldung.com (articles de qualité sur chaque aspect de Spring)
- **Tests** : "Testing Spring Boot Applications" — Baeldung Guide
- **Pratique** : Essayez de recréer votre API sans regarder votre code le dernier jour

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

**[← Semaine 1](./semaine-1.md) | [Retour au README](../README.md) | [Semaine 3 →](./semaine-3.md)**

*Durée : 5 jours | Effort estimé : 25-30h | Dojo : 10-12h*