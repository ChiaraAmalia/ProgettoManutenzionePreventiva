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
 - **supporto**: è il numero di dati di test utilizzati nella predizione
 - **accuratezza**: è l'accuratezza che si ottiene dalla divisione del dataset in training e test, andando a vedere quindi quanti elementi di quella classe sono stati classificati, rispetto al numero totale di elementi di quella classe
 - **macro avg**: è il calcolo della media riportando il problema della classificazione ad una classificazione binaria
 - **weighted avg**: è la media pesata, ovvero ciascun elemento viene moltiplicato per un peso durante il calcolo della media


## Risultati
Avendo riportato tutti i passaggi relativi allo sviluppo del nostro progetto, procediamo con il mostrare i risultati ottenuti, dall’addestramento dei diversi classificatori utilizzati. Procederemo con riportare il risultato dell’accuratezza semplice, calcolata dividendo il dataset in training e test, successivamente procederemo con i risultati dell’accuratezza ottenuti mediante cross-validation, con un numero di fold pari a 5. Per ciascun classificatore verrà riportata anche la relativa curva ROC, mostrando la curva per ciascuna classe predetta. Riporteremo quindi i risultati numerici, rappresentati mediante matrici di confusione, relativi alla predizione ottenuta applicando i dati di test al modello e report sulla classificazione. Verrà riportato infine un grafico a barre rappresentante gli score della cross-validation, con accuratezza media e deviazione standard.

### 1 Logistic Regression
Con il Logistic Regression riusciamo ad ottenere un’accuratezza pari all’88%. Il modello ottenuto è in grado di effettuare una separazione distinta delle classi, soprattutto è capace a distinguere il volo relativo ad un drone senza guasto da un volo con guasto al 10%. Maggiore difficoltà viene riscontrata nella distinzione tra un volo relativo ad un drone senza guasto da un drone con guasto al 5%. Di seguito riportiamo i risultati delle accuratezze ottenute mediante cross-validation:
<p align="center">
<table border="0", align="center">
<tr>
<td>0.89230769</td>
<td>0.88923077</td>
<td>0.84923077</td>
<td>0.93518519</td>
<td>0.87037037</td>
</table>
</p>
Come possiamo vedere dai risultati ottenuti, per ciascun test fatto sui fold, le accuratezze che otteniamo sono accettabili e relativamente alte, ma anche conformi al risultato ottenuto dal calcolo dell’accuratezza semplice.

#### 1.1 Curva ROC
Per quanto riguarda il Logistic Regression, la curva ROC ottenuta è riportata in
figura 4.1
Come possiamo vedere la curva migliore è quella relativa alla classe 2 (ovvero riferito a volo con guasto al 10%) che abbraccia perfettamente l’angolo in alto a sinistra del grafico riportato, con AUC pari a 1. Anche la curva relativa alla classe 0 (nessun guasto) è buona con un’AUC pari a 0.96. Più bassa è invece la curva, e rispettivo AUC, relativamente al volo con guasto al 5%.

#### 1.2 Matrice di confusione
Riportiamo in figura 4.2 i risultati numerici della predizione mediante matrice di confusione, applicando l’insieme dei dati di test al modello addestrato.

<table align="center" border="none">
<tr>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/curva_roc_logreg.png" width=100%>
Figura 4.1: Curva ROC Logistic Regression
  </td>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/ConfMat_logreg.png" width=100%>
Figura 4.2: Matrice di confusione Logistic Regression
</td>
</tr>
</table>

Come possiamo vedere, il modello è in grado di effettuare una netta distinzione tra dati appartenenti alla classe 0 (nessun guasto) e dati appartenenti alla classe 2 (guasto al 10%). Maggiore difficoltà è riscontrata nell’individuazione dei dati appartenenti alla classe 1 (guasto al 10%).

### 2 Decision Tree
Con il Decision Tree riusciamo ad ottenere un’accuratezza pari al 65%. In questo caso l’accuratezza classica risulta essere di molto più bassa rispetto al classificatore
precedente. E' in grado di distinguere un volo con drone senza guasto da un volo con drone con guasto al 10% ma, trova estrema difficoltà nell’individuare i voli relativi a droni con guasto al 5%. Di seguito riportiamo i risultati delle accuratezze ottenute mediante cross-validation:

<p align="center">
<table border="0", align="center">
<tr>
<td>0.66153846</td>
<td>0.65846154</td>
<td>0.65538462</td>
<td>0.65123457</td>
<td>0.66049383</td>
</table>
</p>

Come possiamo vedere dai risultati ottenuti, per ciascun test fatto sui fold, le
accuratezze che otteniamo sono relativamente basse, ma comunque conformi al
risultato ottenuto dal calcolo dell’accuratezza semplice.

#### 2.1 Curva ROC
Per quanto riguarda il Decision Tree, la curva ROC ottenuta è riportata in figura 4.3. In questo caso, la curva ROC relativa alla classe 0 (volo senza guasto) abbraccia molto bene l’angolo in alto a sinistra, rispetto invece alle altre due curve, in quanto il classificatore non è in grado di distinguere i voli di classe 1 dai voli di classe 2. Il valore AUC è molto buono per la classe 0, risulta essere invece più basso per le altre due classi.
#### 2.2 Matrice di confusione
Riportiamo in figura 4.4 i risultati numerici della predizione mediante matrice di confusione, applicando l’insieme dei dati di test al modello addestrato.

<table align="center" border="none">
<tr>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/curva_roc_dectree.png" width=100%>
Figura 4.3: Curva ROC Decision Tree
  </td>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/ConfMat_dectree.png" width=100%>
Figura 4.4: Matrice di confusione Decision Tree
</td>
</tr>
</table>

Come possiamo osservare, il modello è in grado di individuare gli elementi di classe 0 correttamente ma, ha estrema difficoltà nell’andare a riconoscere gli elementi di classe 1, andandoli a classificare tutti di classe 2, comportando quindi la generazione di falsi allarmi in caso di guasto al 5% che viene segnalato come guasto al 10%.

### 3 Random Forest
Con il Random Forest riusciamo ad ottenere un’accuratezza pari al 95%. In questo
caso l’accuratezza classica risulta essere la più alta che riusciamo ad ottenere,
rispetto alle accuratezze di tutti gli altri classificatori. E' in grado di distinguere
tutti le diverse categorie di classificazione in modo corretto, senza compiere errori
rilevanti. Distigue perfettamente i voli relativi a droni senza guasto da voli relativi
a droni con guasto al 10%. Presenta una lieve difficoltà nel distinguere voli di droni
con guasto al 5% da voli di droni con guasto al 10% ed anche con voli di droni
senza guasto. Di seguito riportiamo i risultati delle accuratezze ottenute mediante
cross-validation:

<p align="center">
<table border="0", align="center">
<tr>
<td>0.93538462</td>
<td>0.96615385</td>
<td>0.94461538</td>
<td>0.94753086</td>
<td>0.94753086</td>
</table>
</p>

Come possiamo vedere dai risultati ottenuti, per ciascun test fatto sui fold, le accu-
ratezze che otteniamo sono relativamente alte, ma comunque conformi al risultato
ottenuto dal calcolo dell’accuratezza semplice.
#### 3.1 Curva ROC
Per quanto riguarda il Random Forest, la curva ROC ottenuta è riportata in figura
4.5. Come possiamo osservare, la classe 0 e la classe 2 hanno entrambe una curva ROC che abbraccia perfettamente l’angolo in alto a sinistra del grafico, significato
del fatto che entrambe sono perfettamente distinguibili, con un valore dell’AUC
pari a 1 per entrambe le classi. Simile è la situazione la classe 1 con una curva
ROC leggermente diversa e pi`u spostata verso il basso in alcuni punti ed un valore
dell’AUC pari a 0.96.

#### 3.2 Matrice di confusione
Riportiamo in figura 4.6 i risultati numerici della predizione mediante matrice di
confusione, applicando l’insieme dei dati di test al modello addestrato.

<table align="center" border="none">
<tr>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/curva_roc_randfor.png" width=100%>
Figura 4.5: Curva ROC Random Forest
  </td>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/ConfMat_randfor.png" width=100%>
Figura 4.6: Matrice di confusione Random Forest
</td>
</tr>
</table>

Come possiamo osservare, quasi tutti i dati presenti nell’insieme di test sono stati assegnati alla classe corretta. Si può osservare una lieve sfumatura  nell’assegnazione degli elementi di classe 1 alle altre classi mentre, tutti gli elementi di classe 2 vengono correttamente assegnati alla rispettiva classe di appartenenza, senza commettere alcun errore. Nel complesso il risultato che otteniamo è molto buono.

### 4 Support Vector Machine
Con il Support Vector Machine riusciamo ad ottenere un’accuratezza pari al 67%.
In questo caso l’accuratezza classica risulta essere di molto più bassa rispetto al
classificatore precedente. In questo caso il modello risultante non presenta grandi
difficoltà nella classificazione di voli di drone con guasto al 10%. Non possiamo
però dire la stessa cosa però per quanto riguarda la distinzione tra i voli relativi
a droni senza guasto rispetto a voli relativi a droni con guasto al 5%, in quanto il
classificatore presenta delle difficoltà nell’assegnazione degli elementi alle rispettive
classi. Di seguito riportiamo i risultati delle accuratezze ottenute mediante cross-validation:

<p align="center">
<table border="0", align="center">
<tr>
<td>0.68307692</td>
<td>0.67384615</td>
<td>0.63076923</td>
<td>0.69753086</td>
<td>0.65740741</td>
</table>
</p>

Come possiamo vedere dai risultati ottenuti, per ciascun test fatto sui fold, le
accuratezze che otteniamo sono relativamente basse, ma comunque conformi al
risultato ottenuto dal calcolo dell’accuratezza semplice.
#### 4.1 Curva ROC
Per quanto riguarda il Support Vector Machine, la curva ROC ottenuta è riportata
in figura 4.7.
Come possiamo osservare dal grafico riportato, la curva ROC relativa alla classe 2
abbraccia l’angolo in alto a sinistra, a ragion del fatto che il classificatore è in grado
di distinguere correttamente gli elementi classe 2 dagli altri elementi appartenenti
alle altre classi, con un valore AUC pari allo 9.99. Le altre due classi hanno una
curva ROC decisamente pi`u bassa rispetto alla precedente, relative al fatto che il
classificatore fa difficoltà nella distinzione dei dati. La curva ROC della classe 0 è
leggermente migliore della curva ROC della classe 1 con un AUC di 0.75 mentre
di 0.78 per la classe 0.

#### 4.2 Matrice di confusione
Riportiamo in figura 4.8 i risultati numerici della predizione mediante matrice di
confusione, applicando l’insieme dei dati di test al modello addestrato.

<table align="center" border="none">
<tr>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/curva_roc_svm.png" width=100%>
Figura 4.7: Curva ROC Support Vector Machine
  </td>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/ConfMat_svm.png" width=100%>
Figura 4.8: Matrice di confusione Random Forest
</td>
</tr>
</table>

Come possiamo osservare, non vi sono errori rilevanti per quanto riguarda l’assegnazione degli elementi di classe 2. Particolari differenze invece le possiamo
osservare nell’assegnazione degli elementi di classe 0 e di classe 1. Per quanto riguarda la classe dei voli relativi a droni senza guasto (classe 0), il classificatore è in
grado di fare una corretta distinzione tra questi e i voli relativi a droni con guasto
al 10% (guasto 2), presenta invece una maggiore difficoltà nel distinguere voli senza
guasto da voli con guasto al 5%. Comunque, in questa situazione, la maggior parte
dei dati di classe 0 è assegnato correttamente (su 112 elementi di classe 0, 73 sono
assegnati alla classe corretta, 33 alla classe 1 e 6 alla classe 2). Un discorso simile
può essere fatto per gli elementi di classe 1: in questo caso però, poco meno della
metà degli elementi `e assegnato alla classe corretta (su 109 elementi di classe 1,
51 sono assegnati alla classe corretta, 53 sono assegnati alla classe 0 e 5 alla classe
2). Questo classificatore commette quindi un errore maggiore nell’assegnazione dei
dati di test alle relative classi di appartenenza.

### 5 Multi Layer Perceptron
Con il Multi Layer Perceptron riusciamo ad ottenere un’accuratezza pari al 0.91%.
In questo caso il risultato dell’accuratezza è relativamente alto, in questo caso il
classificatore è in grado di distinguere abbastanza bene gli elementi delle diverse
classi, con qualche incertezza, soprattutto nell’assegnazione degli elementi di classe
1. Di seguito riportiamo i risultati delle accuratezze ottenute mediante cross-
validation:

<p align="center">
<table border="0", align="center">
<tr>
<td>0.88</td>
<td>0.93846154</td>
<td>0.89230769</td>
<td>0.73148148</td>
<td>0.91358025</td>
</table>
</p>

Come possiamo vedere dai risultati ottenuti, per ciascun test fatto sui fold, le accuratezze che otteniamo sono relativamente alte. In questa casistica osserviamo
però una maggiore variazione dell’accuratezza rispetto ai classificatori precedenti,
soprattutto perch ́e, per quanto riguarda la quarta iterazione, otteniamo un’accuratezza di circa 0.73, rispetto alle accuratezze degli altri fold che sono più alte ed
anche più conformi all’accuratezza classica.

#### 5.1 Curva ROC
Per quanto riguarda il Multi Layer Perceptron, la curva ROC ottenuta è riportata
in figura 4.9.
Come possiamo osservare dal grafico riportato, tutte e tre le curve ROC presentano
un andamento simile ed abbracciano molto bene l’angolo in alto a sinistra. Sia
la classe 0 che la classe 2 hanno un valore AUC pari a 0.99, l’AUC relativo alla
classe 1 è invece un po’ pi`u basso, pari allo 0.97, sempre a causa del fatto che si fa
maggiore difficoltà nell’assegnare i dati a questa classe.

#### 5.2 Matrice di confusione
Riportiamo in figura 4.10 i risultati numerici della predizione mediante matrice di
confusione, applicando l’insieme dei dati di test al modello addestrato.

<table align="center" border="none">
<tr>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/curva_roc_mlp.png" width=100%>
Figura 4.9: Curva ROC Support Multi Layer Perceptron
  </td>
<td align="center">
<img src="https://github.com/ChiaraAmalia/ProgettoManutenzionePreventiva/blob/main/immagini/ConfMat_mlp.png" width=100%>
Figura 4.8: Matrice di confusione Multi Layer Perceptron
</td>
</tr>
</table>

Come possiamo vedere, i dati che vengono classificati in modo migliore sono quelli
relativi alla classe 0, in cui solo 2 elementi vengono assegnati ad una classe diversa
da quella corretta. Maggiore differenza la riscontriamo sempre nell’assegnazione
degli elementi di classe 1, in cui una discreta quantità viene assegnata erroneamente
alla classe 0 ed una piccola quantità alla classe 2; tutto sommato la maggior parte
degli elementi viene assegnato alla classe corretta. Simili affermazioni possiamo
farle per la classe 2.

# Autori

:woman_technologist: [Chiara Amalia Caporusso](https://github.com/ChiaraAmalia) 

:woman_technologist: [Margherita Galeazzi](https://github.com/MargheritaGaleazzi)
