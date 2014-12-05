---
layout: post
title: Pod Programming cherche son "Java Architect/Project Leader/Guru" !
date: 2011-07-01 11:22:07.000000000 +02:00
categories:
- News
tags:
- cloudbees
- expressboard
- github
- gradle
- jsf2
- JUG
- podprogramming
status: publish
type: post
published: true
author:
---

La startup rennaise vient de lancer une [offre d'annonce originale via l'eXpress Board](http://www.express-board.fr/offre-d-emploi/nous-recherchons-notre-futur-tech-lead-java/dc738ebc309ad01d0130ac69d47d0010). Pod Programming est à la recherche d'un **Architecte/Project Leader/Guru** pour la version Java de son framework.

Offre d'emploi original puisqu'il faut démontrer son savoir faire au travers d'un fork de leur projet-annonce [disponible sous GitHub](https://github.com/podProgramming/we-recruit) et répondre au questionnaire afin d'accéder au Graal, c'est à dire l'adresse mail à utiliser pour postuler :-)

Je ne suis pas en recherche d'emploi, je m'éclate dans ma société actuelle, mais bon, s'amuser un peu parfois, ca fait du bien ! J'ai donc forké :-)

![my_fork_1-0.png]({{ site.url }}/assets/2011-07-01/my_fork_1-0.png)

## How I forked your project

Après une rapide réflexion, mon fork sera avant tout une migration technologique :

*   Migration GWT vers JSF 2.0 (respectons les standards JEE :p #troll)
*   Migration Maven vers Gradle (soyons visionnaires :p #troll #again)

Le développement d'une petite fonctionnalité s'impose ... mon idée : la carte de France des JUGs ! L'occasion de découvrir la librairie JSF [gmaps4jsf](http://code.google.com/p/gmaps4jsf/).

![jugs]({{ site.url }}/assets/2011-07-01/jugs.png)

Forker c'est bien, packager (avec Gradle) c'est bien aussi, déployer l'application, c'est mieux ! Encore une occasion de tester une nouveauté : RUN@Cloud de [CloudBees](http://www.cloudbees.com). Facilité déconcertante pour rendre disponible mon WAR "dans le nuage" en quelques clics ... : [http://werecruit.sebprunier.cloudbees.net](http://werecruit.sebprunier.cloudbees.net/)

Le code de mon fork sur GitHub : [https://github.com/sebprunier/we-recruit](https://github.com/sebprunier/we-recruit)

_Note pour moi-même : me mettre à GIT sérieusement, et rapidement :-)_