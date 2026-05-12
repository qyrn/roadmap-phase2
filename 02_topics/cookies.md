# Cookies

## C'est quoi
Données envoyées par le serveur (via `Set-Cookie`) et stockées par le navigateur, renvoyées automatiquement à chaque requête. Utilisés pour maintenir une session ou des préférences.

## Comment ça marche

### Cycle de session
Au login, le serveur renvoie un cookie de session dans la response. Sur les requêtes suivantes, le navigateur ajoute automatiquement `Cookie: user_session=...` dans le header pour garder l'utilisateur connecté. Si on supprime ce cookie et qu'on reload la page, on est déconnecté : la prochaine requête sera celle de se reconnecter et on recevra un nouveau cookie.

### Types
- Cookie de session : pas d'attribut `Expires` ou `Max-Age`, supprimé à la fermeture du navigateur
- Cookie persistant : possède `Expires` ou `Max-Age`, reste après fermeture du navigateur

### Attributs
- `Secure` : envoyé uniquement en HTTPS, protège contre l'interception réseau
- `HttpOnly` : pas accessible en JS via `document.cookie`, protection principale contre le vol via XSS
- `SameSite` : protection CSRF
  - `Strict` : envoyé uniquement même site
  - `Lax` : envoyé sur navigation normale (clic lien/redirection) mais pas sur requêtes cross-site type POST/fetch
  - `None` : envoyé partout, doit obligatoirement avoir `Secure`
- `Domain` : définit pour quels domaines le cookie est envoyé. Piège : si `Domain=example.com`, tous les sous-domaines reçoivent le cookie aussi
- `Path=/admin` : envoyé uniquement sur `/admin` et ses sous-répertoires, pas sur `/profile`

### Limites
- Taille max cookie : ~4 ko
- Nombre max cookies par domaine : ~50 selon les navigateurs modernes

### API
- `Set-Cookie` : header HTTP envoyé par le serveur pour stocker un cookie
- `document.cookie` : API JS côté client pour lire/modifier les cookies accessibles. Ne peut pas définir `HttpOnly`

## Failles / Attaques

### XSS (contre HttpOnly)
Un attaquant injecte du JavaScript dans une page (champ commentaire, URL mal filtrée), qui s'exécute dans le navigateur de la victime. Sans `HttpOnly`, le JS lit `document.cookie` et peut voler la session.

Vol de session via XSS : si le cookie contient l'identifiant de session, l'attaquant peut réutiliser la session de la victime (déjà authentifiée, MFA déjà validée). L'attaquant ne casse pas la MFA, il utilise directement une session ouverte.

### MITM (contre Secure)
Sans HTTPS, quelqu'un sur le réseau (Wi-Fi public, proxy) peut intercepter les cookies en clair.

### CSRF (contre SameSite)
Un site malveillant essaie de forcer le navigateur à envoyer une requête authentifiée à un autre site en utilisant les cookies de la victime.

## Détection / Défense
- `HttpOnly=true` : bloque `document.cookie`
- `Secure=true` : force HTTPS
- `SameSite=Lax` ou `Strict` : bloque CSRF
- La spec Fetch interdit aussi l'accès à `Set-Cookie` côté JS, même avec `Access-Control-Expose-Headers: Set-Cookie`. Un payload JS qui essaye de lire `Set-Cookie` via `r.headers` est forcément raté

## Commandes / Payloads
- `document.cookie` dans la console : renvoie uniquement les cookies qui ne sont pas en `HttpOnly`

## Labs faits
- Inspection cookies GitHub (07/05) : cookie `user_session` bien configuré `HttpOnly=true`, `Secure=true`, `SameSite=Lax`. `document.cookie` ne le renvoie pas, confirme que `HttpOnly` fait son travail
- Test Fetch API depuis clubcine.xyz vers GitHub (10-12/05) : header `Set-Cookie` visible dans l'onglet Network mais pas dans `r.headers` côté JS

## Sources
- PortSwigger Academy
