---
layout: post
title: Connecteurs Google+ pour Bonita Open Solution
date: 2011-10-03 09:54:39.000000000 +02:00
categories:
- Tutoriels
tags:
- Bonita
- google+
- java
- json
status: publish
type: post
published: true
author:
comments: true
---

Du 12 au 16 septembre 2011, j'ai suivi à Paris une formation organisée par BonitaSoft :

*   Développement d'applications métier (3 jours)
*   Intégration avancée (2 jours)

Lors de la partie "Développement de connecteurs personnalisés", je me suis dit "tiens, pourquoi je n'essaierais pas de développer des connecteurs pour Google+ ?" et surtout "tiens, c'est bizarre, ils n'existent pas encore dans les [contributions](http://www.bonitasoft.org/exchange/)" ... Forcément puisque Google a sorti la version 1 de son API Google+ le ... 16 septembre ! Donc le dernier jour de ma formation :-)

A défaut d'avoir pu tester l'API G+ en formation, je l'ai fait un peu plus tard ! Voici donc mon humble contribution avec quelques explications de texte.

Pour ceux que ça intéresse :

*   Connecteur [GetUserProfile](http://dl.dropbox.com/u/52447841/bonita/GetProfile.jar)
*   Connecteur [GetActivities](http://dl.dropbox.com/u/52447841/bonita/GetActivities.jar)
*   Connecteur [GetActivity](http://dl.dropbox.com/u/52447841/bonita/GetActivity.jar)

![contribution]({{ site.url }}/assets/2011-10-03/contribution.png)

## Google+ API v1

La version 1 de l'API Google+ est très très légère ... uniquement 3 fonctionnalités, uniquement pour de la récupération d'informations :

*   [people/get](https://developers.google.com/+/api/latest/people/get) : récupère le profil d'une personne,
*   [activities/list](https://developers.google.com/+/api/latest/activities/list) : récupère les activités d'une personne donnée,
*   [activities/get](https://developers.google.com/+/api/latest/activities/get) : récupère le détail d'une activité.

Donc pas de possibilité pour le moment de créer un connecteur publiant une info dans votre flux. Tant pis ! Contentons-nous pour le moment de ce qui existe.

Mon article se concentre sur le connecteur "GetActivities" et donc sur la méthode "activities/list" de l'API.

La requête HTTP GET à effectuer est la suivante :

> https://www.googleapis.com/plus/v1/people/{userId}/activities/{collection}

Les paramètres obligatoires sont les suivants :

*   **userId** : l'identifiant de l'utilisateur. Par exemple : 101859091162395481354 (c'est [moi](http://gplus.to/sebprunier) :-)) ou "me" pour faire référence à l'utilisateur authentifié.
*   **collection** : la collection d'activités à lister. Pour le moment, seule la valeur "public" est supportée.

Le connecteur gère d'autres paramètres optionnels :

*   **maxResults** : nombre maximum d'activités.
*   **fields** : filtre pour récupérer une réponse partielle.

Le connecteur ne gère pas à ce jour

*   la pagination (paramètre "pageToken")
*   les alternatives pour le format de la réponse (paramètre "alt") car seul JSON est supporté à l'heure actuelle.

## Exemple d'appel

Un appel avec comme paramètres "maxResult = 3" et "fields = items/title" donnerait une réponse JSON du genre :

{% highlight json %}
{
 "items": [
  {
   "title": "..."
  },
  {
   "title": "..."
  },
  {
   "title": "..."
  }
 ]
}
{% endhighlight %}

## Configuration du connecteur GetActivities

Afin de tester le connecteur, imaginons un Processus très simple, sans grande utilité :-)

![googleplus_connector_example_1-0]({{ site.url }}/assets/2011-10-03/googleplus_connector_example_1-0.png)

La configuration du connecteur "GetActivities" se fait sur la première étape "get info". La seconde étape sert uniquement à afficher les résultats de l'appel.

Le processus possède 3 données :

*   userId (String) : identifiant de l'utilisateur dont on veut lire les activités publiques  (renseigné dans l'IHM lors de la première étape du processus),
*   maxResults (Integer) : nombre maximum de résultats (renseigné dans l'IHM lors de la première étape du processus),
*   activities (java.util.List) : liste des activités (affichées lors de la deuxième étape du processus)

Voici l'écran associé à la première étape du processus :

![ecran-1]({{ site.url }}/assets/2011-10-03/ecran-1.png)

La première chose à faire est de renseigner votre clé pour l'API Google+. Pour plus de détails : [https://developers.google.com/+/api/oauth#apikey](https://developers.google.com/+/api/oauth#apikey) ou directement [https://code.google.com/apis/console#access](https://code.google.com/apis/console#access)

![connector-config-apikey]({{ site.url }}/assets/2011-10-03/connector-config-apikey.png)

Ensuite vous pouvez renseigner les paramètres d'appel :

![connector-config-userid]({{ site.url }}/assets/2011-10-03/connector-config-userid.png)

## Traitement de la sortie du connecteur

En sortie du connecteur, il faut manipuler les données de la réponse au format JSON. Cela est très simple et peut se faire facilement avec une expression Groovy grâce à la librairie [JSON-lib](http://json-lib.sourceforge.net/) :

{% highlight groovy %}
def jsonObj = new JsonSlurper().parseText( response );
jsonObj.items.title;
{% endhighlight %}

Ce petit morceau de code récupère les titres ("title") des entrées ("items") de la réponse JSON ("jsonObj").

Il est ensuite nécessaire d'affecter le résultat du script Groovy à la variable "activities". Ainsi la deuxième étape du processus pourra afficher correctement les résultats :

![ecran-2]({{ site.url }}/assets/2011-10-03/ecran-2.png)

## Conclusion

Lien vers le processus de la démo : [http://www.filedropper.com/getuseractivities--10](http://www.filedropper.com/getuseractivities--10)

Rappel des liens vers les connecteurs :

*   Connecteur [GetUserProfile](http://dl.dropbox.com/u/52447841/bonita/GetProfile.jar)
*   Connecteur [GetActivities](http://dl.dropbox.com/u/52447841/bonita/GetActivities.jar)
*   Connecteur [GetActivity](http://dl.dropbox.com/u/52447841/bonita/GetActivity.jar)