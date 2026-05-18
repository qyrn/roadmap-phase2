# Injection SQL

## C'est quoi
Vulnérabilité web qui autorise un attaquant à interférer avec les requêtes que l'application fait à sa base de données. Ça peut donner à l'attaquant la possibilité de voir des données normalement inaccessibles, comme les données des utilisateurs ou de l'application.

Dans beaucoup de cas, l'attaquant peut modifier ou supprimer ces données, causant des comportements inattendus de l'application. Dans certaines situations, ça peut monter crescendo vers des attaques sous-jacentes sur les serveurs backend, ou même des denial of service.

## Comment ça marche

### Retrieving data from other database tables (UNION)
Le mot-clé `UNION` en SQL permet, lors d'une SQL injection, de récupérer des données provenant d'autres tables. Un attaquant peut ajouter une deuxième requête `SELECT` à la requête originale pour afficher des informations sensibles comme des usernames ou des mots de passe.

Exemple : si un site affiche des produits d'une catégorie, un attaquant peut injecter une requête avec `UNION SELECT` pour forcer la base de données à retourner aussi les données de la table users.

Points clés :
- `NULL` est compatible avec tous les types de colonnes en SQL : à utiliser pour déterminer le nombre de colonnes via `UNION SELECT NULL, NULL, ...`
- Un string littéral doit être entre apostrophes, sinon le moteur l'interprète comme un identifiant (nom de colonne/table). Réflexe : "string ou identifier ?"

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

### Blind SQLi Boolean-based
Attaque à l'aveugle via un jeu de vrai/faux avec le serveur. On injecte une seule question à la fois (celle dont on veut connaître la réponse), et le serveur renvoie des indices subtils :
- Status code (200 vs 500, 200 vs 302)
- Contenu du body (présence ou absence d'un mot, taille de la réponse, élément HTML)
- Headers (plus rare mais possible)

La condition `AND` exige deux conditions vraies pour être vrai. On l'utilise pour poser une question vrai/faux à la base.

Vu que le serveur ne renvoie aucune donnée dans le body, on utilise `LENGTH` ou `SUBSTRING` pour tester un caractère à la fois, puis on automatise les requêtes avec Burp Intruder pour récupérer tout le mot de passe.

Takeaway : en blind on se doit de confirmer chaque hypothèse en testant le vrai/faux avant de passer à la suivante. Ça évite de partir sur une mauvaise piste ou d'extraire des données incorrectes.

### Blind SQLi avec conditional errors
Quand l'app ne renvoie aucun signal visible dans le body et pas non plus de différence de status code en boolean classique, on force une erreur SQL conditionnelle. Si la condition est vraie, on déclenche une erreur (status 500). Si fausse, la requête passe normalement (200).

Piège : `1/0` (division par zéro) est évalué par le SGBD même quand la condition est fausse. Il faut wrapper l'expression dans une fonction comme `TO_CHAR(1/0)` pour qu'elle ne soit évaluée que dans la branche concernée.

Identification du SGBD au passage : si `SUBSTRING` ne fonctionne pas, tester `SUBSTR` (Oracle).

Méthode dichotomique : tester la bonne première lettre par bissection pour confirmer que l'oracle fonctionne, puis lancer l'Intruder.

Takeaway : toujours tester l'oracle avec une condition forcément fausse avant de bruteforcer.

### Blind SQLi time-based
Quand l'app ne renvoie ni body différent ni status code différent ni erreur exploitable, on utilise le délai de réponse comme signal d'inférence. On force le SGBD à sleep pendant N secondes si la condition est vraie, et on observe le response time.

Identification du SGBD via timing :
- PostgreSQL : `'||pg_sleep(0)--` ne déclenche pas de délai mais pas d'erreur non plus
- MySQL : `' AND SLEEP(0)--`
- MSSQL : `'; WAITFOR DELAY '0:0:0'--`
- Oracle : `'||dbms_pipe.receive_message(('a'),0)||'`

Stacked queries vs concaténation (PostgreSQL) :
- `||` concatène des chaînes : utilisable avec des fonctions comme `pg_sleep()`, mais pas pour insérer un `SELECT` complet (une requête entière n'est pas une valeur à concaténer)
- `;` sépare les instructions SQL : permet d'exécuter plusieurs requêtes consécutives, donc de lancer un `SELECT CASE WHEN...` après avoir terminé la première instruction

Pattern d'extraction blind time-based : `SUBSTRING(password, N, 1) = 'X'` dans le `CASE WHEN`, brute-force position par position via Intruder en triant par response time.

### Filter bypass via XML encoding
Quand un WAF filtre les keywords SQL en lisant le body brut, on peut encoder un caractère du keyword via une référence numérique XML (`&#x55;` = U, `&#x53;` = S). Le parser XML décode avant d'envoyer la requête au moteur SQL, donc le WAF voit un body "propre" mais le SQL reçoit le keyword complet.

Pas besoin d'encoder tout le mot, suffit d'encoder UN caractère du keyword pour casser le pattern matching naïf du WAF.

Décalage entre couche de validation (WAF, lit le body brut) et couche d'exécution (parser XML qui décode avant SQL) = classe de vulnérabilité exploitable.

Méthodo face à un WAF : comparer la dernière payload qui marchait à celle qui échoue et tester chaque différence isolément, plutôt que trial-and-error.

## Failles / Attaques
- UNION-based : exfiltration via une seconde requête SELECT
- Bypass de filtre `WHERE` avec `OR 1=1 --`
- Blind boolean : extraction caractère par caractère via condition vrai/faux + signal HTTP
- Blind conditional errors : extraction caractère par caractère via erreur 500 forcée
- Blind time-based : extraction caractère par caractère via délai de réponse
- Filter bypass via XML numeric character references (contournement WAF)

## Détection / Défense
> TODO : section à compléter, prioritaire pour SOC analyst. À reprendre après les prochains labs.

## Commandes / Payloads

### UNION
- `' UNION SELECT NULL--`
- `' UNION SELECT NULL, NULL--` (ajouter NULL jusqu'à pas d'erreur 500)
- `' UNION SELECT username, password FROM users--`

### Boolean-based
- `' OR 1=1 --`
- En URL encoded via CTRL+U dans Burp : `'+OR+1=1--`
- `' AND SUBSTRING(password, 1, 1) > 'm' --` (test dichotomie)

### Conditional errors (Oracle)
- Payload final lab :
  ```
  ' AND (SELECT CASE WHEN (SUBSTR(password, 1, 1) = 'u') THEN TO_CHAR(1/0) ELSE 'a' END FROM users WHERE username='administrator')='a
  ```

### Time-based (PostgreSQL)
- `'; SELECT CASE WHEN (1=1) THEN pg_sleep(10) ELSE pg_sleep(0) END--`
- URL encoded via stacked queries :
  ```
  TrackingId=x'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
  ```

### Filter bypass XML
- Remplacer un caractère par sa référence numérique XML : `&#x55;` (U), `&#x53;` (S)
- Concaténation `|| '~' ||` peut être bloquée par WAF sur le pattern `'X'` (quote + char + quote) : tester juste `||` sans valeur entre quotes

## Labs faits
- SQL injection via Burp Repeater (08/05) : interception d'une requête de changement de catégorie, analyse de la query `SELECT * FROM products WHERE category = 'Gifts' AND released = 1`, injection de `'+OR+1=1--` après `category=`, encodage via CTRL+U
- UNION attack, determining the number of columns (13/05) : `' UNION SELECT NULL--` puis ajout de NULL jusqu'à pas d'erreur 500
- UNION attack, finding a column containing text (13/05) : remplacement des NULL par la value donnée avec apostrophes
- UNION attack, retrieving data from other tables (13/05) : `' UNION SELECT username, password FROM users--`, 2 colonnes au lieu de 3 attendues
- Blind SQLi with conditional responses (14/05) : exploitation via cookie TrackingId, extraction password administrator caractère par caractère avec SUBSTRING + Intruder, oracle basé sur "Welcome Back"
- Blind SQLi with conditional errors (15/05) : Oracle SGBD identifié via SUBSTR, payload `TO_CHAR(1/0)` pour wrap la division, longueur password trouvée d'abord (20 chars) puis cluster bomb sur position + lettre, filtre status 500
- Blind SQLi with time delays (16/05) : identification PostgreSQL via `pg_sleep(10)` après échec Oracle
- Blind SQLi with time delays and information retrieval (16/05) : stacked queries `;` pour enchaîner SELECT CASE WHEN, Intruder trié par response time
- SQL injection with filter bypass via XML encoding (16/05) : WAF bypass via références numériques XML, isolation du pattern `'X'` qui déclenchait le WAF, password extrait avec concaténation `||`

## Sources
- PortSwigger Academy
- Cheatsheet PortSwigger SQLi
