# Loghebdo S2 — du 13/05 au 17/05

## Stats
- Heures cyber : 10h sur 5 jours
- Jours actifs : 5 / 5
- Labs faits : 8 (PortSwigger SQLi : 3 UNION, 1 Blind Boolean, 1 Blind-Based, 2 Time-delay, 1 Filter) + reconnaissance SQLMap + Web Scanner Python construit
- To-dos faits : 100% (SQLi Filter Bypass Lab 2 skip car le lab n'existe pas)
- Moyenne Anki : 11,8 / 20 (5 jours Anki / 5 actifs : 10, 13, 11, 14, 11)

## Topics couverts
- SQLi UNION
- SQLi Blind Boolean
- SQLi Blind Time-based
- SQLi Filter bypass (XML numeric character references, WAF)
- Stacked queries PostgreSQL (`;` vs `||`)
- SQLMap (reconnaissance, signatures côté logs)
- Web scanner Python (venv, requirements.txt, requests, fetcher / parser / reporter)
- GEOINT (2 challenges)
- OSINT (1 CTF)
- OpenGuessr (2/2)

## Quiz validation
- Quiz manuel : 6.5 / 8
- Quiz hebdo : 4 / 5 (1 question hors scope sur la défense SQLi)

## Ce qui a marché
Confirmer chaque hypothèse en testant le vrai/faux avant de passer à la suivante, ça évite de partir sur une mauvaise piste et permet d'avancer méthodiquement. Toujours tester l'oracle avec une condition forcément fausse avant de bruteforcer. Bien analyser les images avant d'utiliser Google Lens. Face à un WAF, comparer la dernière payload qui marchait à celle qui échoue et tester chaque différence isolément, plutôt que trial-and-error. Test bissection + wrapper `1/0` dans une fonction pour pas qu'il soit évalué tôt.

## Ce qui a bloqué
Bases SQL (AND, comparaisons) et erreur d'interprétation `>` vs `<` (14/05). Anki nouvelles cartes : héritage du speedrun Phase 1, sensation de pas avoir appris (15/05). Formulation du payload + division, résolu via cheatsheet PortSwigger + bissection + wrap de `1/0` (15/05). Filter bypass XML, partie exploitation après bypass initial. À revoir : différence stacked queries / concaténation / encodage de transport, et méthode d'isolation des variables face à un WAF (16/05).

## Ajustement pour S3
- Gandalf niveau 0 à 4
- Reflected XSS Lab 1 et 2

## État général
4 / 5

## Note libre
Passer plus de temps à observer au lieu de généraliser directement.
