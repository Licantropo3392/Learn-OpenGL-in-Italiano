# Hello Window

Vediamo se riusciamo ad avviare GLFW. Per prima cosa, crea un file `.cpp` e aggiungi le seguenti inclusioni in cima al file.

```cpp
#include <glad/glad.h>
#include <GLFW/glfw3.h>
```

!!! warning "Attenzione"
    Assicurati di includere GLAD prima di GLFW. Il file di intestazione di GLAD include al suo interno gli header OpenGL necessari (come `GL/gl.h`), quindi deve precedere qualsiasi altro file che richieda OpenGL (come GLFW).

Successivamente, creiamo la funzione `main` dove andremo a istanziare la finestra di GLFW:

```cpp
int main()
{
    glfwInit();
    glfwWindowHint(GLFW_CONTEXT_VERSION_MAJOR, 3);
    glfwWindowHint(GLFW_CONTEXT_VERSION_MINOR, 3);
    glfwWindowHint(GLFW_OPENGL_PROFILE, GLFW_OPENGL_CORE_PROFILE);
    //glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);
  
    return 0;
}
```

Nella funzione `main` per prima cosa inizializziamo GLFW con `glfwInit()`, dopodiché possiamo configurarla usando `glfwWindowHint`. Il primo argomento di `glfwWindowHint` indica quale opzione vogliamo configurare, selezionandola da un ampio elenco di costanti (*enum*) prefissate con `GLFW_`. Il secondo argomento è un intero che imposta il valore dell'opzione stessa. Un elenco di tutte le opzioni possibili si trova nella documentazione sulla [gestione delle finestre di GLFW](http://www.glfw.org/docs/latest/window.html#window_hints). Se provi a compilare l'applicazione adesso e riscontri molti errori di tipo "undefined reference", significa che non hai linkato correttamente la libreria GLFW.

Poiché questo libro si concentra sulla versione **OpenGL 3.3**, vogliamo comunicare a GLFW che questa è la versione che intendiamo utilizzare. In questo modo GLFW prenderà gli accordi appropriati durante la creazione del contesto OpenGL, assicurando che l'avvio fallisca qualora l'utente non disponga della versione corretta. Impostiamo sia la versione maggiore (*major*) che quella minore (*minor*) a 3. Indichiamo inoltre a GLFW di voler utilizzare esplicitamente il **core-profile**. Questo ci garantirà l'accesso a un sottoinsieme più ridotto di funzionalità di OpenGL, escludendo quelle per la compatibilità con il passato di cui non abbiamo più bisogno.

??? warning "Attenzione se usi macOS"
    Se sei su Mac OS X, devi aggiungere la riga `glfwWindowHint(GLFW_OPENGL_FORWARD_COMPAT, GL_TRUE);` al codice di inizializzazione affinché funzioni.

!!! note "Nota"
    Assicurati di avere la versione di OpenGL 3.3 o superiore installata sul tuo sistema/hardware, altrimenti l'applicazione andrà in crash o mostrerà comportamenti imprevedibili (*undefined behavior*). Per scoprire la versione supportata dal tuo computer, puoi digitare `glxinfo` su Linux o utilizzare un'utility come [OpenGL Extension Viewer](http://download.cnet.com/OpenGL-Extensions-Viewer/3000-18487_4-34442.html) su Windows. Se la versione supportata è inferiore, verifica se la tua scheda video supporta OpenGL 3.3+ (altrimenti è davvero molto vecchia) e aggiorna i driver.

Ora dobbiamo creare l'oggetto finestra. Questo oggetto racchiude tutti i dati relativi alla finestra ed è richiesto dalla maggior parte delle altre funzioni di GLFW.

```cpp
GLFWwindow* window = glfwCreateWindow(800, 600, "LearnOpenGL", NULL, NULL);
if (window == NULL)
{
    std::cout << "Failed to create GLFW window" << std::endl;
    glfwTerminate();
    return -1;
}
glfwMakeContextCurrent(window);
```

La funzione `glfwCreateWindow` richiede come primi due argomenti rispettivamente la larghezza e l'altezza della finestra. Il terzo argomento permette di assegnarle un nome; per ora la chiameremo "LearnOpenGL", ma puoi scegliere il titolo che preferisci. Possiamo ignorare gli ultimi due parametri. La funzione restituisce un puntatore a un oggetto `GLFWwindow` che useremo in seguito. Subito dopo, diciamo a GLFW di rendere il contesto della nostra finestra il contesto principale sul thread corrente.

---

## GLAD

Nel capitolo precedente abbiamo menzionato che GLAD gestisce i puntatori a funzione per OpenGL, pertanto vogliamo inizializzare GLAD prima di chiamare una qualsiasi funzione OpenGL:

```cpp
if (!gladLoadGLLoader((GLADloadproc)glfwGetProcAddress))
{
    std::cout << "Failed to initialize GLAD" << std::endl;
    return -1;
}    
```

Passiamo a GLAD la funzione per caricare l'indirizzo dei puntatori a funzione di OpenGL, che è specifica per ogni sistema operativo. GLFW ci mette a disposizione `glfwGetProcAddress`, che definisce la funzione corretta in base al sistema operativo per cui stiamo compilando.

---

## Viewport (La Finestra di Rendering)

Prima di poter iniziare a renderizzare dobbiamo fare un'ultima cosa: istruire OpenGL sulla dimensione della finestra di rendering, in modo che sappia come visualizzare i dati e le coordinate rispetto alla finestra. Possiamo impostare queste dimensioni tramite la funzione `glViewport`:

```cpp
glViewport(0, 0, 800, 600);
```

I primi due parametri di `glViewport` impostano la posizione dell'angolo inferiore sinistro della finestra. Il terzo e il quarto parametro impostano la larghezza e l'altezza della finestra di rendering in pixel, che impostiamo uguali alla dimensione della finestra di GLFW.

In realtà potremmo impostare le dimensioni della viewport a valori inferiori rispetto a quelli della finestra di GLFW; in tal caso, il rendering di OpenGL verrebbe visualizzato in un riquadro più piccolo, permettendoci ad esempio di mostrare altri elementi al di fuori della viewport di OpenGL.

!!! note "Nota"
    Dietro le quinte, OpenGL utilizza i dati specificati tramite `glViewport` per trasformare le coordinate 2D che ha elaborato in coordinate sullo schermo. Ad esempio, un punto elaborato alla posizione **(-0.5, 0.5)** verrebbe mappato (come trasformazione finale) in **(200, 450)** nelle coordinate dello schermo. Nota che le coordinate elaborate in OpenGL sono comprese tra **-1** e **1**, quindi mappiamo efficacemente dall'intervallo **-1, 1** a **0, 800** e **0, 600**.

Tuttavia, nel momento in cui un utente ridimensiona la finestra, anche la viewport dovrebbe essere aggiornata. Possiamo registrare una funzione di callback sulla finestra che viene chiamata ogni volta che la dimensione varia. Questa funzione di callback per il ridimensionamento ha il seguente prototipo:

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height);  
```

La funzione prende un `GLFWwindow` come primo argomento e due interi che indicano le nuove dimensioni della finestra. Ogni volta che la finestra cambia dimensione, GLFW chiama questa funzione inserendo gli argomenti corretti da elaborare.

```cpp
void framebuffer_size_callback(GLFWwindow* window, int width, int height)
{
    glViewport(0, 0, width, height);
}  
```

Dobbiamo dire a GLFW che vogliamo chiamare questa funzione a ogni ridimensionamento registrandola in questo modo:

```cpp
glfwSetFramebufferSizeCallback(window, framebuffer_size_callback);
```

Quando la finestra viene visualizzata per la prima volta, anche `framebuffer_size_callback` viene chiamata con le dimensioni della finestra risultante. Per i display Retina, la <span style="color: blue;">larghezza</span> e l'<span style="color: blue;">altezza</span> risulteranno significativamente più elevate rispetto ai valori di input originali.

Esistono molte funzioni di callback che possiamo impostare per registrare le nostre funzioni. Ad esempio, possiamo creare una callback per gestire i cambiamenti di input del joystick, elaborare messaggi di errore, ecc. Registriamo le funzioni di callback dopo aver creato la finestra e prima che inizi il ciclo di rendering.

---

## Scaldate i motori (Il Render Loop)

Non vogliamo che l'applicazione disegni una singola immagine e poi si chiuda immediatamente. Vogliamo che continui a disegnare immagini e a gestire l'input dell'utente fino a quando non le viene esplicitamente detto di fermarsi. Per questo motivo dobbiamo creare un `ciclo while`, che chiameremo <span style="color: green;">render loop</span>, che continua a girare finché non diciamo a GLFW di arrestarsi. Il codice seguente mostra un ciclo di rendering molto semplice:

```cpp
while(!glfwWindowShouldClose(window))
{
    glfwSwapBuffers(window);
    glfwPollEvents();    
}
```

* La funzione `glfwWindowShouldClose` controlla all'inizio di ogni iterazione del ciclo se è stato richiesto di chiudere la finestra. In tal caso, restituisce `true` e il ciclo si interrompe.
* La funzione `glfwPollEvents` controlla se sono stati attivati degli eventi (come l'input della tastiera o i movimenti del mouse), aggiorna lo stato della finestra e chiama le funzioni corrispondenti (che possiamo registrare tramite i metodi di callback).
* La funzione `glfwSwapBuffers` scambia il *color buffer* (un grande buffer 2D che contiene i valori di colore per ogni pixel nella finestra di GLFW) utilizzato per disegnare durante questa iterazione e lo mostra come output sullo schermo.

!!! note "Double buffer"
    Quando un'applicazione disegna su un singolo buffer, l'immagine risultante potrebbe mostrare problemi di sfarfallio (*flickering*). Questo accade perché l'immagine finale non viene disegnata in un istante, ma pixel per pixel, solitamente da sinistra a destra e dall'alto in basso. Poiché l'immagine parziale viene mostrata all'utente mentre è ancora in fase di rendering, il risultato può contenere artefatti visivi. Per aggirare il problema, le applicazioni grafiche utilizzano un **doppio buffer**. Il *front buffer* contiene l'immagine finale visualizzata sullo schermo, mentre tutti i comandi di rendering disegnano sul *back buffer*. Non appena tutti i comandi sono terminati, scambiamo (*swap*) il back buffer con il front buffer, in modo che l'immagine venga mostrata solo quando è completa, eliminando gli artefatti.

---

## Un'ultima cosa

Non appena usciamo dal ciclo di rendering, vogliamo ripulire e deallocare correttamente tutte le risorse di GLFW. Possiamo farlo tramite la funzione `glfwTerminate`, che chiamiamo alla fine della funzione `main`.

```cpp
glfwTerminate();
return 0;
```

Questo pulirà tutte le risorse e chiuderà correttamente l'applicazione. Se provi a compilare l'applicazione adesso e tutto è andato a buon fine, dovresti vedere una finestra come questa:

![Finestra nera](https://learnopengl.com/img/getting-started/hellowindow.png "Finestra nera"){ loading=lazy }

Se vedi un'immagine nera molto monotona e noiosa, è tutto corretto! Se non hai ottenuto l'immagine corretta o se hai dei dubbi su come funziona, controlla il codice sorgente completo [qui](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.1.hello_window/hello_window.cpp) (e se lo schermo ha iniziato a lampeggiare con colori diversi, continua a leggere).

In caso di problemi durante la compilazione dell'applicazione, assicurati prima di tutto che tutte le opzioni del linker siano impostate correttamente e di aver incluso le directory giuste nel tuo IDE (come spiegato nel capitolo precedente). Assicurati inoltre che il codice sia corretto; puoi verificarlo confrontandolo con il codice sorgente completo.

---

## Input

Vogliamo anche avere una qualche forma di controllo dell'input in GLFW, e possiamo ottenerla con diverse funzioni dedicate. Useremo `glfwGetKey`, che prende in input la finestra insieme a un tasto specifico, restituendo lo stato corrente del tasto (se è premuto o meno). Creiamo una funzione `processInput` per mantenere il codice organizzato:

```cpp
void processInput(GLFWwindow *window)
{
    if(glfwGetKey(window, GLFW_KEY_ESCAPE) == GLFW_PRESS)
        glfwSetWindowShouldClose(window, true);
}
```

Qui controlliamo se l'utente ha premuto il tasto Esc (se non è premuto, `glfwGetKey` restituisce `GLFW_RELEASE`). Se il tasto è stato premuto, indichiamo a GLFW di chiudersi impostando la proprietà `WindowShouldClose` su `true` tramite `glfwSetWindowShouldClose`. Al controllo successivo nel **ciclo** principale, la condizione fallirà e l'applicazione si chiuderà.

Chiamiamo quindi `processInput` a ogni iterazione del ciclo di rendering:

```cpp
while (!glfwWindowShouldClose(window))
{
    processInput(window);

    glfwSwapBuffers(window);
    glfwPollEvents();
}  
```

Questo ci offre un modo semplice per controllare la pressione di tasti specifici e reagire di conseguenza a ogni <span style="color: green;">**fotogramma**</span> (<span style="color: green;">*frame*</span>).

---

## Rendering

Vogliamo inserire tutti i comandi di rendering all'interno del render loop, poiché vogliamo eseguirli a ogni iterazione o frame del ciclo:

```cpp
// render loop
while(!glfwWindowShouldClose(window))
{
    // input
    processInput(window);

    // i comandi di rendering vanno qui
    ...

    // controlla gli eventi e scambia i buffer
    glfwPollEvents();
    glfwSwapBuffers(window);
}
```

Giusto per testare se tutto funziona, vogliamo ripulire lo schermo con un colore a nostra scelta. All'inizio di ogni frame è buona norma ripulire lo schermo, altrimenti vedremmo ancora i risultati del frame precedente (potrebbe essere l'effetto che stai cercando, ma non normalmente si evita). Possiamo ripulire il buffer del colore dello schermo usando `glClear`, passando i bit del buffer per specificare quale vogliamo azzerare. I bit disponibili sono `GL_COLOR_BUFFER_BIT`, `GL_DEPTH_BUFFER_BIT` e `GL_STENCIL_BUFFER_BIT`. Al momento ci interessano solo i valori del colore, quindi puliamo solo il color buffer.

```cpp
glClearColor(0.2f, 0.3f, 0.3f, 1.0f);
glClear(GL_COLOR_BUFFER_BIT);

```

Nota che specifichiamo anche il colore con cui pulire lo schermo tramite `glClearColor`. Ogni volta che chiamiamo `glClear` sul color buffer, l'intero schermo verrà riempito con il colore configurato da `glClearColor`. In questo caso ci risulterà un colore verde-azzurro.

!!! note "Richiamo"
    Come ricorderai dal capitolo su OpenGL, la funzione `glClearColor` è una funzione che *imposta lo stato* (state-setting), mentre `glClear` è una funzione che *utilizza lo stato* (state-using), in quanto recupera il colore di pulizia dallo stato corrente.

![Finestra colorata](https://learnopengl.com/img/getting-started/hellowindow2.png "Finestra colorata"){ loading=lazy }

Il codice sorgente completo dell'applicazione è disponibile [qui](https://learnopengl.com/code_viewer_gh.php?code=src/1.getting_started/1.2.hello_window_clear/hello_window_clear.cpp).

Siamo pronti per riempire il render loop con moltissime chiamate di rendering, ma questo sarà l'argomento del prossimo capitolo!