# JWT (JSON Web Token)

## C'est quoi
Token utilisé pour l'authentification et le contrôle de session. C'est ce que le serveur te renvoie quand tu te connectes pour te garder connecté. Peut expirer (`exp`) ou non.

## Comment ça marche
3 parties séparées par des points : `header.payload.signature`
- Header : `{ "alg": "HS256", "typ": "JWT" }`
- Payload : `{ "user": "wiener", "isAdmin": true }`
- Signature : générée par le serveur pour vérifier l'intégrité

Header et payload encodés en Base64URL (pas chiffrés). Signature seule garantit que le token n'a pas été modifié.

## Failles / Attaques

### alg: none
Certaines implémentations acceptent un JWT sans signature si `"alg": "none"` dans le header.

### Algo confusion / key confusion
Serveur attend `RS256`, l'attaquant change en `HS256` et utilise la clé publique comme secret HMAC pour signer un faux token.

### Bruteforce de clé HMAC
Si secret faible (`secret`, `admin`, `1234`), bruteforce offline avec Hashcat ou John the Ripper.

## Détection / Défense
- Stocker dans cookies HttpOnly + SameSite=Lax/Strict (pas en localStorage)
- Côté serveur : utiliser `verify()` et pas juste `decode()`
- Surveiller : tokens avec `alg:none`, changements d'algo inattendus, claims anormaux

## Labs faits
- JWT auth bypass via unverified signature (09/05) : décodé token avec Inspector Burp, changé "wiener" → "administrator" dans le sub, envoyé requête /admin/delete?username=carlos
- JWT auth bypass via flawed signature verification (09/05) : modifié alg en "none", supprimé la signature après le point

## Sources
- PortSwigger Academy : JWT vulnerabilities
- jwt.io pour décoder
- Extension Burp : JWT Editor