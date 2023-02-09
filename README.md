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
L’ Electronic Speed Controller (ESC), tabella 1.2, è un componente fondamentale per il corretto funzionamento di un drone multirotore. La sua funzione è quella di collegare il controllore di volo con i motori consentendo la regolazione della velocità degli stessi. Ogni motore è dotato di un suo ESC perché in un sistema di volo multirotore, ogni motore avrà regimi di rotazione diversi rispetto agli altri. In RPM abbiamo i valori misurati dal sensore di velocità relativo al dato ESC, in CURR abbiamo la corrente di alimentazione dell'ESC. Il drone ha una sensorizzazione ad hoc sul motore.

### 1.3 IMU (Informazioni relative all'accelerometro e giroscopio)
<p align="center">
<table>
<caption style="caption-side:bottom">Tabella 1.3: Informazioni relative all'accelerometro e giroscopio</caption>
    <thead>
        <tr>
            <th>Parametro</th>
            <th>Descrizione</th>
        </tr>
    </thead>
    <tbody>
		<tr>
        <th>Instance</th>
        <td>Numero di istanza IMU</td>
    </tr>
    <tr>
        <th>GyrX, GyrY, GyrZ</th>
        <td>Velocità di rotazione grezza del giroscopio in rad/sec</td>
    </tr>
    <tr>
        <th>AccX, AccY, AccZ</th>
        <td>Valori grezzi dell'accelerometro in m/$s^2$</td>
    </tr>
	</tbody>
</table> 
</p>

I parametri presenti nella tabella 1.3 vengono presi per tenere in considerazione la velocità angolare del giroscopio e i relativi valori dell'accelerometro. Tale variabile contiene i dati ad alta frequenza, intorno ai 350 Hz.

### 1.4 RCOU (Valori di uscita servo channel da 9 a 14)
<p align="center">
<table>
<caption style="caption-side:bottom">Tabella 1.4: Valori di uscita servo channel da 9 a 14</caption>
    <thead>
        <tr>
            <th>Parametro</th>
            <th>Descrizione</th>
        </tr>
    </thead>
    <tbody>
		<tr>
        <th>C9,...,C14</th>
        <td>Output canali considerati</td>
    </tr>
	</tbody>
</table> 
</p>

I valori riportati nelle variabili del parametro RCOU, tabella 1.4, contengono i comandi di Pulse-With Modulation (Modulazione della larghezza di impulso) relativi ai sei motori, in cui i canali attivi sono da C9 a C14.
### 1.5 XKF (Output stimatore EKF3)
<p align="center">
<table>
<caption style="caption-side:bottom">Tabella 1.5: Informazioni di attitudine</caption>
    <thead>
        <tr>
            <th>Parametro</th>
            <th>Descrizione</th>
        </tr>
    </thead>
    <tbody>
		<tr>
        <th>Roll</th>
        <td>Rollio stimato</td>
    </tr>
    <tr>
        <th>Pitch</th>
        <td>Beccheggio stimato</td>
    </tr>
    <tr>
        <th>Yaw</th>
        <td>Imbardata stimata</td>
    </tr>
	</tbody>
</table> 
</p>

L'obiettivo nell'utilizzo di questa variabile è verificare che i valori di Roll, Pitch e Yaw siano gli stessi di quelli presenti nella variabile ATT. XKF è una misura aggregata che viene generata da un topic, che si occupa di compiere un filtraggio alla Kalman.
