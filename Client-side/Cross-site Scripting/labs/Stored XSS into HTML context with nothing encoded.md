## Intro

Le lab contient un vulnérabilité de type XSS stockée dans la fonctionnalité de commentaire.
Pour résoudre le lab, il faut appeler la fonction alert() quand l'article de blog est consulté.
## Exploit

Se rendre sur un article de blog et entrer le payload suivant dans la section `Leave a comment`

`<script>alert(document.domain);</script>`

![[xss-stockée.png]]

Maintenant, la fonction alert() se déclenche bien quand on revient sur l'article de blog.

![[xss-stockée2.png]]