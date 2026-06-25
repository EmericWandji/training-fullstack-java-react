# 📖 Semaine 1 : Fondations Java & Architecture Mentale

## 🎯 Objectif Neuro-Pédagogique
> **Démonter la boîte noire.** À la fin de ces 5 jours, vous ne subirez plus la syntaxe de Java. Vous comprendrez exactement comment les objets interagissent, comment la mémoire se fragmente et comment structurer votre pensée pour concevoir des systèmes professionnels.

---

## 🏢 La Métaphore Ancres : L'Écosystème de l'Entreprise

Pour éviter le piège de l'apprentissage par cœur, associons chaque concept technique abstrait à une structure physique que votre cerveau connaît déjà : **Une Entreprise**.


### 1. La Classe vs L'Objet
* **La Classe (`Fiche de Poste`) :** C'est un document papier. Il définit le profil d'un "Vendeur" (son nom, son salaire, et le fait qu'il sait "signer un contrat"). La fiche de poste ne produit rien d'elle-même, c'est un modèle abstrait.
* **L'Objet (`L'Employé en chair et en os`) :** C'est l'action d'instancier. Lorsque vous recrutez Thomas avec le mot-clé `new Vendeur()`, vous créez une entité réelle en mémoire qui occupe un bureau, possède son propre nom et signe des contrats en suivant la fiche de poste.

### 2. L'Encapsulation (Le Bureau Privé)
Dans une entreprise saine, un manager ne va pas fouiller directement dans le portefeuille d'un commercial pour y prendre de l'argent. Le portefeuille est `private`. Pour obtenir une information, le manager appelle une méthode publique : `commercial.getChiffreAffaires()`. 
> **Règle d'or :** L'état interne d'un objet doit toujours être protégé des agressions extérieures.

### 3. L'Héritage & Le Polymorphisme (L'Évolution des Postes)
Vous créez un poste de "Vendeur International". Au lieu de réécrire toute sa fiche de poste, vous utilisez `extends Vendeur`. Il hérite automatiquement des compétences de base. 

Cependant, sa méthode `signerContrat()` est différente (elle gère les devises). En tant que Directeur (`Main`), vous pouvez avoir une liste d'employés mélangés (`List<Vendeur>`) et crier : *"Signez vos contrats !"*. Chaque employé s'exécutera selon sa propre implémentation (standard ou internationale). **C'est le polymorphisme.**

---

## 🧠 Carte Mentale des Notions Techniques

### Jours 1 & 2 : L'Architecture Orientée Objet
* **Les Fondations :** Instanciation (`new`), l'autoréférence (`this`), et la mécanique des constructeurs.
* **Les Niveaux d'Isolation :** `public`, `private`, `protected`, `package-private`. Choisir le plus restrictif par défaut.
* **L'Immuabilité protectrice :** Utilisation de `final` pour graver des données dans le marbre et empêcher les effets de bord.
* **Contrats d'Interfaces vs Classes Abstraites :** * *Interface* = Un contrat de service externe ("Cet objet sait *Imprimer*").
    * *Classe Abstraite* = Une identité génétique partielle ("Cet objet *Est un* Animal").

### Jour 3 : Les Structures de Données & Flux (Le Cerveau Algorithmique)
Pour bien choisir votre conteneur de données, vous devez comprendre sa mécanique d'accès :

| Structure | Accès Mémoire (Lecture) | Doublons | Cas d'Usage Idéal |
| :--- | :--- | :--- | :--- |
| **`ArrayList`** | ⚡ Très rapide ($O(1)$ par index) | ✅ Autorisés | Liste de lecture, indexation fréquente |
| **`LinkedList`** | 🐢 Lent (parcours séquentiel) | ✅ Autorisés | Insertions/Suppressions massives au milieu |
| **`HashSet`** | ⚡ Très rapide ($O(1)$ via Hash) | ❌ Interdits | Vérifier l'unicité (ex: Liste d'identifiants) |
| **`HashMap`** | ⚡ Éclair ($O(1)$ Clé -> Valeur) | Clés uniques | Dictionnaire, index de recherche rapide |

* **L'API Streams (La Ligne d'Assemblage) :** Visualisez les Streams comme un tapis roulant d'usine. Les données défilent, subissent des filtres (`filter`), des transformations (`map`), puis sont emballées à la fin (`collect`).
* **La Ceinture de Sécurité :** `Optional<T>` pour éradiquer définitivement les `NullPointerException`.

### Jour 4 : La Gestion de la Mémoire (Sous le Capot)


* **La `Stack` (Pile d'Exécution) :** Hyper rapide, de taille réduite. Elle stocke les variables locales et les adresses (références). Elle fonctionne en LIFO (Last In, First Out) et se nettoie instantanément dès qu'une méthode se termine.
* **La `Heap` (Tas de Stockage) :** Grand espace partagé. C'est ici que vivent tous les objets créés avec `new`. 
* **Le `Garbage Collector` :** Le concierge invisible de votre application. Si un objet en Heap n'est plus relié à aucune référence dans la Stack, il devient une cible et sa mémoire est libérée automatiquement.

### Jour 5 : La Maîtrise de Git (La Machine à Remonter le Temps)
* **Le Flux Tripartite :** Working Directory (votre espace de brouillon) ➔ Staging Area (le sas de préparation : `git add`) ➔ Local Repository (l'historique immuable : `git commit`).
* **L'Art du Commit Chirurgical :** Un bon message utilise un verbe d'action à l'impératif : `Add user profile validation`, jamais `Fix bugs` ou `WIP`.

---

## 🥋 Dojo de Code Code-Actif : Le Système de Gestion de Bibliothèque

> **Consigne Neuro-Pédagogique :** Ne copiez-collez rien. Tapez chaque caractère. L'action physique d'écrire du code renforce la mémoire procédurale.

### 🧱 Phase 1 : Encapsulation stricte
Créez les classes `Livre`, `Usager`, et `Bibliotheque`.
* **Défi Cognitif :** Vos getters retournant des collections ne doivent jamais donner un accès direct à la structure interne. Utilisez impérativement `Collections.unmodifiableList(votreListe)` pour forcer l'étanchéité de vos objets.

### 🧬 Phase 2 : Polymorphisme Appliqué
Remplacez la classe `Livre` par une classe abstraite `Document` et créez trois spécialisations : `Livre`, `Audiobook`, `Magazine`.
* Chaque sous-classe doit implémenter sa propre formule de calcul pour la méthode `calculerAmende(int joursRetard)`.
* Dans votre classe de test, créez une `List<Document>` contenant un mélange des trois types et parcourez-la en observant comment Java sélectionne dynamiquement le bon calcul d'amende.

### 🌊 Phase 3 : Le Tapis Roulant (Streams)
Dans la classe `Bibliotheque`, interdisez-vous l'utilisation des boucles `for` traditionnelles. Écrivez les algorithmes suivants exclusivement avec l'API Streams :
* `rechercherParAuteur(String auteur)` (avec tolérance à la casse)
* `statistiquesParGenre()` (Indice : Utilisez `Collectors.groupingBy(Document::getGenre, Collectors.counting())`)
* `getTop3PlusEmpruntes()` (Trier et limiter le flux)

### 🚨 Phase 4 : Gestion Immersive des Crises (Exceptions)
Définissez vos propres exceptions métiers héritant de `RuntimeException` (ex: `DocumentIndisponibleException`, `LimiteEmpruntDepasseeException`). 
* Injectez de la logique de contrôle dans vos méthodes métiers. Le système doit lever une exception claire si un usager tente d'emprunter un 6ème document en simultané.

---

## 🏁 Auto-Évaluation Subliminale (Validez vos connexions)

Si vous êtes capable de répondre sereinement à ces trois questions, votre ancrage est réussi :
1.  *Si je modifie une collection renvoyée par un getter non protégé, qu'arrive-t-il à l'objet propriétaire ?*
2.  *Pourquoi la concaténation de `String` dans une boucle `for` classique ($1000$ itérations) met-elle le Garbage Collector en surrégime ? (Pensez à l'immuabilité).*
3.  *Quelle est la différence fondamentale entre la structure d'une `ArrayList` et celle d'une `LinkedList` en termes de disposition dans la Heap ?*

---

## 🔧 Les Pièges À Éviter