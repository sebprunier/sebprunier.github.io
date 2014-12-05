---
layout: post
title: Devoxx France 2012 et Code Story
date: 2011-12-16 15:52:51.000000000 +01:00
categories:
- Communautés
- Fun
tags:
- devoxx
- java
status: publish
type: post
published: true
author:
comments: true
---

Votre société n'est pas sympa avec vous et vous ne savez toujours pas quoi demander au Père Noël ? J'ai une idée : votre place pour Devoxx France 2012 !

![devoxx_france_468_60]({{ site.url }}/assets/2011-12-16/Meet-us-in-Paris.png)

A cette occasion (Devoxx hein, pas Noël :-)), [David Gageot](http://twitter.com/dgageot) et [Jean-Laurent Morlhon](http://twitter.com/morlhon) ont lancé [Code Story](http://www.code-story.net), un projet dont le but est de coder une application en direct pendant 2 jours, à Devoxx France !

Nous en sommes actuellement aux phases de qualification, plus exactement la pré-sélection se termine aujourd'hui, vendredi 16 décembre ! L'occasion de faire un petit retour sur ma solution au premier exercice FooBarQix.

Les liens à regarder :

*   Le blog de David Gageot : [http://blog.javabien.net/2011/11/17/codestory/](http://blog.javabien.net/2011/11/17/codestory/)
*   Le blog de Code Story : [http://www.code-story.net/2011/11/16/foobarqix.html](http://www.code-story.net/2011/11/16/foobarqix.html)

## Pour ceux qui ne sont pas encore au courant ...

... voici le teaser de Devoxx France 2012 ! :-)

<div style="text-align: center; margin-bottom: 20px;">
    <iframe width="640" height="480" src="//www.youtube.com/embed/II6XiGGlJX0?rel=0" frameborder="0" allowfullscreen="1"> </iframe>
</div>

Toutes les infos ici : [http://www.devoxx.fr](http://www.devoxx.fr/) !

## FooBarQix - ma solution pragmatique

Le problème de l'exercice est posé ici : [http://www.code-story.net/2011/11/16/foobarqix.html](http://www.code-story.net/2011/11/16/foobarqix.html). Extrait :

> _Ecrivez un programme qui affiche les nombres de 1 à 100. Un nombre par ligne. Respectez les règles suivantes :_
>
> *   _Si le nombre est divisible par 3 ou contient 3, écrire “Foo” à la place de 3._
>
> *   _Si le nombre est divisible par 5 ou contient 5, écrire “Bar” à la place de 5._
>
> *   _Si le nombre est divisible par 7 ou contient 7, écrire “Qix” à la place de 7._

Une des consignes :

> _Soyez inventif, pragmatique, écrivez du code formidable, impressionnez nous, montrez que vous voulez venir coder avec nous._

En ce qui me concerne, j'ai fait _pragmatique_ :-) Le problème est simple, donc pas la peine de chercher une solution compliquée ! J'ai donc fait un petit morceau de code Java, disponible ici : [https://github.com/sebprunier/FooBarQix_Java/blob/master/src/fr/devoxx/foobarqix/FooBarQix.java](https://github.com/sebprunier/FooBarQix_Java/blob/master/src/fr/devoxx/foobarqix/FooBarQix.java)

Je ne suis pas sûr par contre de répondre à la consigne "_écrivez du code formidable_", et pas sûr non plus _d'impressionner_ grand monde :-) C'est pas grave, j'assume !

#### Divisible par X

Pour savoir si un nombre N est divisible par un autre nombre M, il suffit simplement de vérifier que N modulo M = 0. Ce qui peut se retranscrire de cette manière en Java, dans le cadre de l'exercice (extrait de mon code) :

{% highlight java %}
public static final String FOO = "Foo";
public static final String BAR = "Bar";
public static final String QIX = "Qix";

private static final String[] INTEGER_PATTERNS = { "", "", "", FOO, "", BAR, "", QIX, "", "" };

private String transformModulo(int i, int modulo) {
    return i % modulo == 0 ? INTEGER_PATTERNS[modulo] : '';
}
{% endhighlight %}

#### Contient X

Pour le remplacement des chiffres 3, 5, 7 par "Foo", "Bar", "Qix", on peut s'inspirer de la façon de faire précédente avec cette fois une Map associant les chaînes "Foo", "Bar" et "Qix" aux chiffres adéquats :

{% highlight java %}
// Patterns sorted by char ('0'-'9')
private static final Map CHAR_PATTERNS;
static {
    CHAR_PATTERNS = new HashMap();
    CHAR_PATTERNS.put('0', "");
    CHAR_PATTERNS.put('1', "");
    CHAR_PATTERNS.put('2', "");
    CHAR_PATTERNS.put('3', FOO);
    CHAR_PATTERNS.put('4', "");
    CHAR_PATTERNS.put('5', BAR);
    CHAR_PATTERNS.put('6', "");
    CHAR_PATTERNS.put('7', QIX);
    CHAR_PATTERNS.put('8', "");
    CHAR_PATTERNS.put('9', "");
}
{% endhighlight %}

[Aparté] Vivement les collection literals de Java 8 :-) [/Aparté]

{% highlight java %}
private String replace357(String s) {
    String res = "";

    for (char c : s.toCharArray()) {
        res += CHAR_PATTERNS.get(c);
    }

    return res;
}
{% endhighlight %}

#### Et pour finir ...

Maintenant que nous avons les méthodes de base, il ne reste qu'à tenir compte des priorités définies dans le jeu (diviseurs d'abord, ordre des chiffres) :

{% highlight java %}
protected String[] execute(int max) {
    String[] result = new String[max];

    for (int i = 1; i <= max; i++) {
        String si = String.valueOf(i);
        String line = transformModulo(i, 3)
                    + transformModulo(i, 5)
                    + transformModulo(i, 7)
                    + replace357(si);
        result[i - 1] = "".equals(line) ? si : line;
    }
}
{% endhighlight %}

Et le petit "main" qui va bien !

{% highlight java %}
public static void main(String[] args) {
    String[] result = new FooBarQix().execute(MAX);

    for (String s : result) {
        System.out.println(s);
    }
}
{% endhighlight %}

## Conclusion

Tout est ici : [https://github.com/sebprunier/FooBarQix_Java](https://github.com/sebprunier/FooBarQix_Java) Il y a même un petit test unitaire :-)

C'est une façon de faire, en Java. Je suis sûr qu'on pouvait s'amuser un peu avec les langages fonctionnels, genre Scala ou les nouveaux à la mode. Il y sûrement beaucoup de participants qui ont du s'orienter en ce sens à mon avis ! On verra les solution retenues !

Merci pour ce jeu en tout cas, et on attend avec impatience le "live coding" de Devoxx en avril ! Ca va déchirer, c'est sûr !! :-)