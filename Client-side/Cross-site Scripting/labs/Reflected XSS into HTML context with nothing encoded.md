## Intro

Le but de ce lab est de trouver une vulnérabilité XSS reflected dans la fonction de recherche et de faire pop une alert().
## Exploit

Il suffit d'entrer `<script>alert();</script>` dans la fonction de recherche pour résoudre le lab.

![](https://github.com/0xLuks/portswigger-notes/blob/main/Client-side/Cross-site%20Scripting/img/xss-reflechie.png)
