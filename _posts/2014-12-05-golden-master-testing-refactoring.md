---
layout: post
title: Refactoring avec 1,22% de code couvert par les tests ... Golden Master testing à la rescousse !
date: 2014-12-05
categories:
- Code
- Testing
tags:
- guava
- assertj
- refactoring
- golden master testing
type: post
published: true
comments: true
---

\+ de 10000 lignes de code à refactorer, très peu de tests unitaires, seulement 1,22% de code couvert par les tests. Voilà le petit challenge auquel j'ai été récemment confronté :-)

![coverage]({{ site.url }}/assets/2014-12-05/coverage.png)

Pour autant, pas de panique ! C'est faisable, et encore mieux, c'est faisable ***sereinement*** !

Voici comment je suis venu à bout de ce refactoring.

## Contexte

Le code à refactorer est le code d'un batch écrit en Java, dont l'objectif est de lire des données dans une base PostgreSQL, de les transformer et de les écrire dans des fichiers JSON.

![batch]({{ site.url }}/assets/2014-12-05/batch.jpg)

La lecture depuis une base de données et l'écriture de fichiers sur le filesystem rendent le code difficilement testable unitairement.

Comment faire pour se lancer sereinement dans le refactoring ?

* Mettre en place des TU en utilisant des Mocks ? C'est beaucoup de travail, pour une exécution du code finalement assez éloignée de la réalité.
* Utiliser une base embarquée type HSQLDB ? Impossible, des fonctions avancées de PostgreSQL sont utilisées (colonne de type JSON par exemple).
* Sans tests car "tester c'est douter" ? Mauvaise idée ... ;-)

## Golden Master testing

Une solution consiste à utiliser la technique du **Golden Master Testing**.

Voici une définition que l'on peut donner à ce type de test :

> L'idée générale est de déterminer le comportement du programme, en l'exécutant sur un large jeu de données entrantes, cela avant de commencer le refactoring du code.
> <br>
> Les résultats de cette exécution préalable constituent le **Golden Master**.
> <br>
> Ensuite, lorsque le code est modifié, le programme est réexécuté sur le même jeu de données entrantes et les résultats sont comparés au Golden Master.
> <br>
> Les différences constatées permettent de détecter les modifications du comportent du programme induites par le refactoring.

_Remarque : l'objectif est que le programme refactoré ait le même comportement que le programme original, bugs inclus ! La correction des bugs en même temps que le refactoring risque de poser des problèmes. Il est préférable de les corriger dans un second temps._

## Cool, on a des recettes en cours !

Très bien, mais que peut-on utiliser comme "large jeu de données entrantes" ?

Il s'avère qu'au même moment des recettes étaient en cours sur le logiciel utilisant la base de données.
Plusieurs utilisateurs exécutaient des scénarios de tests depuis plusieurs jours.

Parfait ! Je vais me servir d'un dump de cette base de données de recette comme jeu de données entrantes.

![testeurs]({{ site.url }}/assets/2014-12-05/testeurs.jpg)

## Création du test

J'ai donc construit le test de la manière suivante :

1. Récupération d'un dump de la base de données de recette.
2. Exécution du programme à partir de ce dump.
3. Le Golden Master est constitué des fichiers JSON générés à partir de ce dump (17 682 fichiers).
4. Le code du programme est dupliqué (pour pouvoir relancer le programme original sur un autre dump si besoin).
5. Le code refactoré est exécuté continuellement sur le dump de la base de recette, et les fichiers JSON générés sont comparés au Golden Master

![golden-master]({{ site.url }}/assets/2014-12-05/golden-master.jpg)

## Assertions

Les fichiers JSON sont générés dans une arborescence de dossier précise, et possèdent un nom et un contenu précis.

Pour comparer simplement les fichiers JSON produits par les tests, j'ai utilisé des fonctionnalités des librairies [Guava](https://github.com/google/guava) et [AssertJ](http://joel-costigliola.github.io/assertj/)

Pour parcourir les arboresences de fichiers, Guava propose la notion de [TreeTraverser](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/collect/TreeTraverser.html).

AssertJ permet en une ligne de comparer le contenu de deux fichiers.

La comparaison est donc assez simple :

{% highlight java %}
String tmpDir = StandardSystemProperty.JAVA_IO_TMPDIR.value();
String masterOutputPath = tmpDir + "/extraction-master";
String refactoredOutputPath = tmpDir + "/extraction-refactored";

// Exécution du test
...

// Vérifications
for (File masterFile : Files.fileTreeTraverser().preOrderTraversal(masterOutput)) {
    File refactoredFile = new File(
            masterFile.getPath().replace(
                    masterOutputPath,
                    refactoredOutputPath
            )
    );
    assertThat(refactoredFile).exists();

    if (masterFile.isFile()) {
        assertThat(refactoredFile).hasContentEqualTo(masterFile);
    }
}
{% endhighlight %}

## Conclusion

Au final l'approche est assez simple et se met en place très rapidement !

Le test Golden Master couvre plus de 85% du code, le refactoring a donc pu se faire plus sereinement.

Les outils de couverture de code ont permis d'identifier précisément les scénarios non testés. Des tests manuels ont pu être menés pour vérifier la non régression sur ces scénarios précis.

Inspiration : je me suis inspiré de la technique utilisé par [David Gageot](https://twitter.com/dgageot) lors de sa présentation [du legacy au cloud](https://www.youtube.com/watch?v=q11gydDAMSo).

