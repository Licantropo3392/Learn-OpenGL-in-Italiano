# Cos'è OpenGL?

Prima di iniziare il nostro viaggio, dovremmo innanzitutto definire cosa sia effettivamente OpenGL. OpenGL è considerato principalmente un'<span style="color: green;">**API**</span> (un <span style="color: green;">*Application Programming Interface*</span>) che ci fornisce un ampio set di funzioni per manipolare grafica e immagini. Tuttavia, OpenGL di per sé non è un'API, bensì una mera **specifica**, sviluppata e mantenuta dal [Khronos Group](http://www.khronos.org/).

La specifica di OpenGL stabilisce esattamente quale debba essere il risultato/output di ciascuna funzione e come debba comportarsi. Spetta poi agli sviluppatori che implementano questa specifica trovare una soluzione pratica su come far operare tale funzione. Poiché la specifica di OpenGL non fornisce dettagli sull'implementazione, le versioni effettive sviluppate di OpenGL possono avere implementazioni diverse, a patto che i loro risultati siano conformi alla specifica (e risultino quindi identici per l'utente).

Coloro che sviluppano le librerie OpenGL effettive sono solitamente i produttori di schede grafiche. Ogni scheda grafica che acquisti supporta versioni specifiche di OpenGL, che sono le versioni sviluppate appositamente per quella (serie di) scheda. Quando si utilizza un sistema Apple, la libreria OpenGL è mantenuta dalla stessa Apple, mentre sotto Linux esiste una combinazione tra le versioni dei produttori hardware e gli adattamenti di queste librerie da parte di appassionati. Ciò significa anche che ogni volta che OpenGL mostra un comportamento strano che non dovrebbe avere, la colpa è molto probabilmente dei produttori di schede grafiche (o di chiunque abbia sviluppato/mantenuto la libreria).

!!! note "Nota"
    Poiché la maggior parte delle implementazioni è creata dai produttori di schede grafiche, quando si presenta un bug nell'implementazione questo viene solitamente risolto aggiornando i driver della scheda video; tali driver includono le versioni più recenti di OpenGL supportate dalla tua scheda. Questo è uno dei motivi per cui si consiglia sempre di aggiornare periodicamente i driver grafici.

Khronos ospita pubblicamente tutti i documenti delle specifiche per tutte le versioni di OpenGL. Il lettore interessato può trovare la specifica di OpenGL per la versione 3.3 (che è quella che useremo) [qui](https://www.opengl.org/registry/doc/glspec33.core.20100311.withchanges.pdf): è un'ottima lettura se vuoi approfondire i dettagli di OpenGL (nota come descrivano principalmente solo i risultati e non le implementazioni). Le specifiche forniscono anche un ottimo punto di riferimento per scoprire l'esatto funzionamento delle sue funzioni.

---

## Core-profile vs Immediate mode (Modalità immediata)

In passato, utilizzare OpenGL significava sviluppare in <span style="color: green;">**immediate mode**</span> (spesso definita <span style="color: green;">*fixed function pipeline*</span>), che era un metodo facile da usare per disegnare elementi grafici. La maggior parte delle funzionalità di OpenGL era nascosta all'interno della libreria e gli sviluppatori non avevano molto controllo sul modo in cui OpenGL eseguiva i suoi calcoli. Con il tempo, gli sviluppatori hanno iniziato a richiedere maggiore flessibilità e, di conseguenza, le specifiche sono diventate più elastiche; gli programmatori hanno così ottenuto un maggiore controllo sulla propria grafica. La modalità immediata è davvero facile da usare e da capire, ma è anche estremamente inefficiente. Per questo motivo, a partire dalla versione 3.2, la specifica ha iniziato a considerare *deprecata* la modalità immediata, spingendo gli sviluppatori a programmare in modalità <span style="color: green;">**core-profile**</span> di OpenGL, che è una sezione della specifica da cui sono state rimosse tutte le vecchie funzionalità obsolete.

Quando utilizziamo il core-profile di OpenGL, l'API ci costringe a usare pratiche moderne. Ogni volta che proviamo a utilizzare una delle funzioni deprecate di OpenGL, quest'ultima solleva un errore e smette di disegnare. Il vantaggio di imparare l'approccio moderno è che è molto flessibile ed efficiente. Tuttavia, è anche più difficile da apprendere. La modalità immediata astraeva parecchio dalle operazioni effettive compiute da OpenGL e, sebbene fosse facile da imparare, era difficile afferrare come OpenGL funzionasse davvero. L'approccio moderno richiede che lo sviluppatore comprenda veramente OpenGL e la programmazione grafica e, sebbene sia un po' ostico, consente molta più flessibilità, maggiore efficienza e, cosa fondamentale, una comprensione decisamente migliore della programmazione grafica.

Questo è anche il motivo per cui questo libro è orientato alla versione **OpenGL 3.3 in core-profile**. Sebbene sia più difficile, ne vale ampiamente la pena.

Ad oggi sono disponibili versioni più recenti di OpenGL tra cui scegliere (la 4.6 al momento della scrittura), perciò potresti chiederti: *perché dovrei voler imparare OpenGL 3.3 quando è già uscita la versione 4.6?* La risposta a questa domanda è relativamente semplice. Tutte le versioni future di OpenGL a partire dalla 3.3 aggiungono utili funzionalità extra senza modificare i meccanismi fondamentali dell'architettura; le versioni più recenti introducono semplicemente modi leggermente più efficienti o più utili per portare a termine gli stessi compiti. Il risultato è che tutti i concetti e le tecniche rimangono identici nelle versioni moderne di OpenGL, quindi è perfettamente sensato imparare OpenGL 3.3. Non appena sarai pronto e avrai più esperienza, potrai facilmente utilizzare le funzionalità specifiche delle versioni più recenti.

!!! warning "Attenzione"
    Quando utilizzi le funzionalità dell'ultima versione di OpenGL, solo le schede grafiche più moderne saranno in grado di eseguire la tua applicazione. Questo è il motivo per cui la maggior parte degli sviluppatori in genere si rivolge a versioni inferiori di OpenGL e abilita opzionalmente le funzionalità delle versioni superiori.

In alcuni capitoli troverai caratteristiche più moderne, che saranno opportunamente segnalate come tali.

---

## Estensioni

Una grande caratteristica di OpenGL è il suo supporto alle <span style="color: green;">**estensioni**</span> (<span style="color: green;">*extensions*</span>). Ogni volta che un'azienda grafica inventa una nuova tecnica o una nuova grande ottimizzazione per il rendering, questa si trova spesso sotto forma di estensione implementata nei driver. Se l'hardware su cui gira un'applicazione supporta tale estensione, lo sviluppatore può utilizzare le funzionalità fornite dall'estensione per ottenere una grafica più avanzata o efficiente. In questo modo, un programmatore grafico può utilizzare queste nuove tecniche di rendering senza dover aspettare che OpenGL includa la funzionalità nelle sue versioni future, semplicemente verificando se l'estensione è supportata dalla scheda grafica. Spesso, quando un'estensione è popolare o molto utile, finisce per diventare parte delle future versioni di OpenGL.

Lo sviluppatore deve verificare se una di queste estensioni sia disponibile prima di utilizzarla (oppure usare una libreria di estensioni OpenGL). Questo permette di fare le cose meglio o in modo più efficiente, in base alla disponibilità dell'estensione stessa:

```cpp
if(GL_ARB_extension_name)
{
    // Esegui le nuove cose fighe e moderne supportate dall'hardware
}
else
{
    // Estensione non supportata: procedi alla vecchia maniera
}
```

Con la versione 3.3 di OpenGL raramente avremo bisogno di un'estensione per la maggior parte delle tecniche, ma ovunque sia necessario verranno fornite le dovute istruzioni.

---

## Macchina a stati

OpenGL è di per sé una grande <span style="color: green;">**macchina a stati**</span> (<span style="color: green;">*state machine*</span>): una collezione di variabili che definiscono come OpenGL debba attualmente operare. Lo stato di OpenGL viene comunemente chiamato <span style="color: green;">**contesto**</span> (<span style="color: green;">*OpenGL context*</span>). Quando usiamo OpenGL, spesso cambiamo il suo stato impostando alcune opzioni, manipolando alcuni buffer e infine eseguendo il rendering utilizzando il contesto corrente.

Ogni volta che diciamo a OpenGL che ora vogliamo disegnare linee invece di triangoli, ad esempio, cambiamo lo stato di OpenGL modificando una variabile del contesto che stabilisce come OpenGL debba disegnare. Non appena cambiamo il contesto dicendo a OpenGL che deve disegnare linee, i successivi comandi di disegno disegneranno linee invece di triangoli.

Lavorando in OpenGL ci imbatteremo in diverse <span style="color: green;">**funzioni che modificano lo stato**</span> (*cambiando il contesto*) e in diverse <span style="color: green;">**funzioni che utilizzano lo stato**</span> (*che eseguono operazioni basate sullo stato corrente di OpenGL*). Finché terrai a mente che OpenGL è fondamentalmente una grande macchina a stati, la maggior parte delle sue funzionalità avrà molto più senso.

---

## Oggetti

Le librerie OpenGL sono scritte in C e consentono molte derivazioni in altri linguaggi, ma nel profondo rimangono una libreria C. Poiché molti costrutti del linguaggio C non si traducono al meglio in altri linguaggi di alto livello, OpenGL è stata sviluppata con diverse astrazioni in mente. Una di queste astrazioni sono gli <span style="color: green;">**oggetti**</span> (<span style="color: green;">*objects*</span>).

Un <span style="color: green;">*oggetto*</span> in OpenGL è una raccolta di opzioni che rappresenta un sottoinsieme dello stato di OpenGL. Ad esempio, potremmo avere un oggetto che rappresenta le impostazioni della finestra di disegno; potremmo quindi impostare la sua dimensione, quanti colori supporta e così via. Si potrebbe visualizzare un oggetto come una struttura (struct) in stile C:

```cpp
struct object_name {
    float  option1;
    int    option2;
    char[] name;
};
```

Ogni volta che vogliamo usare gli oggetti, il flusso si presenta generalmente così (con il contesto di OpenGL visualizzato come una grande struct):

```cpp
// Lo Stato di OpenGL
struct OpenGL_Context {
  	...
  	object_name* object_Window_Target;
  	...  	
};

// 1. Crea l'oggetto
unsigned int objectId = 0;
glGenObject(1, &objectId);

// 2. Lega/assegna l'oggetto al contesto
glBindObject(GL_WINDOW_TARGET, objectId);

// 3. Imposta le opzioni dell'oggetto attualmente legato a GL_WINDOW_TARGET
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_WIDTH,  800);
glSetObjectOption(GL_WINDOW_TARGET, GL_OPTION_WINDOW_HEIGHT, 600);

// 4. Ripristina il target del contesto sul valore predefinito
glBindObject(GL_WINDOW_TARGET, 0);
```

Questo piccolo frammento di codice rappresenta un flusso di lavoro (*workflow*) che vedrai frequentemente lavorando con OpenGL. Per prima cosa creiamo un oggetto e memorizziamo un riferimento ad esso sotto forma di ID (i dati reali dell'oggetto sono memorizzati dietro le quinte). Quindi leghiamo l'oggetto (usando il suo ID) alla posizione di destinazione del contesto (la posizione dell'oggetto finestra dell'esempio è definita come <span style="color: blue;">**GL_WINDOW_TARGET**</span>). Successivamente impostiamo le opzioni della finestra e, infine, sleghiamo (*un-bind*) l'oggetto impostando a 0 l'ID dell'oggetto corrente del target della finestra. Le opzioni che abbiamo impostato vengono memorizzate nell'oggetto referenziato da <span style="color: blue;">**objectId**</span> e ripristinate non appena leghiamo nuovamente l'oggetto a <span style="color: blue;">**GL_WINDOW_TARGET**</span>.

!!! warning
    Gli esempi di codice forniti finora sono solo approssimazioni di come opera OpenGL; nel corso del libro incontrerai abbastanza esempi reali.

Il grande vantaggio dell'utilizzo di questi oggetti è che possiamo definire più di un oggetto nella nostra applicazione, impostare le loro opzioni e, ogni volta che iniziamo un'operazione che utilizza lo stato di OpenGL, leghiamo l'oggetto con le nostre impostazioni preferite. Esistono oggetti, ad esempio, che fungono da contenitori per i dati dei modelli 3D (una casa o un personaggio) e ogni volta che vogliamo disegnarne uno, leghiamo l'oggetto contenente i dati del modello che vogliamo renderizzare (dopo aver precedentemente creato e configurato le opzioni per questi oggetti). Avere più oggetti ci permette di specificare molti modelli e, quando vogliamo disegnarne uno specifico, ci basta semplicemente legare l'oggetto corrispondente prima di disegnare, senza dover impostare nuovamente tutte le loro opzioni.

---

## Cominciamo!

Ora hai imparato qualcosa in più su OpenGL come specifica e come libreria, su come funziona approssimativamente sotto il cofano e su alcuni trucchi personalizzati utilizzati da OpenGL. Non preoccuparti se non hai capito tutto subito; nel corso del libro esamineremo ogni passaggio e vedrai abbastanza esempi da comprendere appieno OpenGL.

### Risorse aggiuntive

* [opengl.org](https://www.opengl.org): sito ufficiale di OpenGL.
* [OpenGL registry](https://registry.khronos.org/OpenGL/index_gl.php): ospita le specifiche e le estensioni di OpenGL per tutte le versioni.