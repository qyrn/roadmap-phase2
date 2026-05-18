# Web Scanner

Projet Python : scanner basique qui vérifie la présence des security headers HTTP attendus sur une URL cible.

## Repo

https://github.com/qyrn/web-scanner

## Structure

- `scanner/__init__.py`
- `scanner/fetcher.py` : récupère la réponse HTTP (`requests.get(url)` puis `.headers`)
- `scanner/parser.py` : retourne un dictionnaire `{nom_header: valeur}`, valeur = `"Not Found"` si le header attendu n'est pas présent
- `scanner/reporter.py` : parcourt le dictionnaire de résultats et différencie présence/absence avec des préfixes (if/else)
- `main.py` (racine) : orchestre fetcher → parser → reporter

## Setup

```
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

`requirements.txt` généré via `pip freeze`.

## Date

17/05 (S2 J5)

## Notes liées

- `02_topics/http.md` (security headers)
