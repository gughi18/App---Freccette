# Context — App Segnapunti Freccette

1. **Obiettivo Finale:**
   - Web App / PWA cross-platform (Windows e Android), 100% offline, segnapunti freccette per 2–8 giocatori (301/501, Gotcha, Straight Out / Double Out).
   - Gestione profili giocatore (avatar, partecipazione, statistiche individuali avanzate, storico partite e confronti).
   - Portabilità locale dei dati via backup `.json`.

2. **Stato Attuale & Architettura v1.2:**
   - **Stack & Struttura:** Singolo file `index.html` (HTML5, CSS3, Vanilla JavaScript `"use strict"`), zero librerie esterne, zero backend, 100% offline-first.
   - **Persistenza:** IndexedDB v2 (`segnapunti-freccette`, store `players` con `id` nome minuscolo e store `matches` con `id` timestamp) + fallback `localStorage` (`dt_players`, `dt_matches`). Flag `active` filtrato via (`Store.clean`) e mantenuto in memoria di sessione.
   - **Audio & Sintesi:** Web Audio API (`AudioContext`) per suoni sintetizzati (bust distorto, campanelli ascendenti per doppi/tripli, suono discendente morbido dello Zero e fanfara ottoni) + `window.speechSynthesis` `it-IT` per la lettura del punteggio di turno (toggle ON/OFF e selettore voce femminile/maschile).
   - **Effetti visivi:** Canvas `#fx` per coriandoli/stelle filanti alla vittoria leg/partita; sovrapposizione croci SVG statiche per Bust (`#e2483d`) e Gotcha (`#d0a84e`).

3. **Specifiche Tecniche & Modello Dati (v1.2):**
   - **Record `players`:**
     - `id`, `name`, `avatar` (Data URL JPEG 128×128 o `""`).
     - Contatori cumulativi: `totalDartsThrown`, `totalPointsScored`, `first9Points`, `first9Darts`, `checkoutAttempts`, `checkoutHits`, `count60plus`, `count100plus`, `count140plus`, `count180`, `highestCheckout`.
   - **Record `matches` (Esteso per v1.2):**
     - `id` (`Date.now()`), `startedAt` (timestamp inizio partita), `date` (ISO fine partita), `start` (301/501), `out` (`straight`/`double`), `gotcha` (bool), `legsToWin`.
     - `players` (array di 2–8 nomi nell'ordine estratto), `winner` (nome).
     - `finalRem`: mappa/array dei punti residui di ciascun giocatore al momento della chiusura dell'ultimo leg (usato per l'ordinamento dei non-vincitori nello Storico).
     - `legs`: array di `{winner, starter, turns[]}`.
     - Fallback tollerante: se i vecchi match non possiedono `startedAt` o `finalRem`, la UI li gestisce senza crashare (durata `—`, punti mancanti ricavati dall'ultimo turno se presente).

4. **Specifiche UI/UX & Navigazione (v1.2):**
   - **Struttura Navigazione (4 Tab Fissi nell'Header):**
     1. **Home (`home` - Landing Page):**
        - Parte superiore: Grande pulsante **Avvai Partita** + icona hamburger per le Impostazioni di gioco (301/501, Straight/Double Out, Gotcha, Leg da vincere).
        - Parte inferiore: Form creazione nuovo giocatore (assegna direttamente lo stato `attivo`) + lista di tutti i giocatori ordinati prima per *attivi* (per numero di partite giocate decrescente) e poi per *disattivati* (per numero di partite giocate decrescente).
     2. **Partita (`play`):**
        - Dedicata esclusivamente alla partita in corso. Se non c'è una partita attiva, mostra una vista vuota con un pulsante rapido per andare alla **Home**.
     3. **Statistiche (`stats`):**
        - Vista globale a blocchi espandibili. In cima ha un pulsante **Confronta giocatori** che apre la schermata contestuale di Confronto.
     4. **Storico (`history`):**
        - Elenco card di tutte le partite concluse. Barra superiore card: Data e ora di inizio + badge con durata della partita. Partecipanti ordinati per vincitore primo, poi per punti mancanti allo zero crescenti (`finalRem`).
        - Cliccando una card si apre il dettaglio mantenendo la card della partita in cima.
        - I dati sono organizzati in box verticali pieghevoli: `Leg` (visibile solo se `legsToWin > 1`), `Frecce`, `Punteggi`, `Punti`, `Checkout` e `Grafico a barre`.
        - Le tabelle hanno i giocatori ordinati come nella card, con il vincitore primo e gli altri per punti residui crescenti.
        - Il grafico bersagli usa un asse Y con il numero di frecce e un asse X con `0`, `1–20`, `25`, `D1–D25`, `T1–T20`.
        - La preview mostra i 5 settori con cardinalità maggiore; l'espansione mostra tutti i settori e lo scorrimento è sincronizzato tra i grafici dei giocatori.
   - **Vista Contestuale Confronto:**
     - Non fa più parte dei tab dell'header. È accessibile da **Statistiche** (pulsante in alto) o dal **Profilo Giocatore** nel tab Home (pulsante **Confronta**, che apre la vista pre-selezionando il giocatore come Giocatore 1 e fornendo un pulsante Indietro).

5. **Controlli, Audio & Layout in Partita (v1.2):**
   - **Header Auto-Hide:** Su dispositivi mobile (e durante una partita attiva su desktop), l'header parte nascosto verso l'alto e si richiama tramite **scroll verso l'alto / rotella del mouse**. Nessun pulsante o maniglia extra in basso per evitare tocchi accidentali col tastierino.
   - **Pulsante Abbandona Partita:** Spostato dentro l'header a scomparsa.
   - **Pulsante Undo (Annulla Ultima Freccia):** Spostato nel tastierino numerico accanto ai pulsanti `Bull` e `0`, stilizzato in rosso compatto con icona freccia indietro.
   - **Audio Zero (`0`):** Nuovo suono sintetizzato via Web Audio API: 2 toni pacati discendenti (versione "morbida/negativa" dei campanelli ascendenti di doppio e triplo).
   - **Algoritmo Consigli di Chiusura:**
     - **Double Out:** Filtra ed evita le combinazioni con bersagli singoli/doppi `< 10` (es. evita `D1–D4` e setup come `52 -> T17 + 1`) quando esistono combinazioni alternative con bersagli $\ge 10$ (es. `52 -> 12 + D20` o `20 + D16`).
     - **Straight Out:** Mantiene valide le chiusure immediate su singola freccia `< 10` (es. `8 -> 8`), ma predilige bersagli $\ge 10$ nei setup su più frecce.

6. **Roadmap Future (v2–v4):**
   - **v2:** Export/Import backup `.json` (con merge e sovrascrittura).
   - **v3:** Server unico e sincronizzazione cloud multi-piattaforma.
   - **v4:** Manifest PWA, Service Worker offline, lock orientamento verticale smartphone e pubblicazione su GitHub Pages.