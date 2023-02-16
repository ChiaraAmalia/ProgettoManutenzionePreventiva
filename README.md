# Rilevamento usura pala a partire dall’analisi del log di volo di un drone esarotore
<p align="center">
<a href="#Dataset">Il dataset</a>&nbsp•
<a href="#Come-eseguire-il-nostro-progetto">Come eseguire il nostro progetto</a>&nbsp•
<a href="#Strumenti-e-metodi">Strumenti e metodi</a>&nbsp•
<a href="#Sviluppo-del-progetto">Sviluppo</a>&nbsp•
<a href="#Risultati">Risultati</a>&nbsp•
<a href="#Autori">Autori</a>

</p>

## Dataset
Il dataset è composto da diversi voli, ognuno dei quali in differenti condizioni operative: 

- pala nuova;
- pala usurata ("tagliata" nella misura del 5% della sua lunghezza);
- pala molto usurata ("tagliata" nella misura del 10% della sua lunghezza).

Dal datalog fornitoci siamo andate a recuperare solamente una parte delle variabili presenti al suo interno, ovvero quelle che abbiamo ritenuto più importanti al fine delle successive analisi. 

In ciascuna tabella riportata è compreso anche il campo **TimeUS**, indicante l'istante di tempo (in microsecondi), in cui è stata rilevata la misurazione dei parametri riportati (i microsecondi partono dal momento in cui il sistema è stato avviato). Tale parametro è risultato poi essere di fondamentale importanza per la sincronizzazione dei tempi e l'unione di tutti i parametri utili ai fini delle analisi effettuate.

Per un maggior approfondimento sui parametri utilizzati consultare la documentazione.

## Come eseguire il nostro progetto
Per eseguire il nostro progetto sarà sufficiente scaricare il contenuto del repository di GitHub, e poi mandare in run il file *analisi_tutti_15.ipynb*, non è necessario mandare in run gli altri file in quanto i database relativi ai vari voli sono già stati pre-processati e sono presenti nella repository.

## Strumenti e metodi
### 1 Linguaggi e librerie
Tutto il codice presente nel progetto è stato scritto in linguaggio Python, sfruttando svariate librerie legate al mondo della rappresentazione dei dati, del calcolo scientifico e della classificazione.

Tra le più importanti possiamo individuare:

- **Pandas**: è uno strumento per la manipolazione e l'analisi dei dati veloce e semplice da utilizzare.
- **SciPy**: è una libreria open source di algoritmi e strumenti matematici. Contiene moduli per l'ottimizzazione, per l'algebra lineare, l'integrazione, funzioni speciali, FFT, elaborazione di segnali ed immagini, solver ODE e altri strumenti comuni nelle scienze e nell'ingegneria.
- **Scikit-Learn**: è una libreria open source di apprendimento automatico. Contiene algoritmi di classificazione, regressione e clustering (raggruppamento) e macchine a vettori di supporto, regressione logistica, classificatore bayesiano, k-mean e DBSCAN, ed è progettato per operare con le librerie NumPy e SciPy.
- **Seaborn**: Seaborn è una libreria in Python utilizzata principalmente per creare grafici statistici.

### 2 Strumenti
Per lo sviluppo di questo progetto, è necessario installare i seguenti tool:

- Installazione di *MATLAB*: per l’estrazione dei file utili ai fini dell’analisi.
- Installazione della versione *Python 3.10.2*, utilizzata per lo sviluppo del progetto.
- Installazione delle *librerie Python* utilizzate: Pandas, SciPy, Scikit-Learn, Seaborn.
- Installazione di *Jupyter Notebook*, per l’elaborazione interattiva in tutti i
linguaggi di programmazione, utilizzato per l’elaborazione di parti di codice al fine di analizzarne i risultati intermedi.

## Sviluppo del progetto
L’obiettivo del progetto consiste nell’analizzare i dataset di volo di un drone esarotore in caso di pale nuove e pale usurate (i.e. una pala appositamente danneggiata) al fine di discriminarne il funzionamento.

Si è partiti anzitutto andando ad analizzare le variabili e i relativi parametri presenti nel datalog e, essendo questo riportato per ciascun motore del drone, per ognuna della casistiche considerate (nessun guasto, guasto al 5%, guasto al 10%), in un file con estensione .mat, è risultato necessario l’utilizzo di MATLAB per l’estrazione delle variabili di interesse. 

Successivamente, si è proceduto con il calcolo delle feature in Python, con un’importante fase di pre-processing volta al trimming e alla sincronizzazione dei
tempi con tutte le variabili considerate. Infine, con le feature calcolate nel tempo e in frequenza, si è proceduto con l’analisi vera e propria dei dati, mediante la costruzione di un modello in grado di predirre, su una finestra temporale di un secondo, se il drone considerato nel volo presenta un guasto di una certa entità
sopra riportata o meno.

### 1 Generazione dei file
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

A questo punto, si è proceduto alla creazione del file che ospiterà ciascuna matrice, andando ad inserire prima l’intestazione relativa a quella variabile e, successivamente i valori numerici, contenuti all’interno della corrispondente matrice. Nella costruzione del file si è dovuto specificare la lunghezza massima relativamente a ciascuna cella (16) in quanto alcuni valori venivano tagliati e quindi approssimati, rendendo inutilizzabile tale campo numerico. Si è scelta poi la virgola come delimitatore tra un campo e l’altro in quanto di più semplice integrazione all’interno dell’ambiente Python. Per ciascun file .mat, vengono generati un totale di 13 file, relativamente alle variabili considerate, figura 3.4.

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/file.png" height=375></p> 
<p align="center">Figura 3.4: Elenco file generati</p>

Ciascun file si presenta nel modo riportato in Figura 3.5:

<p align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/esempio_csv.png" height=375></p> 
<p align="center">Figura 3.5: File csv generato</p>

### 2 Costruzione del dataset
Avendo ottenuto i vari file .csv relativi a ciascun volo, procediamo con il calcolo delle feature ai fini della classificazione.
All’interno di ciascuna cartella in cui sono contenuti i file csv relativi al volo considerato, è presente un file con estensione .ipynb che ci consente di costruire il dataset finale contenente tutte le feature.
#### 2.1 Trimming del datalog
Tutti le acquisizioni effettuate nei vari istanti di tempo partono dal momento in cui il drone è stato ”armato” ovvero nel momento in cui le pale dei vari motori iniziano a ruotare. E' risultato quindi necessario andare ad eliminare la parte iniziale e finale in cui il drone è fermo. Sono parecchie le acquisizioni con un valore costante che corrisponde al minimo, ovvero quando il drone viene acceso ma gli si dà una potenza minima che non gli permette di decollare. Andiamo quindi a togliere tutti quei valori prima di arrivare ad una potenza e quindi un’altezza minima e successivi al momento in cui il drone sta per atterrare, andanndo quindi a prendere solamente l’arco di tempo in cui il drone sta volando.
#### 2.2 Sincronizzazione dei tempi
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

#### 2.3 Estrazione delle feature nel tempo e in frequenza
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

### 3 Classificazione
Dopo aver ottenuto, per ciascun volo considerato, un file .csv ad esso associato contenente tutte le feature calcolate relativamente a quel volo, procediamo con la fase di addestramento di un modello in grado di predirre la classe di guasto di un volo, a partire dall'analisi del valori delle feature calcolate su una finestra temporale di un secondo.


#### 3.1 Bilanciamento del dataset
Prima di procedere con la fase di classificazione, ci siamo resi conto che il dataset
era fortemente sbilanciato, ovvero il numero di righe relative ad un guasto del 10%
erano molte di pi`u rispetto alle righe relative a droni senza guasto, ovvero i dati
risultanti sono stati:

 - Nessun guasto: 390
 - Guasto al 5%: 418
 - Guasto al 10%: 541
 
Sulla base di questi risultati, si è valutato quindi di effettuare un oversampling del dataset. Per effettuare l’oversampling ci siamo basati sulla libreria SMOTE(Synthetic Minority Over-sampling Technique): viene quindi selezionata casualmente un’istanza della classe minoritaria, andando ad individuare i K punti più vicini ad essa. Andiamo poi a scegliere a caso uno di questi K punti ed andiamo a tracciare un segmento nello spazio delle feature tra il punto della classe minoritaria scelto ed il punto scelto tra i K più vicini. Le istanze sintetiche vengono generate come combinazione convessa di queste due istanze.

A questo punto, tutte e tre le categorie di guasto conterranno 541 elementi, potendo
quindi procedere con il passo successivo di selezione delle feature pi`u importanti.
#### 3.2 Selezione delle feature
Per scegliere le feature migliori è risultato importante andare ad utilizzare un
particolare metodo, in modo da poter prendere le feature più adatte su cui fare
classificazione. Abbiamo deciso di utilizzare come tecnica statistica l’analisi della
varianza (ANOVA). Con l’analisi della varianza l’obiettivo è appunto quello di
individuare e quindi selezionare quelle feature più importanti al fine di ridurre la complessità del modello. L’idea di base `e appunto individuare tutte quelle
differenze tali per cui determinati valori siano relativi a caratteristiche proprie del
gruppo di appartenenza.

Entrando nel dettaglio, abbiamo deciso di utilizzare l’analisi delle varianze in modo
da poter individuare, sui dati a disposizione, le prime 15 feature più importanti.

#### 3.3 Separazione del dataset
Dopo aver individuato le feature pi`u rilevanti, mediante il metodo ANOVA, pro-
cediamo con lo split del dataset in training e test, definendo una dimensione
dell’insieme dei dati di training pari all’80% dell’insieme dei dati originali mentre
per il test pari al 20%.

In questo modo l’insieme di dati di training sar`a composto da 1298 elementi mentre,
l’insieme dei dati di test sarà composto da 325 elementi.

#### 3.4 Definizione dei classificatori
Per effettuare quindi la classificazione, abbiamo deciso di utilizzare sei tipologie di
classificatori, in modo da poter analizzare i diversi approcci alla classificazione per
ciascun modello ottenuto. Tra i classificatori utilizzati abbiamo:

 - LogisticRegression: è un modello di regressione non lineare che viene utilizzato quando la variabile dipendente è di tipo dicotomico ma, può essere facilmente esteso utilizzando lo schema ovr (One-vs-Rest) ovvero suddividendo il problema di classificazione multiclasse in tanti classificatori binari quante sono le classi presenti nell’insieme dei dati.
 - Decision Tree: è un algoritmo di apprendimento supervisionato non parametrico che si compone di una struttura ad albero gerarchica. L’apprendimento prevede l’utilizzo di una strategia ”divide et impera”.
 - Random Forest: è un algoritmo di apprendimento automatico che combina l’output di più alberi decisionali per raggiungere un unico risultato
 - Support Vector Machine: sono dei modelli di apprendimento supervisionato. Viene costruito un iperpiano (o insieme di iperpiani) in uno spazio a più dimensioni che viene quindi utilizzato per la classificazione.
 - Multi Layer Perceptron: è un modello di rete neurale artificiale che mappa insiemi di dati in ingresso in un insieme di dati in uscita appropriati.
 - Stochastic Gradient Descent: implementa dei modelli lineari regolarizzati con l’apprendimento della discesa stocastica del gradiente. Il gradiente della perdita viene stimato ad ogni campione e il modello viene aggiornato con learning rate.

#### 3.5 Addestramento e validazione
Dopo aver definito tutte le specifiche utili al fine della classificazione, procediamo
con l’addestramento e la validazione dei modelli ottenuti, con successiva valuta-
zione dei risultati ottenuti.

#### 3.6 Matrici di confusione
Con i risultati precedentemente ottenuti si `e proceduto con il plotting delle matrici
di confusione.

Le matrici di confusione vengono utilizzate per visualizzare il risultato ottenuto con
la predizione della classe di appartenenza sui dati di test. E' un metodo utile al fine
di visualizzare eventuali anomalie o semplicemente vedere la quantit`a di elementi
che sono stati classificati correttamente. Nella diagonale principale sono riportati i
dati che sono stati correttamente assegnati alla relativa classe di appartenenza, al
di fuori della diagonale principale sono presenti tutti quei dati che non sono stati
assegnati alla corretta classe di appartenenza.

#### 3.7 Cross Validation Scores
Per analizzare meglio i risultati ottenuti con la cross-validation, siamo andati ad
utilizzare un grafico per l’analisi dell’accuratezza media ed errore risultante dalla
cross-validation. In questo modo possiamo visualizzare l’accuratezza media di ciascun classificatore (mediante un grafico a barre) ed il relativo errore, calcolato mediante deviazione standard.
#### 3.8 Report Classificazione
Ulteriori valori che siamo andati a calcolare sono quelli relativi ad un report sulla
classificazione.
Per ciascun classificatore, ci siamo andati a calcolare:

 - **precision**: è il numero di elementi correttamente classificati di ogni classe rispetto al numero di elementi predetti di quella classe
 - **recall**: è il numero di elementi correttamente classificati di ogni classe rispetto al numero di elementi veri della classe
 - **f1-score**: è una combinazione di precision e recall
 - **supporto**: è la rilevanza statistica
 - **accuratezza**: corrisponde numeri di elementi correttamente classificati di quella classe rispetto al numero totale di elementi di quella classe
 - **macro avg**: è il calcolo della media riportando il problema della classificazione ad una classificazione binaria
 - **weighted avg**: è la media pesata, ovvero ciascun elemento viene moltiplicato per un peso durante il calcolo della media


## Risultati

# Autori

:woman_technologist: [Chiara Amalia Caporusso](https://github.com/ChiaraAmalia) 

:woman_technologist: [Margherita Galeazzi](https://github.com/MargheritaGaleazzi)