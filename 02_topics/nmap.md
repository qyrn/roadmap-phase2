# Nmap

## C'est quoi
Scanner de ports et services réseau. Permet de découvrir les hôtes actifs, les ports ouverts, les versions des services et parfois le système d'exploitation.

## Comment ça marche
Une commande Nmap se décompose généralement en 3 parties mais peut s'étendre jusqu'à 4 :

```
nmap -> type de scan (-sS -sT -sU -sn) -> cible (ip | réseau/24) -> options (-p -A -T4 --script -oA) -> résultats (ports | services | OS)
```

### Types de scans principaux
- `-sS` : scan SYN, semi-ouvert et rapide, utilisé quand on est root, pas de handshake complet donc discret. Rapide. Utilisé surtout en phase de reconnaissance rapide. Le vrai intérêt est qu'il distingue souvent mieux open et filtered qu'un scan TCP connect.
- `-sT` : scan TCP connect, complète la connexion donc plus visible que le scan SYN mais plus fiable. Moyen. Utile quand on n'a pas les droits root ou quand un firewall bloque les SYN scans. Laisse souvent plus de traces dans les logs.
- `-sU` : scan UDP, assez lent mais détecte DNS, DHCP, SNMP. Lent. Piège classique : `open|filtered` veut dire que Nmap n'a reçu aucune réponse, il ne peut pas distinguer entre un port ouvert qui ignore le paquet et un port filtré par firewall.
- `-sn` : ping scan qui découvre les hôtes sans scanner les ports. Très rapide. Utilisé au début d'un audit pour trouver les machines actives sur un réseau.

## Failles / Attaques
Outil offensif d'énumération, pas un vecteur d'attaque en soi.

## Détection / Défense

### Interprétation des états
- `open` : un service répond sur le port
- `closed` : le port est accessible mais aucun service n'écoute, souvent avec un RST TCP en réponse
- `filtered` : Nmap reçoit un ICMP unreachable ou aucune réponse après retransmissions, souvent à cause d'un firewall ou d'un filtrage silencieux

En SOC, voir beaucoup de ports `closed` au lieu de `filtered` peut indiquer qu'un firewall laisse passer les paquets avant de répondre par RST, donc une posture plus permissive.

### Erreurs à ne pas faire
- Scan SYN sans root : les scans SYN, OS et certains scripts nécessitent des permissions root, penser au `sudo`
- Interpréter `filtered` comme fermé : `filtered` veut dire qu'un firewall filtre ou drop les paquets, pas forcément que le port est fermé
- Oublier le `-n` : sur un grand réseau la résolution DNS ralentit énormément le scan
- Faire confiance aveuglément à `-sV` : certaines bannières sont modifiées ou masquées, les versions affichées peuvent être fausses
- Utiliser `-A` trop tôt : le scan agressif génère beaucoup de trafic et peut attirer rapidement l'attention
- Utiliser `-T4`/`-T5` en pensant être discret : ces timings sont rapides mais très visibles dans les IDS/IPS

## Commandes / Payloads

### Options importantes
- `-A` : scan agressif qui combine `-O` + `-sV` + `-sC` + `--traceroute`. Très utile en énumération complète mais extrêmement bruyant dans les logs.
- `-T0` à `-T5` : contrôle la vitesse. T0/T1 conçus pour éviter les IDS et rester très discrets, T3 mode par défaut, T4 sur réseaux rapides pour gagner du temps mais pas discret, T5 privilégie uniquement la vitesse.
- `-p A,B,C` ou `A-B` : scan de ports spécifiques. Souvent utilisé pour gagner du temps après une première découverte.
- `-p-` : scan des 65535 ports. Très lent. Utile car certains services importants tournent sur des ports non standards.
- `-F` : scan uniquement les 100 ports les plus courants. Rapide. Utile pour une reconnaissance rapide avant un scan plus complet.
- `-sV` : détecte les versions des services. Permet souvent d'identifier directement des versions vulnérables.
- `-O` : détecte le système d'exploitation. Nécessite `sudo` car Nmap utilise des raw sockets et analyse les réponses TCP/IP pour faire du fingerprinting.
- `-oA` : exporte le résultat dans tous les formats (`.nmap`, `.xml`, `.gnmap`). Très utile pour garder des traces ou parser les résultats avec d'autres outils.
- `-n` : désactive la résolution DNS. Très utile sur de gros réseaux pour accélérer fortement le scan.
- `--reason` : affiche pourquoi Nmap considère un port comme `open`, `filtered` ou `closed`. Très utile pour comprendre le comportement d'un firewall.
- `-v` / `-vv` : augmente la verbosité, affiche la progression du scan en temps réel. Pratique quand un scan dure longtemps.
- `-d` : mode debug, affiche les paquets envoyés/reçus. Utilisé surtout quand un scan donne des résultats incohérents.
- `--min-rate=1000` : force Nmap à envoyer au minimum 1000 paquets par seconde. Très utilisé pour accélérer les scans complets sur des réseaux internes rapides.

### Scripts NSE
`--script` : permet d'exécuter les scripts NSE (Nmap Scripting Engine). Très puissant pour l'énumération et l'automatisation.

Catégories :
- `safe` : scripts non intrusifs (risque : aucun)
- `default` : exécutés avec `-sC` (risque : faible)
- `discovery` : collecte d'informations (risque : faible)
- `version` : détecte les versions (risque : faible)
- `auth` : tests d'authentification (risque : moyen)
- `vuln` : détection de vulnérabilités (risque : moyen)
- `brute` : brute force (risque : élevé)
- `exploit` : exploitation directe (risque : très élevé)
- `intrusive` : scripts potentiellement disruptifs (risque : élevé)

### Modèles de scans courants

Scan basique pour découvrir les ports ouverts :
```
nmap {ip}
```

Scan ciblé sur certains ports :
```
nmap -p A,B,C,D (ou A-C) {ip}
```

Scan rapide sur tous les ports :
```
nmap -p- -T4 {ip}
```

Scan qui détecte les versions des services :
```
nmap -sV -p A,B {ip}
```

Scan qui détecte le système d'exploitation :
```
sudo nmap -O {ip}
```

Scan agressif qui collecte un maximum d'informations :
```
sudo nmap -A -T4 {ip}
```

Scan furtif sans compléter les connexions :
```
sudo nmap -sS -T2 {ip}
```

Scan UDP qui découvre les services UDP :
```
sudo nmap -sU -p A,B,C {ip}
```

Scan qui trouve les hôtes actifs sans scanner les ports :
```
nmap -sn {ip}
```

Scan qui cherche des vulnérabilités connues :
```
nmap --script vuln -p A,B,C {ip}
```

Combo très utilisé en pentest pour un scan complet rapide :
```
sudo nmap -sV -sC -p- --min-rate=1000 -oA scan {ip}
```

Export multi-format pour sauvegarder les résultats :
```
nmap -oA scan-xxxx-xx-xx {ip}
```

### Debug
```
nmap -v {ip}                                       # progression en temps réel
nmap -vv {ip}                                      # détaille chaque étape
nmap -d {ip}                                       # paquets envoyés/reçus
nmap --script-trace --script {script} {cible}      # debug d'un script NSE
```

## Labs faits

## Sources
