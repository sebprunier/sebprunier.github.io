---
layout: post
title: 'Java 7 : les nouveautés du langage'
date: 2011-09-30 12:05:56.000000000 +02:00
categories:
- News
- Veille
tags:
- java
status: publish
type: post
published: true
author:
---

Le 28 juillet 2011, Oracle a officiellement annoncé la sortie de la version 7 de Java Standard Edition !

On n'y croyait plus :-)

C'est l'occasion de regarder ce qu'il y a de nouveau et de vous présenter - quelques exemples à l'appui - les éléments intéressants à retenir. Cet article n'a pas pour objectif de décrire toutes les nouveautés apportées dans le JDK 1.7, mais se concentre simplement sur les évolutions du langage ([Projet Coin](http://openjdk.java.net/projects/coin/) - [JSR 334](http://www.jcp.org/en/jsr/detail?id=334)) :

*   Utilisation des "String" dans les instructions "switch / case"
*   Nombres binaires et utilisation de l'underscore dans l'écriture des nombres
*   "Catch" multiple et propagation d'exceptions avec vérification des sous-types
*   Inférence de types avec l'opérateur "diamond" (<>)
*   Instruction "try-with-resource"
*   Invocation simplifiée des méthodes utilisant les "varargs"

Si vous souhaitez voir l'ensemble des évolutions, consultez [les release notes officielles](http://www.oracle.com/technetwork/java/javase/jdk7-relnotes-418459.html). D'autres articles suivront sûrement sur ce sujet, en particulier les nouveautés de l'API NIO.2, le framework Fork/Join ou encore l'InvokeDynamic. <!--more-->Allez c'est parti ! Voici quelques exemples de code - réalisés avec Intellij IDEA 10.5 et le JDK 1.7.0 -  illustrant les principales nouveautés introduites dans le langage.

## Utilisation des "String" dans les instructions "switch / case"

Qui n'a jamais essayé d'écrire un bloc "switch/case" avec une chaîne de caractères comme clé ? Qui ne s'est jamais dit au moins une fois "Ah zut c'est vrai, pas de String dans les switch/case ... enchaînons les if/else alors :-(" ?

Donc première nouveauté syntaxique introduite dans la version 1.7 du langage Java : la possibilité d'utiliser le type "String" dans les instructions "switch/case".

Voici un petit exemple de méthode "main()" vérifiant la valeur du premier paramètre envoyé.

(Vous allez sûrement me dire que mon code n'est pas très "safe", que je risque l'ArrayIndexOutOfBoundsException ... je sais mais je me concentre sur les lignes intéressantes :-))

Avant :

{% highlight java %}
public static void main(String args[]) {
    String param = args[0];
    if ("start".equals(param)) {
        MyApplication.start();
    } else if ("stop".equals(param)) {
        MyApplication.stop();
    } else {
        System.err.println("Custom error message ...");
    }
}
{% endhighlight %}

Après :

{% highlight java %}
public static void main(String args[]) {
    switch (args[0]) {
        case "start":
            MyApplication.start();
            break;
        case "stop":
            MyApplication.stop();
            break;
        default:
            System.err.println("Custom error message ...");
            break;
    }
}
{% endhighlight %}

Je trouve que le code est maintenant un peu plus lisible. En revanche, quelques petits détails à ajouter :

*   En cas de valeur "null", le code avec le "switch/case" plante (NullPointerException), alors que dans le code avec les "if/else", on tombe dans le dernier "else".
*   Si on veut être insensible à la casse, avec les "if/else" il suffit de remplacer "equals" par "equalsIgnoreCase", alors qu'avec le "switch/case", il faudrait par exemple convertir le paramètre en minuscule au préalable.

Libre à vous de choisir la syntaxe qui vous convient !

## Nombres binaires et utilisation de l'underscore dans l'écriture des nombres

Autres petites nouveautés dans la syntaxe du langage concernant les nombres :

*   La possibilité d'utiliser les valeurs binaires pour définir des nombres ;
*   La possibilité d'utiliser des "underscores" pour rendre les nombres plus lisibles.

Personnellement il ne m'arrive pas souvent d'avoir besoin d'écrire des nombres en binaires. Pour ceux qui en aurait besoin, la version 1.7 du langage leur permet d'utiliser le préfixe "0b". Exemple pour le nombre 213 :

{% highlight java %}
// 213
int binarynumber = 0b11010101;
{% endhighlight %}

L'autre nouveauté permet de rendre plus lisible la définition de grands nombres en utilisant le caractère "_" pour séparer des blocs de chiffres. Avant :

{% highlight java %}
int bignumber = 45321589;
{% endhighlight %}

Après :

{% highlight java %}
int bignumber = 45_321_589;
{% endhighlight %}

On peut bien évidemment combiner les deux nouveautés :-) :

{% highlight java %}
// 213
int binarynumber = 0b1101_0101;
{% endhighlight %}

## "Catch" multiple

Pour avoir développé à plusieurs reprise des briques logicielles assez techniques, je dois dire que la notion de "catch multiple" aurait rendu certaines parties de mon code plus lisibles ! Prenons un exemple d'utilisation de l'API JDBC qui créé une table fictive dans une instance en mémoire d'une base HSQL :

Avant :

{% highlight java %}
try {
    Class.forName("org.hsqldb.jdbcDriver");
    Connection conn = DriverManager.getConnection("jdbc:hsqldb:mem:testdb", "sa", "");
    Statement stmt = conn.createStatement();
    stmt.execute("CREATE TABLE TMP (DATA VARCHAR(255))");

} catch (ClassNotFoundException e) {
    System.err.println("Error during database access : " + e.getMessage());
} catch (SQLException e) {
    System.err.println("Error during database access : " + e.getMessage());
} catch (Exception e) {
    System.err.println("Unexpected error ! Please contact the technical support");
} finally {
    // close resources.
}
{% endhighlight %}

Après :

{% highlight java %}
try {
    Class.forName("org.hsqldb.jdbcDriver");
    Connection conn = DriverManager.getConnection("jdbc:hsqldb:mem:testdb", "sa", "");
    Statement stmt = conn.createStatement();
    stmt.execute("CREATE TABLE TMP (DATA VARCHAR(255))");

} catch (ClassNotFoundException | SQLException e) {
    System.err.println("Error during database access : " + e.getMessage());
} catch (Exception e) {
    System.err.println("Unexpected error ! Please contact the technical support");
} finally {
    // close resources OR use try-with-statement !
}
{% endhighlight %}

L'intérêt majeur est - comme le montre cet exemple - d'éviter de dupliquer inutilement du code en factorisant les gestions d'erreurs similaires.

## Propagation d'exceptions avec vérification des sous-types

Imaginons une méthode manipulant des données, utilisant comme support de persistance un SGBD et des fichiers. La signature de cette méthode devrait ressembler à cela :

{% highlight java %}
private void manageData() throws SQLException, IOException {
    // Database access, File access ...
}
{% endhighlight %}

L'utilisation de cette méthode pouvait donner ce code avant Java 7 :

{% highlight java %}
public void toto() throws Exception {
    try {
        manageData();
    } catch (Exception e) {
        // your stuff here !
        throw e;
    }
}
{% endhighlight %}

Si on attrape toutes les exceptions possibles et qu'on les re-propage, la signature de la méthode devait être jusqu'à Java 6 "throws Exception".

Maintenant, Java 7 vérifie les sous-types lors de la propagation d'exception et permet ainsi d'être plus précis dans la signature de la méthode :

{% highlight java %}
public void toto() throws SQLException, IOException {
    try {
        manageData();
    } catch (Exception e) {
        // your stuff here !
        throw e;
    }
}
{% endhighlight %}

## Inférence de types avec l'opérateur "diamond" (<>)

Petit rappel (définition issue de [wikipedia](http://fr.wikipedia.org/wiki/Inf%C3%A9rence_de_types)) :

> L'inférence de types est un mécanisme qui permet à un compilateur ou un interpréteur de rechercher automatiquement les types associés à des expressions, sans qu'ils soient indiqués explicitement dans le code source.

Un petit exemple parle mieux qu'un long discours ! Voici ce qu'il fallait écrire avant Java 7 pour déclarer des variables "generics" :

{% highlight java %}
List<String> list = new ArrayList<String>();
list.add("value");

Map<String, Integer> map = new HashMap<String, Integer>();
map.put("key", 123);
{% endhighlight %}

Ou encore un peu plus compliqué :

{% highlight java %}
Map<String, List<BigDecimal>>> anothermap = new HashMap<String, List<BigDecimal>>();
{% endhighlight %}

Maintenant, l'inférence de type utilisée par Java 7 simplifie grandement la syntaxe pour la partie droite des affectations :

{% highlight java %}
List<String> list = new ArrayList<>();
list.add("value");

Map<String, Integer> map = new HashMap<>();
map.put("key", 123);
Map<String, List<BigDecimal>> anothermap = new HashMap<>();
{% endhighlight %}

Une petite simplification qui ne fait pas de mal !

## Instruction "try-with-resource"

"try-with-resource" ou "comment ne plus s'embêter avec de la tuyauterie inutile" !

Prenons un programme tout simple qui lit un fichier ligne par ligne et affiche son contenu. Pour gérer correctement le "BufferedReader" et les exceptions, il fallait écrire ce genre de code :

{% highlight java %}
public static void main(String args[]) {
    String filepath = "src/demo/trywithresource/mytext.txt";

    BufferedReader reader = null;
    try {
        reader = new BufferedReader(new FileReader(filepath));
        String line = reader.readLine();
        while (line != null) {
            System.out.println(line);
            line = reader.readLine();
        }
    } catch (IOException e) {
        System.err.println("Erreur : " + e.getMessage());
    } finally {
        if (reader != null) {
            try {
                reader.close();
            } catch (IOException e) {
                System.err.println("Erreur : " + e.getMessage());
            }
        }
    }
}
{% endhighlight %}

Ce qui m'énerve par dessus tout avec ce code, c'est le "try/catch" dans le bloc "finally". Gérer explicitement l'erreur de l'appel "reader.close()" ne sert vraiment pas à grand chose ... en effet, si une "IOException" se produit à ce niveau, il n'y a pas grand chose à faire, si ce n'est tracer l'erreur :-)

L'instruction "try-with-resource" de Java 7 permet de gérer automatiquement la fermeture des ressources (readers, writers, sockets, connexions ...). Adieu le bloc "finally" et surtout adieu les bugs liés aux oublis d'appels de la méthode "close()" !

{% highlight java %}
public static void main(String args[]) {</span>
    String filepath = "src/demo/trywithresource/mytext.txt";

    try (BufferedReader reader = new BufferedReader(new FileReader(filepath))) {
        String line = reader.readLine();
        while (line != null) {
            System.out.println(line);
            line = reader.readLine();
        }
    } catch (IOException e) {
        System.err.println("Erreur : " + e.getMessage());
    }
}
{% endhighlight %}

Remarque : le "try-with-resource" fonctionne avec les classes implémentant l'interface "java.lang.AutoClosable". Vous pouvez donc utiliser ce mécanisme sur vos propres classes !

## Conclusion

Java 7 est arrivé près de 5 ans après la sortie de Java 6. Il, était temps !

Mais on attend avec impatience Java SE 8, car beaucoup de fonctionnalités initialement prévues ont été mises de côté, notamment :

*   Les closures (lambda expressions)
*   Projet Jigsaw (modules)
*   Collection literals (simplification de la syntaxe pour les listes, ensembles et maps)

Suite au prochain épisode ! :-)