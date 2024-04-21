# XSS réfléchie

## Qu'est-ce qu'une XSS réfléchie ?

Cela arrive lorsqu'une application reçoit des données dans une requête HTTP et inclut ces données dans la réponse immédiate de manière non sécurisée.

Exemple avec un site web qui fournit une fonction de recherche et reçoit le terme de la recherche entré par l'utilisateur dans un paramètre URL.

`https://insecure-website.com/search?term=gift`

L'application répercute le terme recherché fourni dans la réponse à cette URL

`<p>You searched for: gift</p>`

En supposant que l'application n'effectue aucun traitement sur les données, un attaquant peut contruire une attaque

`https://insecure-website.com/search?term=<script>/*+Bad+stuff+here...+*/</script>`

Ce qui donne

`<p>You searched for: <script>/* Bad stuff here... */</script></p>`

Si un autre utilisateur  de l'application effectue une demande de l'URL de l'attaquant, alors le script fourni par l'attaquant va s'exécuter dans le navigateur de l'utilisateur victime dans le contexte de leur session.
## Quel est l'impact des attaques XSS réfléchie ?

Si un attaquant peut contrôler un script qui est exécuté dans le navigateur de la victime, alors il peut généralement compromettre totalement cet utilisateur :

- Effectuer toute action au sein de l'application que l'utilisateur peut réaliser ;
- Visualiser toute information que l'utilisateur est capable de voir ;
- Modifier toute information que l'utilisateur est capable de modifier ;
- Initier des intéractions avec d'autres utilisateurs de l'application, y compris des attaques malveillantes qui sembleront provenir de l'utilisateur victime initial.

Il existe plusieurs moyens par lesquels un attaquant peut inciter un utilisateur victime à effectuer une requête qu'il contrôle, afin de mener une attaque XSS réfléchie. Cela inclut de placer des liens sur un site web contrôlé par l'attaquant, ou sur un autre site web qui permet de générer de contenu, ou encore en envoyant un lien dans un mail, tweet ou autre message.

L'attaque pourrait permettre de viser directement un utilisateur connu, ou pourrait être une attaque contre tous les utilisateurs de l'application.

La nécessité d'un mécanisme de diffusion externe pour l'attaque signifie que l'impact de la XSS réfléchie est généralement moins grave que celui de la XSS stored, où une attaque autonome peut-être diffusée au sein de l'application vulnérable elle-même.
## La XSS réfléchie dans différents contextes

Il y a plusieurs différentes catégories d'XSS réfléchie. L'emplacement des données reflétées dans la réponse de l'application détermine quel type de payload est requis pour l'exploiter et peut aussi affecter l'impact de la vulnérabilité.

De plus, si l'application effectue de la validation ou autre traitement sur les données soumises avant qu'elles ne soient reflétées, cela affectera généralement quel type de payload XSS est requis.
## Comment trouver et tester les vulnérabilités XSS réfléchies ?

Le test manuel comporte les étapes suivantes :

- **Tester chaque point d'entrée.** Tester séparément chaque point d'entrée des données dans les requêtes HTTP de l'application. Il s'agit notamment des paramètres ou autres données contenu dans la chaîne de requête de l'URL et dans le corps du message, ainsi que du chemin d'accès au fichier de l'URL. Il inclut également aussi les en-têtes HTTP, bien qu'un comportement de type XSS qui ne peut être déclenché que par certains en-têtes HTTP puisse ne pas être exploitable en pratique.

- **Soumettre des valeurs alphanumériques aléatoires**. Pour chaque point d'entrée, soumettre une valeur aléatoire unique et déterminer si la valeur est reflétée dans la réponse. La valeur doit être conçue pour survivre à la plupart des validations d'entrées, elle doit donc être assez courte et ne contenir que des caractères alphanumériques. Mais elle doit être assez longue pour éviter des correspondances accidentelles au sein de la réponse. Une valeur alphanumérique aléatoire autour de 8 caractères est idéal

- **Déterminer le contexte de la réfléxion**. Pour chaque endroit de la réponse où la valeur aléatoire est reflétée, déterminer son contexte. Il peut s'agit de texte entre des balises HTML, d'un attribut de balise qui peut être cité, dans une chaîne JavaScript, etc.

- **Tester un payload candidat**. En fonction du contexte de la réfléxion, tester un payload XSS candidat initial qui déclenchera l'exécution de JavaScript si c'est reflété sans modification dans la réponse. Le moyen le plus facile pour tester des payloads et d'envoyer la requête à Burp Repeater, modifier la requête pour insérer le payload candidat, émettre la demande et alors examiner la réponse voir pour si le payload à fonctionné. Un moyen efficace est de laisser la valeur aléatoire originale dans la requête et de placer le payload XSS candidat avant ou après celle-ci pour facilement repérer où c'est reflété.

- **Tester des payloads alternatifs**. Si le payload XSS candidat a été modifié par l'application ou complètement bloqué, alors vous devrez tester des payloads alternatifs et d'autres techniques susceptibles de mener à une attaque XSS efficace en fonction du contexte de la réflexion et du type de validation d'entrée qui est effectuée.

- **Tester l'attaque dans un navigateur**. Enfin, si vous parvenez à trouver un payload fonctionnel dans Burp Repeater, transférez l'attaque dans un véritable navigateur (en collant l'URL dans la barre d'adresse ou en modifiant la requête dans la vue d'interception de Burp Proxy) et vérifier que le JavaScript est bien exécuté. Souvent, il est préférable d'exécuter du JavaScript simple comme `alert(document.domain)`qui déclenchera une popup visible dans le navigateur si l'attaque réussit.
## FAQ

### Quelle est la différence entre une XSS réfléchie et une XSS stockée ?

Une XSS réfléchie se produit lorsqu'une application prend une entrée d'une requête HTTP et l'incorpore dans la réponse immédiate d'une manière non sécurisée.
Alors qu'avec une XSS stockée, l'application stocke l'entrée et l'incorpore dans une réponse ultérieure d'une manière non sécurisée.
### Quelle est la différence entre une XSS réfléchie et une self-XSS ?

La Self-XSS implique un comportement applicatif similaire à la XSS réfléchie ordinaire, mais, elle ne peut pas être déclenché de manière normale via une URL conçue ou une requête inter-domaine. La vulnérabilité n'est déclenchée que si la victime soumet elle-même le payload XSS à partir de son navigateur.

Réaliser une attaque de type self-XSS implique normalement l'utilisation de l'ingénieurie sociale pour amener la victime à coller dans son navigateur une entrée fournie par l'attaquant. En tant que tel, elle est généralement considéré comme un problème mineur de faible impact.
## Sources

- [Portswigger](https://portswigger.net/web-security/cross-site-scripting/reflected)