---
layout: post
title: Java 7 - Retour du Nantes JUG de novembre
date: 2011-12-28 17:43:02.000000000 +01:00
categories:
- Communautés
- Tutoriels
tags:
- fork-join
- java
- JUG
status: publish
type: post
published: true
author:
comments: true
---

Le 3 novembre dernier, j'ai animé la session de présentation de Java 7 au JUG de Nantes. L'idée était de faire un JUG basé essentiellement sur des démos.

C'était ma toute première fois en tant que speaker ! :-) Il y a eu du bon et du moins bon, le principal était de se lancer !

L'objectif de cet article est de faire un petit zoom sur ce qui a été vu en live pendant la session et qui n'était pas prévu au départ dans la démo :

*   Les améliorations apportées par l'instruction "try-with-resource" concernant le masquage des exceptions,
*   Le "multicatch" avec des exceptions ayant une interface commune
*   Un exemple simple d'utilisation du framework Fork/Join

PS : les évolutions du langage (Coin Project) sont décrites dans [un de mes précédents articles](/news/veille/java-7-les-nouveautes-dans-le-langage/).

![Duke3D]({{ site.url }}/assets/2011-12-28/Duke3D.png)

## Try-with-resource et masquage d'exceptions

Avant java 7, écrire du code avec une bonne gestion des exceptions (sans perte d'exceptions par exemple) était assez sportif, comme le montre cet article qui m'a été conseillé par [Manuel Boillod](https://twitter.com/#!/mboillod) : [http://tutorials.jenkov.com/java-exception-handling/exception-handling-templates.html](http://tutorials.jenkov.com/java-exception-handling/exception-handling-templates.html)

La question m'a donc été posée lors de la démo sur la nouvelle instruction "try-with-resource" : "Vu qu'on n'écrit plus le bloc finally avec l'appel explicite de la méthode 'close()', que se passe-t-il si l'instruction 'try' lance une exception et le 'close()' également ? Perd-on l'exception d'origine levée dans le 'try' ?"

Réponse : et bien non ! (ce qui est plutôt rassurant au passage). Les exceptions masquées sont désormais conservées dans la stack.

Cf. l'API [Throwable](http://docs.oracle.com/javase/7/docs/api/java/lang/Throwable.html) de Java 7 :

> public final void addSuppressed(Throwable exception)

> public final Throwable[] getSuppressed()


Voici le petit morceau de code qui a été fait en live pour illustrer cela. Prenons une classe "AutoClosable" de test, dont les deux méthodes "connect()" et "close()" lancent des exceptions :

{% highlight java %}
public class MyResource implements AutoCloseable {

    public void connect() throws IllegalArgumentException {
        throw new IllegalArgumentException("connect");
    }

    @Override
    public void close() throws IllegalArgumentException {
        throw new IllegalArgumentException("close");
    }
}
{% endhighlight %}

Et utilisons cette classe dans un "main" avec la nouvelle instruction "try-with-resource" :

{% highlight java %}
public static void main(String[] args) {

    try (MyResource res = new MyResource()) {
        res.connect();
    } catch (IllegalArgumentException e) {
        e.printStackTrace();
    }
}
{% endhighlight %}

Le résultat de l'exécution est le suivant :

    java.lang.IllegalArgumentException: connect
        at MyResource.connect(MyResource.java:4)
        at Main.main(Main.java:6)
        Suppressed: java.lang.IllegalArgumentException: close
            at MyResource.close(MyResource.java:10)
            at Main.main(Main.java:7)

Quelques liens pour conclure sur ce point :

*   Des explications sur [le site officiel d'Oracle](http://docs.oracle.com/javase/tutorial/essential/exceptions/tryResourceClose.html#suppressed-exceptions)
*   La présentation de [Julien Ponge](http://live.julien.ponge.info/java-7-au-jug-summercamp-2011) au JugSummerCamp 2011 : [http://www.parleys.com/#st=5&amp;id=2671](http://www.parleys.com/#st=5&amp;id=2671)

## Multi Catch avec des exceptions ayant une interface commune

Autre petit morceau de code réalisé en live à propos des améliorations concernant les exceptions (multi catch, more precise rethrow). Il s'agit cette fois d'illustrer la meilleure analyse des flux au niveau de la gestion des exceptions.

Imaginons une interface "IFinalcialException" commune à deux exceptions concrètes "FinancialException" et "FinancialException2" :

{% highlight java %}
public interface IFinancialException {
    public String getMyCode();
    public void setMyCode(String myCode);
}

public class FinancialException
    extends Exception
    implements IFinancialException { ... }

public class FinancialException2
    extends Exception
    implements IFinancialException { ... }

{% endhighlight %}

Dans un bloc "catch" multiple traitant les deux types d'exception "FinancialException" et "FinancialException2", il est possible d'accéder aux méthodes de l'interface commune aux deux exceptions :

{% highlight java %}
try {
    // ... do something here !
}
catch (FinancialException | FinancialException2 e) {
    logger.info(e.getMyCode());
    logger.log(Level.SEVERE, "Error while ETL execution", e);
}
{% endhighlight %}

## Fork/Join

Le JUG a été également l'occasion de présenter très rapidement la nouvelle API fork/join de Java 7. L'exemple était simple et sans grand intérêt, mais a permis d'illustrer facilement le fonctionnement de l'API, en particulier :

*   la classe "java.util.concurrent.RecursiveAction"
*   la classe "java.util.concurrent.ForkJoinPool"

Il s'agissait d'additionner le contenu d'une liste de nombres entiers, en programmant de façon parallèle en s'appuyant sur l'algorithme très simple proposé sur le [site d'oracle](http://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html) :

    if (my portion of the work is small enough)
        do the work directly
    else
        split my work into two pieces
        invoke the two pieces and wait for the results

Ce qui donne la classe action suivante :

{% highlight java %}
public class ForkJoinCalculator extends RecursiveAction {

    private static final long serialVersionUID = -9181212554873539763L;

    private List<Integer> data;
    private Integer result = 0;

    public ForkJoinCalculator(List<Integer> data) {
        this.data = data;
    }

    @Override
    protected void compute() {
        // if (my portion of the work is small enough)
        // do the work directly
        int size = data.size();
        if (size == 1) {
            result = data.get(0);
            return;
        }

        // else
        // split my work into two pieces
        ForkJoinCalculator c1 = new ForkJoinCalculator(data.subList(0, size / 2));
        ForkJoinCalculator c2 = new ForkJoinCaculator(data.subList(size / 2, size));

        // invoke the two pieces and wait for the results
        invokeAll(c1, c2);
        result += c1.result;
        result += c2.result;
    }

    public Integer getResult() {
        return result;
    }
}
{% endhighlight %}

Et le code permettant de démarrer le pool de threads :

{% highlight java %}
public static void main(String[] args) {
    ForkJoinCalculator c = new ForkJoinCalculator(Data.BIG_LIST);
    ForkJoinPool pool = new ForkJoinPool();
    pool.invoke(c);
    System.out.println("Result : " + c.getResult());
}
{% endhighlight %}

## Les slides du JUG

<div style="text-align: center; margin: 20px;">
    <iframe src="//www.slideshare.net/slideshow/embed_code/10021960" width="425" height="355" frameborder="0" marginwidth="0" marginheight="0" scrolling="no" style="border:1px solid #CCC; border-width:1px; margin-bottom:5px; max-width: 100%;" allowfullscreen> </iframe> <div style="margin-bottom:5px"> <strong> <a href="//fr.slideshare.net/sebprunier/nantes-jug-java-7-10021960" title="Nantes Jug - Java 7" target="_blank">Nantes Jug - Java 7</a> </strong> from <strong><a href="//www.slideshare.net/sebprunier" target="_blank">Sébastien Prunier</a></strong> </div>
</div>

## Le code des démos

Le code des démos est disponible sur GitHub :

*   [https://github.com/sebprunier/nantesjug-java7](https://github.com/sebprunier/nantesjug-java7)
*   [https://github.com/sebprunier/nantesjug-fork-join](https://github.com/sebprunier/nantesjug-fork-join)
        
    
