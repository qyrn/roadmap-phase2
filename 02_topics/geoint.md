# GeoINT

## C'est quoi
Géolocalisation d'une image/vidéo en se basant sur les indices visuels (panneaux, enseignes, architecture, végétation, plaques, etc.). Sous-discipline de l'OSINT.

## Méthode générale
1. Analyser l'image avant Streetview : langue, écritures, panneaux, enseignes
2. Restreindre la zone : pays puis ville
3. Chercher des indices uniques (enseigne locale, monument, numéro de rue)
4. Google Maps / Streetview pour la localisation précise

## Outils
- Google Maps + Streetview
- Recherche d'image inversée (Yandex, Google Lens)
- exiftool pour les métadonnées EXIF (si l'image en a)

## Pièges à éviter
- Foncer sur Streetview avant d'avoir analysé l'image
- Ignorer les contraintes implicites (numéros pairs/impairs côté rue)

## Défis résolus
- **#1 — Hambourg, Weidenallee 69** (05/05, 20 min) : enseigne Hamburger Sparkasse → ville identifiée. Numéros visibles 67/69 → côté pair/impair de la rue → adresse exacte.
- **#2 — [à venir]**
- **#3 — [à venir]**

## Sources
- Sector035 Sunday Quiz
- OSINT Dojo