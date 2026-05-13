# Injection SQL

## C'est quoi
Vulnérabilité web qui autorise un attaquant à interférer avec les requêtes que l'application fait à sa base de données. Ça peut donner à l'attaquant la possibilité de voir des données normalement inaccessibles, comme les données des utilisateurs ou de l'application.

Dans beaucoup de cas, l'attaquant peut modifier ou supprimer ces données, causant des comportements inattendus de l'application. Dans certaines situations, ça peut monter crescendo vers des attaques sous-jacentes sur les serveurs backend, ou même des denial of service.

## Comment ça marche

### Retrieving data from other database tables (UNION)
Le mot-clé `UNION` en SQL permet, lors d'une SQL injection, de récupérer des données provenant d'autres tables. Un attaquant peut ajouter une deuxième requête `SELECT` à la requête originale pour afficher des informations sensibles comme des usernames ou des mots de passe.

Exemple : si un site affiche des produits d'une catégorie, un attaquant peut injecter une requête avec `UNION SELECT` pour forcer la base de données à retourner aussi les données de la table users.

### Logique de `OR 1=1 --`
Requête originale : `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

Construction de l'injection : `'` (finir la string) + espace + `OR 1=1` (rend la condition WHERE toujours vraie sur chaque ligne) + `--` (commente la suite de la commande, ici `AND released = 1`).

`OR 1=1` vs `OR 1=2` :
- `1=1` toujours vrai donc ramène tous les produits (toutes les catégories)
- `1=2` toujours faux donc ne ramène que les lignes qui matchent la première condition (ici `category = 'Gifts'`)

Exemple avec la requête `WHERE category = 'Gifts' OR 1=2` :
- Ligne 1 (Gifts) : première condition vraie : match
- Ligne 2 (Books) : première fausse, `1=2` fausse aussi : skip
- Ligne 3 (Gifts) : première vraie : match
- Ligne 4 (Toys) : première fausse, `1=2` fausse : skip

## Failles / Attaques
- UNION-based : exfiltration via une seconde requête SELECT
- Bypass de filtre `WHERE` avec `OR 1=1 --`

## Détection / Défense
> TODO : section à compléter, prioritaire pour SOC analyst. À reprendre après les prochains labs.

## Commandes / Payloads
- `' OR 1=1 --`
- En URL encoded via CTRL+U dans Burp : `'+OR+1=1--`

## Labs faits
- SQL injection via Burp Repeater (08/05) : interception d'une requête de changement de catégorie, analyse de la query `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`, injection de `'+OR+1=1--` après `category=`, encodage via CTRL+U

## Sources
- PortSwigger Academy
