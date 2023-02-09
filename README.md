# Rilevamento usura pala a partire dall’analisi del log di volo di un drone esarotore
## 1. Dataset
Il dataset è composto da diversi voli, ognuno dei quali in differenti condizioni operative: 

- pala nuova;
- pala usurata ("tagliata" nella misura del 5% della sua lunghezza);
- pala molto usurata ("tagliata" nella misura del 10% della sua lunghezza).

Dal datalog fornitoci siamo andate a recuperare solamente una parte delle variabili presenti al suo interno, ovvero quelle che abbiamo ritenuto più importanti al fine delle successive analisi. 

Di seguito riportiamo una breve descrizione delle variabili prese in esame e del relativo contenuto, recuperata dal sito di ardupilot.

In ciascuna tabella riportata è compreso anche il campo **TimeUS**, indicante l'istante di tempo (in microsecondi), in cui è stata rilevata la misurazione dei parametri riportati (i microsecondi partono dal momento in cui il sistema è stato avviato). Tale parametro è risultato poi essere di fondamentale importanza per la sincronizzazione dei tempi e l'unione di tutti i parametri utili ai fini delle analisi effettuate.

### 1.1 ATT (informazioni di attitudine)
<p align="center">
<table align="center">
<caption style="caption-side:bottom">Tabella 1.1: Informazioni di attitudine</caption>
    <thead>
        <tr>
            <th>Parametro</th>
            <th>Descrizione</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>DesRoll</th>
            <td>Angolo desiderato di rollio dal pilota, in gradi (se negativo a sinistra, se positivo a destra)</td>
        </tr>
        <tr>
            <th>Roll</th>
            <td>Angolo di rollio effettivo del velivolo, in gradi (se negativo a sinistra, se positivo a destra)</td>
        </tr>
        <tr>
            <th>DesPitch</th>
            <td>Angolo di beccheggio desiderato dal pilota, in gradi (se negativo in avanti, se positivo all'indietro)</td>
        </tr>
        <tr>
            <th>Pitch</th>
            <td>Angolo di beccheggio effettivo del velivolo, in gradi  (se negativo in avanti, se positivo all'indietro)</td>
        </tr>
        <tr>
            <th>DesYaw</th>
            <td>Direzione desiderata dal pilota, in gradi (0 = nord)</td>
        </tr>
        <tr>
            <th>Yaw</th>
            <td>Direzione effettiva del velivolo, in gradi (0 = nord)</td>
        </tr>
    </tbody>
</table>
</p>

I parametri presenti nella tabella 1.1 vengono considerati per poter prendere i desiderati e gli effettivi dei valori di Rollio, Beccheggio e Bardatura. Questi rappresentano gli angoli di inclinazione del velivolo, insieme ai valori desiderati dagli angoli imposti dal controllore, che vengono assegnati da radiocomando.

### 1.2 ESC (Feedback ricevuto dagli ESC)
<p align="center">
<table>
<caption style="caption-side:bottom">Tabella 1.2: Feedback ricevuto dagli ESC</caption>
    <thead>
        <tr>
            <th>Parametro</th>
            <th>Descrizione</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <th>Instance</th>
            <td>Numero di istanza ESC, relativa al motore considerato</td>
        </tr>
        <tr>
            <th>RPM</th>
            <td>Tasso di rotazione del motore riportato</td>
        </tr>
        <tr>
            <th>Curr</th>
            <td>Corrente di input ricevuta dall'ESC</td>
        </tr>
    </tbody>
</table> 
</p>