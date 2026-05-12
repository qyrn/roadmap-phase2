# Burp Suite

## C'est quoi
Suite d'outils pour tester la sécurité des applications web. Permet d'intercepter, modifier et rejouer le trafic HTTP/WebSocket entre le navigateur et le serveur.

## Comment ça marche

### Burp Proxy
Opère en tant que web proxy server entre le navigateur et l'application cible. Permet d'intercepter, inspecter et modifier le trafic qui passe dans les deux directions (serveur et application), y compris en HTTPS. Peut être utilisé pour envoyer des requêtes aux autres outils de Burp.

### Burp Repeater
Outil qui rend possible de modifier et envoyer un message HTTP ou WebSocket en boucle.

Utilisations :
- Envoyer des requêtes avec des paramètres différents pour tester les input-based vulnerabilities
- Envoyer une série de requêtes HTTP dans une séquence spécifique pour tester les vulnérabilités en plusieurs procédés ou celles qui se basent sur la manipulation de l'état de connexion
- Vérifier manuellement les problèmes que Burp Scanner a reportés

### Burp Intruder
Automatisation de payloads sur des positions dans la requête. Mode sniper utilisé pour bruteforce un champ à la fois.

## Failles / Attaques

## Détection / Défense

## Commandes / Payloads
- CTRL+U dans Repeater : URL encode la sélection
- Inspector : décode les JWT directement dans la requête

## Labs faits
- SQLi via Repeater (08/05) : interception d'une requête de changement de catégorie, envoi dans Repeater, modification des params avec `'+OR+1=1--` puis CTRL+U
- JWT bypass via unverified signature (09/05) : envoi dans Repeater, décodage du token avec Inspector, modification du `sub`
- Username + password bruteforce via Intruder (07/05) : sniper attack sur username puis sur password

## Sources
- PortSwigger Academy
