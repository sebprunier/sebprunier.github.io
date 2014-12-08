---
layout: post
title: Zen Coding avec IntelliJ Idea
date: 2014-12-01
categories:
- Code
tags:
- intellij idea
type: post
published: true
comments: true
---

Sûrement que la plupart d'entre vous connaissent et utilisent déjà la fonctionnalité de "Zen Coding" d'Intellij Idea.
Voici quand même quelques petits exemples d'utilisation pour ceux qui ne connaîtraient pas :-)

Le "Zen Coding" permet de générer des snippets de code XML (et donc également HTML) à partir d'une expression inline.

Par exemple, pour générer un template HTML basique écrivez l'expression suivante :

`html>(head>title{Hello})+body{Hello World !}`

Tapez `Tab` à la fin de la ligne pour générer le snippet :

{% highlight html %}
<html>
<head>
    <title>Hello</title>
</head>
<body>Hello World !</body>
</html>
{% endhighlight %}

## Grid bootstrap

Un autre exemple intéressant pour générer rapidement une grille basée sur le framework CSS [Twitter Bootstrap](http://getbootstrap.com/css/#grid)

Expression : `div.row>div.col-md-3*4>p{colonne $}`

Snippet correspondant :

{% highlight html %}
<div class="row">
    <div class="col-md-3">
        <p>colonne 1</p>
    </div>
    <div class="col-md-3">
        <p>colonne 2</p>
    </div>
    <div class="col-md-3">
        <p>colonne 3</p>
    </div>
    <div class="col-md-3">
        <p>colonne 4</p>
    </div>
</div>
{% endhighlight %}

## Tableau

Autre exemple pour générer un squelette de tableau HTML de 2 lignes et 3 colonnes, avec entête.

Expression : `table.table>(thead>tr>th*3>{col $})+tbody>tr*4>td*3`

Snippet correspondant :

{% highlight html %}
<table class="table">
    <thead>
    <tr>
        <th>col 1</th>
        <th>col 2</th>
        <th>col 3</th>
    </tr>
    </thead>
    <tbody>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    <tr>
        <td></td>
        <td></td>
        <td></td>
    </tr>
    </tbody>
</table>
{% endhighlight %}

## Conclusion

Voilà, c'est tout ! :-)

Si cela vous intéresse, il est également possible de générer des snippets CSS : [http://docs.emmet.io/css-abbreviations](http://docs.emmet.io/css-abbreviations/)

Quelques liens utiles :

* [Documentation Intellij Idea](https://www.jetbrains.com/idea/help/emmet.html)
* [Documentation Emmet](http://docs.emmet.io/abbreviations/)

La fonctionnalité "Zen coding" (basée désormais sur l'outil [Emmet](http://emmet.io/)) est disponible sur la plupart des IDE :

* [Emmet Eclipse plugin](http://marketplace.eclipse.org/content/emmet-ex-zen-coding-eclipse-plugin)
* [NetBeans Zen Coding plugin](http://plugins.netbeans.org/plugin/41792/zen-coding)
