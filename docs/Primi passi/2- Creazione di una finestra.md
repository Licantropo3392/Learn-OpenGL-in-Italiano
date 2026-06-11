# Creazione di una finestra

La prima cosa da fare prima di iniziare a creare grafiche sbalorditive è configurare un contesto OpenGL e una finestra dell'applicazione in cui disegnare. Tuttavia, queste operazioni cambiano da un sistema operativo all'altro, e OpenGL cerca intenzionalmente di astrarsi da tali procedure. Ciò significa che dovremmo occuparci di creare una finestra, definire un contesto e gestire l'input dell'utente interamente da soli.

Fortunatamente esistono diverse librerie che offrono proprio le funzionalità che cerchiamo, alcune delle quali pensate specificamente per OpenGL. Queste librerie ci risparmiano tutto il lavoro legato allo specifico sistema operativo, fornendoci una finestra e un contesto OpenGL in cui renderizzare. Tra le più popolari troviamo GLUT, SDL, SFML e GLFW. Su LearnOpenGL utilizzeremo **GLFW**. Sentiti libero di usare una qualsiasi delle altre librerie: la configurazione della maggior parte di esse è molto simile a quella di GLFW.

---

## GLFW

GLFW è una libreria scritta in C, mirata specificamente a OpenGL. Ci fornisce lo stretto indispensabile per renderizzare contenuti sullo schermo: ci permette di creare un contesto OpenGL, definire i parametri della finestra e gestire l'input dell'utente, il che è più che sufficiente per i nostri scopi.

L'obiettivo di questo capitolo e del prossimo è installare e avviare GLFW, assicurandoci che crei correttamente un contesto OpenGL e che mostri una semplice finestra con cui iniziare a sperimentare. Questo capitolo adotta un approccio passo-passo per scaricare, compilare e collegare (*linkare*) la libreria GLFW. Al momento della scrittura utilizzeremo l'IDE Microsoft Visual Studio 2019 (nota che il processo è identico sulle versioni più recenti di Visual Studio). Se utilizzi un altro IDE (o una versione precedente), non preoccuparti: il procedimento sarà simile sulla maggior parte degli altri ambienti di sviluppo.

---

## Compilazione di GLFW

GLFW può essere scaricata dalla pagina di [download](http://www.glfw.org/download.html) del loro sito ufficiale. GLFW fornisce già file di intestazione (*header files*) e binari precompilati per Visual Studio dal 2012 fino al 2019, ma per completezza compileremo GLFW partendo direttamente dal codice sorgente. Questo ti permetterà di comprendere il processo di compilazione delle librerie open source, dato che non tutte mettono a disposizione dei binari precompilati. Procediamo quindi a scaricare il *Source package*.

!!! warning "Attenzione"
    Compileremo tutte le librerie come binari a 64 bit, quindi assicurati di scaricare i binari a 64 bit nel caso decidessi di usare quelli precompilati.

Una volta scaricato il pacchetto dei sorgenti, estrailo e apri il suo contenuto. Ci interessano solo pochi elementi:

1. La libreria risultante dalla compilazione.
2. La cartella `include`.

Compilare la libreria dal codice sorgente garantisce che il file finale sia perfettamente ottimizzato per la tua CPU e il tuo sistema operativo, un lusso che i binari precompilati non sempre offrono. Il problema nel distribuire il codice sorgente al pubblico, però, è che non tutti usano lo stesso IDE o lo stesso sistema di build per sviluppare le proprie applicazioni; ciò significa che i file di progetto o di soluzione inclusi potrebbero non essere compatibili con la configurazione di altre persone. Gli utenti dovrebbero quindi configurare un proprio progetto inserendo manualmente i file `.c`/`.cpp` e `.h`/`.hpp`, il che è piuttosto noioso. Proprio per risolvere questo problema esiste uno strumento chiamato **CMake**.

### CMake

CMake è un tool in grado di generare file di progetto o di soluzione a scelta dell'utente (ad esempio per Visual Studio, Code::Blocks, Eclipse) a partire da una serie di file sorgente, utilizzando degli script CMake predefiniti. Questo ci permette di generare un file di progetto di Visual Studio 2019 partendo dal pacchetto sorgente di GLFW. Per prima cosa dobbiamo scaricare e installare CMake dalla loro pagina di [download](http://www.cmake.org/cmake/resources/software.html).

Una volta installato, puoi scegliere di avviare CMake da riga di comando o tramite la loro interfaccia grafica (GUI). Per non complicare troppo le cose useremo l'interfaccia grafica. CMake richiede una cartella contenente il codice sorgente e una cartella di destinazione per i file binari. Per la cartella sorgente selezioniamo la radice della cartella del pacchetto sorgente di GLFW che abbiamo scaricato in precedenza e per la cartella di build creiamo una nuova cartella chiamata *build* e poi selezionala.

![CMake GUI](https://learnopengl.com/img/getting-started/cmake.png "CMake GUI")

Una volta impostate le cartelle, clicca sul pulsante `Configure` in modo che CMake possa leggere le impostazioni richieste e il codice sorgente. Dovremo poi scegliere il generatore per il progetto: poiché stiamo usando Visual Studio 2019, sceglieremo l'opzione `Visual Studio 16` (Visual Studio 2019 è noto anche come Visual Studio 16). CMake mostrerà ora le possibili opzioni di build per configurare la libreria risultante. Possiamo lasciarle ai loro valori predefiniti e cliccare di nuovo su `Configure` per salvare le impostazioni. Infine, clicchiamo su `Generate` i file di progetto verranno generati nella cartella `build`.

### Compilazione

Nella cartella `build` troverai un file chiamato `GLFW.sln`; aprilo con Visual Studio 2019. Poiché CMake ha generato un file di progetto che contiene già le giuste configurazioni, dobbiamo solo compilare la soluzione. Assicurati che la configurazione di compilazione in alto sia impostata su **Debug** e **x64**, quindi clicca su `Genera soluzione` (`Build Solution`). Questo produrrà un file di libreria compilato che si troverà in `build/src/Debug` col nome di `glfw3.lib`.

Ora che abbiamo generato la libreria, dobbiamo assicurarci che il nostro IDE sappia dove trovare sia la libreria stessa che i file di inclusione per il nostro programma OpenGL. Ci sono due approcci comuni per farlo:

1. **Approccio 1 (Sconsigliato):** Trovare le cartelle `/lib` e `/include` dell'IDE o del compilatore e aggiungere il contenuto della cartella `include` di GLFW nella cartella `/include` dell'IDE, e allo stesso modo aggiungere `glfw3.lib` nella cartella `/lib` dell'IDE. Questo funziona, ma non è l'approccio raccomandato: diventa difficile tenere traccia dei file e, in caso di una nuova installazione dell'IDE o del compilatore, dovrai ripetere il processo da capo.
2. **Approccio 2 (Raccomandato):** Creare una nuova struttura di cartelle in una posizione a tua scelta che contenga tutti i file di intestazione e le librerie di terze parti a cui fare riferimento dall'IDE. Potresti, ad esempio, creare una singola cartella principale che contiene una cartella `Libs` e una cartella `Include`, dove memorizzerai rispettivamente i file `.lib` e gli header per i progetti OpenGL. In questo modo tutte le librerie esterne sono organizzate in un unico posto (che può anche essere condiviso tra più computer). L'unico requisito è che ogni volta che creiamo un nuovo progetto, dobbiamo indicare all'IDE dove trovare queste cartelle.

Una volta salvati i file in una posizione di tua scelta, possiamo iniziare a creare il nostro primo progetto OpenGL con GLFW.

---

## Il nostro primo progetto

Per prima cosa apriamo Visual Studio e creiamo un nuovo progetto. Scegli **C++** se vengono mostrate più opzioni e seleziona `Progetto vuoto` (*Empty Project*) (ricordandoti di dare al progetto un nome appropriato). Dato che compileremo tutto a 64 bit e il progetto parte di default a 32 bit, dobbiamo cambiare il menu a discesa in alto (accanto a Debug) da `x86` a `x64`:

![Menu a tendina](https://learnopengl.com/img/getting-started/x64.png "Menu a tendina")

Fatto questo, abbiamo finalmente uno spazio di lavoro pronto per ospitare la nostra primissima applicazione OpenGL!

### Collegamento (Linking)

Affinché il progetto possa utilizzare GLFW, dobbiamo <span style="color: green;">linkare</span> la libreria al progetto. Questo si fa specificando che vogliamo usare `glfw3.lib` nelle impostazioni del linker, ma il nostro progetto non sa ancora dove trovare questo file poiché abbiamo salvato le librerie in un'altra cartella. Dobbiamo quindi aggiungere prima questa directory al progetto.

Possiamo istruire l'IDE affinché prenda in considerazione questa cartella quando cerca i file di libreria e di inclusione. Fai clic con il tasto destro sul nome del progetto nell'`Esploratore delle soluzioni` (`Solution Explorer`) e vai su `Proprietà`, poi su `Directory di VC++` (`VC++ Directories`) come da immagine:

![Proprietà](https://learnopengl.com/img/getting-started/vc_directories.png "Proprietà")

Da qui in poi puoi aggiungere le tue cartelle per consentire al progetto di sapere dove cercare. Puoi farlo inserendo manualmente il percorso nel testo o cliccando sulla riga corrispondente e selezionando l'opzione `<Modifica...>` (`<Edit...>`). Fallo sia per le `Directory delle librerie` (`Library Directories`) che per le `Directory di inclusione` (`Include Directories`).

![Library Directories](https://learnopengl.com/img/getting-started/include_directories.png "Library Directories")

Qui puoi aggiungere tutte le cartelle extra che desideri e, da quel momento in poi, l'IDE cercherà anche in quelle directory. Non appena includi la cartella `Include` di GLFW, sarai in grado di trovare tutti i suoi file di intestazione scrivendo `#include <GLFW/...>`. Lo stesso vale per le directory delle librerie.

Dato che Visual Studio ora può trovare tutti i file richiesti, possiamo finalmente linkare GLFW al progetto andando sulla scheda `Linker` e poi su `Input`:

![Linker](https://learnopengl.com/img/getting-started/linker_input.png "Linker")

Per collegare una libreria devi specificare il suo nome al linker. Poiché il nome della libreria è `glfw3.lib`, aggiungilo al campo `Dipendenze aggiuntive` (`Additional Dependencies`) (sia manualmente che usando l'opzione `<Modifica...>`) e da questo momento in poi, GLFW verrà collegata durante la compilazione.

Oltre a GLFW, dobbiamo aggiungere un collegamento alla libreria di OpenGL, ma questo passaggio varia a seconda del sistema operativo:

#### Libreria OpenGL su Windows

Se sei su Windows, la libreria OpenGL `opengl32.lib` viene fornita insieme a Microsoft SDK, che è installato di default con Visual Studio. Poiché questo capitolo utilizza il compilatore di VS ed è su Windows, aggiungiamo `opengl32.lib` alle impostazioni del linker (nel campo *Dipendenze aggiuntive*). Nota che l'equivalente a 64 bit della libreria OpenGL si chiama comunque `opengl32.lib`, esattamente come quello a 32 bit, il che è una scelta di nome un po' sfortunata.

#### Libreria OpenGL su Linux

Sui sistemi Linux devi collegarti alla libreria `libGL.so` aggiungendo `-lGL` alle impostazioni del linker. Se non riesci a trovare la libreria, probabilmente devi installare i pacchetti di sviluppo Mesa, NVIDIA o AMD (es. `libgl1-mesa-dev`).

Una volta aggiunte sia la libreria GLFW che quella OpenGL nelle impostazioni del linker, puoi includere i file di intestazione di GLFW nel codice in questo modo:

```cpp
#include <GLFW/glfw3.h>
```

!!! note "Nota per gli utenti Linux"
    Se compili con GCC, i seguenti parametri sulla riga di comando ti aiuteranno a compilare il progetto: `-lglfw3 -lGL -lX11 -lpthread -lXrandr -lXi -ldl`. Non collegare correttamente queste librerie genererà molti errori di tipo "undefined reference" (riferimento non definito).

Questo conclude la configurazione di GLFW.

---

## GLAD

Non siamo ancora del tutto pronti, perché c'è un'ultima cosa che dobbiamo fare. Poiché OpenGL è essenzialmente solo uno standard/specifica, spetta al produttore del driver implementarlo in un driver software supportato dalla specifica scheda grafica. Poiché esistono tantissime versioni diverse di driver OpenGL, la posizione di gran parte delle sue funzioni non è nota a tempo di compilazione (*compile-time*) e deve essere richiesta a tempo di esecuzione (*run-time*). È quindi compito del programmatore recuperare la posizione delle funzioni di cui ha bisogno e memorizzarle in dei puntatori a funzione per un uso successivo.

Il recupero di queste posizioni dipende dal sistema operativo. In Windows, il processo si presenta all'incircica così:

```cpp
// 1. Definisci il prototipo della funzione
typedef void (*GL_GENBUFFERS) (GLsizei, GLuint*);
// 2. Trova la funzione e assegnala a un puntatore a funzione
GL_GENBUFFERS glGenBuffers = (GL_GENBUFFERS)wglGetProcAddress("glGenBuffers");
// 3. Ora la funzione può essere chiamata normalmente
unsigned int buffer;
glGenBuffers(1, &buffer);
```

Come puoi vedere, il codice appare complesso ed è un processo estremamente noioso da ripetere per ogni singola funzione di cui potresti aver bisogno e che non è ancora dichiarata. Per fortuna esistono librerie create appositamente per questo scopo, tra cui **GLAD** è una delle più popolari e aggiornate.

### Configurazione di GLAD

GLAD è una libreria [open source](https://github.com/Dav1dde/glad) che gestisce tutto il lavoro noioso di cui abbiamo appena parlato. Ha un sistema di configurazione leggermente diverso rispetto alla maggior parte delle librerie open source tradizionali: utilizza un [sito web](http://glad.dav1d.de/) in cui possiamo indicare quale versione di OpenGL desideriamo definire e caricare tutte le relative funzioni.

1. Vai sul [sito web di GLAD](https://glad.dav1d.de/).
2. Assicurati che il linguaggio (*Language*) sia impostato su **C++**.
3. Nella sezione **API**, seleziona una versione di OpenGL che sia almeno la **3.3** (che è quella che useremo noi, ma versioni superiori vanno altrettanto bene).
4. Assicurati che il profilo (*Profile*) sia impostato su **Core** e che l'opzione **Generate a loader** sia spuntata.
5. Ignora le estensioni (per ora) e clicca su **Generate** per produrre i file della libreria.

!!! warning "Attenzione"
    Assicurati di utilizzare la versione **GLAD1** dal [link](https://glad.dav1d.de/) sopra indicato. Esiste anche una versione GLAD2, ma non funzionerà con il codice di queste guide.

A questo punto GLAD ti metterà a disposizione un file zip contenente due cartelle `include` e un singolo file `glad.c`.

* Copia entrambe le cartelle di inclusione (`glad` e `KHR`) nella tua directory degli `include` personalizzata (o aggiungi un percorso extra che punti a queste cartelle).
* Aggiungi il file `glad.c` all'interno del tuo progetto.

Dopo aver completato questi passaggi, sarai in grado di aggiungere la seguente direttiva di inclusione in cima al tuo file di codice:

```cpp
#include <glad/glad.h> 
```

Premendo il pulsante di compilazione non dovresti riscontrare alcun errore. Se è così, siamo pronti a passare al prossimo capitolo, dove vedremo come utilizzare concretamente GLFW e GLAD per configurare un contesto OpenGL e mostrare una finestra a schermo. Controlla un'ultima volta che tutte le directory di inclusione e delle librerie siano corrette e che i nomi inseriti nel linker corrispondano esattamente ai file.

---

### Risorse aggiuntive

* [GLFW: Window Guide](https://www.glfw.org/docs/latest/window_guide.html): guida ufficiale di GLFW sulla configurazione e gestione di una finestra.
* [Building applications](https://www.glfw.org/docs/latest/build_guide.html): fornisce ottime informazioni sul processo di compilazione/linking della tua applicazione e una vasta lista di possibili errori (con relative soluzioni).
* [GLFW con Code::Blocks](https://www.google.com/search?q=https://www.glfw.org/docs/latest/build_guide.html%23build_link_codeblocks): compilazione di GLFW nell'IDE Code::Blocks.
* [Running CMake](https://cmake.org/runningcmake/): una breve panoramica su come eseguire CMake sia su Windows che su Linux.
* [Polytonic/Glitter](https://github.com/Polytonic/Glitter): un semplice progetto boilerplate pre-configurato con tutte le librerie rilevanti; ottimo se desideri un progetto di esempio senza la seccatura di dover compilare le librerie da solo.
* [A Beginner’s Guide to Setup OpenGL in Linux (Debian)](https://www.google.com/search?q=https://blogs.igalia.com/itoral/2014/07/30/a-beginners-guide-to-setup-opengl-in-linux-ubuntu-14-04/): guida passo-passo per configurare OpenGL su Ubuntu insieme all'installazione delle librerie richieste (GLFW e GLAD).