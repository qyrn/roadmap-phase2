# SQLMap

## C'est quoi
Outil d'automatisation de l'exploitation des SQL injection. Côté attaquant, il teste automatiquement plusieurs techniques jusqu'à trouver une qui marche. Côté SOC, l'outil laisse des signatures très reconnaissables dans les logs.

Contexte perso : intro découverte, pas maîtrise. Objectif SOC = savoir détecter, pas attaquer.

## Comment ça marche

### Concepts
- DBMS = DataBase Management System (PostgreSQL, MySQL, Oracle, MSSQL...)

### Flow d'attaque
sqlmap teste automatiquement plusieurs techniques (boolean blind, time-based, stacked queries, UNION) jusqu'à trouver une qui marche. Une fois confirmé, on peut passer à `--dbs` puis `--tables` puis `--dump`.

## Failles / Attaques

## Détection / Défense

### Signatures côté SOC (le takeaway principal)
- User-Agent par défaut `sqlmap/x.x.x`
- Chaînes aléatoires de 4-5 chars (ex : `'pYRp'='pYRp'`)
- Concaténations `CHR()` longues pour les UNION (`CHR(113)||CHR(107)||...`)
- Volume anormal : 50+ requêtes en moins d'une minute sur un même endpoint
- 500 Internal Server Error répétées sur un même paramètre

Reconnaître ces patterns dans des logs = identifier sqlmap immédiatement, sans confusion possible avec une attaque manuelle.

## Commandes / Payloads

### Flags de base
- `-u` : URL cible
- `--cookie="..."` : cookie de session
- `-p "param"` : paramètre à tester en priorité
- `--dbs` : énumération des bases
- `--tables` : énumération des tables
- `--dump` : extraction des données

## Labs faits
- Reconnaissance avec SQLMap (16/05) : tests pour mieux comprendre comment ça fonctionne, focus signatures côté défense

## Sources
- PortSwigger Academy
