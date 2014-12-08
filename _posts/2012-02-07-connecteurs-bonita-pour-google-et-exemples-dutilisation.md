---
layout: post
title: Connecteurs Bonita pour Google+ et exemples d'utilisation
date: 2012-02-07 18:42:33.000000000 +01:00
categories:
- Communautés
- Tutoriels
tags:
- Bonita
- cloudbees
- google+
- groovy
- jsTree
status: publish
type: post
published: true
author:
comments: true
---

J'ai récemment mis à jour ma contribution des connecteurs Bonita permettant d'utiliser l'API Google+.

Il y a quelques évolutions intéressantes je pense par rapport à [la première version](http://sebprunier.wordpress.com/2011/10/03/connecteurs-google-pour-bonita-open-solution/) de ces connecteurs :

*   Intégrations des nouvelles [APIs](https://developers.google.com/+/api/)
    *   People : search, listByActivity
    *   Activities : search
    *   Comments : get, list
*   Utilisation de l'api [google-api-java-client](http://code.google.com/p/google-api-java-client/). Les connecteurs retournent maintenant des objets du package _com.google.api.services.plus.model_ (et non plus directement la réponse JSON). Cela simplifie grandement le traitement du retour des appels de connecteurs.
*   Un seul JAR pour l'ensemble des connecteurs, contenant les dépendances ("all-in-one")

J'ai également contribué un exemple d'utilisation de chacun de ces connecteurs. J'ai profité de cet exemple pour intégrer la librairie [jsTree ](http://www.jstree.com/)(plugin jQuery) pour présenter certains résultats d'appels sous forme d'arbre.

Cet article est l'occasion de présenter les [connecteurs](http://www.bonitasoft.org/exchange/extension_view.php?eid=283) et l'[exemple d'utilisation](http://www.bonitasoft.org/exchange/extension_view.php?eid=284) en quelques points.

![googleplus]({{ site.url }}/assets/2012-02-07/googleplus.jpg)

## Les connecteurs dans un seul JAR

Afin d'éviter de multiplier les JARs en créant un connecteur pour chaque fonctionnalité de l'API Google+, j'ai intégré le code de l'ensemble des connecteurs au sein d'un même projet et développé avec l'IDE Eclipse (et non Bonita Studio).

Le code source est disponible sur GitHub : [https://github.com/sebprunier/bonita-googleplus-connectors](https://github.com/sebprunier/bonita-googleplus-connectors)

C'est un simple projet Maven. Le code des connecteurs se trouve dans le dossier "src/main/java" et les fichiers de configuration (XML et properties) se situent dans le dossier "src/main/resources"

Afin de proposer un JAR unique contenant les dépendances nécessaires à son bon fonctionnement, j'utilise le plugin maven "maven-shade-plugin".

{% highlight xml %}
<!-- Shade : package Google+ API dependencies into the JAR -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-shade-plugin</artifactId>
    <version>1.5</version>
    <configuration>
        <finalName>${project.name}-${project.version}-all-in-one</finalName>
    </configuration>
    <executions>
        <execution>
            <phase>package</phase>
            <goals>
                <goal>shade</goal>
            </goals>
        </execution>
    </executions>
</plugin>
{% endhighlight %}

Seul petit détail : lorsque vous importez le JAR des connecteurs Google+ dans Bonita Studio, vous obtenez le message suivant :

![bonita-gplus-import]({{ site.url }}/assets/2012-02-07/bonita-gplus-import.png)

C'est normal, rien de grave ! Cliquez simplement sur Ok :-)

## Une classe de base pour tous les connecteurs

Un des avantages du regroupement de tous les connecteurs dans un même projet, c'est que l'on peut créer une classe de base qui leur est commune. Un peu de factorisation ne fait pas de mal ! Par exemple, la création du client Google+ est centralisée :

{% highlight java %}
/**
 * Creates a Plus client. This method must be used by concrete subclasses.
 *
 * @return a Plus client.
 * @throws Exception
 * error while creating client.
 */
protected final Plus getPlusClient() throws Exception {
    JsonFactory jsonFactory = new GsonFactory();
    HttpTransport httpTransport = new NetHttpTransport();
    JsonHttpRequestInitializer jsonHttpRequestInitializer = new JsonHttpRequestInitializer() {
        @Override
        public void initialize(JsonHttpRequest request) throws IOException {
            PlusRequest plusRequest = (PlusRequest) request;
            plusRequest.setKey(apiKey);
            if (fields != null &amp;&amp; !"".equals(fields)) {
                plusRequest.setFields(fields);
            }
            plusRequest.setAlt(resultRepresentationType);
        }
    };

    Plus.Builder builder = Plus.builder(httpTransport, jsonFactory);
    builder.setJsonHttpRequestInitializer(jsonHttpRequestInitializer);
    return builder.build();
}
{% endhighlight %}

Au départ j'avais même stéréotypé la classe de base avec un paramètre "T" qui correspond au type de retour du connecteur. Pour aller encore plus loin dans la factorisation.

{% highlight java %}
public abstract class GooglePlusConnector<T> extends Connector
{% endhighlight %}

Mais patatra, [bug](http://www.bonitasoft.org/bugs/view.php?id=7837) lors de l'utilisation du connecteur, à cause de la généricité ! En attendant la correction, le code est sur une [branche](https://github.com/sebprunier/bonita-googleplus-connectors/tree/with-generics).

## L'intégration Continue avec Cloudbees

Le projet `bonita-googleplus-connectors` contient également des tests unitaires.

Afin de les exécuter, il est nécessaire de définir une propriété dont la valeur sera celle de l'[apiKey](https://code.google.com/apis/console) utilisée pour exécuter les tests. Cela évite de mettre en dur dans le code la valeur de ma clé. Chacun la sienne pour tester, rapport aux quotas :-)

{% highlight java %}
/**
 * Returns the apiKey used for testing. It must be set with a system property : <code>-DapiKey=XXXXXX</code>.
 *
 * @return the ApiKey used for testing.
 */
protected final String getApiKey() {
    return System.getProperty("apiKey");
}
{% endhighlight %}

Histoire que les tests servent vraiment à quelque chose, ils sont exécutés de manière continue grâce à la plateforme [DEV@Cloud](http://www.cloudbees.com/dev.cb) mise à disposition par [CloudBees](http://www.cloudbees.com/).

![bonita-gplus-cloudbees.png]({{ site.url }}/assets/2012-02-07/bonita-gplus-cloudbees.png)

## Exemple d'intégration de jsTree pour l'affichage des résutlats

Intégrer des API externes au sein de processus est très simple à faire avec Bonita !

Des exemples sont déjà disponibles sur le web, notamment une [contribution](http://www.bonitasoft.org/exchange/extension_view.php?eid=196) illustrant l'intégration de GoogleMaps dans une page de formulaire : [http://www.bonitasoft.org/blog/uncategorized/add-your-own-widgets-to-your-forms-example-1-google-maps-api/](http://www.bonitasoft.org/blog/uncategorized/add-your-own-widgets-to-your-forms-example-1-google-maps-api/)

Afin de rendre mon exemple un peu plus intéressant, j'ai intégré le plugin jQuery : [jsTree](http://www.jstree.com/).

Je ferai bientôt un article plus détaillé qui présente la procédure pas-à-pas. Dans les grandes lignes, il faut :

* Intégrer les fichiers JS et les thèmes CSS dans les ressources de l'application :

![]({{ site.url }}/assets/2012-02-07/bonita-gplus-jstree-import.png)

* Personnaliser le fichier BonitaApplication.html afin de faire le lien vers les fichiers JS et CSS précédemment ajoutés :

{% highlight html %}
<!-- JS-TREE-->
<script type="text/javascript" language="javascript" src="js/jquery.js"></script>
<script type="text/javascript" language="javascript" src="js/jquery.cookie.js"></script>
<script type="text/javascript" language="javascript" src="js/jquery.hotkeys.js"></script>
<script type="text/javascript" language="javascript" src="js/jquery.jstree.js"></script>
<link href="themes/default/style.css" rel="stylesheet" type="text/css"/>
{% endhighlight %}

* Ajouter un composant HTML permettant l'affichage de l'arbre dans le formulaire Bonita :

![]({{ site.url }}/assets/2012-02-07/bonita-gplus-htmljstree.png)

* Spécifier le contenu HTML et JavaScript nécessaire à l'affichage de l'arbre (nous verrons par la suite comment la variable "out_jstreeXmlData" est alimentée) :

{% highlight groovy %}
'''
<div id="jstreedata" style="text-align:left;" />
<script language="javascript">
$(function () {
    $("#jstreedata").jstree({
        "xml_data" : { "data" : "
'''
+ out_jstreeXmlData +
'''
"},
        "plugins" : [ "themes", "xml_data", "ui" ]
    });
});
</script>
'''
{% endhighlight %}

Et voilà le résultat pour l'appel au connecteur People:Get :

![]({{ site.url }}/assets/2012-02-07/bonita-gplus-peopleget.png)

## Construction des données jsTree avec le MarkupBuilder Groovy

Le composant JsTree peut être alimenté avec des données XML, respectant un [format bien défini](http://www.jstree.com/documentation/xml_data).

Le connecteur People:Get retourne un objet java de type _com.google.api.services.plus.model.Person_. Il est possible de créer facilement en Groovy le flux XML permettant d'alimenter jsTree, à l'aide d'un [MarkupBuilder](http://groovy.codehaus.org/Creating+XML+using+Groovy).

C'est comme cela qu'est alimentée la variable "out_jstreeXmlData" :

{% highlight groovy %}
import groovy.xml.MarkupBuilder
def writer = new StringWriter()
def xml = new MarkupBuilder(writer)
xml.root() {
    item(id:result.getId(), parent_id:'0', state:'open') {
        content {
            name(result.getDisplayName())
        }
    }
    item(id:"kind-"+result.getId(), parent_id:result.getId()) {
        content {
            name('kind : ' + result.getKind())
        }
    }
    item(id:"id-"+result.getId(), parent_id:result.getId()) {
        content {
            name('id : ' + result.getId())
        }
    }
    item(id:"gender-"+result.getId(), parent_id:result.getId()) {
        content {
            name('gender : ' + result.getGender())
        }
    }
}
return writer.toString()
{% endhighlight %}

## Exemple de traitement des résultats avec des closures Groovy

Un dernier petit bout de code pour la route !

Un simple exemple d'utilisation d'une Closure en Groovy, afin de créer une liste de String (des prénoms+nom en l'occurrence) à  partir d'une liste l'objets _com.google.api.services.plus.model.Person _(issus de l'exécution du connecteur People:ListByActivity par exemple) :

{% highlight groovy %}
def displayNames = []
def clos = {
    it.getDisplayName()
}
result.collect( displayNames, clos )
return displayNames
{% endhighlight %}

Cela permet d'alimenter un composant de type SelectBox dans les formulaires (en dessous de l'arbre jsTree dans la capture d'écran ci-dessous) :

![]({{ site.url }}/assets/2012-02-07/bonita-gplus-peoplelistbyactivity.png)

## Conclusion

Les connecteurs Google+ auront un réel intérêt quand l'API permettra de poster des activités ! Pour le moment les possibilités restent limitées. Je vous invite tout de même à tester ces connecteurs, à regarder l'exemple et à me faire des retours, histoire d'améliorer ce qui peut l'être !

Quelques liens utiles :

*   Connecteurs : [http://www.bonitasoft.org/exchange/extension_view.php?eid=283](http://www.bonitasoft.org/exchange/extension_view.php?eid=283)
*   Exemple : [http://www.bonitasoft.org/exchange/extension_view.php?eid=284](http://www.bonitasoft.org/exchange/extension_view.php?eid=284)
*   Code source sous GitHub : [https://github.com/sebprunier/bonita-googleplus-connectors](https://github.com/sebprunier/bonita-googleplus-connectors)

Les connecteurs et les exemples ont été réalisés avec Bonita Open Solution 5.6.1.
