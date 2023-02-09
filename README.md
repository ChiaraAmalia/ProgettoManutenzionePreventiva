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

## 2. Strumenti e metodi
### 2.1 Linguaggi e librerie
Tutto il codice presente nel progetto è stato scritto in linguaggio Python, sfruttando svariate librerie legate al mondo della rappresentazione dei dati, del calcolo scientifico e della classificazione.

Tra le più importanti possiamo individuare:

- **Pandas**: è uno strumento per la manipolazione e l'analisi dei dati veloce e semplice da utilizzare.
- **SciPy**: è una libreria open source di algoritmi e strumenti matematici. Contiene moduli per l'ottimizzazione, per l'algebra lineare, l'integrazione, funzioni speciali, FFT, elaborazione di segnali ed immagini, solver ODE e altri strumenti comuni nelle scienze e nell'ingegneria.
- **Scikit-Learn**: è una libreria open source di apprendimento automatico. Contiene algoritmi di classificazione, regressione e clustering (raggruppamento) e macchine a vettori di supporto, regressione logistica, classificatore bayesiano, k-mean e DBSCAN, ed è progettato per operare con le librerie NumPy e SciPy.
- **Seaborn**: Seaborn è una libreria in Python utilizzata principalmente per creare grafici statistici.

### 2.2 Strumenti
Per lo sviluppo di questo progetto, è necessario installare i seguenti tool:

- Installazione di *MATLAB*: per l’estrazione dei file utili ai fini dell’analisi.
- Installazione della versione *Python 3.10.2*, utilizzata per lo sviluppo del progetto.
- Installazione delle *librerie Python* utilizzate: Pandas, SciPy, Scikit-Learn, Seaborn.
- Installazione di *Jupyter Notebook*, per l’elaborazione interattiva in tutti i
linguaggi di programmazione, utilizzato per l’elaborazione di parti di codice al fine di analizzarne i risultati intermedi.

## 3. Sviluppo del progetto
L’obiettivo del progetto consiste nell’analizzare i dataset di volo di un drone esarotore in caso di pale nuove e pale usurate (i.e. una pala appositamente danneggiata)
al fine di discriminarne il funzionamento.

Si è partiti anzitutto andando ad analizzare le variabili e i relativi parametri presenti nel datalog e, essendo questo riportato per ciascun motore del drone, per ognuna della casistiche considerate (nessun guasto, guasto al 5%, guasto al 10%), in un file con estensione .mat, è risultato necessario l’utilizzo di MATLAB per l’estrazione delle variabili di interesse. 

Successivamente, si è proceduto con il calcolo delle feature in Python, con un’importante fase di pre-processing volta al trimming e alla sincronizzazione dei
tempi con tutte le variabili considerate. Infine, con le feature calcolate nel tempo e in frequenza, si è proceduto con l’analisi vera e propria dei dati, mediante la
costruzione di un modello in grado di predirre, su una finestra temporale di un secondo, se il drone considerato nel volo presenta un guasto di una certa entit`a
sopra riportata o meno.
### 3.1 Generazione dei file
Inizialmente, il datalog a disposizione era sottoforma di file .mat, quindi manipolabile solamente mediante l’utilizzo dell’ambiente software MATLAB. 

Essendo il datalog, per ciascun volo considerato, una tabella composta da un certo numero di matrici, l’obiettivo era quello di creare tanti file .csv, contenenti ognuno una matrice (ognuna associata a una variabile considerata) con i relativi valori, ognuno con
la propria intestazione. Per ciascun motore e, di conseguenza, ciascun log di volo fornitoci, abbiamo creato delle cartelle, ognuna contenente il file .mat associato a
quel log (visibile nella figura 3.1).

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/lista_cartelle.png" height=375></p> 
<p align="center">Figura 3.1: Lista delle cartelle che contengono il relativo file .mat</p>
