# 🎨 Semaine 3 — Frontend React : Donner une Interface à votre API

> **Objectif de la semaine :** Construire une interface web interactive qui consomme l'API REST de la Semaine 2. Comprendre comment React organise le code d'interface, réagit aux données, et gère les interactions utilisateur — en partant de zéro.

**Durée estimée :** 35 à 40 heures  
**Prérequis :** Semaine 2 validée — l'API `dojo-bibliotheque` doit répondre sur `localhost:8080`

---

## 🧭 Table des matières

1. [Le concept clé — Des briques qui se souviennent](#-le-concept-clé--des-briques-qui-se-souviennent)
2. [React — Ce que c'est vraiment](#-react--ce-que-cest-vraiment)
3. [Notions techniques incontournables](#-notions-techniques-incontournables)
   - [Composants & JSX](#1--composants--jsx)
   - [Props — Transmettre des données](#2--props--transmettre-des-données)
   - [State — La mémoire du composant](#3--state--la-mémoire-du-composant)
   - [useEffect — Réagir aux changements](#4--useeffect--réagir-aux-changements)
   - [Fetch API — Parler à votre backend](#5--fetch-api--parler-à-votre-backend)
4. [Dojo de Code](#-dojo-de-code--linterface-de-la-bibliothèque)
5. [Critères de validation](#-critères-de-validation)
6. [Conseils de recruteur](#-conseils-de-recruteur)

---

## 🧠 Le concept clé — Des briques qui se souviennent

### L'interface comme un tableau d'affichage vivant

Imaginez un grand tableau d'affichage dans le hall d'une bibliothèque. Dessus : la liste des livres disponibles, un compteur d'adhérents, un panneau "Livres en retard".

Avec du HTML classique, ce tableau est **imprimé une fois**. Si la liste des livres change, quelqu'un doit décrocher le tableau, le réécrire entièrement, et le raccrocher. Même pour changer un seul livre.

React transforme ce tableau en **tableau électronique intelligent**. Chaque panneau est un composant autonome. Chaque composant a sa propre mémoire. Quand la mémoire change, le panneau se met à jour **tout seul**, uniquement lui, sans toucher aux autres.

> Vous ne gérez plus le tableau manuellement. Vous décrivez à quoi ressemble chaque panneau *selon son état*, et React s'occupe de tout mettre à jour au bon moment.

---

### Les LEGO comme modèle mental des composants

Un système LEGO fonctionne ainsi :
- Chaque **brique a une forme précise** — elle ne change pas selon où on la pose.
- On **assemble les briques** pour construire quelque chose de plus grand.
- Si on améliore une brique, **toutes les constructions** qui l'utilisent en bénéficient.
- Une brique peut **contenir d'autres briques** (emboîtement).

C'est exactement le modèle React. Un `<BoutonAjouter />` est une brique. Une `<CarteLivre />` est une brique. Une `<ListeLivres />` assemble plusieurs `<CarteLivre />`. Une `<App />` assemble tout.

---

## 🌱 React — Ce que c'est vraiment

### Le problème que React a résolu

Avant les frameworks comme React (avant 2013), construire une interface web dynamique ressemblait à ceci :

```javascript
// JavaScript "vanilla" : vous manipulez le HTML manuellement
document.getElementById('compteur').innerText = '5';
document.getElementById('liste').innerHTML = '';
livres.forEach(livre => {
    const li = document.createElement('li');
    li.innerText = livre.titre;
    document.getElementById('liste').appendChild(li);
});
```

Ça fonctionne pour 10 livres. Avec 500 livres, des filtres, des formulaires, des erreurs à afficher, des états de chargement — c'est ingérable. Le code devient un enchevêtrement de manipulations DOM impossibles à déboguer.

**Facebook a créé React en 2013** pour résoudre exactement ce problème : *comment maintenir une interface complexe synchronisée avec des données qui changent en permanence ?*

### Ce que React apporte concrètement

| Sans React (JS vanilla) | Avec React |
| :--- | :--- |
| Vous manipulez le DOM manuellement | React met à jour le DOM pour vous |
| Le code UI et la logique sont mélangés | Les composants séparent chaque responsabilité |
| Réutiliser du code HTML est difficile | Un composant se réutilise partout |
| Gérer l'état de l'application est complexe | `useState` centralise la mémoire du composant |
| Les mises à jour sont lentes (tout est re-rendu) | Le Virtual DOM minimise les changements réels |

### SPA — Single Page Application

Votre API envoie du JSON. Votre navigateur reçoit du HTML. Normalement, chaque clic sur un lien charge une nouvelle page HTML du serveur.

Une **SPA** change ça : une seule page HTML est chargée au démarrage. Ensuite, React prend le contrôle et simule la navigation en JavaScript, sans jamais recharger la page. Les données viennent de l'API en arrière-plan.

> C'est pourquoi Gmail, Notion, ou votre banque en ligne semblent si rapides : ils ne rechargent pas la page à chaque action.

### React vs les autres frameworks

| Framework | Créé par | Philosophie |
| :--- | :--- | :--- |
| **React** | Meta (Facebook) | Bibliothèque UI légère — vous choisissez le reste |
| **Angular** | Google | Framework complet et opinioné |
| **Vue.js** | Evan You | Entre React et Angular — progressif |

React est **le plus utilisé en France en 2025** (>60% des offres frontend). C'est pourquoi on le choisit ici.

### L'écosystème — les outils que vous allez utiliser

Avant d'écrire la première ligne React, il faut comprendre l'outillage.

**Node.js** — React tourne dans le navigateur, mais les outils de développement tournent dans Node.js (un environnement JavaScript côté serveur). Vous avez besoin de Node.js installé, pas pour votre app, mais pour les outils qui la construisent.

**npm** — Node Package Manager. C'est le Maven du monde JavaScript. Il télécharge les bibliothèques (appelées **packages**) et les gère.

```bash
npm install react       # installe React
npm install axios       # installe Axios (pour les requêtes HTTP)
npm run dev             # lance l'application en mode développement
npm run build           # compile l'application pour la production
```

**Vite** — L'outil qui crée votre projet React et le lance en local. Il remplace l'ancien `create-react-app`, bien plus lent. Avec Vite, le démarrage prend moins d'une seconde.

### Ce qui se passe quand vous lancez `npm run dev`

```
1. Vite démarre un serveur de développement local (port 5173)
        ↓
2. Vite compile votre code JSX en JavaScript compréhensible par le navigateur
        ↓
3. Le navigateur charge index.html (une seule fois)
        ↓
4. React prend le contrôle de <div id="root"> dans index.html
        ↓
5. React affiche votre composant <App /> à l'intérieur
        ↓
6. Quand vous modifiez un fichier, Vite recharge uniquement ce qui a changé
           (Hot Module Replacement — pas de rechargement de page)
```

Vous verrez votre app sur `http://localhost:5173`.

### Structure d'un projet React créé avec Vite

```
bibliotheque-frontend/
├── index.html              ← La seule vraie page HTML (contient <div id="root">)
├── package.json            ← Dépendances et scripts (comme pom.xml)
├── vite.config.js          ← Configuration de Vite
└── src/
    ├── main.jsx            ← Point d'entrée — monte <App /> dans le DOM
    ├── App.jsx             ← Composant racine de l'application
    ├── components/         ← Vos composants réutilisables
    │   ├── LivreCard.jsx
    │   ├── ListeLivres.jsx
    │   └── LivreForm.jsx
    ├── services/           ← Logique d'appel API (séparée des composants)
    │   └── livreService.js
    └── assets/             ← Images, icônes
```

**`index.html`** — le seul fichier HTML de toute l'application :

```html
<!DOCTYPE html>
<html lang="fr">
  <head>
    <meta charset="UTF-8" />
    <title>Bibliothèque</title>
  </head>
  <body>
    <div id="root"></div>   <!-- React va tout injecter ici -->
    <script type="module" src="/src/main.jsx"></script>
  </body>
</html>
```

**`src/main.jsx`** — le point d'entrée React :

```jsx
import { StrictMode } from 'react'
import { createRoot } from 'react-dom/client'
import App from './App.jsx'

createRoot(document.getElementById('root')).render(
  <StrictMode>
    <App />
  </StrictMode>
)
// React prend le <div id="root"> et y affiche <App />
```

---

## 📚 Notions techniques incontournables

### 1 · Composants & JSX

#### Qu'est-ce qu'un composant ?

Un composant React est **une fonction JavaScript qui retourne de l'interface**. Rien de plus.

```jsx
// Un composant = une fonction qui commence par une majuscule
// et qui retourne du JSX
function CarteLivre() {
  return (
    <div className="carte">
      <h2>Clean Code</h2>
      <p>Robert C. Martin — 2008</p>
    </div>
  );
}

// On l'utilise comme une balise HTML personnalisée
function App() {
  return (
    <div>
      <h1>Ma Bibliothèque</h1>
      <CarteLivre />   {/* Utilisation du composant */}
    </div>
  );
}
```

#### Qu'est-ce que JSX ?

JSX ressemble à du HTML dans du JavaScript. Ce n'est ni l'un ni l'autre — c'est une **syntaxe spéciale** que Vite transforme en appels JavaScript.

```jsx
// Ce que vous écrivez (JSX) :
const element = <h1 className="titre">Bonjour</h1>;

// Ce que Vite transforme en coulisses :
const element = React.createElement('h1', { className: 'titre' }, 'Bonjour');
```

Vous n'avez pas besoin de comprendre la transformation. Vous avez besoin de connaître les règles JSX :

| Règle JSX | HTML classique | JSX correct |
| :--- | :--- | :--- |
| `class` → `className` | `<div class="box">` | `<div className="box">` |
| Les balises doivent être fermées | `
` | `
` |
| Un seul élément racine | `<h1>...</h1><p>...</p>` | `<><h1>...</h1><p>...</p></>` |
| JavaScript entre accolades | `<p>2 + 2</p>` (texte) | `<p>{2 + 2}</p>` (calcul → 4) |

#### Les accolades `{}` — la passerelle vers JavaScript

Dans JSX, tout ce qui est entre `{}` est du JavaScript exécuté :

```jsx
function CarteLivre() {
  const titre = "Clean Code";
  const annee = 2008;
  const estDisponible = true;

  return (
    <div>
      <h2>{titre}</h2>                              {/* Variable */}
      <p>Publié en {annee}</p>                      {/* Expression */}
      <p>{2026 - annee} ans depuis la publication</p> {/* Calcul */}
      <p>{estDisponible ? "✅ Disponible" : "❌ Emprunté"}</p> {/* Ternaire */}
    </div>
  );
}
```

---

### 2 · Props — Transmettre des données

Un composant statique qui affiche toujours "Clean Code" n'est pas très utile. Les **props** (propriétés) permettent de passer des données à un composant depuis son parent.

Pensez aux props comme aux **paramètres d'une fonction** — ils viennent de l'extérieur, le composant ne peut pas les modifier.

```jsx
// Le composant reçoit les props comme argument
function CarteLivre({ titre, auteur, anneePublication, disponible }) {
  return (
    <div className="carte">
      <h2>{titre}</h2>
      <p>{auteur} — {anneePublication}</p>
      <span>{disponible ? "✅ Disponible" : "❌ Emprunté"}</span>
    </div>
  );
}

// Le parent passe les données via des attributs JSX
function App() {
  return (
    <div>
      <CarteLivre
        titre="Clean Code"
        auteur="Robert C. Martin"
        anneePublication={2008}
        disponible={true}
      />
      <CarteLivre
        titre="The Pragmatic Programmer"
        auteur="Hunt & Thomas"
        anneePublication={1999}
        disponible={false}
      />
    </div>
  );
}
```

#### Afficher une liste avec `.map()`

En React, on ne fait jamais de boucle `for`. On utilise `.map()` pour transformer un tableau de données en tableau de composants :

```jsx
function ListeLivres({ livres }) {
  return (
    <div className="liste">
      {livres.map(livre => (
        // La prop "key" est obligatoire — elle aide React à identifier chaque élément
        <CarteLivre
          key={livre.isbn}
          titre={livre.titre}
          auteur={livre.auteur}
          anneePublication={livre.anneePublication}
          disponible={livre.disponible}
        />
      ))}
    </div>
  );
}
```

> **La `key` est obligatoire dans une liste.** Sans elle, React ne sait pas quel élément a changé quand la liste se met à jour. Utilisez toujours un identifiant unique — jamais l'index du tableau (`0, 1, 2...`) si la liste peut changer d'ordre.

---

### 3 · State — La mémoire du composant

Une variable JavaScript normale dans un composant est recréée à chaque rendu. Elle ne "se souvient" de rien.

`useState` est la mémoire persistante d'un composant. Quand l'état change, React **re-affiche le composant** avec la nouvelle valeur — automatiquement.

```jsx
import { useState } from 'react';

function Compteur() {
  // useState retourne [valeurActuelle, fonctionPourLaChanger]
  const [compte, setCompte] = useState(0); // 0 = valeur initiale

  return (
    <div>
      <p>Clics : {compte}</p>
      <button onClick={() => setCompte(compte + 1)}>
        Cliquer
      </button>
    </div>
  );
}
```

**Ce qui se passe étape par étape :**
1. Au premier affichage : `compte = 0`, le DOM affiche "Clics : 0"
2. L'utilisateur clique → `setCompte(1)` est appelé
3. React re-affiche le composant avec `compte = 1`
4. Le DOM affiche "Clics : 1" — uniquement ce qui a changé est mis à jour

#### State avec des objets et tableaux

```jsx
function RechercheBar() {
  const [filtre, setFiltre] = useState('');
  const [typeFiltre, setTypeFiltre] = useState('titre'); // 'titre' ou 'auteur'

  return (
    <div>
      <input
        type="text"
        value={filtre}
        onChange={e => setFiltre(e.target.value)}
        placeholder={`Rechercher par ${typeFiltre}...`}
      />
      <select
        value={typeFiltre}
        onChange={e => setTypeFiltre(e.target.value)}
      >
        <option value="titre">Titre</option>
        <option value="auteur">Auteur</option>
      </select>
    </div>
  );
}
```

> **Règle fondamentale :** Ne jamais modifier l'état directement (`state.valeur = ...`). Toujours passer par la fonction setter (`setValeur(...)`). C'est cette fonction qui déclenche le re-rendu.

---

### 4 · useEffect — Réagir aux changements

`useEffect` permet d'exécuter du code **en réaction à quelque chose** : au premier affichage du composant, ou quand une valeur précise change.

```jsx
import { useState, useEffect } from 'react';

function Exemple() {
  const [count, setCount] = useState(0);

  // Déclenché UNE SEULE FOIS au premier affichage (tableau vide [])
  useEffect(() => {
    console.log('Composant monté');
  }, []);

  // Déclenché à chaque fois que "count" change
  useEffect(() => {
    console.log('count a changé :', count);
  }, [count]);

  // Déclenché à CHAQUE re-rendu (pas de tableau — déconseillé)
  useEffect(() => {
    console.log('Re-rendu');
  });

  return <button onClick={() => setCount(c => c + 1)}>{count}</button>;
}
```

**Le tableau de dépendances `[]` est la clé :**

| Syntaxe | Quand s'exécute |
| :--- | :--- |
| `useEffect(() => {...}, [])` | Une seule fois, au montage du composant |
| `useEffect(() => {...}, [valeur])` | Au montage ET chaque fois que `valeur` change |
| `useEffect(() => {...})` | À chaque re-rendu (rarement utile) |

---

### 5 · Fetch API — Parler à votre backend

#### Charger des données au montage du composant

Le pattern le plus courant en React : charger des données depuis une API au premier affichage.

```jsx
import { useState, useEffect } from 'react';

function ListeLivres() {
  const [livres, setLivres] = useState([]);       // les données
  const [chargement, setChargement] = useState(true); // état de chargement
  const [erreur, setErreur] = useState(null);    // erreur éventuelle

  useEffect(() => {
    fetch('http://localhost:8080/api/livres')
      .then(reponse => {
        if (!reponse.ok) {
          throw new Error(`Erreur serveur : ${reponse.status}`);
        }
        return reponse.json();
      })
      .then(donnees => {
        setLivres(donnees);
        setChargement(false);
      })
      .catch(err => {
        setErreur(err.message);
        setChargement(false);
      });
  }, []); // [] = une seule fois au montage

  if (chargement) return <p>Chargement en cours...</p>;
  if (erreur)     return <p style={{ color: 'red' }}>Erreur : {erreur}</p>;

  return (
    <div>
      {livres.map(livre => (
        <CarteLivre key={livre.isbn} {...livre} />
      ))}
    </div>
  );
}
```

#### Séparer la logique d'appel API dans un service

Le `fetch` ne doit pas vivre directement dans le composant — exactement comme en Spring, on ne met pas la logique métier dans le Controller.

```javascript
// src/services/livreService.js
const BASE_URL = 'http://localhost:8080/api';

export const livreService = {

  async getAll() {
    const reponse = await fetch(`${BASE_URL}/livres`);
    if (!reponse.ok) throw new Error(`Erreur ${reponse.status}`);
    return reponse.json();
  },

  async creer(livre) {
    const reponse = await fetch(`${BASE_URL}/livres`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(livre),
    });
    if (!reponse.ok) throw new Error(`Erreur ${reponse.status}`);
    return reponse.json();
  },

  async supprimer(isbn) {
    const reponse = await fetch(`${BASE_URL}/livres/${isbn}`, {
      method: 'DELETE',
    });
    if (!reponse.ok) throw new Error(`Erreur ${reponse.status}`);
  },
};
```

```jsx
// Dans le composant — propre et lisible
import { livreService } from '../services/livreService';

function ListeLivres() {
  const [livres, setLivres] = useState([]);

  useEffect(() => {
    livreService.getAll()
      .then(setLivres)
      .catch(err => console.error(err));
  }, []);
  // ...
}
```

---

## 🥋 Dojo de Code — L'interface de la Bibliothèque

> Vous allez construire le frontend React qui consomme votre API Spring Boot. À la fin, un utilisateur peut consulter, filtrer et ajouter des livres depuis un navigateur.

### Avant de commencer — Les 3 questions

```
✅ Business  : Qui utilise cette interface ? Le bibliothécaire.
               Quels besoins ? Voir la liste des livres, rechercher par titre ou auteur,
               ajouter un nouveau livre, voir rapidement la disponibilité.

✅ Dev       : L'API tourne sur localhost:8080 → problème CORS à anticiper.
               Aucune authentification pour l'instant (simplification volontaire).
               Le filtre de recherche doit fonctionner sans appel API supplémentaire
               (filtrage côté client sur la liste déjà chargée).

✅ QA        : Scénarios à valider :
               - L'app affiche "Chargement..." pendant la requête
               - Si l'API est éteinte, un message d'erreur clair s'affiche
               - La recherche filtre en temps réel (sans rechargement)
               - Le formulaire se vide après un ajout réussi
               - Le nouveau livre apparaît dans la liste sans recharger la page
```

### Structure cible du projet

```
bibliotheque-frontend/
├── index.html
├── package.json
├── vite.config.js
└── src/
    ├── main.jsx
    ├── App.jsx
    ├── components/
    │   ├── CarteLivre.jsx
    │   ├── ListeLivres.jsx
    │   ├── RechercheBar.jsx
    │   └── LivreForm.jsx
    └── services/
        └── livreService.js
```

### Étape 1 — Initialiser le projet React (Jour 1)

```bash
# Créer le projet avec Vite
npm create vite@latest bibliotheque-frontend -- --template react
cd bibliotheque-frontend

# Installer les dépendances
npm install

# Lancer le serveur de développement
npm run dev
```

Ouvrez `http://localhost:5173`. Vous voyez la page d'accueil Vite+React. C'est votre point de départ.

**Nettoyez les fichiers générés automatiquement :**
- Videz `src/App.jsx` — vous repartez d'une page blanche
- Supprimez `src/App.css` et `src/index.css` (on gardera du CSS minimal)

**`src/App.jsx` — votre point de départ vide :**

```jsx
function App() {
  return (
    <div>
      <h1>📚 Bibliothèque</h1>
      {/* Les composants viendront ici */}
    </div>
  );
}

export default App;
```

Lancez `npm run dev`. Vous devez voir "📚 Bibliothèque" dans le navigateur.

### Étape 2 — Créer le composant CarteLivre (Jour 1-2)

```jsx
// src/components/CarteLivre.jsx

function CarteLivre({ isbn, titre, auteur, anneePublication, disponible }) {
  return (
    <div style={{
      border: '1px solid #ccc',
      borderRadius: '8px',
      padding: '16px',
      margin: '8px',
      backgroundColor: disponible ? '#f0fff0' : '#fff0f0'
    }}>
      <h3>{titre}</h3>
      <p><strong>Auteur :</strong> {auteur}</p>
      <p><strong>Année :</strong> {anneePublication}</p>
      <p>
        <strong>Statut :</strong>{' '}
        <span>{disponible ? '✅ Disponible' : '❌ Emprunté'}</span>
      </p>
    </div>
  );
}

export default CarteLivre;
```

Testez-le dans `App.jsx` avec des données en dur :

```jsx
import CarteLivre from './components/CarteLivre';

function App() {
  return (
    <div>
      <h1>📚 Bibliothèque</h1>
      <CarteLivre
        isbn="978-0-13-468599-1"
        titre="Clean Code"
        auteur="Robert C. Martin"
        anneePublication={2008}
        disponible={true}
      />
    </div>
  );
}
```

### Étape 3 — Connecter à l'API et gérer le chargement (Jour 2-3)

D'abord, créez le service :

```javascript
// src/services/livreService.js
const BASE_URL = 'http://localhost:8080/api';

export const livreService = {
  async getAll() {
    const reponse = await fetch(`${BASE_URL}/livres`);
    if (!reponse.ok) throw new Error(`Erreur serveur : ${reponse.status}`);
    return reponse.json();
  },

  async creer(livre) {
    const reponse = await fetch(`${BASE_URL}/livres`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(livre),
    });
    if (!reponse.ok) throw new Error(`Erreur serveur : ${reponse.status}`);
    return reponse.json();
  },
};
```

Ensuite, créez `ListeLivres.jsx` qui charge les données et les affiche :

```jsx
// src/components/ListeLivres.jsx
import { useState, useEffect } from 'react';
import CarteLivre from './CarteLivre';
import { livreService } from '../services/livreService';

function ListeLivres({ filtre }) {
  const [livres, setLivres]         = useState([]);
  const [chargement, setChargement] = useState(true);
  const [erreur, setErreur]         = useState(null);

  useEffect(() => {
    setChargement(true);
    livreService.getAll()
      .then(data => {
        setLivres(data);
        setChargement(false);
      })
      .catch(err => {
        setErreur(err.message);
        setChargement(false);
      });
  }, []); // Chargement une seule fois au montage

  // Filtrage côté client — pas de nouvel appel API
  const livresFiltres = livres.filter(livre =>
    livre.titre.toLowerCase().includes(filtre.toLowerCase()) ||
    livre.auteur.toLowerCase().includes(filtre.toLowerCase())
  );

  if (chargement) return <p>⏳ Chargement des livres...</p>;
  if (erreur)     return <p style={{ color: 'red' }}>❌ {erreur} — L'API est-elle démarrée ?</p>;
  if (livresFiltres.length === 0) return <p>Aucun livre trouvé pour "{filtre}"</p>;

  return (
    <div style={{ display: 'flex', flexWrap: 'wrap' }}>
      {livresFiltres.map(livre => (
        <CarteLivre key={livre.isbn} {...livre} />
      ))}
    </div>
  );
}

export default ListeLivres;
```

### Étape 4 — Ajouter la recherche en temps réel (Jour 3)

```jsx
// src/components/RechercheBar.jsx
function RechercheBar({ valeur, onChange }) {
  return (
    <input
      type="text"
      value={valeur}
      onChange={e => onChange(e.target.value)}
      placeholder="Rechercher par titre ou auteur..."
      style={{ padding: '8px', width: '300px', fontSize: '16px' }}
    />
  );
}

export default RechercheBar;
```

Assemblez dans `App.jsx` — **l'état de recherche vit dans App** car il est partagé entre `RechercheBar` et `ListeLivres` :

```jsx
import { useState } from 'react';
import ListeLivres from './components/ListeLivres';
import RechercheBar from './components/RechercheBar';

function App() {
  const [filtre, setFiltre] = useState('');

  return (
    <div style={{ padding: '24px', fontFamily: 'sans-serif' }}>
      <h1>📚 Bibliothèque</h1>
      <RechercheBar valeur={filtre} onChange={setFiltre} />
      <ListeLivres filtre={filtre} />
    </div>
  );
}

export default App;
```

Testez : en tapant dans le champ, la liste se filtre instantanément.

### Étape 5 — Formulaire d'ajout d'un livre (Jour 4)

```jsx
// src/components/LivreForm.jsx
import { useState } from 'react';
import { livreService } from '../services/livreService';

function LivreForm({ onLivreAjoute }) {
  const [formulaire, setFormulaire] = useState({
    isbn: '', titre: '', auteur: '', anneePublication: ''
  });
  const [erreur, setErreur]   = useState(null);
  const [succes, setSucces]   = useState(false);
  const [envoi, setEnvoi]     = useState(false);

  function gererChangement(e) {
    const { name, value } = e.target;
    setFormulaire(prev => ({ ...prev, [name]: value }));
  }

  async function gererSoumission(e) {
    e.preventDefault(); // Empêche le rechargement de la page
    setEnvoi(true);
    setErreur(null);

    try {
      const nouveau = await livreService.creer({
        ...formulaire,
        anneePublication: parseInt(formulaire.anneePublication),
      });
      setSucces(true);
      setFormulaire({ isbn: '', titre: '', auteur: '', anneePublication: '' });
      onLivreAjoute(nouveau); // Prévient le parent qu'un livre a été ajouté
      setTimeout(() => setSucces(false), 3000);
    } catch (err) {
      setErreur(err.message);
    } finally {
      setEnvoi(false);
    }
  }

  return (
    <form onSubmit={gererSoumission} style={{ margin: '24px 0' }}>
      <h2>Ajouter un livre</h2>
      {erreur  && <p style={{ color: 'red' }}>❌ {erreur}</p>}
      {succes  && <p style={{ color: 'green' }}>✅ Livre ajouté !</p>}

      {[
        { name: 'isbn',              label: 'ISBN',              type: 'text'   },
        { name: 'titre',             label: 'Titre',             type: 'text'   },
        { name: 'auteur',            label: 'Auteur',            type: 'text'   },
        { name: 'anneePublication',  label: 'Année',             type: 'number' },
      ].map(({ name, label, type }) => (
        <div key={name} style={{ marginBottom: '8px' }}>
          <label>{label} : </label>
          <input
            type={type}
            name={name}
            value={formulaire[name]}
            onChange={gererChangement}
            required
          />
        </div>
      ))}

      <button type="submit" disabled={envoi}>
        {envoi ? 'Envoi...' : 'Ajouter'}
      </button>
    </form>
  );
}

export default LivreForm;
```

Mettez à jour `App.jsx` pour que le nouveau livre apparaisse **sans recharger** :

```jsx
import { useState } from 'react';
import ListeLivres from './components/ListeLivres';
import RechercheBar from './components/RechercheBar';
import LivreForm from './components/LivreForm';

function App() {
  const [filtre, setFiltre]           = useState('');
  const [livresAjoutes, setLivresAjoutes] = useState([]);

  function ajouterLivre(nouveau) {
    setLivresAjoutes(prev => [...prev, nouveau]);
  }

  return (
    <div style={{ padding: '24px', fontFamily: 'sans-serif' }}>
      <h1>📚 Bibliothèque</h1>
      <RechercheBar valeur={filtre} onChange={setFiltre} />
      <LivreForm onLivreAjoute={ajouterLivre} />
      <ListeLivres filtre={filtre} livresAjoutes={livresAjoutes} />
    </div>
  );
}

export default App;
```

### Étape 6 — Versionner avec Git Flow (Jour 5)

```bash
# Initialiser depuis develop (déjà créé en S1/S2)
git checkout develop
git pull

# Feature 1 : structure de base
git checkout -b feature/react-setup
git commit -m "feat: initialiser le projet React avec Vite"
git commit -m "feat: ajouter le composant CarteLivre"
git push -u origin feature/react-setup
# → Pull Request : feature/react-setup → develop

git checkout develop ; git pull

# Feature 2 : connexion API
git checkout -b feature/connexion-api
git commit -m "feat: créer livreService avec getAll et creer"
git commit -m "feat: implémenter ListeLivres avec gestion chargement et erreur"
git push -u origin feature/connexion-api
# → Pull Request : feature/connexion-api → develop

git checkout develop ; git pull

# Feature 3 : fonctionnalités utilisateur
git checkout -b feature/recherche-et-formulaire
git commit -m "feat: ajouter RechercheBar avec filtrage temps réel"
git commit -m "feat: ajouter LivreForm avec gestion des états d'envoi"
git push -u origin feature/recherche-et-formulaire
# → Pull Request : feature/recherche-et-formulaire → develop
```

---

## ✅ Critères de validation

Fermez ce document. Expliquez chaque point à voix haute ou par écrit, sans aide :

**JavaScript & React — Fondations**
- [ ] **Composant** — Qu'est-ce qu'un composant React ? En quoi est-il différent d'une balise HTML ?
- [ ] **JSX** — Citez deux différences entre JSX et HTML classique. Que devient `<h1>Bonjour</h1>` après compilation ?
- [ ] **`export default`** — Pourquoi faut-il exporter un composant ? Quelle est la différence entre export default et export nommé ?

**Props & State**
- [ ] **Props vs State** — Quelle est la différence fondamentale ? Lequel peut être modifié par le composant lui-même ?
- [ ] **`key` dans une liste** — Pourquoi est-elle obligatoire ? Pourquoi ne doit-on pas utiliser l'index comme clé ?
- [ ] **Remonter l'état** — Pourquoi l'état du filtre de recherche vit-il dans `App` et non dans `RechercheBar` ?

**useEffect & API**
- [ ] **`useEffect(fn, [])` vs `useEffect(fn, [valeur])`** — Quelle est la différence de déclenchement ?
- [ ] **Les 3 états d'un appel API** — Nommez-les et expliquez comment vous les gérez dans le composant.
- [ ] **Séparation service/composant** — Pourquoi met-on le `fetch` dans `livreService.js` plutôt que directement dans le composant ?

**Git Flow**
- [ ] **Git Flow** — Depuis quelle branche avez-vous créé chaque feature branch ? Où chaque PR est-elle mergée ?

---

## 🎯 Conseils de recruteur

> Le frontend est souvent le premier test technique en entreprise. Ces points reviennent systématiquement.

### Questions fréquentes et ce que le recruteur attend

**"Expliquez-moi la différence entre props et state."**
> Le mauvais candidat : *"Les props c'est ce qu'on passe au composant et le state c'est interne."*  
> Le bon candidat : *"Les props sont en lecture seule — un composant ne peut pas modifier ses propres props. Le state est la mémoire interne du composant, qu'il contrôle entièrement. Quand le state change via `setState`, React re-affiche le composant. Si plusieurs composants partagent un état, on le fait remonter au plus proche ancêtre commun."*

**"Qu'est-ce que le Virtual DOM ?"**
> React maintient une **représentation en mémoire** du DOM (le Virtual DOM). Quand l'état change, React calcule la différence entre l'ancien et le nouveau Virtual DOM, puis applique **uniquement les changements nécessaires** au vrai DOM. C'est ce qui rend React performant même avec des listes longues.

**"Pourquoi `useEffect` avec `[]` en deuxième argument ?"**
> Le tableau vide signifie "pas de dépendances". Donc l'effet ne se lance qu'au montage du composant, jamais en mise à jour. Sans tableau, l'effet se relance à chaque rendu — ce qui provoquerait une boucle infinie si on fait un appel API dedans.

**"Comment géreriez-vous une liste de 10 000 éléments en React ?"**
> Pour un Junior, la réponse attendue est : pagination côté serveur (on ne charge pas 10 000 éléments d'un coup), ou virtualisation (bibliothèques comme `react-window` qui n'affichent que les éléments visibles). Citer ces approches montre que vous pensez à la performance.

**"Qu'est-ce qu'un effet de bord en React ?"**
> Tout ce qui interagit avec l'extérieur du composant : appel API, minuterie (`setTimeout`), abonnement à un événement, modification du titre de la page. Ces actions ne doivent pas être directement dans le corps du composant — elles doivent vivre dans un `useEffect`.

### Ce que votre GitHub doit montrer

Un recruteur frontend regarde :
1. **Les composants sont-ils petits et ciblés ?** Un composant de 200 lignes est un signal d'alarme.
2. **La gestion des états de chargement et d'erreur est-elle présente ?** Un composant qui ignore les erreurs n'est pas prêt pour la production.
3. **La logique d'appel API est-elle séparée des composants ?** Le `fetch` dans le composant = couplage fort.
4. **Les commits Git Flow racontent-ils une progression logique ?**

---

## 🔗 Ressources complémentaires

| Ressource | Type | Pourquoi |
| :--- | :--- | :--- |
| [React — Documentation officielle](https://react.dev) | Documentation | La référence — réécrite en 2023, excellente pour débutants |
| [Vite — Getting Started](https://vitejs.dev/guide/) | Documentation | Comprendre votre outil de build |
| [JavaScript.info](https://javascript.info) | Tutoriel | Si vous avez des lacunes JS (map, async/await, destructuring) |
| [React DevTools](https://react.dev/learn/react-developer-tools) | Extension | Inspecter les composants, props et state dans le navigateur |
| [Excalidraw](https://excalidraw.com) | Outil | Dessiner l'arbre de composants avant de coder |

---

**[⬅ Semaine 2 — Backend Spring Boot](./semaine-2.md)** · **[Semaine 4 — Intégration Full-Stack ➔](./semaine-4.md)**
