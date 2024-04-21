# XSS stored

## Qu'est-ce qu'une XSS stockée ?

La XSS stockée (aussi connue comme XSS de second ordre ou XSS persistente) se produit lorsque l'application reçoit des données d'une source non fiable et inclut ces données dans ses réponses HTTP ultérieures de manière non sécurisé.

Supposons qu'un site web permette aux utilisateurs de soumettre des commentaires sur les article de blog, qui sont ensuite affichés aux autres utilisateurs.

Les utilisateurs soumettent leurs commentaires en utilisant une requête HTTP du type suivant :

```html
POST /post/comment HTTP/1.1
Host: vulnerable-website.com
Content-Length: 100

postId=3&comment=This+post+was+extremely+helpful.&name=Carlos+Montoya&email=carlos%40normal-user.net
```

Après l'envoi de ce commentaire, tout utilisateur visitant l'article de blog recevra le message suivant dans la réponse de l'application.

```html
<p>This post was extremely helpful.</p>
```

En supposant que l'application ne réalise aucun autre traitement des données, un attaquant peut soumettre un commentaire malveillant comme celui-ci :

```js
<script>/* Bad stuff here... */</script>
```

Dans la requête de l'attaquant, ce commentaire doit être URL-encodé comme ceci

```js
comment=%3Cscript%3E%2F*%2BBad%2Bstuff%2Bhere...%2B*%2F%3C%2Fscript%3E
```

Tout utilisateur qui visite l'article de blog recevra maintenant le message suivant dans la réponse de l'application.

```html
<p><script>/* Bad stuff here... */</script></p>
```

Le script fourni par l'attaquant s'exécute alors dans le navigateur de l'utilisateur victime, dans le contexte de sa session avec l'application.
## Quel est l'impact des attaques XSS stockée ?

Si un attaquant peut contrôler un script exécuté dans le navigateur de la victime, il peut généralement compromettre totalement cet utilisateur. L'attaquant peut effectuer n'importe laquelle des actions applicables à l'impact des [vulnérabilités XSS réfléchies](https://portswigger.net/web-security/cross-site-scripting/reflected).

En terme d'exploitabilité, la différence clé entre une XSS réfléchie et stockée est qu'une vulnérabilité XSS stockée permet des attaques qui sont auto-contenues au sein de l'application elle-même. L'attaquant n'a pas besoin de trouver un moyen externe d'inciter d'autres utilisateurs à réaliser une requête particulière contenant leur exploit. Au lieu de cela, l'attaquant place son exploit dans l'application elle-même et attend simplement que les utilisateurs le rencontrent.



## La XSS stockée dans différents contextes


## Comment trouver et tester les vulnérabilités XSS stockée ?


## Sources

- [Portswigger](https://portswigger.net/web-security/cross-site-scripting/stored)