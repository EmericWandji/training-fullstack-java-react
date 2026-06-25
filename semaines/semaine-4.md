# 🔗 Semaine 4 : Intégration Full Stack & Préparation Technique

## 🎯 Objectif Pédagogique

**Assembler le frontend et le backend en une application cohérente, testée et prête pour la production. Comprendre les enjeux d'intégration, de performance et de sécurité. Vous préparer aux évaluations techniques d'embauche.**

C'est la semaine où tout s'assemble et où vous devenez **opérationnel**.

---

## 💡 Explication Feynman : "Intégration, c'est un mariage"

Frontend et Backend doivent vivre ensemble :
- **Frontend** = La mariée (belle interface, attire les utilisateurs)
- **Backend** = Le marié (logique, données, sécurité)
- **API** = Le serment (contrat que les deux respectent)
- **Bugs d'intégration** = Les crises de couple (mauvaise communication)

**Le secret** : Communiquer clairement. Chacun fait son boulot sans s'occuper des détails internes de l'autre. Et puis c'est magnifique.

---

## 📚 Notions Techniques Incontournables

### Jour 1 : Configuration et Déploiement Local
- [ ] **CORS** : configuration côté Spring, problèmes typiques
- [ ] **Variables d'environnement** : .env côté React, application.properties côté Spring
- [ ] **Reverse proxy** : servir React via Spring Boot
- [ ] **Docker basique** : Dockerfile, docker-compose
- [ ] **Base de données** : PostgreSQL au lieu de H2, migrations
- [ ] **Démarrage simultané** : concurrently, scripts npm

### Jour 2 : Tests d'Intégration
- [ ] **Tests API complets** : @SpringBootTest, MockMvc, assertions
- [ ] **Tests end-to-end** : scénarios utilisateur complets
- [ ] **Mocks vs vrais appels** : quand utiliser quoi
- [ ] **Couverture de code** : JaCoCo (objectif: 70%+)
- [ ] **Performance testing** : requêtes lentes, bottlenecks
- [ ] **Assertions intelligentes** : vérifier le vrai comportement

### Jour 3 : Sécurité et Authentification
- [ ] **JWT (JSON Web Tokens)** : création, validation, stockage
- [ ] **Hachage de mots de passe** : BCrypt, nunca plain text
- [ ] **HTTPS et SSL** : pourquoi c'est obligatoire en production
- [ ] **Input validation** : côté client ET côté serveur
- [ ] **CSRF protection** : tokens et SameSite cookies
- [ ] **Rate limiting** : prévention des brute force
- [ ] **OWASP Top 10** : les pires vulnérabilités

### Jour 4 : Performance et Monitoring
- [ ] **Database indexing** : analyse des requêtes lentes
- [ ] **Caching** : Redis, EhCache, cache headers HTTP
- [ ] **Lazy loading** : images, code splitting React
- [ ] **Monitoring** : logs structurés, metrics (Prometheus)
- [ ] **Profiling** : JProfiler, Chrome DevTools
- [ ] **Alertes** : notifications d'erreurs, anomalies

### Jour 5 : Préparation aux Tests Techniques
- [ ] **Live coding** : structures de données, algorithmes
- [ ] **Code review** : corriger du code existant
- [ ] **Design patterns** : savoir les nommer et les appliquer
- [ ] **Questions architecturales** : trade-offs, décisions
- [ ] **Pair programming simulation** : communication et collaboration

---

## 🥋 Dojo de Code : Blog Complet, Sécurisé, Testé

### Phase 1 : Configuration et CORS (Jour 1)

````yaml
# Backend: application.properties
spring.datasource.url=jdbc:postgresql://localhost:5432/blog_db
spring.datasource.username=postgres
spring.datasource.password=password
spring.jpa.hibernate.ddl-auto=validate
spring.jpa.show-sql=false

# Logging
logging.level.root=WARN
logging.level.com.blog=INFO
logging.level.org.springframework.web=DEBUG

# CORS Configuration (à implémenter en Java)
````

````java
// Backend: Configuration CORS
@Configuration
public class CorsConfig {
    @Bean
    public WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {
            @Override
            public void addCorsMappings(CorsRegistry registry) {
                registry.addMapping("/api/**")
                    .allowedOrigins("http://localhost:3000")
                    .allowedMethods("GET", "POST", "PUT", "DELETE")
                    .allowedHeaders("*")
                    .allowCredentials(true)
                    .maxAge(3600);
            }
        };
    }
}
````

````javascript
// Frontend: Configuration axios
// src/services/api.js
import axios from 'axios';

const API = axios.create({
    baseURL: process.env.REACT_APP_API_URL || 'http://localhost:8080/api',
    timeout: 5000
});

// Intercepteur pour les erreurs
API.interceptors.response.use(
    response => response,
    error => {
        if (error.response?.status === 401) {
            // Token expiré, rediriger login
            window.location.href = '/login';
        }
        return Promise.reject(error);
    }
);

export default API;
````

### Phase 2 : Authentification JWT (Jour 2)

````java
// Backend: Ajoutez Spring Security

// 1. Dépendances
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
    <version>0.11.5</version>
</dependency>

// 2. Entity User avec hachage
@Entity
public class User {
    @Id
    @GeneratedValue
    private Long id;
    
    @Column(unique = true)
    private String email;
    
    private String passwordHash; // Jamais plain text!
    private String nom;
    
    // Constructeurs, getters, setters...
}

// 3. Service JWT
@Service
public class JwtService {
    private static final String SECRET = "your-secret-key-min-32-chars";
    private static final long EXPIRATION = 86400000; // 24 hours
    
    public String generateToken(User user) {
        return Jwts.builder()
            .setSubject(user.getEmail())
            .setIssuedAt(new Date())
            .setExpiration(new Date(System.currentTimeMillis() + EXPIRATION))
            .signWith(SignatureAlgorithm.HS512, SECRET)
            .compact();
    }
    
    public String extractEmail(String token) {
        return Jwts.parser()
            .setSigningKey(SECRET)
            .parseClaimsJws(token)
            .getBody()
            .getSubject();
    }
    
    public boolean isTokenValid(String token) {
        try {
            Jwts.parser().setSigningKey(SECRET).parseClaimsJws(token);
            return true;
        } catch (JwtException | IllegalArgumentException e) {
            return false;
        }
    }
}

// 4. Contrôleur d'authentification
@RestController
@RequestMapping("/api/auth")
public class AuthController {
    
    @Autowired
    private JwtService jwtService;
    
    @Autowired
    private PasswordEncoder passwordEncoder;
    
    @PostMapping("/register")
    public ResponseEntity<?> register(@RequestBody RegisterRequest request) {
        // Validations...
        User user = new User();
        user.setEmail(request.getEmail());
        user.setPasswordHash(passwordEncoder.encode(request.getPassword()));
        // Sauvegardez...
        
        return ResponseEntity.ok(new AuthResponse(jwtService.generateToken(user)));
    }
    
    @PostMapping("/login")
    public ResponseEntity<?> login(@RequestBody LoginRequest request) {
        // Récupérez l'utilisateur
        // Vérifiez le password avec passwordEncoder.matches()
        // Retournez le token
        
        return ResponseEntity.ok(new AuthResponse(token));
    }
}

// 5. Configuration Security
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
            .cors().and()
            .authorizeRequests()
                .antMatchers("/api/auth/**").permitAll()
                .antMatchers("/api/articles").permitAll() // Lecture public
                .anyRequest().authenticated()
            .and()
            .addFilterBefore(new JwtAuthenticationFilter(jwtService), 
                             UsernamePasswordAuthenticationFilter.class);
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return new BCryptPasswordEncoder();
    }
}
````

````javascript
// Frontend: Gestion du token

// src/services/auth.js
import API from './api';

export async function register(email, password) {
    const response = await API.post('/auth/register', { email, password });
    if (response.data.token) {
        localStorage.setItem('token', response.data.token);
    }
    return response.data;
}

export async function login(email, password) {
    const response = await API.post('/auth/login', { email, password });
    if (response.data.token) {
        localStorage.setItem('token', response.data.token);
        API.defaults.headers.common['Authorization'] = `Bearer ${response.data.token}`;
    }
    return response.data;
}

export function logout() {
    localStorage.removeItem('token');
    delete API.defaults.headers.common['Authorization'];
}

// Au démarrage, récupérez le token
const token = localStorage.getItem('token');
if (token) {
    API.defaults.headers.common['Authorization'] = `Bearer ${token}`;
}

// src/components/ProtectedRoute.jsx
function ProtectedRoute({ children }) {
    const token = localStorage.getItem('token');
    return token ? children : <Navigate to="/login" />;
}
````

### Phase 3 : Tests d'Intégration Complets (Jour 2-3)

````java
// Backend: Tests d'intégration

@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@AutoConfigureMockMvc
public class ArticleIntegrationTest {
    
    @Autowired
    private MockMvc mockMvc;
    
    @Autowired
    private ArticleRepository articleRepository;
    
    @BeforeEach
    public void setUp() {
        articleRepository.deleteAll();
        
        Article article = new Article();
        article.setTitre("Test Article");
        article.setContenu("Contenu test");
        articleRepository.save(article);
    }
    
    @Test
    public void testGetAllArticles() throws Exception {
        mockMvc.perform(get("/api/articles"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$", hasSize(1)))
            .andExpect(jsonPath("$[0].titre").value("Test Article"));
    }
    
    @Test
    public void testCreateArticleWithoutAuth() throws Exception {
        mockMvc.perform(post("/api/articles")
            .contentType(MediaType.APPLICATION_JSON)
            .content("""
                {"titre": "Nouveau", "contenu": "Test"}
            """))
            .andExpect(status().isUnauthorized());
    }
    
    @Test
    @WithMockUser(username = "user@test.com")
    public void testCreateArticleWithAuth() throws Exception {
        mockMvc.perform(post("/api/articles")
            .contentType(MediaType.APPLICATION_JSON)
            .content("""
                {"titre": "Nouveau", "contenu": "Test"}
            """))
            .andExpect(status().isCreated())
            .andExpect(jsonPath("$.id").exists());
    }
}
````

````javascript
// Frontend: Tests d'intégration avec React Testing Library

import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import axios from 'axios';
import App from './App';

jest.mock('axios');

describe('Blog Integration', () => {
    
    test('affiche articles et permet recherche', async () => {
        const mockArticles = [
            { id: 1, titre: 'Article 1', contenu: 'Contenu 1' },
            { id: 2, titre: 'Article 2', contenu: 'Contenu 2' }
        ];
        
        axios.get.mockResolvedValue({ data: mockArticles });
        
        render(<App />);
        
        // Attendre le chargement
        await waitFor(() => {
            expect(screen.getByText('Article 1')).toBeInTheDocument();
        });
        
        // Recherche
        const searchInput = screen.getByPlaceholderText(/Rechercher/i);
        fireEvent.change(searchInput, { target: { value: 'Article 1' } });
        
        expect(screen.getByText('Article 1')).toBeInTheDocument();
        expect(screen.queryByText('Article 2')).not.toBeInTheDocument();
    });
    
    test('crée un commentaire après auth', async () => {
        // Mock login
        localStorage.setItem('token', 'fake-token');
        
        // Mock API calls
        axios.get.mockResolvedValue({ data: mockArticle });
        axios.post.mockResolvedValue({ data: newComment });
        
        render(<App />);
        
        // Ouvrir article
        fireEvent.click(screen.getByText('Lire la suite'));
        
        // Remplir le formulaire
        fireEvent.change(screen.getByPlaceholderText(/Votre nom/i), 
            { target: { value: 'Jean' } });
        fireEvent.change(screen.getByPlaceholderText(/Votre commentaire/i), 
            { target: { value: 'Super article!' } });
        
        // Soumettre
        fireEvent.click(screen.getByText('Commenter'));
        
        // Vérifier l'appel API
        await waitFor(() => {
            expect(axios.post).toHaveBeenCalledWith(
                expect.stringContaining('/commentaires'),
                expect.objectContaining({ contenu: 'Super article!' })
            );
        });
    });
});
````

### Phase 4 : Déploiement et Performance (Jour 4)

````dockerfile
# Dockerfile pour le backend
FROM maven:3.8-openjdk-11 as builder
WORKDIR /app
COPY . .
RUN mvn clean package -DskipTests

FROM openjdk:11-slim
COPY --from=builder /app/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
````

````dockerfile
# Dockerfile pour le frontend
FROM node:16 as builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
````

````yaml
# docker-compose.yml
version: '3.8'

services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_DB: blog_db
      POSTGRES_PASSWORD: password
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data

  backend:
    build: ./blog-api
    ports:
      - "8080:8080"
    depends_on:
      - postgres
    environment:
      SPRING_DATASOURCE_URL: jdbc:postgresql://postgres:5432/blog_db
      SPRING_DATASOURCE_PASSWORD: password

  frontend:
    build: ./blog-frontend
    ports:
      - "80:80"
    depends_on:
      - backend

volumes:
  postgres_data:
````

### Phase 5 : Préparation aux Tests Techniques (Jour 5)

````markdown
## Exercices de Préparation

### 1. Live Coding : Algorithme Classique (30 min)
Implémentez:
- Fibonacci récursif vs itératif
- Recherche binaire dans un array trié
- Inversion d'une linkedlist

### 2. Design Pattern : Reconnaître et Appliquer
"Vous avez ces classes, quel pattern appliquer ?"
- Factory Pattern pour créer des Articles
- Observer Pattern pour les notifications
- Strategy Pattern pour différents types de tri

### 3. Code Review : "Trouvez les bugs"
```java
public class BlogService {
    List<Article> articles = new ArrayList<>();
    
    public synchronized void addArticle(Article a) {
        articles.add(a);
    }
    
    public List<Article> getAll() { // Bug: concurrent modification
        return articles;
    }
    
    public void deleteAfterDate(LocalDate date) {
        articles.removeIf(a -> a.getDate().isBefore(date)); // Good
    }
}
```

### 4. Questions Architecturales
- "Comment scaleriez-vous cette application à 1M d'utilisateurs ?"
- "Comment implémenter un système de cache efficace ?"
- "Quelle est la meilleure façon de gérer les sessions ?"

### 5. Pair Programming Simulation
"Vous et un développeur senior devez ajouter une feature:
Filters avancés (date, auteur, nombre de commentaires).
Discutez votre approche, écrivez le code ensemble."
````

### Critères de Validation

- [ ] Frontend et Backend démarrent sans erreurs
- [ ] CORS configuré correctement
- [ ] Authentification JWT fonctionnelle
- [ ] Endpoints protégés retournent 401 sans token
- [ ] Tests d'intégration passant (>70% couverture)
- [ ] Application dockerisée et run via docker-compose
- [ ] Performances acceptables (réponses < 200ms)
- [ ] Logs structurés et utiles
- [ ] Code propre et commenté
- [ ] README avec instructions de démarrage

---

## ✅ Critères de Validation : Êtes-vous Prêt pour l'Industrie ?

### Expliquer Oralement
- [ ] "Expliquez le flux complet d'une requête du client au serveur"
- [ ] "Comment sécuriseriez-vous une API ?" (au moins 3 layers)
- [ ] "Qu'est-ce que le n+1 problem et comment le résoudre ?"
- [ ] "Décrivez votre processus de déboggage" (méthodologie)
- [ ] "Parlez d'un bug compliqué que vous avez résolu" (histoire réelle)

### Coder Sans Référence
- [ ] Implémenter une file d'attente (Queue) avec les patterns corrects
- [ ] Déboguer une requête qui hang (timeout)
- [ ] Optimiser une boucle N+1 queries
- [ ] Écrire un test mocking une dépendance externe
- [ ] Faire un code review constructif

### Business Acumen
- [ ] Comprendre trade-offs: sécurité vs vitesse, simple vs robuste
- [ ] Poser les bonnes questions avant de coder
- [ ] Estimer l'effort d'une feature
- [ ] Parler de "technical debt" intelligemment

---

## 💼 Conseils de Recruteur : L'Entretien Final

### Le Jour J

**Structure classique (4h):**
1. Petit-déj + discussion culture (15 min)
2. Live coding (1h) : problème algorithme
3. Architecture + design review (1h)
4. Integration + "codebase réel" (1h)
5. Discussions soft skills (30 min)

### Red Flags qui vous éliminent

🚩 Vous ne pouvez pas expliquer ce que vous avez codé
🚩 Vous blâmez les frameworks ("C'est Spring qui fait ça")
🚩 Vous n'avez jamais déployé une application
🚩 Vous ne testez pas votre code
🚩 Vous codez sans penser avant

### Points Qui Vous Hissent en Premier

⭐ "Je n'ai pas compris la question, pouvez-vous clarifier ?"
⭐ Vous refactorisez votre code en temps réel avec explications
⭐ Vous discutez de sécurité proactivement
⭐ Vous dites "Je pourrais implémenter ça mieux" et montrez
⭐ Vous posez des questions intelligentes sur l'équipe/produit

---

## 🎓 Checklist de Fin de Formation

Avant de dire "J'ai terminé" :

### Code & Repo
- [ ] Code sur GitHub, propre, avec historique de commits
- [ ] README.md avec instructions claires
- [ ] Tous les tests passent (backend + frontend)
- [ ] Pas de warnings à la compilation
- [ ] Pas de secrets en dur (tokens, mots de passe)

### Documentation
- [ ] Architecture expliquée (diagramme simple)
- [ ] API documentée (endpoints, payload, codes réponse)
- [ ] Instructions de déploiement
- [ ] Décisions techniques justifiées

### Compétences
- [ ] Vous pouvez expliquer chaque choix fait
- [ ] Vous pouvez déboguer tout bug rapidement
- [ ] Vous pouvez améliorer les performances
- [ ] Vous connaissez vos limites et les reconnaître

---

## 📚 Ressources Bonus

- **Design Patterns** : "Design Patterns: Elements of Reusable OOP" - Gang of Four
- **Système Distribuées** : "Designing Data-Intensive Applications" - Martin Kleppmann
- **Sécurité** : OWASP Top 10 (owasp.org)
- **DevOps Basique** : "The Docker Book" - James Turnbull
- **Entretien** : LeetCode (Easy, puis Medium)

---

## 🎯 Résumé et Prochaines Étapes

| Jour | Focus | Livrable |
|------|-------|----------|
| 1 | Configuration, CORS, déploiement local | App fonctionnelle localement |
| 2-3 | Authentification, tests d'intégration | Suites de tests passantes |
| 4 | Dockerisation, performance | App en container |
| 5 | Préparation entretien | Exercices de coding |

### Après la Formation

1. **Continuez à pratiquer** : LeetCode 30 min/jour
2. **Contribuez open source** : trouvez un petit projet intéressant
3. **Construisez 2-3 projets perso** : montrez votre style
4. **Apprenez la suite** : WebSockets, microservices, cloud
5. **Candidatez** : vous êtes prêt !

---

## 💬 Message de Clôture

Vous avez réalisé un parcours intensif et exigeant. De débutant à Junior Opérationnel, ce n'est pas rien. Vous connaissez maintenant les fondations solides sur lesquelles reposent les applications professionnelles.

**Souvenez-vous :**
- Le code n'est pas une destination, c'est un craft
- Vous ne savez jamais tout, et c'est ok
- Les meilleurs développeurs posent des questions
- Refactorer son vieux code est une victoire

**Maintenant : allez construire quelque chose de grand.**

---

**[← Semaine 3](./semaine-3.md) | [Retour au README](../README.md)**

*Durée: 5 jours | Effort: 30-35h | Total formation: 100-130h | Effort Dojo final: 12-15h*

---

## 📞 Feedback

Vous avez des améliorations à proposer ? Créez une issue ou une PR.
Bonne chance ! 🚀