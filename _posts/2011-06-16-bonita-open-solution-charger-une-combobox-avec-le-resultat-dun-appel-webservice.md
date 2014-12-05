---
layout: post
title: Bonita Open Solution - charger une Combobox avec le résultat d'un appel WebService
date: 2011-06-16 15:35:33.000000000 +02:00
categories:
- Tutoriels
tags:
- Bonita
- BPM
- SOAP
- WebService
status: publish
type: post
published: true
meta:
  _edit_last: '24226490'
author: 
---

Ce tutoriel illustre pas-à-pas une manière possible d'alimenter dynamiquement le contenu d'une liste déroulante (combobox proposant la liste des pays).

![bonita-objectif]({{ site.url }}/assets/2011-06-16/bonita-objectif.png)

La démo a été réalisée avec la version 5.4.2 de Bonita Open Solution et permet d'illustrer :
* l'utilisation du connecteur WebService de Bonita,
* l'alimentation dynamique d'une combobox, dans un pageflow de saisie.

## Le WebService d'alimentation

Les valeurs de la combo sont issues du résultat d'un appel à un WebService fournissant la liste des pays : [www.webservicex.net](http://www.webservicex.net/country.asmx?WSDL)

L'appel au service "GetCountries" retourne la réponse SOAP suivante, que nous devrons ensuite traiter au niveau du connecteur WebService Bonita :

{% highlight xml %}
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:xsd="http://www.w3.org/2001/XMLSchema">
  <soap:Body>
    <GetCountriesResponse xmlns="http://www.webserviceX.NET">
      <GetCountriesResult>
        <![CDATA[
          <NewDataSet>
            <Table>
              <Name>Afghanistan, Islamic State of</Name>
            </Table>
            <Table>
              <Name>Albania</Name>
            </Table>
            ...
            <Table>
              <Name>Zimbabwe</Name>
            </Table>
          </NewDataSet>
        ]]>
      </GetCountriesResult>
    </GetCountriesResponse>
  </soap:Body>
</soap:Envelope>
{% endhighlight %}

*Remarque : le contenu de la réponse SOAP, donc la liste des pays, est au format XML (section CDATA).*

## Le processus de la démo

Le processus mis en oeuvre pour les besoins de la démo n'a - soyons clair - aucun intérêt fonctionnel ! :-) Il définit simplement deux activités permettant respectivement de :
* choisir un pays (ici intervient la liste déroulante dans le pageflow)
* afficher le pays choisi

![bonita-process]({{ site.url }}/assets/2011-06-16/bonita-process.png "bonita-process")

Deux variables sont rattachées au processus :

*   **listePays** : une variable "Objet Java" de type java.util.List, qui permet de stocker la liste des pays et qui servira à alimenter les valeurs de la combobox,
*   **paysChoisi** : une variable de type "Texte" pour stocker le pays sélectionné dans la combobox.

![bonita-variable-liste.png]({{ site.url }}/assets/2011-06-16/bonita-variable-liste.png)]

## La configuration du connecteur WebService

### Sélection du connecteur

Le connecteur utilisé est "Client Web Service", dans la catégorie "Web Services".

![bonita-connecteurws.png]({{ site.url }}/assets/2011-06-16/bonita-connecteurws.png)]

J'ai choisi d'ajouter le connecteur au niveau de l'activité "Choisir un pays". J'aurais pu tout aussi bien l'ajouter directement au niveau du processus. A vous de choisir en fonction de vos besoins.

Le connecteur est activé dès l'entrée dans l'activité, afin que la liste des pays soit disponible pour le pageflow de saisie :

![bonita-connecteurws-1.png]({{ site.url }}/assets/2011-06-16/bonita-connecteurws-1.png)]

### Configuration de l'appel WebService

La configuration de l'appel au webservice est basique. N'hésitez pas à utiliser un outil comme [soapUI](http://www.soapui.org/) pour vous aider à créer la requête SOAP :

{% highlight xml %}
<soapenv:Envelope
        xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/"
        xmlns:web="http://www.webserviceX.NET">
   <soapenv:Header/>
   <soapenv:Body>
      <web:GetCountries/>
   </soapenv:Body>
</soapenv:Envelope>
{% endhighlight %}

![bonita-connecteurws-2.png]({{ site.url }}/assets/2011-06-16/bonita-connecteurws-2.png)]

### Traitement du résultat

Il reste maintenant à traiter le résultat de l'appel, c'est à dire la réponse SOAP. L'objectif est de créer une liste de valeurs à partir de l'objet **"response"** (javax.xml.transform.dom.DOMSource), et d'affecter cette liste à la variable **"listePays"**.

La création de la liste de valeurs à partir de la réponse est l'étape sûrement la plus complexe. Il est nécessaire d'éditer une expression à l'aide de l'éditeur groovy, afin d'écrire le traitement. N'étant pas spécialiste groovy, je vous présente <span style="text-decoration:underline;">une</span> façon de faire, rapide et pas forcément très "safe" :

{% highlight java %}
// Récupération du contenu (liste des pays en XML)
Element root = ((DOMSource) response).getNode().getFirstChild();
NodeList result = root.getElementsByTagName("GetCountriesResult");
String xmlContent = result.item(0).getTextContent();

// Récuparation de la liste des pays (avec XPath)
DocumentBuilder builder = DocumentBuilderFactory.newInstance().newDocumentBuilder();
Document doc = builder.parse(new ByteArrayInputStream(xmlContent.getBytes()));
String expression = "//Name";
XPath xpath = XPathFactory.newInstance().newXPath();
NodeList nodes = (NodeList) xpath.evaluate(expression, doc, XPathConstants.NODESET);

// Alimentation de la liste (résultat en sortie)
List<String> names = new ArrayList<String>();
for (int i = 0; i < nodes.getLength(); i++) {
    names.add(nodes.item(i).getTextContent());
}
return names;
{% endhighlight %}

_(ah, ce bon vieux java comme dirait l'autre ... :-))_

L'affectation du résultat du traitement dans la variable "listePays" se fait graphiquement :

![bonita-connecteurws-3.png]({{ site.url }}/assets/2011-06-16/bonita-connecteurws-3.png)]

A ce stade, la configuration du connecteur est terminée ! Passons à l'étape de création du pageflow.

## L'alimentation de la combobox

Maintenant le reste, c'est du gâteau !

Au niveau du pageflow de saisie de l'activité "Choisir un pays", il suffit simplement d'alimenter la combobox avec la variable "listePays" et de stocker la valeur choisie dans "paysChoisi" :

![bonita-pageflow]({{ site.url }}/assets/2011-06-16/bonita-pageflow.png)

Cette variable peut être réutilisée par la suite, par exemple dans le pageflow lié à l'activité "Afficher le pays choisi" :

![bonita-pageflow-2]({{ site.url }}/assets/2011-06-16/bonita-pageflow-2.png)]

## Conclusion

On voit ici toute la puissance de Bonita Open Solution, notamment avec son approche "Connecteurs", mais également la relative complexité de l'utilisation du Client Web Service avec sa configuration assez bas-niveau (écriture de la requête SOAP, traitement de la réponse).

[Téléchargez la démo !](http://www.filedropper.com/processusdemocombows--10)
