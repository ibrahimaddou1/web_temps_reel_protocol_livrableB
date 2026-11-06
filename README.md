# TP WebSocket et P2P

## Tracker HTTP — Livrable B

Démarrer le tracker dans un terminal :

```bash
npm run tracker -- 4000
```

Dans un autre terminal, annoncer un serveur WebSocket (exemples pour Bash/WSL) :

```bash
curl -i -X POST http://localhost:4000/announce -H 'Content-Type: application/json' -d '{"id":"serveur-ibrahim","wsUrl":"ws://localhost:3000/ws"}'
```

Consulter les pairs en ligne :

```bash
curl http://localhost:4000/peers
```

`POST /announce` renvoie 201 à la création, 200 au renouvellement, 400 pour un format invalide et 409 si un identifiant actif désigne une autre URL. `GET /peers` renvoie 200 avec la liste dans `peers`.

Une annonce reste valide pendant 30 secondes. La renouveler avant expiration, par exemple toutes les 10 secondes. Sans renouvellement, le pair disparaît de la liste. Les données sont conservées en mémoire. À ce stade, l’annonce se fait manuellement avec curl.

## Installation

Installer Node.js et npm, puis exécuter dans le dossier du projet :

```bash
npm ci
```

## Connexion WebSocket — Étape A

Dans un premier terminal, démarrer le serveur :

```bash
npm start -- http://localhost:3000
```

Dans un deuxième terminal, démarrer le client :

```bash
npm run client -- ws://localhost:3000/ws
```

Le client affiche `Connecté au serveur WebSocket`. Le serveur affiche `Client WebSocket connecté`.

Les connexions, les déconnexions et les erreurs WebSocket sont affichées dans le terminal.

Le serveur envoie un ping WebSocket toutes les 5 secondes lorsqu’aucun pong n’est en attente. Le client répond automatiquement. Sans pong sous 15 secondes, le serveur ferme la connexion. Le client ferme aussi la connexion s’il ne reçoit aucun ping pendant 20 secondes. Un message indique la connexion perdue.

Utiliser Ctrl+C pour arrêter le client ou le serveur.

Pour tester depuis un autre ordinateur, remplacer `localhost` dans les deux commandes par l’IPv4 de l’ordinateur qui héberge le serveur. Le port 3000 doit être accessible depuis cet ordinateur.

## Protocole HTTP entre camarades

Les échanges HTTP sont implémentés dans `serveur.js`.

Chaque camarade démarre son serveur avec sa propre IPv4, par exemple :

```bash
npm start -- http://172.20.10.2:3000
```

Dans ce même terminal, se connecter au serveur du camarade, par exemple :

```text
connect http://172.20.10.4:3001
```

Après la poignée de main, la console affiche `Connexion établie avec` le nom du camarade ou son URL. Les noms sont renseignés dans la Map `noms` de `serveur.js`.

Pour envoyer un message à tous les camarades connectés :

```text
message {"action":"bonjour","payload":{"texte":"Salut !"}}
```

Le destinataire affiche `Message reçu de` avec le nom ou l’URL de l’expéditeur et le message.

