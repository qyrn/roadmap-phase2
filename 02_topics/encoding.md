# Encoding

## C'est quoi
Transformations de caractères selon le contexte (HTML, URL, transport). Ce n'est pas du chiffrement.

## Comment ça marche

### HTML encoding
Transforme les caractères spéciaux HTML. Exemple : `<` devient `&lt;`. Contexte : HTML (pages web, affichage). But : empêcher l'interprétation comme du code.

Analogie : c'est comme si on donne un texte qui dit "*crie* bonjour" et qu'on crie "*crie* bonjour" parce qu'on n'avait pas eu l'info qu'entre les astérisques ça ne se lit pas, ça se joue. Quand on a compris, on refait la scène en criant juste "bonjour". Les astérisques sont une indication scénique, pas du texte à dire. En HTML, les caractères spéciaux comme `< >` sont vus comme des instructions. L'encoding sert à les neutraliser pour qu'ils soient lus comme du texte normal.

### URL encoding
Encode les caractères interdits dans une URL. Exemple : espace devient `%20`. Contexte : URL / requêtes HTTP. But : transmettre correctement les données.

Analogie : c'est comme écrire une adresse postale avec des caractères spéciaux que la machine de tri ne comprend pas, donc on remplace certains caractères par une version adaptée pour éviter que le courrier parte au mauvais endroit ou que l'adresse soit mal lue. Ça empêche l'URL de "se casser" à cause de caractères spéciaux comme les espaces, `?`, `&`, etc.

### Base64
Transforme des données en texte (64 caractères). Contexte : transport de données (headers, tokens, emails). But : compatibilité, pas sécurité. Réversible directement, donc aucune protection.

Analogie : traduire une image avec des mots prononçables pour ne pas casser le canal. Mais ce n'est PAS du chiffrement. Tout le monde peut retraduire facilement le message dans sa forme originale.

### encodeURI vs encodeURIComponent
- `encodeURI` : pour une URL complète, garde `/ : ? &`
- `encodeURIComponent` : pour un paramètre, encode tout le reste
- Piège : utiliser `encodeURI` sur un paramètre casse la requête ou rend un bypass possible

## Failles / Attaques

### Mauvais encoding
Mauvais encoding égale injection possible.

### Double decoding
Une donnée est encodée 2 fois, puis décodée 2 fois côté serveur ou navigateur. Peut bypass des filtres (XSS, path traversal).

### Base64 confondu avec du chiffrement
Base64 ressemble à du "chiffré" mais ne l'est pas. Décodable sans clé.

## Détection / Défense
Chaque contexte a son encoding :
- HTML : HTML encoding
- URL : URL encoding
- JS / API : souvent encodage spécifique

Utiliser le bon encoding selon le contexte. Ne jamais s'appuyer sur Base64 pour la sécurité.

## Commandes / Payloads

## Labs faits

## Sources
