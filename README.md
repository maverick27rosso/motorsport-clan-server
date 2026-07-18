# Motorsport Clan · Server stanze

Server WebSocket per la modalità **gara online** di Motorsport Clan.
Le stanze vivono in memoria (nessun database): quando escono tutti, la stanza si
chiude da sola dopo 60 secondi. Il gioco (statico + classifica hotlap) resta su
Vercel; questo server gestisce solo il realtime delle gare.

## Come funziona

- Ogni client simula **la propria auto** con la fisica locale (identica per
  tutti) e invia il proprio stato ~15 volte al secondo.
- Il server fa da **relay + arbitro**: ritrasmette a tutti un unico snapshot,
  calcola la classifica live, sincronizza il semaforo di partenza e gestisce i
  comandi dell'admin (avvia / riavvia / termina).
- Modello *client-authoritative*: latenza percepita zero sulla propria auto.
  L'anti-cheat (come per l'hotlap) verrà aggiunto in seguito.

## Sviluppo locale

```bash
cd server
npm install
npm start          # ascolta su ws://localhost:8080
```

Nel gioco, aprendo da `localhost`/`file://`, il client si collega
automaticamente a `ws://localhost:8080` (vedi `js/config.js` → `raceServer`).

## Deploy su Render

1. Questa cartella `server/` è un repository git a sé: va pushata su GitHub.
2. Su [render.com](https://render.com): **New → Blueprint** e collega il repo
   (il file `render.yaml` configura tutto), oppure **New → Web Service** con
   Build `npm install` e Start `npm start`.
3. Render assegna un URL `wss://<nome>.onrender.com`: incollarlo in
   `js/config.js` → `GP.GAME.raceServer` e ridistribuire il gioco su Vercel.

> Nota: sul piano gratuito il servizio va in sospensione dopo inattività; la
> prima connessione dopo una pausa attende ~30-60 s il risveglio.
