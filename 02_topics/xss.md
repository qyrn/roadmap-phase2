# XSS (Cross-Site Scripting)

## C'est quoi
Un attaquant injecte du JavaScript dans une page (champ commentaire, URL mal filtrée, etc.), qui s'exécute dans le navigateur de la victime.

## Comment ça marche

### Le DOM
Le DOM est la représentation du HTML en mémoire sous forme d'objets manipulables par JavaScript. C'est un arbre :
- `document` = la racine
- les éléments HTML = des nodes (Element nodes)
- le texte = des Text nodes
- les attributs = des Attribute nodes
- les commentaires HTML = des Comment nodes

JS peut lire, modifier, supprimer ou créer des nodes dans cet arbre.

Accès courants :
- `document` = toute la page sous forme d'objet manipulable par JS (HTMLDocument)
- `document.title` = le titre de l'onglet
- `document.body` = le body de la page
- `document.head` = le head de la page
- `document.URL` = l'URL de la page
- `document.cookie` = les cookies accessibles en JS
- `document.querySelector('h1')` = le premier `<h1>`
- `document.querySelectorAll('a')` = tous les liens `<a>`
- `document.getElementById('mp-tfa')` = l'élément avec l'id `mp-tfa`

Créer un élément :
```js
const div = document.createElement('div')
document.body.appendChild(div)
```

C'est aussi la base du DOM-based XSS : un attaquant peut injecter un `<script>` ou injecter du JS dans un attribut HTML.

### La Fetch API
Donne une interface pour exécuter des requêtes HTTP et traiter les responses. Par défaut, `fetch()` effectue une requête GET.

`fetch()` retourne une Promise et pas directement une Response, donc on utilise `.then()` pour attendre la réponse et la déballer.

Si `mode: 'no-cors'` est utilisé, la méthode doit être uniquement GET, POST ou HEAD.

Propriétés (`status`, `url`, `headers`) lues directement. Fonctions (`json()`) appelées, retournent aussi une Promise.

Si le fetch échoue (CORS, réseau coupé, URL invalide), la Promise est rejected, on utilise `.catch()`.

## Failles / Attaques

### Ce qu'un attaquant fait via le DOM
- lire le contenu de la page
- modifier le HTML
- modifier des formulaires
- envoyer des requêtes avec `fetch()`
- voler des données
- faire des actions au nom de la victime
- afficher du faux contenu (phishing)
- lire les cookies non HttpOnly

### Pourquoi `document.cookie` est une cible
Les cookies peuvent contenir l'identifiant de session. Si un attaquant vole ce cookie :
- il peut réutiliser la session de la victime
- il profite d'une session déjà authentifiée
- la MFA/A2F a déjà été validée au moment de la vraie connexion

L'attaquant ne casse pas la MFA : il utilise directement une session déjà ouverte.

### SSL strip
Comme si un faux interprète se mettait entre le client et un serveur. Avec le serveur, il parle correctement et de manière sécurisée. Avec la victime, il parle en clair pour pouvoir écouter et modifier toute la conversation sans qu'elle s'en rende compte. Le serveur pense être en HTTPS, mais la victime reste en HTTP.

## Détection / Défense

### HttpOnly
Bloque `document.cookie`. Réduit fortement l'impact d'un XSS sur le vol de session.

### Spec Fetch (limitation navigateur)
Le navigateur ne laisse lire que certains headers par défaut (CORS-safelisted headers). Pour exposer d'autres headers à JS, le serveur doit utiliser `Access-Control-Expose-Headers`.

`Set-Cookie` est interdit d'accès en JavaScript même si le header apparaît dans l'onglet Network. Ce blocage vient directement de la spec Fetch ("forbidden response header names"), ce n'est pas une option de configuration serveur. Même avec `Access-Control-Expose-Headers: Set-Cookie`, le navigateur refusera toujours de l'exposer à JS.

Conséquence : un attaquant XSS ne peut pas voler un cookie HttpOnly avec fetch parce que JS ne peut pas lire `Set-Cookie`. HttpOnly et la protection Fetch travaillent ensemble :
- HttpOnly bloque `document.cookie`
- La spec Fetch bloque la lecture de `Set-Cookie` dans les responses

Un payload JS qui essaye de lire `Set-Cookie` est forcément raté.

### HSTS
Le navigateur garde en mémoire quels endroits exigent absolument d'être accompagnés par une connexion sécurisée. Une fois qu'il le sait, il refuse catégoriquement d'aller vers une version non sécurisée ou simulée de cet endroit, peu importe ce qu'un attaquant essaie de faire.

### HSTS preload list
Le navigateur peut déjà avoir une liste d'endroits connus comme sécurisés avant même la première visite, pour éviter le SSL strip dès le départ.

## Commandes / Payloads

### Pattern fetch classique
```js
fetch('https://site.com')
  .then(r => {
    console.log(r.status);
    return r.json();
  })
  .then(data => {
    console.log(data);
  })
  .catch(err => {
    console.log(err);
  });
```

### DevTools
- `$0` : élément sélectionné dans l'inspecteur, réutilisable dans la console
- `document.querySelector('h1')` en pratique pour récupérer un élément

## Labs faits
- Test Fetch API depuis clubcine.xyz vers GitHub qyrn (10-12/05) : le header `Set-Cookie` apparaissait dans Network mais pas dans `r.headers` côté JavaScript, confirmant le blocage par la spec Fetch

## Sources
