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
- Utiliser Google Lens trop tôt avant d'avoir extrait manuellement les indices visibles

## Défis résolus
- **#1 — Hambourg, Weidenallee 69** (05/05, 20 min) : enseigne Hamburger Sparkasse → ville identifiée. Numéros visibles 67/69 → côté pair/impair de la rue → adresse exacte.
- **#2 — Brüssow, Allemagne** (14/05, OpenGuessr) : voitures + plaques européennes (bleu à gauche), panneau "Am See" + "Schützenhaus Brüssow" (Stand de Tir de Brüssow en allemand) → pays + ville identifiés.
- **#3 — Singrauli, Inde** (14/05, OpenGuessr) : architecture vernaculaire Asie du Sud (maisons plain-pied), pancarte en hindi confirmée via Google Lens, ville trouvée via arrêt de bus traduit par Lens.
- **#4 — Berlin, Alexanderstraße 40** (15/05, 10 min, exo Julia Bayer X) : enseigne "We Work" "Social Hub", architecture européenne moderne. Google Maps + Google Lens → adresse exacte. Question "qu'est-ce qui manque" : tuyau + barrières taggées (consensus communautaire X, pas confirmé officiellement).
- **#5 — Choisy le Roi, arrêt Charles Jules Vaillant** (15/05, 2 min) : panneau ligne RATP 185 + 183 → Île-de-France. Indice partiel "Charles..." sur le panneau, croisé avec la liste des arrêts de la ligne 185. Confirmé par l'auteur de la photo.

## Sources
- Sector035 Sunday Quiz
- OSINT Dojo
- openguessr.com
- Julia Bayer X (https://x.com/bayer_julia/status/2018423485022835056)
