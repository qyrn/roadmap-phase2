# HTTP

## C'est quoi
Protocole de communication entre un client et un serveur, basé sur des requêtes et des réponses.

## Comment ça marche

### Structure d'une requête
Method -> Path -> Protocol Version -> Headers -> Ligne vide -> Body (optionnel)

### Structure d'une réponse
Protocol Version -> Status code -> Status message -> Headers -> Ligne vide -> Body

### Méthodes HTTP
- GET : chercher une ressource
- POST : envoyer des données au serveur pour créer/modifier quelque chose
- OPTIONS
- HEAD

GET met les params dans l'URL, POST les met dans le body (après la ligne vide qui sépare headers et contenu, donc invisible dans l'URL).

### Status codes
- 2xx : OK
- 3xx : REDIRECT
- 4xx : ERREUR CLIENT
- 5xx : ERREUR SERVEUR

Mnémo : 401 c'est avant l'auth, 403 c'est après l'auth.

## Failles / Attaques
- HTTP en clair : interception possible (MITM sur Wi-Fi public, proxy)

## Détection / Défense
- Forcer HTTPS via HSTS : le navigateur garde en mémoire quels endroits exigent d'être accompagnés par une connexion sécurisée et refuse les versions non sécurisées
- HSTS preload list : liste d'endroits connus comme sécurisés avant même la première visite, pour éviter les problèmes dès le départ
- SSL strip : un faux interprète se met entre le client et le serveur. Avec le serveur il parle en HTTPS, avec la victime il parle en HTTP pour intercepter et modifier la conversation

### Security headers
- `X-Frame-Options` : empêche que ton site soit chargé dans une iframe sur un autre site. Absence = clickjacking possible.
- `Content-Security-Policy` (CSP) : restreint d'où peuvent venir les scripts, styles, images. Absence = XSS beaucoup plus facile à exploiter.
- `Strict-Transport-Security` (HSTS) : force le navigateur à utiliser HTTPS. Absence = downgrade attack possible.
- `X-Content-Type-Options: nosniff` : empêche le navigateur de "deviner" le type de fichier. Absence = MIME sniffing attacks.

## Commandes / Payloads

Exemple GET :
```
GET /search?q=chaussure&page=2 HTTP/1.1
Host: amazon.fr
```

Exemple POST :
```
POST /login HTTP/1.1
Host: amazon.fr
Content-Type: application/x-www-form-urlencoded
Content-Length: 27

username=qyrn&password=lol
```

## Labs faits

## Sources
- PortSwigger Academy
