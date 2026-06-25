# ⚛️ Semaine 3 : Frontend React

## 🎯 Objectif Pédagogique

**Comprendre le paradigme de React — composants, state, props, cycle de vie — pour construire une interface moderne, réactive et performante qui communique proprement avec une API.**

Après cette semaine, React ne sera plus une "bibliothèque magique" mais un système logique que vous contrôlez.

---

## 🏭 Comprendre React : L'Analogie de la Chaîne de Montage

Imaginez une usine automobile avec des postes de montage spécialisés.

Chaque **poste de montage** est un **composant** React. Le poste "Moteur" sait monter un moteur — c'est tout ce qu'il fait. Le poste "Tableau de bord" s'occupe du tableau de bord. Chaque poste est indépendant, testable, et remplaçable.

Les **matériaux** arrivent du poste précédent sur une palette. Le poste ne peut pas modifier cette palette — il reçoit ce qu'on lui donne et l'utilise. Ce sont les **props** : des données en lecture seule, transmises du parent vers l'enfant.

Mais chaque poste a aussi son propre **état interne** : le nombre de boulons vissés, l'avancement de l'opération. Cet état peut changer au fil du temps, et quand il change, le tableau de suivi du poste est automatiquement mis à jour. C'est le **state**.

Et l'usine elle-même est **réactive** : si quelque chose change — un matériau différent arrive, un état interne est modifié — la partie concernée du tableau de suivi (le DOM) est recalculée et mise à jour automatiquement. Pas toute l'usine — juste ce qui a changé.

C'est ce qui donne son nom à React : une interface qui **réagit** aux changements de données.

---

## 📚 Notions Techniques Incontournables

### Jour 1 : Fondamentaux React

- [ ] **Composants fonctionnels** : la norme actuelle. Une fonction JavaScript qui reçoit des `props` et retourne du JSX. Préférés aux composants classes.
- [ ] **JSX** : syntaxe qui ressemble à du HTML dans du JavaScript. Transpilé par Babel en `React.createElement()`. Toujours fermer les balises (`<input />`), `className` à la place de `class`.
- [ ] **Props** : données passées d'un parent vers ses enfants, immuables du point de vue de l'enfant. `PropTypes` pour valider le type des props reçues.
- [ ] **Rendering conditionnel** : `{condition && <Composant />}`, ternaire `{condition ? <A /> : <B />}`, early return
- [ ] **Listes et clés** : `.map()` pour rendre une liste de composants, l'attribut `key` obligatoire — React l'utilise pour identifier les éléments et optimiser les re-renders
- [ ] **Événements** : `onClick`, `onChange`, `onSubmit` — les event handlers sont des fonctions. `e.preventDefault()` pour les formulaires.
- [ ] **Virtual DOM** : React maintient une copie virtuelle du DOM en mémoire, compare l'ancien et le nouveau (diffing), et ne met à jour dans le vrai DOM que ce qui a changé

### Jour 2 : State et Hooks

- [ ] **`useState`** : `const [valeur, setValeur] = useState(valeurInitiale)`. Déclare une variable d'état et sa fonction de mise à jour.
- [ ] **Immuabilité** : ne jamais modifier le state directement (`state.push(...)` → React ne détecte pas le changement). Toujours créer un nouvel objet/tableau : `setItems([...items, newItem])`
- [ ] **`setState` asynchrone** : React batchifie les mises à jour. Pour lire la valeur précédente : `setCount(prev => prev + 1)` plutôt que `setCount(count + 1)`
- [ ] **`useEffect`** : exécute du code après le rendu. Dépendances : `[]` = une fois au montage, `[dep]` = à chaque changement de `dep`, pas de tableau = à chaque rendu.
- [ ] **Cleanup dans useEffect** : retournez une fonction pour nettoyer (annuler un fetch, retirer un listener) — évite les memory leaks
- [ ] **Rules of Hooks** : appelez les hooks uniquement au top-level du composant, jamais dans une condition ou une boucle
- [ ] **`useCallback`** : mémorise une fonction pour éviter qu'elle soit recréée à chaque rendu — utile quand on la passe en prop à un composant mémoïsé
- [ ] **`useMemo`** : mémorise le résultat d'un calcul coûteux — ne recalcule que quand les dépendances changent

### Jour 3 : Gestion d'État Avancée

- [ ] **Lifting state up** : quand deux composants ont besoin du même état, remontez-le dans leur ancêtre commun et passez-le en props
- [ ] **Context API** : `createContext()`, `Provider`, `useContext()` — partager de l'état global (utilisateur connecté, thème) sans prop drilling à travers 5 niveaux
- [ ] **`useReducer`** : pour un state complexe avec plusieurs sous-valeurs ou des transitions nommées. Pattern `action.type` / `switch` — rend les transitions prévisibles et testables.
- [ ] **Prop drilling** : anti-pattern où on passe des props à travers plusieurs niveaux de composants sans qu'ils les utilisent. Solution : Context ou restructuration des composants.
- [ ] **`localStorage`** : persistance simple entre sessions. `localStorage.setItem(key, JSON.stringify(value))` / `JSON.parse(localStorage.getItem(key))`

### Jour 4 : Communication avec les APIs

- [ ] **`fetch`** : API native du navigateur. `fetch(url).then(r => r.json()).then(data => ...)`. Gère les erreurs réseau mais pas les codes HTTP 4xx/5xx (vérifiez `response.ok`).
- [ ] **`axios`** : bibliothèque tierce. Rejette automatiquement les codes d'erreur HTTP, syntaxe plus concise, intercepteurs plus faciles à configurer.
- [ ] **Pattern standard** : `useEffect` pour déclencher le fetch au montage, `useState` pour `data`, `loading` et `error`, toujours gérer les trois états dans le JSX
- [ ] **Gestion d'erreurs** : `try/catch` avec `async/await`, afficher un message utilisateur (pas un `console.error` invisible)
- [ ] **Loading states** : spinner pendant le chargement, skeleton screens pour les interfaces complexes
- [ ] **Authentification** : stocker le token (localStorage ou cookie), l'inclure dans les headers (`Authorization: Bearer <token>`), intercepteurs axios pour le renouvellement
- [ ] **CORS** : erreur côté navigateur quand le backend n'autorise pas l'origine du frontend. Solution : configuration CORS côté Spring (Semaine 4) ou proxy en développement.
- [ ] **AbortController** : annuler un fetch en cours lors du démontage du composant pour éviter les "Can't perform state update on unmounted component"

### Jour 5 : Performance et Patterns Avancés

- [ ] **`React.memo`** : enveloppe un composant pour éviter qu'il se re-rende si ses props n'ont pas changé (comparaison superficielle). N'optimisez pas prématurément.
- [ ] **Custom Hooks** : extraire de la logique réutilisable dans une fonction commençant par `use`. Un custom hook peut utiliser d'autres hooks. Rend les composants plus lisibles.
- [ ] **Code splitting** : `React.lazy()` + `<Suspense>` pour charger un composant uniquement quand il est nécessaire — réduit le bundle initial
- [ ] **Debouncing** : attendre que l'utilisateur cesse de taper avant de déclencher une recherche. Évite un appel API par frappe de clavier.
- [ ] **Error Boundaries** : composant classe qui capture les erreurs de rendu de ses enfants et affiche une UI de fallback
- [ ] **Tests avec React Testing Library** : tester comme un utilisateur — `render()`, `screen.getByText()`, `userEvent.click()`, `waitFor()`

---

## 🥋 Dojo de Code : Dashboard Blog Interactif

### Contexte

Vous construisez le frontend de la plateforme blog. Consommez l'API créée en Semaine 2. Les utilisateurs listent les articles, les filtrent en temps réel, lisent le détail d'un article, et postent des commentaires.

---

### Phase 1 : Structure et Composants (Jour 1)

```javascript
// 1. Créez le projet
npx create-react-app blog-frontend
cd blog-frontend
npm install axios

// 2. Organisation des dossiers
src/
  components/
    ArticleCard.jsx       // Un article dans la liste
    ArticleDetail.jsx     // Vue détaillée d'un article
    ArticleList.jsx       // La liste des articles
    CommentForm.jsx       // Formulaire de commentaire
    CommentList.jsx       // Liste des commentaires
    Header.jsx            // Navigation
    LoadingSpinner.jsx    // Indicateur de chargement
    ErrorMessage.jsx      // Affichage d'erreur uniforme
  hooks/
    useArticles.js        // Custom hook pour les articles
    useArticleSearch.js   // Custom hook pour la recherche
    useComments.js        // Custom hook pour les commentaires
  services/
    articleService.js     // Appels API articles
    commentaireService.js // Appels API commentaires
  pages/
    HomePage.jsx
    ArticlePage.jsx
  App.jsx

// TODO: Composant ArticleCard
function ArticleCard({ article, onSelect }) {
    // Props attendues: article (objet avec id, titre, auteur, dateCreation, nombreCommentaires)
    //                  onSelect (fonction appelée au clic)

    const formatDate = (dateString) => {
        return new Date(dateString).toLocaleDateString('fr-FR', {
            year: 'numeric', month: 'long', day: 'numeric'
        });
    };

    return (
        <article className="article-card" onClick={() => onSelect(article)}>
            <h2>{article.titre}</h2>
            <p className="article-meta">
                Par {article.auteur} · {formatDate(article.dateCreation)}
                · {article.nombreCommentaires} commentaire(s)
            </p>
            <p>{article.contenu.substring(0, 150)}...</p>
            <button>Lire la suite →</button>
        </article>
    );
}

export default ArticleCard;
```

---

### Phase 2 : State, Hooks et Navigation (Jour 1-2)

```javascript
// TODO: App.jsx avec state global minimal
import { useState } from 'react';
import ArticleList from './components/ArticleList';
import ArticleDetail from './components/ArticleDetail';
import Header from './components/Header';

function App() {
    const [selectedArticle, setSelectedArticle] = useState(null);

    return (
        <div className="app">
            <Header onLogoClick={() => setSelectedArticle(null)} />

            <main>
                {selectedArticle ? (
                    <ArticleDetail
                        articleId={selectedArticle.id}
                        onBack={() => setSelectedArticle(null)}
                    />
                ) : (
                    <ArticleList onSelectArticle={setSelectedArticle} />
                )}
            </main>
        </div>
    );
}

export default App;

// TODO: ArticleList — gère le chargement et l'affichage de la liste
import { useState, useEffect } from 'react';
import { fetchArticles } from '../services/articleService';
import ArticleCard from './ArticleCard';
import LoadingSpinner from './LoadingSpinner';
import ErrorMessage from './ErrorMessage';

function ArticleList({ onSelectArticle }) {
    const [articles, setArticles] = useState([]);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);
    const [searchTerm, setSearchTerm] = useState('');

    useEffect(() => {
        setLoading(true);
        setError(null);

        fetchArticles()
            .then(data => setArticles(data))
            .catch(err => setError(err.message))
            .finally(() => setLoading(false));
    }, []);

    const filteredArticles = articles.filter(article =>
        article.titre.toLowerCase().includes(searchTerm.toLowerCase()) ||
        article.auteur.toLowerCase().includes(searchTerm.toLowerCase())
    );

    if (loading) return <LoadingSpinner />;
    if (error) return <ErrorMessage message={error} />;

    return (
        <section>
            <input
                type="text"
                placeholder="Rechercher par titre ou auteur..."
                value={searchTerm}
                onChange={e => setSearchTerm(e.target.value)}
            />
            <p>{filteredArticles.length} article(s) trouvé(s)</p>
            {filteredArticles.length === 0 ? (
                <p>Aucun article ne correspond à votre recherche.</p>
            ) : (
                filteredArticles.map(article => (
                    <ArticleCard
                        key={article.id}
                        article={article}
                        onSelect={onSelectArticle}
                    />
                ))
            )}
        </section>
    );
}
```

---

### Phase 3 : Service Layer et Appels API (Jour 2-3)

```javascript
// TODO: src/services/articleService.js
// Centralise tous les appels à l'API — les composants n'appellent jamais fetch directement

import axios from 'axios';

const api = axios.create({
    baseURL: process.env.REACT_APP_API_URL || 'http://localhost:8080/api',
    timeout: 10000,
    headers: { 'Content-Type': 'application/json' }
});

// Intercepteur : log des erreurs et redirection si 401
api.interceptors.response.use(
    response => response,
    error => {
        console.error(`API Error ${error.response?.status}:`, error.message);
        return Promise.reject(error);
    }
);

export const fetchArticles = async () => {
    const response = await api.get('/articles');
    return response.data;
};

export const fetchArticleById = async (id) => {
    const response = await api.get(`/articles/${id}`);
    return response.data;
};

export const createArticle = async (articleData) => {
    const response = await api.post('/articles', articleData);
    return response.data;
};

export const searchArticles = async (terme) => {
    const response = await api.get('/articles/recherche', { params: { terme } });
    return response.data;
};

// TODO: src/services/commentaireService.js — implémentez de même
// fetchCommentaires(articleId), createCommentaire(articleId, data), deleteCommentaire(id)
```

---

### Phase 4 : Custom Hooks et Formulaires (Jour 3-4)

```javascript
// TODO: Custom Hook pour encapsuler la logique de fetch d'un article
// src/hooks/useArticle.js

import { useState, useEffect } from 'react';
import { fetchArticleById } from '../services/articleService';

export function useArticle(articleId) {
    const [article, setArticle] = useState(null);
    const [loading, setLoading] = useState(true);
    const [error, setError] = useState(null);

    useEffect(() => {
        if (!articleId) return;

        const abortController = new AbortController();
        setLoading(true);
        setError(null);

        fetchArticleById(articleId)
            .then(data => {
                if (!abortController.signal.aborted) {
                    setArticle(data);
                }
            })
            .catch(err => {
                if (!abortController.signal.aborted) {
                    setError(err.message);
                }
            })
            .finally(() => {
                if (!abortController.signal.aborted) {
                    setLoading(false);
                }
            });

        // Cleanup : annule le fetch si le composant est démonté
        return () => abortController.abort();
    }, [articleId]);

    return { article, loading, error };
}

// Utilisation dans ArticleDetail :
function ArticleDetail({ articleId, onBack }) {
    const { article, loading, error } = useArticle(articleId);

    if (loading) return <LoadingSpinner />;
    if (error) return <ErrorMessage message={error} onRetry={() => {}} />;

    return (
        <article>
            <button onClick={onBack}>← Retour</button>
            <h1>{article.titre}</h1>
            <p>Par {article.auteur}</p>
            <div>{article.contenu}</div>
            <CommentSection articleId={articleId} />
        </article>
    );
}

// TODO: Formulaire de commentaire contrôlé
function CommentForm({ articleId, onCommentAdded }) {
    const [formData, setFormData] = useState({ contenu: '', auteur: '' });
    const [submitting, setSubmitting] = useState(false);
    const [error, setError] = useState(null);

    const handleChange = (e) => {
        const { name, value } = e.target;
        setFormData(prev => ({ ...prev, [name]: value }));
    };

    const handleSubmit = async (e) => {
        e.preventDefault();

        if (!formData.contenu.trim() || !formData.auteur.trim()) {
            setError('Tous les champs sont requis');
            return;
        }

        setSubmitting(true);
        setError(null);

        try {
            const newComment = await createCommentaire(articleId, formData);
            onCommentAdded(newComment);
            setFormData({ contenu: '', auteur: '' }); // Reset
        } catch (err) {
            setError('Impossible d\'envoyer le commentaire. Réessayez.');
        } finally {
            setSubmitting(false);
        }
    };

    return (
        <form onSubmit={handleSubmit}>
            <input
                type="text"
                name="auteur"
                placeholder="Votre nom"
                value={formData.auteur}
                onChange={handleChange}
                disabled={submitting}
                required
            />
            <textarea
                name="contenu"
                placeholder="Votre commentaire..."
                value={formData.contenu}
                onChange={handleChange}
                disabled={submitting}
                rows={4}
                required
            />
            {error && <p className="error-message">{error}</p>}
            <button type="submit" disabled={submitting}>
                {submitting ? 'Envoi en cours...' : 'Publier le commentaire'}
            </button>
        </form>
    );
}
```

---

### Phase 5 : Optimisation et Tests (Jour 4-5)

```javascript
// TODO: Custom Hook avec debounce pour la recherche
// src/hooks/useDebounce.js
import { useState, useEffect } from 'react';

export function useDebounce(value, delay = 300) {
    const [debouncedValue, setDebouncedValue] = useState(value);

    useEffect(() => {
        const timer = setTimeout(() => setDebouncedValue(value), delay);
        return () => clearTimeout(timer);
    }, [value, delay]);

    return debouncedValue;
}

// Utilisation : appel API seulement 300ms après que l'utilisateur cesse de taper
function SearchBar({ onSearch }) {
    const [term, setTerm] = useState('');
    const debouncedTerm = useDebounce(term, 300);

    useEffect(() => {
        if (debouncedTerm.length > 2 || debouncedTerm === '') {
            onSearch(debouncedTerm);
        }
    }, [debouncedTerm, onSearch]);

    return (
        <input
            value={term}
            onChange={e => setTerm(e.target.value)}
            placeholder="Rechercher..."
        />
    );
}

// TODO: Mémoïsation pour éviter les re-renders inutiles
const ArticleCard = React.memo(function ArticleCard({ article, onSelect }) {
    // Ne se re-rend que si `article` ou `onSelect` ont changé
    return (/* JSX */);
});

// Stabiliser la référence de onSelect avec useCallback
function ArticleList({ onSelectArticle }) {
    const handleSelect = useCallback((article) => {
        onSelectArticle(article);
    }, [onSelectArticle]);

    return articles.map(a => <ArticleCard key={a.id} article={a} onSelect={handleSelect} />);
}

// TODO: Tests avec React Testing Library
import { render, screen, fireEvent, waitFor } from '@testing-library/react';
import userEvent from '@testing-library/user-event';

// Mocker le module de service
jest.mock('../services/articleService');
import { fetchArticles } from '../services/articleService';

describe('ArticleList', () => {
    test('affiche un spinner pendant le chargement', () => {
        fetchArticles.mockReturnValue(new Promise(() => {})); // Reste en pending
        render(<ArticleList onSelectArticle={() => {}} />);
        expect(screen.getByRole('progressbar')).toBeInTheDocument();
    });

    test('affiche les articles après chargement', async () => {
        fetchArticles.mockResolvedValue([
            { id: 1, titre: 'Mon Premier Article', auteur: 'Alice', contenu: 'Contenu...', dateCreation: '2024-01-01', nombreCommentaires: 0 }
        ]);

        render(<ArticleList onSelectArticle={() => {}} />);

        const titre = await screen.findByText('Mon Premier Article');
        expect(titre).toBeInTheDocument();
    });

    test('affiche un message d\'erreur si l\'API échoue', async () => {
        fetchArticles.mockRejectedValue(new Error('Serveur indisponible'));
        render(<ArticleList onSelectArticle={() => {}} />);

        await waitFor(() => {
            expect(screen.getByText(/Serveur indisponible/i)).toBeInTheDocument();
        });
    });

    test('filtre les articles selon la recherche', async () => {
        fetchArticles.mockResolvedValue([
            { id: 1, titre: 'Article Java', auteur: 'Alice', contenu: '...', dateCreation: '2024-01-01', nombreCommentaires: 0 },
            { id: 2, titre: 'Article React', auteur: 'Bob', contenu: '...', dateCreation: '2024-01-02', nombreCommentaires: 0 }
        ]);

        render(<ArticleList onSelectArticle={() => {}} />);
        await screen.findByText('Article Java');

        await userEvent.type(screen.getByPlaceholderText(/rechercher/i), 'React');

        expect(screen.queryByText('Article Java')).not.toBeInTheDocument();
        expect(screen.getByText('Article React')).toBeInTheDocument();
    });
});
```

---

### Critères de Validation du Dojo

- [ ] Application démarre sans erreurs console et sans warning
- [ ] Articles chargés depuis l'API Spring de la Semaine 2 et affichés
- [ ] Recherche/filtrage fonctionnel en temps réel (avec debounce)
- [ ] Clic sur un article → affichage du détail avec ses commentaires
- [ ] Formulaire de commentaire → envoi à l'API → affichage immédiat dans la liste
- [ ] Gestion des trois états systématiquement : loading, error, data
- [ ] Aucun memory leak (cleanup dans les useEffect avec AbortController)
- [ ] Au moins 3 custom hooks extraits et réutilisables
- [ ] `React.memo` sur les composants qui reçoivent des fonctions en props
- [ ] Tests couvrant : chargement, erreur, rendu des données, interaction utilisateur

---

## ✅ Critères de Validation : Êtes-vous Prêt pour la Semaine 4 ?

### Expliquer Oralement (sans hésiter)

- [ ] "Quelle est la différence entre props et state ?" — avec un exemple de votre application
- [ ] "Quand utiliser useEffect ?" — listez 3 cas d'usage concrets
- [ ] "Pourquoi l'immutabilité est-elle fondamentale en React ?" — lien avec la détection des changements
- [ ] "Comment gérez-vous le state partagé entre composants ?" — lifting state up, Context, pourquoi choisir l'un ou l'autre
- [ ] "Quand utiliser React.memo et useCallback ?" — sans sur-optimiser

### Coder Sans Référence

- [ ] Créer un composant avec `useState` et un handler d'événement
- [ ] Appeler une API dans `useEffect` avec gestion loading/error/data
- [ ] Créer un formulaire contrôlé qui se reset après soumission
- [ ] Passer des callbacks en props entre composants parent et enfant
- [ ] Écrire un test de composant avec mock d'une dépendance

### Déboguer Rapidement

- [ ] "Mon composant ne se re-rende pas après un setState" — identifier pourquoi (mutation directe du state)
- [ ] "CORS error" — expliquer l'origine du problème et la solution correcte
- [ ] "Infinite re-renders" — identifier la dépendance manquante ou mal configurée dans useEffect

---

## 💼 Ce qui est Testé en Entretien

### Questions Orales Fréquentes

**"Expliquez le cycle de vie d'un composant React"**
- ✅ Attendu : Montage (`useEffect` avec `[]`) → Mises à jour (re-renders lors des changements de props/state) → Démontage (cleanup du useEffect)
- ❌ À éviter : Confusion avec les méthodes des class components dans un contexte de hooks

**"Différence entre useState et useReducer"**
- ✅ Attendu : `useState` pour un état simple et indépendant, `useReducer` quand plusieurs sous-valeurs sont liées ou quand la valeur suivante dépend de la précédente selon des transitions nommées
- ❌ À éviter : "Je ne sais pas utiliser useReducer"

**Code Review — Trouvez les problèmes :**

```jsx
function ArticleList({ articles }) {
    const [filtered, setFiltered] = useState(articles); // Problème 1

    return (
        <ul>
            {filtered.map(article => (
                <li onClick={() => console.log(article)}>  {/* Problème 2 */}
                    {article.titre}
                </li>
            ))}
        </ul>
    );
}
```

- ✅ Attendu : (1) `filtered` devrait être dérivé des props par un `useMemo`, pas un state qui ne se synchronisera pas si `articles` change. (2) `key` manquante sur le `<li>`.
- ❌ À éviter : "Ça marche"

### Red Flags qui vous éliminent

🚩 Vous modifiez le state directement : `articles.push(newArticle)` ou `state.titre = "..."`  
🚩 Vous ne comprenez pas pourquoi un tableau vide en dépendance de useEffect est différent d'aucun tableau  
🚩 Vous n'avez jamais écrit un seul test React  
🚩 Vous faites des appels API dans le corps du composant (hors useEffect)

---

## 🔧 Pièges Courants

| Piège | Symptôme | Solution |
|-------|----------|----------|
| **Mutation directe du state** | Changements pas visibles, re-render pas déclenché | Créez toujours un nouvel objet/tableau : `setItems([...items, new])` |
| **useEffect en boucle infinie** | Lag + spam console | Un objet/tableau en dépendance est recréé à chaque render — utilisez `useMemo` ou `useCallback` |
| **CORS en développement** | `Access-Control-Allow-Origin` error | Configurez CORS côté Spring OU ajoutez un proxy dans `package.json` : `"proxy": "http://localhost:8080"` |
| **Memory leak** | "Can't perform state update on unmounted component" | Cleanup useEffect avec `AbortController` ou variable `isMounted` |
| **Stale closure** | Variable "gelée" dans un callback | Vérifiez que les dépendances du useEffect sont complètes |
| **Key incorrecte** | Bugs visuels en réordonnant ou filtrant une liste | Utilisez un `id` unique et stable — jamais l'index du tableau |

---

## 📚 Ressources

- **Docs officielles** : react.dev — le point de départ absolu, réécrit en 2023, excellent
- **Livre** : "Learning React" — Alex Banks & Eve Porcello (2e édition)
- **Pratique interactive** : Scrimba "Learn React" (exercices directement dans le navigateur)
- **Vidéos** : "React Hooks Tutorial" — Web Dev Simplified (YouTube, très clair)
- **Testing** : testing-library.com/docs/react-testing-library/intro (documentation officielle)

---

## 🎯 Résumé

| Jour | Focus | Livrable attendu |
|------|-------|-----------------|
| 1 | Composants, JSX, props, listes | Articles affichés en liste avec clés |
| 2 | useState, useEffect, événements | Filtre fonctionnel, article sélectionnable |
| 3 | Service layer, useEffect API | Articles et commentaires chargés depuis Spring |
| 4 | Custom hooks, formulaires contrôlés | Commentaires envoyés et affichés |
| 5 | React.memo, tests | Application optimisée et tests passants |

---

**[← Semaine 2](./semaine-2.md) | [Retour au README](../README.md) | [Semaine 4 →](./semaine-4.md)**

*Durée : 5 jours | Effort estimé : 25-30h | Dojo : 10-12h*