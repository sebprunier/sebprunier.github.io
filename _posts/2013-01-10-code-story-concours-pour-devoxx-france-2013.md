---
layout: post
title: Code Story - Concours pour Devoxx France 2013
date: 2013-01-10 11:21:46.000000000 +01:00
categories:
- Code
tags:
- CodeStory
- DevoxxFR
- heroku
- java
- jetty
status: publish
type: post
published: true
author:
comments: true
---

Bonne nouvelle en ce début d'année : l'équipe Code Story réitère son concours de code pour l'édition 2013 de Devoxx France !

J'ai envie de dire : "Cool, un jeu" ! Donc forcément, je participe ! :-)

C'est l'occasion de tester un certain nombre de choses que j'avais envie de découvrir depuis un moment.

C'est aussi et surtout l'occasion de s'amuser un peu !

L'objectif de cette première phase est de mettre à disposition un serveur web publique qui devra répondre aux différentes questions posées par l'équipe de Code Story. La première question posée est simple :

> L’url `http://(serveur)/?q=Quelle+est+ton+adresse+email` de votre serveur doit répondre par un document ne contenant que votre adresse email.

Hâtez-vous, la phase de pré-sélection a déjà démarré !! Tous les détails sont donnés sur le site de Code Story : [code-story.net](http://code-story.net/)

![Code_Story_Logo]({{ site.url }}/assets/2013-01-10/code_story_logo.png)


## Mes premiers choix techniques

Mes premiers choix techniques sont totalement arbitraires :-) Pour autant je pars sur des choses assez simples pour commencer !

**J'ai besoin de développer un serveur web pour répondre aux questions**

[Xebia](http://www.xebia.fr/) propose tout un tas d'exemples de code permettant de démarrer rapidement un simple serveur web : [https://github.com/xebia-france/extreme_startup_servers](https://github.com/xebia-france/extreme_startup_servers)

Je pense partir sur le langage Java (très original hein !), je choisis donc Jetty. C'est très simple, quelques lignes de code vont suffir pour démarrer le concours !

**J'ai besoin que ce server web soit publique**

Je me rappelle la [session de David Gageot au Jugsummercamp 2013](https://sites.google.com/site/jugsummercamp/presentations#TOC-Du-legacy-au-Cloud-en-moins-d-une-heure---David-Gageot---Tools-in-Action-55-min-), qui a utilisé la plateforme [Heroku](http://www.heroku.com/) pour déployer rapidement une petite application web dans le cloud.

Cette session m'avait donné envie de tester Heroku, c'est donc l'occasion !

**Je pars donc sur un programme Java utilisant un serveur web Jetty, qui sera déployé sur Heroku.**

## Une façon de résoudre le problème

Voilà mon tout premier morceau de code permettant de répondre à la première question posée.

A noter l'utilisation d'une variable d'environnement PORT (qui sera mise à disposition par Heroku) pour le démarrage du serveur.

{% highlight java %}
public class WebServer {

    private Server server;

    public WebServer(int port) {
        this.server = new Server(port);
        server.setHandler(new WebServerHandler());
    }

    public void start() throws Exception {
        this.server.start();
    }

    public void startAndWait() throws Exception {
        start();
        this.server.join();
    }

    public void stop() throws Exception {
        this.server.stop();
    }

    private static class WebServerHandler extends AbstractHandler {

        private static final String QUERY_PARAMETER = "q";

        @Override
        public void handle(
            String target, 
            HttpServletRequest request, 
            HttpServletResponse response, 
            int dispatch
        ) throws IOException, ServletException {
        
            String question = request.getParameter(QUERY_PARAMETER);
            String answer = "Bad question ...";
            if ("Quelle est ton adresse email".equals(question)) {
                answer = "sebastien.prunier@gmail.com";
            }
            response.setContentType("text/plain");
            response.setStatus(HttpServletResponse.SC_OK);
            PrintWriter writer = response.getWriter();
            writer.println(answer);
            writer.close();
        }
    }

    public static void main(String[] args) throws Exception {
        int port = Integer.parseInt(System.getenv("PORT"));
        WebServer webServer = new WebServer(port);
        webServer.startAndWait();
    }
}
{% endhighlight %}

## N'oublions pas les tests !

Je choisis de faire simple pour l'instant d'un point de vue des tests. Je me contente de tests de validation globaux, i.e je vérifie que les réponses de mon serveur web sont correctes vis-à-vis des questions envoyées par l'équipe CodeStory.

{% highlight java %}
public class WebServerTest {

    private WebServer webServer;
    private int port;

    @Before
    public void setUp() throws Exception {
        this.port = new Random().nextInt(65535);
        this.webServer = new WebServer(port);
        this.webServer.start();
    }

    @After
    public void tearDown() throws Exception {
        this.webServer.stop();
    }

    @Test
    public void question1_should_return_email_address() throws Exception {
        URL url = new URL("http://localhost:" + port + "/?q=Quelle+est+ton+adresse+email");
        HttpURLConnection httpConn = (HttpURLConnection) url.openConnection();
        Assert.assertEquals(200, httpConn.getResponseCode());

        BufferedReader reader = new BufferedReader(
            new InputStreamReader(httpConn.getInputStream())
        );
        Assert.assertEquals("sebastien.prunier@gmail.com", reader.readLine());
        reader.close();
    }
}
{% endhighlight %}

## Déploiement sur Heroku

Déployer ma petite application Java sur Heroku a été très facile. Il m'a suffit de suivre la documentation, que je trouve vraiment bienfaite : [https://devcenter.heroku.com/categories/java](https://devcenter.heroku.com/categories/java)

Je ne vais donc pas ré-écrire ici la même chose que dans la doc, ca serait inutile :-)

Pour résumer :

* j'ai installé la boîte à outils fournie par Heroku ([heroku toolbelt](https://toolbelt.heroku.com/))
* mon code source est héberger sur un repo Git.
* j'ai une branche remote nommée "heroku" qui référence un repo Git correspondant à mon application sur Heroku
* quand j'effectue un push de mon code sur cette branche, Heroku lance un build de mon application. Il détecte que c'est une application java et lance la commande maven suivante :

`mvn -B -DskipTests=true clean install`

* il démarre ensuite mon application avec la commande que j'ai définie dans un fichier Procfile à la racine de mon projet :

`web: java -cp target/classes:target/dependency/* com.sebprunier.devoxxfr.WebServer`

* comme il s'agit d'une application web (commande préfixée par web:), Heroku me met à disposition la variable d'environnement PORT, qui est utilisée par mon serveur pour démarrer.

## Pour conclure

Voilà, je n'en dis pas plus jusqu'à la fin du concours :-) Prochain article sur le sujet quand la première phase sera terminée !

En attendant, amusez-vous bien !