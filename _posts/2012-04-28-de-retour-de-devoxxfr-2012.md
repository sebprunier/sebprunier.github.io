---
layout: post
title: 'De retour de #DevoxxFR 2012 !!'
date: 2012-04-28 11:18:19.000000000 +02:00
categories:
- Communautés
- News
tags:
- CodeStory
- communautés
- DevoxxFR
status: publish
type: post
published: true
author:
comments: true
---

> "Où étiez vous les 19 et 20 avril dernier ?"

J'ai un alibi solide Monsieur l'Agent, j'étais à Devoxx France ! :-)

![badge-devoxx]({{ site.url }}/assets/2012-04-28/badge-devoxx.png)

> "Ah oui, et vous avez des témoins ?"

Oh oui je pense ... 1250 environ !!

![devoxx-1250]({{ site.url }}/assets/2012-04-28/devoxx-1250.png)

> "La salle n'a pas l'air très remplie ... Il va falloir m'en dire plus !"

C'est normal, c'était juste avant les keynotes du jeudi ... J'ai quelques preuves matérielles aussi ! Regardez :

![goodies]({{ site.url }}/assets/2012-04-28/goodies.png)

> "Mouais, pas très solide tout ça ..."

Bon pour vous convaincre, voici un petit résumé de ces deux jours de conférences ...

## goto devoxxfr;

Se lever à 5h du matin, d'habitude ça ne m'enchante pas vraiment ! Mais quand on sait que deux jours de conférences nous attendent, pour la première édition française de Devoxx, le réveil est tout de suite plus léger ! Gare de Nantes, direction Paris !!

![sncf-devoxxfr]({{ site.url }}/assets/2012-04-28/sncf-devoxxfr.png)

## Les Keynotes

Les Keynotes sont ces "petits moments Nutella" permettant de se mettre en jambe eu début de chaque journée de conférences.

* Portrait du développeur en "The Artist" par Patrick Chanezon : [http://www.parleys.com/#st=5&amp;id=3300&amp;sl=4](http://www.parleys.com/#st=5&amp;id=3300&amp;sl=4)
* Fier d'être développeur par Pierre Pezziardi : [http://www.parleys.com/#st=5&amp;id=3345&amp;sl=0](http://www.parleys.com/#st=5&amp;id=3345&amp;sl=0)

C'est aussi l'occasion de faire passer quelques messages sur le tweetwall ! :-)

![tweetwall-devoxxfr]({{ site.url }}/assets/2012-04-28/tweetwall-devoxxfr.png)

## Code Story

Une idée géniale, tout simplement ! [David](http://code-story.net/about/david.html "David"), [Jean-Laurent](http://code-story.net/about/jean-laurent.html "Jean-Laurent"), [Eric](http://code-story.net/about/eric.html "Eric") et [Sebastian](http://code-story.net/about/sebastian.html "Sebastian") ont développé une application en live pendant les deux jours de conférence. Qu'ont-ils développé ? Une application de reporting de la vie d'un projet (lien avec le repository github, le job jenkins, ...), avec attribution de badges pour les développeurs (fat-commiter, build-breaker). Sympa non ?

J'ai assisté à plusieurs itérations. C'était génial, j'ai pu voir :

* Le script "[unbreakable build](https://gist.github.com/2708610 "unbreakable-buid")" de David Gageot
* Du déploiement continu en production avec du Git, du Maven et du Jenkins
* Une multitude de technos, mais chacune utilisée à juste titre !
    * Du NodeJs
    * Du Guava, du Guice, du Joda-time ...
    * Du Github API
    * Du Less CSS
    * Du mustache.js
    * Du testing qui déchire avec expect.js et zombie.js
    * _(Et encore plein d'autres choses !)_
* Dens gens qui maîtrisent leurs outils de développement !
* Des gens qui prennent du plaisir à développer !!
* Des gens qui partagent ce plaisir !!!

Bref, c'est dans cette petite salle que j'ai passé pas mal de mon temps ! Et je ne regrette pas du tout !

![code-story]({{ site.url }}/assets/2012-04-28/code-story.png)

Tous les liens utiles :

*   [http://code-story.net/](http://code-story.net/)
*   [https://github.com/CodeStory/code-story-devoxx-france](https://github.com/CodeStory/code-story-devoxx-france)

## Quelques Conférences en vrac ...

### Introduction à Ceylon

_Par Stéphane Epardaud et Emmanuel Bernard_

L'occasion de découvrir un nouveau langage de programmation : Ceylon. Stéphane et Emmanuel commencent par nous présenter les motivations qui ont poussé à la création de ce nouveau langage. Pour résumer, l'objectif est de faciliter la vie du développeur !

S'en suivent une série d'exemple illustrant les grands principes de Ceylon, avec entre autres :

*   le code du constructeur directement dans la classe
*   uniquement deux portées : shared (public) et default (privé)
*   pas de surcharge de méthode, par contre : possibilité d'avoir des valeurs par défaut pour les paramètres des méthodes
*   gestion secure des valeurs null grace à l'utilisation du point d'interrogation (plus de NullPointerException en vue !)
*   intersection et union de types
*   etc ...

Plus de détails ici :

*   [http://ceylon-lang.org/documentation/introduction/](http://ceylon-lang.org/documentation/introduction/)
*   [http://www.devoxx.com/pages/viewpage.action?pageId=6128283](http://www.devoxx.com/pages/viewpage.action?pageId=6128283)

### sizeOf en Java, parce que la taille ça compte !

_Par Alex Snaps_

Alex nous a fait descendre dans les méandres de la gestion de la mémoire au niveau des machines virtuelles Java (notamment les différences entre systèmes 32 et 64 bits), exemples de code à l'appui ! L'idée était de démontrer que calculer la taille mémoire d'un objet  n'est pas trivial, et que par conséquent le support dans EhCache 2.5 du dimensionnement des caches en octets est une vraie performance !

Une session très très technique, mais qui valait clairement le coup !

Plus de détails ici :

*   [http://ehcache.org/documentation/configuration/cache-size](http://ehcache.org/documentation/configuration/cache-size)
*   [http://www.devoxx.com/pages/viewpage.action?pageId=6128308](http://www.devoxx.com/pages/viewpage.action?pageId=6128308)

### Optimisez votre site web sur mobile

_Par Romain Maton_

Une conférence sur le format "boîte à outil du développeur mobile". Romain présente les spécificités du développement mobile, les pièges à éviter, les bonnes pratiques et les bons outils et frameworks à utiliser. Une heure très pragmatique, avec un speaker qui a de bons retours d'expérience sur le sujet !

Plus de détails ici :

*   [http://www.devoxx.com/display/FR12/Optimiser+votre+site+Web+sur+mobile](http://www.devoxx.com/display/FR12/Optimiser+votre+site+Web+sur+mobile)

### Quicky - Tester la persistance Java avec Arquillian

_Par Alexis Hassler_

Arquillian est un outil permettant d'effectuer des tests d'intégration de vos applications directement dans un conteneur. Arquillian gère plusieurs conteneurs dans des modes de déploiement différents (embedded / remote),  s'intègre avec JUnit ou TestNG, peut s'exécuter via Ant ou encore Maven ... Bref, toutes les bases sont là !

Un quicky très sympa, essentiellement à base de démo, qui donne envie de tester Arquillian dès en rentrant !

_PS : Est-ce la fin des frameworks de mocking ? :-)_

Tous les détails ici :

*   [http://www.devoxx.com/display/FR12/Tester+la+persistance+Java+avec+Arquillian](http://www.devoxx.com/display/FR12/Tester+la+persistance+Java+avec+Arquillian)

## Les Cast Codeurs en live !

Quoi de mieux qu'un épisode live des Cast Codeurs pour finir la première édition française de Devoxx ? Une heure de détente et de franche rigolade, accompagnée d'une bonne bière offerte par Atlassian ! Bref, un très bon moment :-)

Si vous voulez écouter ou visionner l'épisode 57 :

*   [http://lescastcodeurs.com/2012/04/21/les-cast-codeurs-podcast-episode-57-en-direct-de-devoxx-france-2012/](http://lescastcodeurs.com/2012/04/21/les-cast-codeurs-podcast-episode-57-en-direct-de-devoxx-france-2012/)
*   [http://www.devoxx.com/display/FR12/Les+Cast+Codeurs+Podcast](http://www.devoxx.com/display/FR12/Les+Cast+Codeurs+Podcast)

![cast-codeurs-devoxxfr]({{ site.url }}/assets/2012-04-28/cast-codeurs-devoxxfr.png)

## Conclusion

Je suis très satisfait de ces deux journées de conférences ! Et très content d'avoir participé à cette première édition de Devoxx France. Je pense que je renouvellerai l'expérience l'année prochaine, en assistant cette fois aux universités la première journée.

Donc rendez-vous l'année prochaine pour [#DevoxxFR](http://devoxx.fr "Devoxx France") 2013 ! Avant cela, n'oubliez pas le [#BreizhCamp](http://www.breizhcamp.org/ "BreizhCamp") 2012 à Rennes les 14 et 15 juin ! Cette fois-ci je suis de l'autre côté du mirroir le temps d'une [Université](http://app.breizhcamp.cloudbees.net/talk/11.htm) (Développez un bugtracker personnalisé avec Bonita Open Solution) !

![breizhcamp]({{ site.url }}/assets/2012-04-28/breizhcamp.png)