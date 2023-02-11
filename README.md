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

Successivamente, si è proceduto al caricamento in MATLAB di ciascun file, in modo da poterne analizzare le variabili al suo interno. Ciascuna variabile presente
all’interno di ogni file viene rappresentata per mezzo di una matrice, così come la relativa intestazione, composta da un certo numero di righe ed una sola colonna (figura 3.2).

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/att_mat.png" height=375></p> 
<p align="center">Figura 3.2: Rappresentazione matriciale ed intestazione relativa alla variabile ATT</p>

A questo punto si è proceduto con la creazione del  file .csv, contenenti tali matrici e relativi valori.
Il file .mat viene anzitutto caricato nell’ambiente software, in modo da
permetterci l’accesso alle svariate matrici presenti 3.3:

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/workspace.png" height=375></p> 
<p align="center">Figura 3.3: Workspace contenente tutte le matrici e relative intestazioni </p>

A questo punto, si è proceduto alla creazione del file che ospiterà ciascuna matrice, andando ad inserire prima l’intestazione relativa a quella variabile e, successivamente i valori numerici, contenuti all’interno della corrispondente matrice. Nella costruzione del file si è dovuto specificare la lunghezza massima relativamente a ciascuna cella (16) in quanto alcuni valori venivano tagliati e quindi approssimati, rendendo inutilizzabile tale campo numerico. Si è scelta poi la virgola come delimitatore tra un campo e l’altro in quanto di più semplice integrazione all’interno dell’ambiente Python. Per ciascun file .mat, vengono generati un totale di 13 file,
relativamente alle variabili considerate, figura 3.4.

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/file.png" height=375></p> 
<p align="center">Figura 3.4: Elenco file generati</p>

Ciascun file si presenta nel modo riportato in Figura 3.5:

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/esempio_csv.png" height=375></p> 
<p align="center">Figura 3.5: File csv generato</p>

### 3.2 Costruzione del dataset
Avendo ottenuto i vari file .csv relativi a ciascun volo, procediamo con il calcolo delle feature ai fini della classificazione.
All’interno di ciascuna cartella in cui sono contenuti i file csv relativi al volo considerato, è presente un file con estensione .ipynb che ci consente di costruire il dataset finale contenente tutte le feature.
#### 3.2.1 Trimming del datalog
Tutti le acquisizioni effettuate nei vari istanti di tempo partono dal momento in cui il drone è stato ”armato” ovvero nel momento in cui le pale dei vari motori iniziano a ruotare. E' risultato quindi necessario andare ad eliminare la parte iniziale e finale in cui il drone è fermo. Sono parecchie le acquisizioni con un valore costante che corrisponde al minimo, ovvero quando il drone viene acceso ma gli si dà una potenza minima che non gli permette di decollare. Andiamo quindi a togliere tutti quei valori prima di arrivare ad una potenza e quindi un’altezza minima e successivi al momento in cui il drone sta per atterrare, andanndo quindi a prendere solamente l’arco di tempo in cui il drone sta volando.

###### Variabile RCOU
In tale variabile sono presenti i canali di output relativi alla potenza dei sei motori. Questa variabile è risultata di fondamentale importanza in quanto è servita per effettuare il trimming del datalog.

Dopo aver recuperato il file .csv relativo alla variabile RCOU, siamo andati a rimuovere tutti quei canali che non risultavano essere attivi, andando quindi a considerare solo dal C9 al C14. Abbiamo considerato una soglia di potenza dei motori media pari a 1350 in quanto, se la potenza dei motori è pari a 1000 allora significa che non passa corrente quindi il drone ancora non è in volo inoltre, la potenza media massima che viene raggiunta è intorno ai 1700 di conseguenza, 1350 risulta la scelta migliore. Siamo quindi andati a prendere tutti i valori tali per cui la potenza in quei canali era maggiore o uguale a 1350.

A questo punto, dal dataframe ottenuto andiamo a prendere i valori di TimeUS ovvero degli istanti di tempo minimo e massimo per effettuare il trimming con le altre variabili, al fine di considerare solamente la finestra temporale in cui il drone è effettivamente in volo.
###### Variabili ATT e XKF
A questo punto andiamo a prendere i parametri relativi alle variabili ATT e XKF per effettuare che i valori di Roll, Pitch e Yaw siano effettivamente gli stessi.

Siamo quindi andati a prendere le due variabili di interesse, rimuovere i parametri non utili ai fini dell'analisi e togliere la parte del datalog che non rientra nella finestra temporale considerata.

Abbiamo quindi proceduto al confronto dei valori nei parametri delle variabili per vedere se effettivamente corrispondessero. Se tali valori non corrispondono viene effettuata una media tra i due, andando a riassegnare il valore alla variabile ATT in corrispondenza di quel parametro.
###### Variabili ESC
A questo punto procediamo con l'elettricità che scorre nei motori. Andiamo anzitutto a rimuovere tutti parametri non utili ai fini della nostra analisi, tenendo quindi in considerazione solamente i parametri relativi ad RPM e Curr, ritenute le più importanti. Effettuiamo anche in questo caso un trimming del datalog relativo a tutte le variabili ESC considerate, ognuna associata ad un motore, prendendo la finestra temporale compresa tra i valori di min e man individuati precedentemente. 

Il risultato che otteniamo non è altro che composto da sei variabili ESC, ognuna associata ad un motore del nostro drone, prendendo in considerazione solamente l'arco di tempo selezionato ed i parametri scelti.

###### Variabili IMU
Per quanto riguarda le variabili IMU, sono tre le istanze che abbiamo a disposizione. Dopo aver rimosso tutti i parametri non utili ai fini delle analisi che si vogliono effettuare, per ogni variabile si è presa solamente la finestra temporale compresa nell'intervallo di tempo tra i valori di min e max calcolati precedentemente. Al fine di considerare solamente una variabile associata all'IMU, si è optato prima per una concatenazione di queste con un successivo calcolo della media, effettuando un raggruppamento sulla colonna dei tempi.

Ciò che si ottiene quindi è un'unica variabile contenente al suo interno una media delle tre variabili IMU.
#### 3.2.2 Sincronizzazione dei tempi
In questa fase, avendo ottenuto tutte le variabili e i relativi parametri utili ai fini delle analisi, è risultato necessario, per poter unire tutte le tabelle, effettuare una sincronizzazione dei tempi.

###### Unione delle tabelle
Il primo passo di questa fase è stato unire tutte le tabelle, risultanti dalle precedenti operazioni, per fare ciò abbiamo sfruttato il metodo di pandas *merge_ordered()*, progettata per dati ordinati come dati di serie temporali e che semplicemente esegue l'unione di questi dati (mantenendo l'ordine).

In seguito abbiamo proceduto andando ad imputare i valori mancanti e per fare ciò abbiamo utilizzato un altro metodo di pandas, ovvero *DataFrame.fillna()* che permette di andare a riempire i valori mancanti con il metodo specificato tra le parentesi.

Come è possibile vedere nel codice riportato sotto, noi abbiamo applicato tale funzione due volte, una prima volta con il metodo *"ffill"* che sostanzialmente propaga l'ultima osservazione valida in avanti fino alla prossima valida, e successivamente, per riempire i valori che in questo modo rimanevano vuoti, con il metodo *"bfill"* che utilizza ,l'osservazione valida successiva per colamre le lacune (lavora all'indietro).

Dopo aver unito tutti i parametri delle tabelle, si è proceduto con la sincronizzazione dei tempi del volo, impostando a 0 $\mu$s la prima riga della tabella, indicando il fatto che il volo inizi in quell'istante di tempo, andando quindi a sottrarre a ciascuna riga, relativamente alla colonna dei tempi, il vaore della prima. 

###### Sincronizzazione con 350 misurazioni al secondo
Essendo noto il fatto che il campionamento avveniva con una frequenza pari a: $f_c=350$, ma che i sensori potevano in realta campionare a tempi leggermente diversi, si è forzatamente sincronizzato il dataset per fare in modo che le misurazzioni fossero effettivamente 350 al secondo.

Quindi noto che il tempo di campionamento fosse pari a: $T=1/f_c=1/350=0.002857s=2857\mu s$, utilizzando un semplice ciclo for, si è creato un dataframe con i tempi in modo tale che tra le varie misurazioni trascorressero veramente $2857\mu s$.

Infine si è unito questo dataframe a quello precedentemente ottenuto, si sono rimosse le righe con i tempi che non rispettavano la distanza tra le misurazioni.

A questo punto trasformiamo la colonna dei tempi, espressa in $\mu$s, in un formato HH:MM:SS.

Questa trasformazione la effettuiamo per verificare se effettivamente vi sono $2857 \mu s$ tra una misurazione e l'altra, e per rendere più leggibile la variabile temporale.

#### 3.2.3 Estrazione delle feature nel tempo e in frequenza
Dopo aver verificato se la sincronizzazione dei tempi è andata a buon fine, si è proceduto con il calcolo delle feature nel tempo ed in frequenza per vedere se queste sono significanti.

Come prima cosa si è dovuta definire una funzione per il calcolo del Root Mean Square in quanto essa non era già implementata in pandas.

Successivamente si è proceduto creando una funzione che per ogni secondo andasse a calcolare:

- Feature nel tempo, quali:
    - media;
    - varianza;
    - curtosi;
    - root mean square.

- Feature in frequenza, quali:
    - frequenza del 1°picco;
    - ampiezza del 1°picco;
    - frequenza del 2°picco;
    - ampiezza del 2°picco.

Per fare ciò si è utilizzata la funzione per il calcolo del root mean square introdotta in precedenza, e funzioni preimpostate per il calcolo della altre feature, poi si è trasformata la serie temporale in frequenza e si sono trovate le features in questo dominio.

###### Creazione del dataset
In questa fase si è proceduto con l'applicazione della funzione precedentemente definita a tutte le variabili presenti all'interno della tabella sincronizzata.

Andiamo adesso ad effettuare un merge di tutte le tabelle ottenute dall'applicazione della funzione, sulla base della colonna dei tempi. Procediamo quindi con l'inserimento della colonna relativa al guasto associando, per quel datalog di volo che stiamo considerando, il guasto corrente.

Il dataset contenente tutte le feature calcolate viene quindi convertito in formato .csv, per essere poi analizzato nella fase successiva

### 3.3 Classificazione
Dopo aver ottenuto, per ciascun volo considerato, un file .csv ad esso associato contenente tutte le feature calcolate relativamente a quel volo, procediamo con la fase di addestramento di un modello in grado di predirre la classe di guasto di un volo, a partire dall'analisi del valori delle feature calcolate su una finestra temporale di un secondo.

