# 📘 Semaine 1 — Fondations Java : Penser en Objets, Versionner son Travail

> **Objectif de la semaine :** Maîtriser les bases du langage Java, raisonner en termes d'objets et de responsabilités, comprendre comment la mémoire fonctionne, et versionner son travail avec Git dès le premier jour.

**Durée estimée :** 35 à 40 heures  
**Prérequis :** JDK 21 installé, IntelliJ IDEA, Git configuré avec un compte GitHub

---

## 🧭 Table des matières

1. [Le concept clé — Comprendre avant de définir](#-le-concept-clé--comprendre-avant-de-définir)
2. [Notions techniques incontournables](#-notions-techniques-incontournables)
   - [POO — Programmation Orientée Objet](#1--poo--programmation-orientée-objet)
   - [Collections Java](#2--collections-java)
   - [Gestion de la mémoire — Stack & Heap](#3--gestion-de-la-mémoire--stack--heap)
   - [Git — Contrôle de version et workflow d'équipe](#4--git--contrôle-de-version-et-workflow-déquipe)
3. [Dojo de Code](#-dojo-de-code--la-bibliothèque-de-quartier)
4. [Critères de validation](#-critères-de-validation)
5. [Conseils de recruteur](#-conseils-de-recruteur)

---

## 🧠 Le concept clé — Comprendre avant de définir

### La POO vue comme une entreprise bien organisée

Imaginez que vous créez une entreprise de livraison de colis.

Dans cette entreprise, vous avez des **rôles distincts** :
- Un **Livreur** sait conduire, connaît son secteur, et porte des colis.
- Un **Colis** a un poids, une adresse de destination et un statut (en transit, livré, perdu).
- Un **Manager** orchestre les livraisons, affecte les livreurs aux colis.

Aucun rôle ne fait le travail d'un autre. Le Livreur ne décide pas lui-même de son itinéraire global — c'est le Manager. Le Colis ne se livre pas lui-même — c'est le Livreur.

**C'est exactement ce qu'est la POO.** Chaque `class` est un rôle avec ses propres données (les attributs) et ses propres capacités (les méthodes). Le code est une organisation, pas une liste d'instructions à la suite.

> **Règle à retenir :** Si une classe fait "trop de choses", c'est comme un employé qui occupe trois postes en même temps. Ça finit mal. Chaque classe a **une seule responsabilité** (principe SOLID : Single Responsibility).

---

### La mémoire vue comme les bureaux d'une entreprise

Toujours dans votre entreprise de livraison :
- La **Stack** (pile) c'est le bureau de l'employé. Petit, rapide, organisé. Quand la tâche est terminée, le bureau est vidé.
- Le **Heap** (tas) c'est l'entrepôt commun. Plus grand, partagé, mais moins rapide d'accès. On y stocke les objets lourds qui doivent persister.

Quand vous écrivez `new Colis()` en Java, vous créez un objet dans l'**entrepôt** (Heap). La variable qui y fait référence, elle, est sur le **bureau** (Stack) de la méthode courante.

---

## 📚 Notions techniques incontournables

### 1 · POO — Programmation Orientée Objet

#### Classes et objets

Une `class` est le plan de construction. Un `object` est la construction elle-même.

```java
// Le plan (la classe)
public class Livre {
    private String titre;
    private String auteur;
    private int anneePublication;

    // Constructeur
    public Livre(String titre, String auteur, int anneePublication) {
        this.titre = titre;
        this.auteur = auteur;
        this.anneePublication = anneePublication;
    }

    // Méthode
    public String resumer() {
        return titre + " par " + auteur + " (" + anneePublication + ")";
    }

    // Getters
    public String getTitre() { return titre; }
    public String getAuteur() { return auteur; }
    public int getAnneePublication() { return anneePublication; }
}

// L'objet (une instance du plan)
Livre livre1 = new Livre("Clean Code", "Robert C. Martin", 2008);
System.out.println(livre1.resumer());
// → "Clean Code par Robert C. Martin (2008)"
```

#### Les 4 piliers de la POO

| Pilier | En une phrase | Exemple concret |
| :--- | :--- | :--- |
| **Encapsulation** | Cacher les données internes, n'exposer que ce qui est nécessaire | `private` sur les attributs, `public` sur les getters |
| **Héritage** | Une classe enfant hérite des capacités de son parent | `class RomanPolicier extends Livre` |
| **Polymorphisme** | Un même message, des comportements différents selon le destinataire | Méthode `resumer()` redéfinie différemment dans chaque sous-classe |
| **Abstraction** | Définir un contrat sans imposer l'implémentation | `interface Empruntable` avec méthode `emprunter()` |

#### Interfaces et abstraction

```java
// Un contrat : tout ce qui implémente cette interface DOIT savoir s'emprunter
public interface Empruntable {
    void emprunter(String nomEmprunteur);
    void retourner();
    boolean estDisponible();
}

// Livre respecte le contrat
public class Livre implements Empruntable {
    private boolean disponible = true;
    private String emprunteurActuel;

    @Override
    public void emprunter(String nomEmprunteur) {
        if (!disponible) {
            throw new IllegalStateException("Ce livre est déjà emprunté.");
        }
        this.emprunteurActuel = nomEmprunteur;
        this.disponible = false;
    }

    @Override
    public void retourner() {
        this.emprunteurActuel = null;
        this.disponible = true;
    }

    @Override
    public boolean estDisponible() {
        return disponible;
    }
}
```

> **À ne pas confondre :** Une `interface` définit un contrat (ce qu'on peut faire). Une `class abstraite` peut avoir du code partagé en plus du contrat. En cas de doute, préférez l'interface.

---

### 2 · Collections Java

Les Collections sont les structures de données du quotidien. Choisir la mauvaise structure, c'est comme ranger des dossiers en vrac dans une grande boîte quand on a besoin de les retrouver par nom.

#### Les trois structures essentielles

| Structure | Analogie | Usage | Ordre | Doublons |
| :--- | :--- | :--- | :--- | :--- |
| `ArrayList<T>` | Liste de courses | Accès par index, parcours séquentiel | ✅ Oui | ✅ Oui |
| `HashMap<K,V>` | Dictionnaire | Recherche rapide par clé | ❌ Non | ❌ Non (clés) |
| `HashSet<T>` | Liste d'invités unique | Vérifier l'appartenance | ❌ Non | ❌ Non |

```java
import java.util.*;

// ArrayList : une liste ordonnée de livres
List<Livre> catalogue = new ArrayList<>();
catalogue.add(new Livre("Clean Code", "Martin", 2008));
catalogue.add(new Livre("The Pragmatic Programmer", "Hunt & Thomas", 1999));

// Parcourir avec un for-each
for (Livre livre : catalogue) {
    System.out.println(livre.resumer());
}

// HashMap : retrouver un livre par son ISBN
Map<String, Livre> indexISBN = new HashMap<>();
indexISBN.put("978-0-13-468599-1", new Livre("Clean Code", "Martin", 2008));
Livre trouve = indexISBN.get("978-0-13-468599-1");

// HashSet : liste des auteurs sans doublons
Set<String> auteurs = new HashSet<>();
auteurs.add("Robert C. Martin");
auteurs.add("Robert C. Martin"); // ignoré, déjà présent
System.out.println(auteurs.size()); // → 1
```

#### Itération moderne avec les Streams (aperçu)

```java
// Filtrer les livres publiés après 2000
List<Livre> livresRecents = catalogue.stream()
    .filter(l -> l.getAnneePublication() > 2000)
    .toList();

// Trier par titre
List<Livre> parTitre = catalogue.stream()
    .sorted(Comparator.comparing(Livre::getTitre))
    .toList();
```

---

### 3 · Gestion de la mémoire — Stack & Heap

#### La Stack (pile d'exécution)

- Stocke les **variables locales** et les **références** aux objets.
- Organisée en **frames** : chaque appel de méthode crée un frame, qui est détruit quand la méthode se termine.
- Gestion **automatique**, très rapide.

#### Le Heap (tas)

- Stocke les **objets** créés avec `new`.
- Partagé entre tous les threads.
- Géré par le **Garbage Collector** (GC) de la JVM.

```java
public static void main(String[] args) {
    // "titre" est une référence sur la Stack
    // L'objet String "Clean Code" est dans le Heap
    String titre = "Clean Code";

    // "livre" est une référence sur la Stack
    // L'objet Livre est dans le Heap
    Livre livre = new Livre("Clean Code", "Martin", 2008);

    afficherTitre(livre); // nouveau frame sur la Stack
} // frame de main() détruit → "livre" et "titre" libérés de la Stack

static void afficherTitre(Livre l) {
    // "l" est une référence locale sur la Stack,
    // mais elle pointe vers le MÊME objet Livre dans le Heap
    System.out.println(l.getTitre());
} // frame d'afficherTitre() détruit
```

#### Le Garbage Collector

Quand plus aucune référence ne pointe vers un objet dans le Heap, le GC peut le supprimer. Vous n'avez pas à le faire manuellement (contrairement au C/C++).

```java
Livre livre = new Livre("A", "Auteur", 2020); // objet créé dans le Heap
livre = null; // plus aucune référence → éligible au Garbage Collector
```

> **Piège courant :** Conserver une référence vers un objet dont on n'a plus besoin (dans une `List` statique, par exemple). Le GC ne peut pas le collecter → **fuite mémoire** (memory leak).

---

### 4 · Git — Contrôle de version et workflow d'équipe

#### Pourquoi Git ?

Git est le journal de bord de votre code. Chaque `commit` est une sauvegarde nommée, datée, et réversible. Travailler sans Git, c'est écrire un roman sans jamais sauvegarder — et sans pouvoir revenir en arrière quand on réalise qu'on a effacé le meilleur chapitre.

Mais Git seul ne suffit pas. En équipe, la vraie question n'est pas *"comment sauvegarder ?"* mais *"comment plusieurs développeurs travaillent-ils sur le même code sans se marcher dessus ?"*. C'est là qu'intervient un **workflow de branches**.

#### Les commandes essentielles

```bash
# Initialiser un dépôt local
git init

# Vérifier l'état des fichiers
git status

# Ajouter des fichiers à la zone de staging
git add Livre.java
git add .           # ajouter tous les fichiers modifiés

# Committer avec un message clair
git commit -m "feat: ajouter la classe Livre avec encapsulation"

# Connecter à un dépôt distant (GitHub)
git remote add origin https://github.com/votre-username/dojo-bibliotheque.git

# Envoyer sur GitHub
git push -u origin main

# Récupérer les modifications distantes
git pull

# Créer une branche de fonctionnalité
git checkout -b feature/gestion-emprunts

# Revenir sur develop et fusionner
git checkout develop
git merge --no-ff feature/gestion-emprunts
git branch -d feature/gestion-emprunts
```

#### Convention de messages de commit

Adoptez dès maintenant la convention **Conventional Commits** :

| Préfixe | Usage | Exemple |
| :--- | :--- | :--- |
| `feat:` | Nouvelle fonctionnalité | `feat: ajouter la recherche par auteur` |
| `fix:` | Correction de bug | `fix: corriger le retour de livre déjà disponible` |
| `refactor:` | Restructuration sans changement de comportement | `refactor: extraire la validation dans une méthode privée` |
| `test:` | Ajout ou modification de tests | `test: ajouter les tests unitaires de Livre` |
| `docs:` | Documentation | `docs: mettre à jour le README` |

> **Règle d'or Git :** Un commit = une intention. Ne mélangez jamais plusieurs modifications sans rapport dans le même commit.

---

#### Git Flow — Le workflow que vous appliquerez dans tous les Dojos

Git Flow est une convention de nommage et d'organisation des branches. Ce n'est pas un outil magique — c'est un **accord entre développeurs** sur la façon de collaborer. Dans les entreprises, tout le monde parle de `feature branch`, de `release`, de `hotfix`. Ces mots viennent de Git Flow.

**Les branches du modèle Git Flow :**

```
 main          ─────●────────────────────────●──────
                    │ (tag v1.0)              │ (tag v1.1)
 develop       ─────●────●────●──────────────●──────
                         │    │              │
 feature/...        ─────●────┘         ─────┘
                    (fusionnée)          (fusionnée)
```

| Branche | Rôle | Règle |
| :--- | :--- | :--- |
| `main` | Code en production — toujours stable | On ne commit jamais directement dessus |
| `develop` | Intégration continue — code validé, pas encore en prod | Base de départ pour toute feature |
| `feature/nom` | Développement d'une fonctionnalité | Créée depuis `develop`, fusionnée dans `develop` |
| `release/x.y` | Préparation d'une mise en production | Créée depuis `develop`, fusionnée dans `main` ET `develop` |
| `hotfix/nom` | Correction urgente en production | Créée depuis `main`, fusionnée dans `main` ET `develop` |

**Le workflow que vous suivrez dans chaque Dojo :**

```bash
# 1. Démarrer depuis develop (jamais depuis main)
git checkout develop
git pull

# 2. Créer une branche pour votre fonctionnalité
git checkout -b feature/gestion-emprunts

# 3. Travailler, committer régulièrement
git add .
git commit -m "feat: ajouter la méthode emprunterLivre"
git commit -m "feat: ajouter la méthode retournerLivre"
git commit -m "test: vérifier la limite de 3 emprunts par adhérent"

# 4. Pousser la branche sur GitHub
git push -u origin feature/gestion-emprunts

# 5. Ouvrir une Pull Request : feature/gestion-emprunts → develop
# (sur l'interface GitHub)

# 6. Après validation de la PR, supprimer la branche locale
git checkout develop
git pull
git branch -d feature/gestion-emprunts
```

> **Pourquoi `--no-ff` sur le merge ?** L'option `--no-ff` (no fast-forward) force la création d'un commit de merge même quand ce n'est pas nécessaire techniquement. Résultat : l'historique conserve une trace visuelle de chaque fonctionnalité. En entreprise, c'est souvent imposé par les règles de branche du dépôt.

---

#### Scrum — Le cadre dans lequel vous allez travailler

Dans 95 % des équipes de développement en France, le travail est organisé en **Scrum**. Vous allez entendre ces mots dès votre premier jour. Autant les connaître maintenant.

Scrum est un **cadre de travail agile** : au lieu de tout planifier sur 6 mois et de livrer une fois (cascade), on livre par petits incréments appelés **Sprints**.

**Les éléments clés de Scrum :**

| Élément | C'est quoi | Durée / Fréquence |
| :--- | :--- | :--- |
| **Sprint** | Une itération de travail avec un objectif clair | 1 à 4 semaines (souvent 2) |
| **Product Backlog** | La liste de tout ce qu'on veut construire, priorisée | Vivante, mise à jour en continu |
| **Sprint Backlog** | Ce qu'on s'engage à faire pendant CE sprint | Figé au démarrage du sprint |
| **User Story** | Une fonctionnalité décrite du point de vue utilisateur | *"En tant que bibliothécaire, je veux enregistrer un emprunt..."* |
| **Daily Standup** | Réunion quotidienne de 15 min max | Chaque jour du sprint |
| **Sprint Review** | Démonstration de ce qui a été livré | Fin de sprint |
| **Rétrospective** | L'équipe s'améliore elle-même | Fin de sprint, après la review |

**Le format d'une User Story :**

```
En tant que [rôle],
Je veux [action],
Afin de [bénéfice].

Critères d'acceptation :
- [ ] Quand un livre est déjà emprunté, le système refuse et affiche un message clair
- [ ] La durée d'emprunt par défaut est de 14 jours
- [ ] L'adhérent ne peut pas avoir plus de 3 emprunts simultanés
```

**Comment Scrum se traduit dans vos Dojos :**

- Chaque **Dojo = un mini-Sprint** d'une semaine
- Les **fonctionnalités à implémenter = votre Sprint Backlog**
- Chaque **feature branch = une User Story en cours**
- La **Pull Request = la Demo / Review de votre Story**

Vous pratiquez déjà Scrum sans le savoir. À partir de maintenant, vous le faites consciemment.

---

#### Les 3 Amigos — Construire la bonne chose du premier coup

Imaginez que vous commencez à coder une fonctionnalité, vous passez 2 jours dessus, et au moment de la démo, le Product Owner dit : *"Ce n'est pas du tout ce que je voulais."* Deux jours de travail à jeter.

Les **3 Amigos** est une pratique qui évite exactement ça. Avant de coder quoi que ce soit, trois perspectives se réunissent autour d'une User Story pour **s'aligner sur ce qui doit être construit**.

**Les 3 rôles :**

| Rôle | Question qu'il apporte | Ce qu'il cherche à clarifier |
| :--- | :--- | :--- |
| **Product Owner / Business** | *"Qu'est-ce qu'on veut ?"* | L'intention métier, la valeur pour l'utilisateur |
| **Développeur** | *"Comment on le construit ?"* | La faisabilité technique, les cas limites, les contraintes |
| **QA / Testeur** | *"Comment on sait que c'est bon ?"* | Les critères d'acceptation, les cas d'erreur, les scénarios oubliés |

**Ce qui sort d'une session 3 Amigos :**

- Des critères d'acceptation clairs et validés par les trois
- Les cas limites identifiés *avant* le développement (pas après)
- Un accord commun sur la définition de "terminé" (Definition of Done)

**Dans le cadre des Dojos :**

Vous jouez les 3 rôles vous-même. Avant de commencer une feature branch, posez-vous les trois questions :

```
✅ Business  : Quelle valeur cette fonctionnalité apporte-t-elle à l'utilisateur final ?
✅ Dev       : Quels sont les cas limites et les contraintes techniques ?
✅ QA        : Quels scénarios vais-je tester pour valider que c'est correct ?
```

Répondre à ces trois questions *avant* d'écrire la première ligne de code vous fera gagner plus de temps qu'aucun autre réflexe de développeur.

---

## 🥋 Dojo de Code — La Bibliothèque de Quartier

> Ce projet sera la fondation sur laquelle vous bâtirez l'API REST (S2), l'interface React (S3) et l'application complète (S4). Prenez-le au sérieux.

### Contexte

Vous développez le système de gestion d'une **bibliothèque de quartier**. Le bibliothécaire a besoin de :
- Gérer un catalogue de livres
- Enregistrer des adhérents
- Gérer les emprunts (qui a emprunté quoi, et quand)

### Étape 1 — Modéliser les entités (Jour 1-2)

Créez les classes suivantes dans un projet Maven :

```
src/
└── main/
    └── java/
        └── fr/dojo/bibliotheque/
            ├── model/
            │   ├── Livre.java
            │   ├── Adherent.java
            │   └── Emprunt.java
            ├── service/
            │   └── BibliothequeService.java
            └── Main.java
```

**`Livre.java`** doit avoir : `isbn` (String), `titre` (String), `auteur` (String), `anneePublication` (int), `disponible` (boolean).

**`Adherent.java`** doit avoir : `id` (int), `nom` (String), `prenom` (String), `email` (String).

**`Emprunt.java`** doit avoir : une référence vers un `Livre`, une référence vers un `Adherent`, une `dateEmprunt` (`LocalDate`), une `dateRetourPrevue` (`LocalDate`), un `estRendu` (boolean).

### Étape 2 — Implémenter le service (Jour 3-4)

Créez `BibliothequeService.java` qui gère :

```java
public class BibliothequeService {
    private List<Livre> catalogue = new ArrayList<>();
    private List<Adherent> adherents = new ArrayList<>();
    private List<Emprunt> emprunts = new ArrayList<>();

    // À implémenter :
    public void ajouterLivre(Livre livre) { /* ... */ }
    public void inscrireAdherent(Adherent adherent) { /* ... */ }
    public Emprunt emprunterLivre(String isbn, int adherentId) { /* ... */ }
    public void retournerLivre(String isbn) { /* ... */ }
    public List<Livre> rechercherParAuteur(String auteur) { /* ... */ }
    public List<Emprunt> getEmpruntsEnCours() { /* ... */ }
    public List<Emprunt> getEmpruntsEnRetard() { /* ... */ }
}
```

**Contraintes métier à respecter :**
- On ne peut pas emprunter un livre déjà emprunté → lever une `IllegalStateException`
- On ne peut pas emprunter si l'adhérent a plus de 3 emprunts en cours
- La durée d'emprunt par défaut est de 14 jours
- Un emprunt en retard est un emprunt dont la `dateRetourPrevue` est dépassée et qui n'est pas encore rendu

### Étape 3 — Tester manuellement dans Main (Jour 4)

```java
public class Main {
    public static void main(String[] args) {
        BibliothequeService service = new BibliothequeService();

        // Ajouter des livres
        service.ajouterLivre(new Livre("978-0-13-468599-1", "Clean Code", "Robert C. Martin", 2008));
        service.ajouterLivre(new Livre("978-0-13-235088-4", "The Pragmatic Programmer", "Hunt & Thomas", 1999));

        // Inscrire un adhérent
        service.inscrireAdherent(new Adherent(1, "Dupont", "Alice", "alice@email.com"));

        // Emprunter un livre
        Emprunt emprunt = service.emprunterLivre("978-0-13-468599-1", 1);
        System.out.println("Emprunt créé, retour prévu le : " + emprunt.getDateRetourPrevue());

        // Tentative d'emprunt d'un livre déjà emprunté
        try {
            service.emprunterLivre("978-0-13-468599-1", 1);
        } catch (IllegalStateException e) {
            System.out.println("Erreur attendue : " + e.getMessage());
        }
    }
}
```

### Étape 4 — Versionner sur GitHub avec Git Flow (Jour 5)

**Avant d'écrire la moindre ligne de code pour cette étape, appliquez les 3 questions :**
```
✅ Business  : Quelle valeur la recherche avancée apporte-t-elle au bibliothécaire ?
✅ Dev       : Quels cas limites ? (mot vide, casse, caractères spéciaux)
✅ QA        : Quels scénarios vais-je tester pour valider que c'est correct ?
```

1. Créez un dépôt GitHub `dojo-bibliotheque` avec une branche `main` et une branche `develop`

```bash
# Initialiser le dépôt et créer la branche develop
git init
git add .
git commit -m "feat: initialiser le projet dojo-bibliothèque"
git branch develop
git push -u origin main
git push origin develop
```

2. Pour chaque fonctionnalité, ouvrez une branche depuis `develop` :

```bash
# Démarrer depuis develop
git checkout develop
git pull

# Créer la feature branch
git checkout -b feature/modeles-entites
git commit -m "feat: ajouter les modèles Livre, Adherent et Emprunt"
git push -u origin feature/modeles-entites
# → Ouvrir une Pull Request : feature/modeles-entites → develop (sur GitHub)
```

3. Après validation de chaque PR, créez la branche suivante depuis `develop` mis à jour :

```bash
git checkout develop
git pull
git checkout -b feature/service-emprunts
git commit -m "feat: implémenter BibliothequeService avec gestion des emprunts"
git commit -m "test: ajouter les scénarios de test dans Main"
git push -u origin feature/service-emprunts
# → Ouvrir une Pull Request : feature/service-emprunts → develop
```

4. Créez une dernière branche `feature/recherche-avancee` depuis `develop` et implémentez `rechercherParTitre(String motCle)` avec une recherche insensible à la casse. PR → `develop`.

> **`main` reste vide de commits directs.** Tout passe par `develop` via des Pull Requests. C'est exactement le workflow que vous utiliserez en entreprise.

---

## ✅ Critères de validation

Fermez ce document. Expliquez chaque point à voix haute ou par écrit, sans aide :

**Java**
- [ ] **La différence entre une classe et un objet** — Pouvez-vous donner une analogie différente de celle du cours ?
- [ ] **L'encapsulation** — Pourquoi met-on les attributs en `private` ? Que se passe-t-il si on les laisse `public` ?
- [ ] **L'interface vs la classe abstraite** — Quand choisit-on l'un plutôt que l'autre ?
- [ ] **ArrayList vs HashMap** — Dans quel cas utilise-t-on chacun ? Quelle est la complexité de `get()` sur un `HashMap` ?
- [ ] **Stack vs Heap** — Que se passe-t-il en mémoire quand on appelle `new Livre(...)`  ?
- [ ] **Le Garbage Collector** — Quand un objet est-il éligible à la collecte ?

**Git & Workflow**
- [ ] **Git Flow** — Citez les 5 types de branches du modèle. Depuis quelle branche crée-t-on une `feature` ? Où la fusionne-t-on ?
- [ ] **Pull Request** — Quel est le rôle d'une PR dans Git Flow ? Pourquoi ne commit-on jamais directement sur `main` ?
- [ ] **Conventional Commits** — Quel préfixe utilisez-vous pour un bug fix ? Pour une nouvelle fonctionnalité ?

**Pratiques d'équipe**
- [ ] **Scrum** — Qu'est-ce qu'un Sprint ? Qu'est-ce qu'une User Story ? Écrivez la User Story de la fonctionnalité "emprunter un livre".
- [ ] **3 Amigos** — Quels sont les 3 rôles ? Quelle question chacun apporte-t-il ? Pourquoi faire cet exercice *avant* de coder ?

---

## 🎯 Conseils de recruteur

> Ces points sont testés dans **90% des entretiens Junior Java**. Préparez des réponses claires et courtes.

### Questions fréquentes et ce que le recruteur attend

**"Expliquez-moi la POO en 2 minutes."**
> Le recruteur ne veut pas une définition Wikipedia. Il veut voir si vous *pensez* en objets. Parlez d'un exemple concret de votre Dojo. Mentionnez les 4 piliers sans les réciter, mais en les illustrant.

**"Quelle est la différence entre `==` et `.equals()` en Java ?"**
> `==` compare les **références** (est-ce le même objet en mémoire ?). `.equals()` compare la **valeur** (est-ce que le contenu est identique ?). Piège classique avec les Strings.
```java
String a = new String("hello");
String b = new String("hello");
System.out.println(a == b);      // false — deux objets différents dans le Heap
System.out.println(a.equals(b)); // true  — même contenu
```

**"Quand utilise-t-on un `HashSet` plutôt qu'une `ArrayList` ?"**
> Quand on veut **garantir l'unicité** des éléments et qu'on n'a pas besoin d'ordre. La vérification d'appartenance (`contains`) est en O(1) sur un `HashSet`, contre O(n) sur une `ArrayList`.

**"Qu'est-ce que le Garbage Collector ?"**
> C'est le mécanisme de la JVM qui libère automatiquement la mémoire des objets qui ne sont plus référencés. Il évite les fuites mémoire manuelles. Le risque : garder des références inutiles qui empêchent le GC de travailler.

**"Montrez-moi votre GitHub."**
> C'est pourquoi vous commitez dès le premier jour. Un GitHub avec des commits réguliers, des messages clairs, et des branches vaut plus qu'un long discours. Le recruteur regarde la régularité, pas la perfection.

### Ce qui fait la différence entre deux candidats au même niveau

Un candidat qui dit : *"J'ai fait un projet de gestion de bibliothèque, j'ai utilisé une `HashMap` pour indexer les livres par ISBN parce que la recherche par clé est en O(1) et que l'ISBN est unique"* — ce candidat passe.

Un candidat qui dit : *"J'ai utilisé des listes et des maps"* — ce candidat ne passe pas.

**La précision technique dans la justification des choix, c'est ce qui distingue le Junior du stagiaire.**

---

## 🔗 Ressources complémentaires

| Ressource | Type | Pourquoi |
| :--- | :--- | :--- |
| [Java 21 Official Docs](https://docs.oracle.com/en/java/javase/21/) | Documentation | La référence |
| [Baeldung — Java Collections](https://www.baeldung.com/java-collections) | Tutoriel | Exemples pratiques sur les collections |
| [Conventional Commits](https://www.conventionalcommits.org/fr/) | Standard | Convention de messages de commit |
| [Learn Git Branching](https://learngitbranching.js.org/?locale=fr_FR) | Interactif | Apprendre Git visuellement |
| [Java Visualizer](https://cscircles.cemc.uwaterloo.ca/java_visualize/) | Outil | Visualiser la Stack et le Heap pas à pas |

---

**[⬅ Retour au README](../README.md)** · **[Semaine 2 — Backend Spring Boot ➔](./semaine-2.md)**
 