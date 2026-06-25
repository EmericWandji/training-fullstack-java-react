# 📖 Semaine 1 : Fondations Java

## 🎯 Objectif Pédagogique

**Maîtriser les concepts fondamentaux de la POO et savoir les appliquer pour écrire du code Java maintenable — sans ces bases, tout framework que vous toucherez restera une boîte noire.**

À la fin de cette semaine, vous aurez construit les fondations sur lesquelles reposent tous les frameworks professionnels que vous utiliserez ensuite.

---

## 🏢 Comprendre la POO : L'Analogie de l'Entreprise

Imaginez que vous créez une entreprise de zéro.

Vous définissez des **postes de travail** — Vendeur, Magasinier, Manager. Chaque poste a des responsabilités claires et une fiche de poste rédigée avant même d'embaucher quelqu'un. Ces fiches, ce sont vos **classes** : des modèles abstraits qui décrivent ce qu'un employé de ce type sait faire.

Ensuite, vous **embauchez des employés réels** pour occuper ces postes. Marie est vendeuse, Paul est manager. Chaque personne réelle est un **objet** — une instance concrète d'une classe. Vous pouvez avoir dix vendeurs (dix objets `Vendeur`) qui partagent tous la même fiche de poste.

Chaque employé a des **caractéristiques** : un nom, un salaire, un département. Ce sont les **propriétés** (ou champs). Chaque employé **effectue des actions** : vendre, inventorier, valider une commande. Ce sont les **méthodes**.

Quand vous créez un poste de "Vendeur Senior", il hérite de toutes les responsabilités du Vendeur de base, en y ajoutant les siennes. C'est **l'héritage**. Et chaque employé travaille dans un bureau fermé : vous ne fouillez pas directement dans ses dossiers — vous passez par une procédure officielle. C'est **l'encapsulation**.

Cette organisation permet à l'entreprise de grandir sans devenir incontrôlable. Exactement comme du bon code.

---

## 📚 Notions Techniques Incontournables

### Jour 1-2 : POO et Fondamentaux

- [ ] **Classes et Objets** : définition, instanciation avec `new`, constructeurs (avec et sans paramètres), le mot-clé `this`
- [ ] **Propriétés et Méthodes** : modificateurs d'accès `public`, `private`, `protected`, `package-private` — et pourquoi chaque niveau existe
- [ ] **Encapsulation** : pourquoi les champs sont `private`, rôle des getters/setters, immuabilité avec `final`
- [ ] **Héritage** : mot-clé `extends`, appel au constructeur parent avec `super()`, redéfinition de méthodes avec `@Override`
- [ ] **Polymorphisme** : une même référence de type parent qui pointe vers un objet enfant, appel dynamique de méthodes à l'exécution
- [ ] **Interfaces** : définir un contrat avec `interface` et `implements`, méthodes `default`, différence avec les classes abstraites
- [ ] **Classes abstraites** : quand une classe ne doit pas être instanciée directement, méthodes abstraites obligatoires pour les sous-classes
- [ ] **Les 4 piliers** : encapsulation, abstraction, héritage, polymorphisme — savoir les définir, les distinguer et les illustrer avec votre propre code

### Jour 3 : Collections et Structures de Données

- [ ] **List** : séquence ordonnée, autorise les doublons. `ArrayList` pour l'accès rapide par index O(1), `LinkedList` pour les insertions/suppressions en milieu de liste O(1)
- [ ] **Set** : interdit les doublons. `HashSet` (pas d'ordre garanti), `TreeSet` (éléments triés), `LinkedHashSet` (ordre d'insertion)
- [ ] **Map** : association clé → valeur. `HashMap` (accès O(1)), `TreeMap` (clés triées), `LinkedHashMap` (ordre d'insertion)
- [ ] **Complexités algorithmiques** : O(1) vs O(n) vs O(log n) — savoir les expliquer concrètement, pas juste les citer
- [ ] **Itération** : boucle `for`, `for-each`, iterateurs explicites, `forEach()` avec lambda
- [ ] **Streams API (Java 8+)** : `filter()`, `map()`, `reduce()`, `collect()`, `sorted()`, `distinct()`, `findFirst()`, `anyMatch()`, `Collectors.groupingBy()`
- [ ] **Comparable vs Comparator** : implémenter `compareTo()` pour le tri naturel d'une classe, `Comparator.comparing()` pour un tri contextuel externe
- [ ] **Optional** : éviter les `NullPointerException` avec `Optional.of()`, `Optional.ofNullable()`, `orElse()`, `orElseThrow()`, `ifPresent()`

### Jour 4 : Gestion de la Mémoire

- [ ] **Stack** : contient les variables locales et les références. Libérée automatiquement à la fin de l'appel de méthode (LIFO). Rapide mais limitée en taille.
- [ ] **Heap** : contient tous les objets créés avec `new`. Partagée entre tous les threads, gérée par le Garbage Collector. Plus grande mais plus lente.
- [ ] **Références vs valeurs** : en Java, les primitifs (`int`, `boolean`, `double`...) vivent en Stack directement. Les objets vivent en Heap et on manipule des **références** (adresses mémoire) vers eux.
- [ ] **Garbage Collection** : le GC détecte automatiquement les objets sans référence active et libère leur mémoire. Vous ne libérez rien manuellement en Java.
- [ ] **NullPointerException** : causes les plus fréquentes (référence non initialisée, méthode retournant null non vérifiée), prévention avec `Optional` ou validations explicites
- [ ] **Immuabilité** : pourquoi `String` est immuable en Java, ce que ça implique (`"a" + "b"` crée un nouvel objet), `StringBuilder` pour les concaténations en boucle
- [ ] **Memory leaks en Java** : cas classiques — collections statiques qui grossissent sans être nettoyées, listeners non retirés, connexions non fermées dans un `finally`

### Jour 5 : Git et Contrôle de Version

- [ ] **Les trois zones** : working directory (vos fichiers sur disque), staging area (ce que vous préparez à committer avec `git add`), repository local (l'historique des commits)
- [ ] **Commandes essentielles** : `git init`, `git add`, `git commit -m`, `git push`, `git pull`, `git clone`, `git status`, `git log --oneline`, `git diff`
- [ ] **Branches** : `git checkout -b feature/ma-feature` pour créer, `git checkout main` pour changer, `git merge feature/ma-feature` pour fusionner, `git branch -d` pour supprimer
- [ ] **Workflow professionnel** : une branche par fonctionnalité (feature branches), pull request pour la revue de code, merge dans `main` après validation
- [ ] **Résolution de conflits** : comprendre les marqueurs `<<<<<<<`, `=======`, `>>>>>>>` dans un fichier en conflit, résoudre manuellement puis `git add` et `git commit`
- [ ] **Messages de commit** : format impératif court ("Add user authentication", "Fix null pointer in BookService"), pas "corrections" ni "wip" ni "save"
- [ ] **.gitignore** : ce qu'on n'inclut jamais dans un dépôt — `target/`, `node_modules/`, `.env`, `.idea/`, `*.class`

---

## 🥋 Dojo de Code : Système de Gestion de Bibliothèque

### Contexte

Vous construisez un système pour gérer une bibliothèque municipale. Les utilisateurs empruntent des documents, les retournent, et paient des amendes en cas de retard. Ce projet doit être construit **de zéro, en pur Java, sans aucun framework**.

C'est volontairement simple dans le domaine métier, mais ambitieux dans la rigueur technique attendue.

---

### Phase 1 : POO Basique (Jour 1-2)

```java
// TODO: Créez ces trois classes avec une POO correcte

// 1. Classe Livre
//    Propriétés: titre (String), auteur (String), isbn (String),
//                nombrePages (int), disponible (boolean)
//    Méthodes: emprunter(), retourner(), afficherInfo()

// 2. Classe Usager
//    Propriétés: id (Long), nom (String), email (String),
//                livresEmpruntes (Collection<Livre>)
//    Méthodes: emprunterLivre(Livre), retournerLivre(Livre), consulterHistorique()

// 3. Classe Bibliotheque
//    Propriétés: nom (String), adresse (String), catalogueLivres (Collection<Livre>)
//    Méthodes: ajouterLivre(Livre), retirerLivre(String isbn),
//              rechercherParTitre(String), afficherCatalogue()

// CONTRAINTES OBLIGATOIRES :
// - Tous les champs sont private
// - Les getters qui exposent des Collections retournent une COPIE non modifiable
//   → utilisez Collections.unmodifiableList(maListe)
// - Les constructeurs initialisent tous les champs obligatoires
// - Les champs immuables (id, isbn) n'ont PAS de setter
// - Pas de logique métier dans les getters
```

---

### Phase 2 : Héritage et Polymorphisme (Jour 2-3)

```java
// TODO: Refactorisez avec une hiérarchie d'héritage

// 1. Classe abstraite Document (Livre devient un Document)
//    Propriétés communes: titre, auteur, anneePublication, nombreEmprunts
//    Méthodes abstraites — chaque sous-type l'implémente différemment :
//      - int getDureeEmpruntJours()
//      - double calculerAmende(int joursRetard)

// 2. Livre hérite de Document
//    Ajoute: nombrePages (int), genre (String)
//    getDureeEmpruntJours() → retourne 21
//    calculerAmende(int jours) → retourne jours * 0.20

// 3. Audiobook hérite de Document
//    Ajoute: dureeEnHeures (double), narrateur (String), format (String: "MP3", "WAV"...)
//    getDureeEmpruntJours() → retourne 14
//    calculerAmende(int jours) → retourne jours * 0.30

// 4. Magazine hérite de Document
//    Ajoute: numero (int), periodicite (String: "Hebdomadaire", "Mensuel")
//    getDureeEmpruntJours() → retourne 7
//    calculerAmende(int jours) → retourne jours * 0.10

// Démonstration du polymorphisme attendue dans Main :
List<Document> docs = new ArrayList<>();
docs.add(new Livre("Clean Code", "Robert Martin", 2008, 431, "Informatique"));
docs.add(new Audiobook("Atomic Habits", "James Clear", 2018, 5.8, "James Clear", "MP3"));
docs.add(new Magazine("Le Monde Informatique", "Divers", 2024, 1547, "Hebdomadaire"));

// La même boucle, trois comportements différents — c'est le polymorphisme en action
for (Document doc : docs) {
    System.out.println(doc.getTitre() + " : " + doc.getDureeEmpruntJours() + " jours d'emprunt");
}
```

---

### Phase 3 : Collections et Streams (Jour 3-4)

```java
// TODO: Ajoutez ces méthodes à Bibliotheque
// OBLIGATION : utilisez les Streams API dans chaque méthode — aucune boucle for autorisée ici

// 1. Recherche par auteur (insensible à la casse)
List<Document> rechercherParAuteur(String auteur)
// Exemple : rechercherParAuteur("martin") trouve "Robert Martin"

// 2. Documents actuellement empruntés par un usager donné
List<Document> getDocumentsEmpruntes(Usager usager)

// 3. Amende totale d'un usager (somme de toutes ses amendes en cours)
double calculerAmendeTotale(Usager usager)
// Calculez d'abord les jours de retard avec ChronoUnit.DAYS.between(...)

// 4. Nombre de documents disponibles par genre
Map<String, Long> statistiquesParGenre()
// Hint : Collectors.groupingBy() + Collectors.counting()

// 5. Top 3 des documents les plus empruntés (par nombreEmprunts, décroissant)
List<Document> getTop3PlusEmpruntes()
// Hint : .sorted(Comparator.comparingInt(Document::getNombreEmprunts).reversed()).limit(3)

// Exemple de structure attendue pour rechercherParAuteur :
public List<Document> rechercherParAuteur(String auteur) {
    return catalogueDocuments.stream()
        .filter(doc -> doc.getAuteur().toLowerCase().contains(auteur.toLowerCase()))
        .collect(Collectors.toList());
}
```

---

### Phase 4 : Validations et Edge Cases (Jour 4-5)

```java
// TODO: Rendez le système robuste face aux cas limites

// 1. Dates avec java.time — ne jamais utiliser java.util.Date
//    dateEmprunt : LocalDate.now()
//    dateRetourPrevue : LocalDate.now().plusDays(doc.getDureeEmpruntJours())
//    Jours de retard : ChronoUnit.DAYS.between(dateRetourPrevue, LocalDate.now())

// 2. Exceptions personnalisées pour les règles métier
public class DocumentIndisponibleException extends RuntimeException {
    public DocumentIndisponibleException(String titre) {
        super("Le document '" + titre + "' est déjà emprunté et non disponible.");
    }
}

public class DocumentNonEmprunteException extends RuntimeException {
    public DocumentNonEmprunteException(String titre) {
        super("Le document '" + titre + "' ne figure pas dans vos emprunts en cours.");
    }
}

public class LimiteEmpruntDepasseeException extends RuntimeException {
    public LimiteEmpruntDepasseeException(int limite) {
        super("Vous avez atteint la limite de " + limite + " emprunts simultanés.");
    }
}

// 3. Règles à enforcer dans les méthodes métier :
//    - emprunterLivre() : si disponible == false → DocumentIndisponibleException
//    - emprunterLivre() : si l'usager a déjà 5 emprunts → LimiteEmpruntDepasseeException
//    - retournerLivre() : si le livre n'est pas dans ses emprunts → DocumentNonEmprunteException

// 4. Scénario de test complet dans Main :
//    - Créer 10 documents variés (5 livres, 3 audiobooks, 2 magazines)
//    - Créer 5 usagers
//    - Simuler 3 emprunts normaux
//    - Simuler 1 emprunt avec date de retour dépassée (setDateRetourPrevue pour tester)
//    - Calculer et afficher les amendes de chaque usager
//    - Afficher les statistiques par genre
//    - Tenter un 6e emprunt → capturer LimiteEmpruntDepasseeException et afficher un message lisible
```

---

### Critères de Validation du Dojo

- [ ] Toutes les classes compilent et s'exécutent sans erreur ni warning
- [ ] Aucun champ n'est `public` ou accessible directement depuis l'extérieur
- [ ] Les getters de collections retournent des copies non modifiables (`unmodifiableList`)
- [ ] L'héritage est utilisé sans duplication de code entre les sous-classes
- [ ] Le polymorphisme fonctionne : une boucle sur `List<Document>` appelle la bonne implémentation de chaque type
- [ ] Les Streams API sont utilisées dans toutes les méthodes de recherche — aucune boucle `for` dans `Bibliotheque`
- [ ] `java.time.LocalDate` utilisé pour toutes les dates (aucun `java.util.Date`)
- [ ] Les exceptions personnalisées sont levées dans les cas limites et capturées dans Main avec des messages utiles
- [ ] La classe Main couvre tous les scénarios définis
- [ ] Le code est versionné sur GitHub avec des commits atomiques et des messages descriptifs

---

## ✅ Critères de Validation : Êtes-vous Prêt pour la Semaine 2 ?

### Expliquer Oralement (sans hésiter)

- [ ] "Quelle est la différence entre une classe et un objet ?" — illustrez avec votre Dojo
- [ ] "Pourquoi utiliser des interfaces plutôt que l'héritage simple dans certains cas ?"
- [ ] "Expliquez le polymorphisme avec un exemple concret tiré de votre code"
- [ ] "Qu'est-ce que l'encapsulation et quel problème concret elle résout ?"
- [ ] "Quand choisissez-vous List, Set ou Map ?" — un cas d'usage pour chacun
- [ ] "Que se passe-t-il en mémoire quand vous faites `new ArrayList<>()` ?"

### Coder Sans Référence

- [ ] Créer une classe avec constructeur, champs privés, getters/setters appropriés
- [ ] Implémenter une interface dans une classe concrète et l'utiliser via la référence d'interface
- [ ] Utiliser `stream().filter().map().collect(Collectors.toList())` sur une liste
- [ ] Gérer une `NullPointerException` de façon préventive et curative
- [ ] Faire un commit, créer une branche, pousser sur GitHub et ouvrir une pull request

### Déboguer Rapidement

- [ ] Comprendre pourquoi une variable est `null` en lisant la stacktrace ligne par ligne
- [ ] Identifier pourquoi modifier une collection retournée par un getter affecte l'état interne de l'objet
- [ ] Lire un message d'erreur de compilation Java et trouver la ligne fautive en moins de 2 minutes

---

## 💼 Ce qui est Testé en Entretien

### Questions Orales Fréquentes

**"Expliquez la POO"**
- ✅ Attendu : une analogie claire, les 4 piliers nommés et illustrés avec un exemple issu de votre projet
- ❌ À éviter : la définition Wikipedia récitée sans exemple concret

**"Différence entre héritage et composition ?"**
- ✅ Attendu : héritage = relation "est un" (un Livre EST un Document), composition = relation "a un" (un Usager A une liste de livres). La composition réduit le couplage et est souvent préférable.
- ❌ À éviter : "Je sais pas, j'utilise surtout l'héritage"

**"Quelle est la complexité de `get()` sur un HashMap ?"**
- ✅ Attendu : O(1) en moyenne grâce au hachage, O(n) dans le pire cas en cas de collisions massives. Explication du principe : la clé est hashée pour trouver le bon "seau".
- ❌ À éviter : silence ou confusion avec ArrayList

### Exercice Typique de Design en Entretien

```
"Modélisez un système de notification.
Les utilisateurs peuvent recevoir des notifications par email, SMS et push.
Montrez comment vous structurez les classes."
```

Réponse attendue : une interface `NotificationSender` avec une méthode `envoyer(Notification n)`, trois implémentations (`EmailSender`, `SmsSender`, `PushSender`), une classe `NotificationService` qui dépend de l'**interface** — pas des implémentations concrètes. Si demain on ajoute WhatsApp, `NotificationService` ne change pas.

### Red Flags qui vous éliminent

🚩 Vous confondez la classe et l'objet ("la classe ArrayList contient mes données")  
🚩 Vous ne savez pas la différence de complexité entre ArrayList et LinkedList  
🚩 Votre historique Git est un seul commit nommé "final final v2"  
🚩 Vous ne savez pas ce que fait `@Override` ou pourquoi l'utiliser

---

## 🔧 Pièges Courants

| Piège | Symptôme | Solution |
|-------|----------|----------|
| **Exposer la collection interne** | Des modifications externes changent l'état de l'objet | Retournez `Collections.unmodifiableList(maListe)` |
| **NullPointerException silencieuse** | Crash sans contexte utile | Validez les paramètres à l'entrée des méthodes publiques |
| **Stream consommé deux fois** | `IllegalStateException` au second parcours | Un Stream est à usage unique — recréez-le si besoin |
| **Oublier `@Override`** | La méthode parente n'est pas redéfinie, aucun avertissement | Toujours annoter — le compilateur vérifie alors la signature |
| **`==` sur des Strings** | Compare les références, pas le contenu | Utilisez `.equals()` ou `.equalsIgnoreCase()` |
| **`java.util.Date`** | API confuse, problèmes de timezone, dépréciée | Toujours `java.time.LocalDate` / `LocalDateTime` depuis Java 8 |

---

## 📚 Ressources

- **Livre** : "Effective Java" — Joshua Bloch (items 1 à 20 cette semaine, incontournable)
- **Docs** : docs.oracle.com/en/java/javase/11/docs/api (consultez chaque classe utilisée)
- **Streams** : "Java 8 Stream Tutorial" sur winterbe.com (le guide le plus clair et concis)
- **Git** : "Pro Git" — Scott Chacon (gratuit sur git-scm.com/book, chapitres 1 à 3)
- **Pratique** : Exercism.io piste Java (exercices courts, corrections commentées par des mentors)

---

## 🎯 Résumé

| Jour | Focus | Livrable attendu |
|------|-------|-----------------|
| 1 | Classes, objets, encapsulation | `Livre`, `Usager`, `Bibliotheque` compilent avec tests manuels |
| 2 | Héritage, polymorphisme, interfaces | `Document` + 3 sous-classes fonctionnelles |
| 3 | Collections, Streams, Optional | Méthodes de recherche et statistiques avec Streams |
| 4 | Mémoire, exceptions, dates java.time | Validations métier et calcul des amendes |
| 5 | Git, révision, documentation | Code poussé sur GitHub, README du projet rédigé |

---

**[Retour au README](../README.md) | [Semaine 2 →](./semaine-2.md)**

*Durée : 5 jours | Effort estimé : 25-30h | Dojo : 12-15h*