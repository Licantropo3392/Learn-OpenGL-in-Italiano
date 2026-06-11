# Introduzione

Se sei arrivato fin qui, probabilmente vuoi imparare il funzionamento interno della computer grafica e fare da solo tutte quelle cose fighe che fanno i programmatori esperti. Fare le cose da soli è estremamente divertente e formativo, e ti permette di comprendere a fondo la programmazione grafica. Tuttavia, ci sono alcuni elementi da tenere in considerazione prima di iniziare il tuo viaggio.

## Prerequisiti

Poiché OpenGL è un'API grafica e non una piattaforma a sé stante, richiede un linguaggio in cui operare, e il linguaggio scelto è il C++. Di conseguenza, per questi capitoli è richiesta una discreta conoscenza del linguaggio di programmazione C++. Tuttavia, cercherò di spiegare la maggior parte dei concetti utilizzati, inclusi gli argomenti avanzati del C++ dove necessario; quindi non è richiesto essere un esperto di C++, ma dovresti essere in grado di scrivere qualcosa in più di un semplice programma "Hello World". Se non hai molta esperienza con il C++, ti consiglio i tutorial gratuiti su [www.learncpp.com](https://www.learncpp.com).

Inoltre, lungo il percorso utilizzeremo un po' di matematica (algebra lineare, geometria e trigonometria) e cercherò di spiegare tutti i concetti matematici necessari. Tuttavia, non sono un matematico nell'anima, quindi anche se le mie spiegazioni potrebbero essere facili da capire, molto probabilmente saranno incomplete. Pertanto, dove necessario, fornirò collegamenti a valide risorse che spiegano il materiale in modo più completo. Non lasciarti spaventare dalle conoscenze matematiche richieste prima di iniziare il tuo viaggio in OpenGL; quasi tutti i concetti possono essere compresi con un background matematico di base e cercherò di ridurre la matematica al minimo, dove possibile. La maggior parte delle funzionalità non richiede nemmeno di comprendere tutta la matematica, purché tu sappia come usarla.

## Struttura

LearnOpenGL è suddiviso in una serie di sezioni generali. Ogni sezione contiene diversi capitoli che spiegano dettagliatamente vari concetti. Ciascuno dei capitoli è visibile nel menu alla tua sinistra. I concetti vengono insegnati in modo lineare (quindi si consiglia di procedere dall'alto verso il basso, a meno che non sia indicato diversamente), in cui ogni capitolo spiega la teoria di base e gli aspetti pratici.

Per rendere i concetti più facili da seguire e dare loro una struttura aggiuntiva, il libro contiene riquadri, blocchi di codice, suggerimenti cromatici e riferimenti alle funzioni.

### Riquadri

!!! note "Nota"
    Racchiudono note o funzionalità/suggerimenti utili su OpenGL o sull'argomento in questione.

!!! warning "Attenzione"
    Conterranno avvertimenti o altre funzionalità a cui devi prestare particolare attenzione.

### Codice

Nel sito troverai molti piccoli frammenti di codice posizionati all'interno di riquadri grigio scuro con l'evidenziazione della sintassi, come puoi vedere qui sotto:

``` cpp
// Questo riquadro contiene codice
```

Poiché questi forniscono solo frammenti di codice, ovunque sia necessario fornirò un link all'intero codice sorgente richiesto per un determinato argomento.

### Suggerimenti cromatici

Alcune parole sono visualizzate con un colore diverso per rendere ancora più chiaro il loro significato speciale:

- <span style="color: green;">**Definizione**</span>: le parole in **verde** specificano una definizione, ovvero un aspetto o un nome importante di qualcosa che probabilmente sentirai nominare spesso.
- <span style="color: red;">**Struttura del programma**</span>: le parole in **rosso** specificano nomi di funzioni o nomi di classi.
- <span style="color: blue;">**Variabili**</span>: le parole in **blu** specificano le variabili, incluse tutte le costanti di OpenGL.

### Riferimenti alle funzioni OpenGL

Una funzionalità particolarmente apprezzata di LearnOpenGL è la possibilità di esaminare la maggior parte delle funzioni di OpenGL ovunque appaiano nei contenuti. Ogni volta che nel testo si trova una funzione documentata sul sito, questa apparirà con una sottolineatura leggermente visibile. Puoi passare il mouse sopra la funzione e, dopo un breve intervallo, una finestra pop-up mostrerà le informazioni rilevanti su di essa, inclusa una panoramica chiara di ciò che fa effettivamente la funzione. Passa il mouse sopra `glEnable` (**TODO**) per vederla in azione.

Ora che hai preso un po' di confidenza con la struttura del sito, salta alla sezione *Primi passi* per iniziare il tuo viaggio in OpenGL!