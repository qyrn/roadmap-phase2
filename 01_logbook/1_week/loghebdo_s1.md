# Loghebdo S1 — du 05/05 au 12/05

## Stats
- Heures cyber : 13h / 18h
- Jours actifs : 7 / 6
- Labs faits : 4
- Moyenne Anki : 11,3 / 20 (sur 6 jours : 3, 7, 11, 20, 13, 14)

## Topics couverts
- HTTP (request/response, méthodes, status codes)
- Cookies (HttpOnly, Secure, SameSite, Set-Cookie vs Cookie)
- Authentification (user enumeration, bruteforce, indicateurs)
- Burp Suite (Proxy, Repeater, Intruder)
- SQL Injection (UNION, OR 1=1, --)
- JWT (alg none, signature bypass)
- Nmap (types de scans, options, scripts NSE)
- Fetch API (Promises, limitations CORS, Set-Cookie bloqué)
- DOM (nodes, querySelector, createElement)
- XSS prevention (HSTS, SSL strip)
- Encoding (HTML, URL, Base64)
- GeoINT (#1 résolu)

## Quiz validation
- Quiz auto S1 : 5 / 5
- Quiz manuel : 7 / 8

## Ce qui a marché
La lecture théorique ciblée juste avant un lab a un bien meilleur ratio rétention/temps que la lecture longue à vide. La boucle doc → questions → auto-test verrouille mieux que la lecture seule, et deux labs d'affilée sur le même thème ancrent la mécanique. Combo manip → doc → fiche, et test-avant-fiche, restent les méthodes qui font le mieux avancer.

## Ce qui a bloqué
Burp crash au lancement (07/05). Confusion direction Set-Cookie / Cookie. `OR 1=1` et `--` (08/05). Compréhension des signatures JWT (09/05). Lecture passive sur Fetch n'a pas marché, débloqué en passant en console-first (10/05). DOM pas compris avec la doc seule, débloqué via une vidéo (12/05). Anki au départ très bas (3/20 le 05/05) à cause de cartes mal formulées et de la pause.

## Ajustement pour S2
S2 J1 : UNION-based SQLi lab 1 PortSwigger + théorie SQLi.

## État général
3 / 5

## Note libre
Fil rouge de la semaine : analyser avant d'agir (blog, GeoINT, business logic, lab). Le réflexe "outil d'abord" coûte du temps et de la précision. La régularité Anki paye (3 → 7 → 11 → 20).
