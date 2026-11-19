%% 2026-11-14
voglio un'applicazione per sostituire l'applicazione che uso attualmente. vorrei snellire alcune parti ed aggiungerne altre che non sono disponibili. la caratteristica che più mi interessa modificare è la gestione delle statistiche giocatori.

## Specifiche Tecniche & Persistenza Dati (aggiunta per `context.md`)
* **Architettura:** Web App / PWA cross-platform (HTML/CSS/JS) utilizzabile sia su desktop (Windows) che su mobile (Android), funzionante al 100% offline.
* **Salvataggio Dati:** Persistenza locale nel browser tramite **IndexedDB** (con fallback `localStorage`) senza dipendenze da server o database esterni.
* **Portabilità Cross-Device:** Pulsanti dedicati nella UI per **Esporta Backup (.json)** e **Importa Backup (.json)** (con opzione di sovrascrittura o merge dello storico) per sincronizzare manualmente i dati tra PC e smartphone.
* **Motore Statistiche Giocatore (requisito core):**
  * Salvataggio dei contatori grezzi cumulativi per singolo giocatore (`totalDartsThrown`, `totalPointsScored`, `first9Points`, `first9Darts`, `checkoutAttempts`, `checkoutHits`, `count60plus``count100plus`, `count140plus`, `count180`, `highestCheckout`) + array storico delle partite (`matches`) con dettaglio turni/leg per generare grafici di andamento.
  * Aggiornamento automatico delle statistiche a fine leg/partita e schermata dedicata alla consultazione delle medie (3-Dart Avg, First 9 Avg, Checkout %, trend storico).
### Roadmap Future (v2-v4)
v2 - backup e portabilità
v3 - metriche avanzate e grafici
v4 - rifinitura PWA

## Modalità di gioco
501, 301
### Varianti
gotcha
### Check out
straight out, double out %%



%% 2026-11-14
1. aggiungi consigli di chiusura (che tenga conto se straight out o double out)
2. inserimento punteggi in turno da 3 inutile. elimina
3. aggiungi la possibilità di scrivere da tastierino, con shortcut per attivare doppi (/) e tripli (*) da 
4. blurra il bull se il triplo è selezionato 
5. aggiungei una grande croce rossa sul giocatore che ha fatto bust, da tenere fino all'inizio del suo prossimo turno, eliminando il banner che esce in basso
6. Aggiungi feedback audio nativo (con un interruttore Audio ON/OFF nell'header): - Alla chiusura di un turno valido (3 frecce), leggi ad alta voce il punteggio totale del turno tramite `window.speechSynthesis` (lingua `it-IT`). - In caso di Bust (sballo), riproduci un breve suono che stia bene con una grossa x rossa, sintetizzato tramite Web Audio API (`AudioContext`), senza usare file audio esterni.
   
   2026-11-14
7. annulla ultimo deve annullare solo l'ultima freccia, anche quando il turno passa all'altro giocatore
8. aggiungi possibilità di scegliere tra voce maschile e femminile con elenco a tendina apribile da una freccia dentro audio on, cliccare il bottone audio fa solo acceso o spento, per aprire il menù solo con la freccetta.
9. a fine partita chiedi se salvare o meno
10. la freccia che compare affianco alla scritta giocatore non apre nessuna tendina e non permette di scegliere giocatori già inseriti
11. scelta chiusura: il default deve essere straight out.
12. leg da vincere massimo 3, togli il 4
13. per il gotcha implementa una croce come quella del bust, utilizzando il colore che hai scelto per il banner, elimina il banner
14. aggiungi tab per confronto statistiche tra 2 giocatori
%%

%%
2026-11-15
1. inverti i pulsanti straight out - double out
2. rivoluzioniamo la gestione dei giocatori. vorrei un tab apposito per la lista giocatori. SOLAMENTE in questa pagina è possibile aggiungere nuovi giocatori.
3. aggiungi la possibilità di giocare in più di 2 giocatori.
4. nella tab giocatori attivare o disattivare i giocatori partecipanti con una spunta. nella pagina principale i giocatori appariranno solo come lista a fondo pagina.
%%

---
%%
2026-11-15
1. il tab statistiche mostra tutte le statistiche di tutti i giocatori (le statistiche del singolo sono adesso visionabili dal tab giocatori)
2. adattare per schermo intero (PC) fino a 4 giocatori su singola riga dentro la partita
3. [BUG] audio preselezionato su voce femminile all'avvio ma con voce maschile. default femminile sia di spunta che di voce.
4. suono bust più cattivo
%%
   
   %%
2026-11-15
1. tab statistiche → blocchi di statistica specifica (media3frecce, media primi 9, percentuale statistiche), cliccabile ed ingrandibile.
2. statistica primaria da visualizzare vittorie/partite giocate e annessa percentuale
3. toggle scontri diretti nella pagina confronto appena sotto la selezione dei 2 giocatori
   %%

%%
2026-11-19
1. aggiungiamo un suono anche per lo zero, suono piu pacato del bust a due toni (come se zero e bust fossero la versione negativa di doppio e triplo)
2. avviando una partita l'header scompare in alto e ci mettiamo in modalità a schermo intero/ridimensionato alla pagina per includere tutto tranne l'header.
3. annulla ultima freccia lo spostiamo vicino a bull e zero, il pulsante invece che testuale diventa una specie di indietro, con freccia e magari in rosso
4. abbandona partita lo rimandiamo dentro l'header
5. valutiamo la possibilità di aggiungere una freccia (nella parte più bassa della schermata che punta verso l'alto, la immagino come una freccia senza fusto, larga e piatta) che rimandi all'apertura dell'header vs scroll verso l'alto
6. dobbiamo modificare i consigli di chiusura. al momento non ci piace la proposta (ad esempio per un 52 propone T17 e 1). l'idea che abbiamo scritto durante il testing è "evita di consigliare frecce <10, se possibile e disponibile tra le combinazioni di chiusura, scarta le proposte con frecce <10". il valore del 10 possiamo valutarlo insieme
7. cambiamo la landing page da partita a giocatori
8. cambiamo la pagina giocatori per accogliere l'avvio della partita:
	   * circa mezza pagina in basso la lista dei giocatori. questi verranno ordinati prima come giocatori attivi (ordinati per numero di partite giocate decrescente), poi successivamente i disattivati (ordinati per numero di partite giocate decrescente)
	   * all'aggiunta di un nuovo giocatore, questo deve essere direttamente attivato
	   * la restante parte superiore rimane libera per un grande tasto avvia partita e un piccolo hamburger per scegliere le impostazioni di gioco (visto che tendenzialmente giochiamo sempre le stesse modalità)
9. a questo punto mi viene in mente che potremmo ripetere il comportamento di header a scomparsa su tutte le pagine da smartphone
10. la pagina partita la lasciamo solo per la partita in corso, altrimenti dice solo che non ci sono partite in corso, con pulsante che rimanda al tab giocatori, che potremmo valutare di chiamare home a questo punto
11. aggiungiamo la pagina con lo storico di tutte le partite salvate [STORICO]
   - il tab di ogni partita sarà composto da una barra superiore in cui inseriamo la data e l'ora di inizio. al suo fianco un piccolo rettangolino con la durata della partita.
   - sotto questa barra inseriamo la lista dei partecipanti (ordinata per vincitore prima e poi gli altri in ordine di punti mancanti allo zero)
   - cliccando sul tab della partita si apre una [nuova schermata] in cui il tab appena cliccato rimane uguale e sotto vediamo altri dati della partita (tutti questi nuovi tab con lo stesso ordine iniziale)
	   - tab[leg] inseriamo un tab sul numero di leg vinti SE abbiamo giocato a leg multipli, altrimenti se abbiamo giocato partita secca non lo mettiamo
	   - tab [frecce] con numero di frecce tirate e percentuali di doppi e tripli
	   - tab [punteggi] con media (3 frecce), media prime 9 frecce e punteggio massimo
	   - tab [punti] col conteggio dei vari 60+ 100+ eccetera
	   - tab [checkout]
	   - grafici a barre per giocatore con numero frecce per ogni "casella" colpita


%%

(inserisci data)
1. esempio modifica proposta
2. 

%% per le prossime versioni
1. statistiche avanzate 
2. impostare l'architettura per un server statistiche unico per tenerci aggiornati su più piattaforme
3. rendere a schermo intero e bloccata in verticale per l'uso da smartphone
4.  Quando l'app è stabile e vuoi usarla ovunque senza accendere il PC:

	1. Crea un repository pubblico su **GitHub**, carica `index.html` e attiva **Settings > Pages > Branch: main**.
    
	2. Ottieni un link HTTPS permanente (`[https://tuoutente.github.io/freccette-301/](https://tuoutente.github.io/freccette-301/)`): da Chrome su Android potrai cliccare sui tre puntini > **Aggiungi a schermata Home** per averla come app a schermo intero senza barra degli indirizzi.







---
%% 
## Zona Dubbi Umani 

- siamo sicuri che questo ↓ sia una buona cosa?
 ![[Pasted image 20261119223444.png]]

- 



%%