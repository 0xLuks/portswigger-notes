# Qu'est-ce qu'une XSS ?

Le Cross-site scripting (XSS) est une vulnérabilité de sécurité web qui permet à un attaquant de compromettre les intéractions des utilisateurs avec une application vulnérable. Ca permet à un attaquant de contourner la politique de même origine (SOP) qui est conçue afin de séparer les sites web les uns des autres.

Les vulnérabilités XSS permettent à un pirate de se faire passer pour l'utilisateur victime, d'effectuer toutes les actions que l'utilisateur est en mesure de réaliser et d'accéder à toutes ses données.

Si l'utilisateur dispose d'accès privilégié sur l'application, l'attaquant est en mesure de prendre le contrôle total de toutes les fonctionnalités et données de l'application.
# Comment ça fonctionne ?

Une XSS consiste à manipuler un site web vulnérable dans le but de renvoyer du code javascript malveillant aux utilisateurs. Lorsque le code malveillant s'exécute dans le navigateur de la victime, l'attaque peut compromettre totalement son intéraction avec l'application.

![[xss-work.svg]]
# XSS - PoC

Il est possible de confirmer ce type de vulnérabilité en injectant un payload qui amène votre propre navigateur à exécuter du JavaScript arbitraire. Depuis longtemps, il est d'usage d'utiliser la fonction `alert()`, car elle est courte, inoffensive et difficile à manquer lorsqu'elle est appelée avec succès.

Cependant, il y a un petit problème avec Google Chrome, à partir de la version 92 (juillet 2021). Les iframes cross-origin ne peuvent pas appeler `alert()`. Dans ce cas, on peut utiliser la fonction `print()`.

Plus de détails dans cet [article](https://portswigger.net/research/alert-is-dead-long-live-print).
# Quels sont les types d'attaques XSS

Il existe trois principaux types d'attaque XSS :
- [XSS réfléchies](https://portswigger.net/web-security/cross-site-scripting#reflected-cross-site-scripting) où le script malveillant provient de la requête HTTP en cours ;
- [XSS stockées](https://portswigger.net/web-security/cross-site-scripting#stored-cross-site-scripting) où le script malveillant provient de la base de données du site web ;
- [XSS basé sur le DOM](https://portswigger.net/web-security/cross-site-scripting#dom-based-cross-site-scripting),  où la vulnérabilité dans le code client-side plutôt que dans le code côté serveur.
## Reflected XSS

La vulnérabilité XSS réfléchie est la variété la plus simple de cross-site scripting.

> [!IMPORTANT]  
> Cela arrive lorsqu'une application reçoit des données dans une requête HTTP et inclut ces données dans la réponse immédiate de manière non sécurisée.

Exemple :

```html
https://insecure-website.com/status?message=All+is+well. 
<p>Status: All is well.</p>
```

L'application n'effectue pas de traitement des données, donc un attaquant peut construire une attaque de cette façon

```js
https://insecure-website.com/status?message=<script>/*+Bad+stuff+here...+*/</script>
<p>Status: <script>/* Bad stuff here... */</script></p>
```

Si l'utilisateur visite l'URL construit par l'attaquant, le script de l'attaquant s'exécute dans le navigateur de la victime, dans le contexte de la session de l'utilisateur avec l'application. Il est donc possible d'effectuer toute action et récupérer n'importe quelles données de l'utilisateur.
## Stored XSS

La vulnérabilité XSS stockée (XSS persistente ou de second ordre) survient lorsqu'une application reçoit des données d'une source non fiable et inclut ces données dans ses réponses HTTP ultérieures d'une manière non sécurisée.

Les requêtes HTTP peuvent être soumises à l'application via des requêtes HTTP comme par exemple des commentaires sur un article de blog, des surnoms d'utilisateurs dans un salon de discussion ou des coordonnées sur une commande de client.

Dans d'autres cas, elles peuvent provenir d'autres sources non fiable type une application de webmail affichant des messages reçus par SMTP etc.

Exemple avec une simple application qui contient un dashboard permettant d'écrire des messages et les afficher aux autres utilisateurs.

```html
<p>Hello, this is my message!</p>
```

L'application n'effectue pas de traitement des données, de sorte qu'un pirate peut facilement envoyer un message qui attaque d'autres utilisateurs.

```html
<p><script>/* Bad stuff here... */</script></p>
```
## DOM-based XSS

Une XSS basé sur le DOM (DOM XSS) survient lorsqu'une application contient du JavaScript côté client qui traite des données provenant d'une source non fiable d'une manière non sécurisée, généralement en écrivant les données dans le DOM.

Dans cet exemple, une application utilise du JavaScript pour lire la valeur d'un champ de saisie et l'écrire dans un élément du code HTML :

```js
var search = document.getElementById('search').value;
var results = document.getElementById('results');
results.innerHTML = 'You searched for: ' + search;
```

Si l'attaquant peut contrôler la valeur du champ de saisie, il peut facilement construire une valeur malveillante qui va causer l'exécution de leur propre script.

```html
You searched for: <img src=1 onerror='/* Bad stuff here... */'>
```

Dans un cas typique, le champ d'entrée serait rempli à partir d'une partie de la requête HTTP, telle qu'un paramètre de chaîne de requête URL, permettant à l'attaquant de lancer une attaque à partir d'une URL malveillante (exactement comme une XSS reflected).
# Que faire avec une XSS ?

- Usurper l'utilisateur victime ou se faire passer pour lui ;
- Effectuer toute action que l'utilisateur est capable de réaliser ;
- Lire toutes les données où l'utilisateur a accès ;
- Capturer les informations d'identification de l'utilisateur ;
- Effectuer une défiguration du site web ;
- Injecter un trojan dans le site web.
# Impact des vulnérabilités XSS












