# 📖 Module 1 : Fondations Java Moderne (17/21), Architecture Mémoire & POO

## 🎯 Objectif Pédagogique
> **Démonter la boîte noire.** À la fin de ce premier module, vous saurez exactement ce qui se passe dans la mémoire de votre ordinateur (Stack & Heap) lorsque vous manipulez des données, et comment utiliser Java 17/21 pour écrire un code clair, sécurisé et sans lignes inutiles.

---

## 🏢 L'Analogie Principale : L'Écosystème de l'Entreprise

Pour comprendre la Programmation Orientée Objet (POO) simplement, projetons le code dans un univers concret : **Le fonctionnement d'une entreprise.**

`[ Fiche de Poste (Classe) ] ──(Recrutement: new)──► [ Employé Réel (Objet en Heap) ]`

### 1. La Classe vs L'Objet
* **La Classe (`La Fiche de Poste`) :** C'est un plan écrit. Il définit les informations d'un "Développeur" (Nom, Compétence) et ses actions (méthode `coder()`). La fiche de poste ne produit rien seule.
* **L'Objet (`L'Employé en chair et en os`) :** C'est l'action concrète. Quand vous écrivez `Developpeur dev = new Developpeur("Emeric");`, vous recrutez une personne réelle. Elle consomme de l'espace physique (la mémoire), possède ses propres valeurs et s'exécute.

### 2. Les Records (Le Formulaire Officiel Scellé) - *Java 16+*
Souvent, on manipule des reçus ou des fiches d'information qui ne doivent jamais être modifiés après leur création. Avant, cela demandait beaucoup de code (getters, equals, hashCode). 
Aujourd'hui, on utilise un **Record**. C'est un document scellé, nativement immuable :

```java
public record CarteMembre(String identifiant, LocalDate dateAdhesion) {}
```

> **Le Grand Avantage :** Votre code reste propre et va à l'essentiel. Le Record garantit l'intégrité de vos données sans effort supplémentaire.

### 3. L'Encapsulation (La Protection des Données)
Les informations internes d'un service sont verrouillées (`private`). Si l'extérieur veut interagir, il doit utiliser les guichets officiels (les méthodes publiques). Cela évite qu'une erreur venant d'ailleurs ne corrompe la logique de votre application.

### 4. Les Sealed Classes (La Hiérarchie Verrouillée) - *Java 17*
Dans notre entreprise, seuls certains postes spécifiques ont le droit d'effectuer des opérations sensibles. On empêche la création de sous-catégories non contrôlées avec le mot-clé `sealed` :

```java
public sealed class PosteVendeur permits VendeurBoutique, VendeurEnLigne {}
```

> **À retenir :** Vous maîtrisez entièrement la structure de votre code. Personne ne peut étendre votre classe sans une autorisation explicite (`permits`).

---

## ⚡ Sous le Capot : Stack vs Heap (La Gestion de la Mémoire)

Java gère le stockage à travers deux zones distinctes. Comprendre cela permet d'éviter les bugs les plus fréquents.

```text
 VARIABLES LOCALES (Stack)             OBJETS RÉELS (Heap)
┌─────────────────────────┐           ┌──────────────────────────────┐
│ int age = 26;           │           │                              │
│                         │           │  ┌────────────────────────┐  │
│ Usager u1 ──────────────┼───────────┼─►│ Usager Object (Record) │  │
│                         │           │  │ nom: "Emeric"          │  │
│ Usager u2 ──────────────┼───────────┘  └────────────────────────┘  │
└─────────────────────────┘           └──────────────────────────────┘
```

### 🗂️ La Stack (La Pile d'Exécution)
* **Nature :** Une structure ultra-rapide mais de petite taille.
* **Ce qu'elle stocke :** Les variables simples (`int`, `double`, `boolean`) et les **adresses** (références) qui pointent vers la Heap.
* **Comportement :** Dès qu'une méthode se termine, son espace dans la Stack est instantanément nettoyé.

### 🏜️ La Heap (Le Tas de Stockage)
* **Nature :** Un immense espace partagé par toute l'application.
* **Ce qu'elle stocke :** Tous les objets créés avec `new`, les instances de `Record`, et les textes (`String`).
* **Comportement :** Les objets y restent tant qu'ils sont utilisés. Lorsqu'un objet est abandonné, le **Garbage Collector** (le nettoyeur de Java) vient le détruire pour libérer de la place.

### 🚨 Références vs Valeurs : Le Piège à Éviter

```java
// Primitifs : Copie de la valeur dans la Stack
int x = 10;
int y = x;
y = 20; // x vaut toujours 10. Ils sont indépendants.

// Objets : Copie de la RÉFÉRENCE (l'adresse) dans la Stack
Usager u1 = new Usager("Emeric");
Usager u2 = u1; 
u2.setNom("Thomas");
System.out.println(u1.getNom()); // 😱 Affiche "Thomas" !
```

> **L'explication visuelle :** `u1` et `u2` sont deux télécommandes distinctes situées dans la Stack, mais elles contrôlent la **même télévision** dans la Heap. Changer de chaîne avec `u2` modifie aussi l'écran pour `u1`.

---

## 🔧 Les Pièges Courants et Leurs Solutions

| Le Bug / Symptôme | La Cause Réelle | La Solution Java 17/21 |
| :--- | :--- | :--- |
| `NullPointerException` | Une variable pointe vers le vide (`null`) et tente d'exécuter une action. | Utiliser `Optional<T>` ou le Pattern Matching pour valider proprement les données. |
| Lenteurs sur les textes | Les `String` sont immuables. Concaténer dans une grande boucle crée d'innombrables objets qui saturent la Heap. | Utiliser les **Text Blocks** `"""` pour les textes fixes et **StringBuilder** pour les textes générés en boucle. |
| `==` qui échoue sur des textes identiques | `==` compare uniquement les adresses dans la Stack, pas le contenu réel du texte. | Toujours utiliser `.equals()`. |

---

## 🥋 Dojo de Code : Le Système de Bibliothèque Moderne

> **Consigne de Pratique :** Tapez chaque ligne vous-même. C'est l'action physique de coder qui transforme la syntaxe en un véritable automatisme.

### 🏗️ Phase 1 : Architecture Robuste
1. Créez un **Record** nommé `Usager` comprenant un identifiant, un nom et un email.
2. Créez une classe abstraite et `sealed` nommée `Document`, autorisant (`permits`) uniquement `Livre`, `Audiobook`, et `Magazine`.
3. Dans votre classe `Bibliotheque`, assurez-vous que les listes renvoyées utilisent `Collections.unmodifiableList()` pour bloquer toute modification externe.

### 🧬 Phase 2 : Pattern Matching (Java 21)
Dans votre calcul d'amendes, utilisez le **Pattern Matching pour switch** (Java 21). Plus besoin de tester le type avec des `if` complexes :

```java
public double calculerAmendeModerne(Document doc, int joursRetard) {
    return switch (doc) {
        case Livre l -> joursRetard * 0.5;
        case Audiobook a -> joursRetard * 1.0;
        case Magazine m -> joursRetard * 1.5;
    }; 
}
```

### 🌊 Phase 3 : Sequenced Collections & Flux
Exploitez l'API Streams et les **Sequenced Collections** de Java 21 pour manipuler vos listes :
* Utilisez `.stream().filter(...).toList()` pour trier vos données lisiblement.
* Utilisez `catalogue.getFirst()` ou `catalogue.getLast()` pour accéder directement aux extrémités de vos listes sans calculer d'index.

---

## 🏁 Auto-Évaluation (Le Diagnostic Rapide)

Prenez un instant pour valider ces acquis fondamentaux :

1. *Pourquoi un Record est-il particulièrement fiable pour représenter de la donnée pure ?*
2. *Grâce au mot-clé `sealed`, pourquoi un bloc `switch` moderne n'a plus besoin d'une clause `default` si toutes les sous-classes permises sont listées ?*
3. *Quelle est la grande différence de durée de vie entre une variable stockée dans la Stack et un objet placé dans la Heap ?*

---

**[Retour à l'Accueil du README](../README.md) | [Passer au Module 2 : Data Streams & Pattern Matching →](./semaine-2.md)**