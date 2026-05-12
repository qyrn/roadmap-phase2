# Authentification

## C'est quoi
Mécanisme qui permet de vérifier l'identité d'un utilisateur avant de lui donner accès à une ressource.

## Comment ça marche

### Les 3 facteurs principaux
- Knowledge factors : quelque chose que tu connais (mot de passe, réponse à une question de sécurité)
- Possession factors : quelque chose que tu as (objet physique, token de sécurité)
- Inherence factors : quelque chose que tu es ou que tu fais (biométrie, patterns de comportement)

## Failles / Attaques

### Username enumeration
Quand on trouve un username mais avec un mot de passe incorrect, ou un username déjà pris, ça réduit considérablement le temps pour bruteforce car on peut créer une liste de usernames à bruteforce très rapidement.

### Indicateurs exploitables en bruteforce
- Error messages : parfois le site affiche un message différent si le username est faux ou si juste le mot de passe est incorrect. Si le message change, même légèrement, ça peut indiquer que le username existe
- Response times : si une requête prend un peu plus longtemps, ça peut vouloir dire que le site a fait une vérification supplémentaire (comparer le mot de passe parce que le username était valide). Ça peut révéler qu'un compte existe
- Status codes : si une tentative retourne un code HTTP différent des autres, ça peut indiquer que le username ou le mot de passe est correct

## Détection / Défense
- Messages d'erreur génériques (ne pas distinguer "username inconnu" et "mot de passe incorrect")
- Temps de réponse constants
- Rate limiting / lockout
- Mnémo : 401 c'est avant l'auth, 403 c'est après l'auth

## Commandes / Payloads
Workflow Intruder pour bruteforce (PortSwigger lab) :
1. Intercepter le POST /login avec Burp
2. Envoyer le body dans l'Intruder
3. Surligner le username, coller la liste d'usernames en payload
4. Lancer une sniper attack
5. Repérer l'username avec une length différente
6. Refaire la même chose en surlignant le password, avec la liste de passwords

## Labs faits
- Username enumeration via different responses (07/05) : sniper attack sur username via la liste PortSwigger, repéré une length différente, puis sniper attack sur password avec username trouvé

## Sources
- PortSwigger Academy
