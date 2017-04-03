Rivediamo velocemente alcuni metodi javascript molto utili che diventeranno particolarmente importanti nella gestione dei dati utilizzando framework e architetture moderne.
- [Manipolazione array](#array)
  - [filter](#filter)
  - [map](#map)
  - [reduce](#reduce)
  - [forEach](#forEach)  
  - [ES6](#es6)
- [Operazioni sulle funzioni](#funcops)
  - [call](#call)
  - [apply](#apply)
  - [bind](#bind)
  - [Higher-Order Function](#functor)

# <a name="array"></a>Manipolazione array
Di seguito alcuni metodi nativi degli array javascript che forniscono un'alternativa più leggibile e più flessibile al classico ciclo ```for```.
## <a name="filter"></a>filter
Il metodo ```filter``` permette di filtrare un array secondo un criterio di nostra scelta ed ottenere come risultato un nuovo array contenente solo gli elementi dell'array originale che soddisfano quel criterio.
La sintassi è la seguente:
```js
var arrayFiltrato = array.filter(filtro)
```
La funzione filtro è una normale funzione (può anche essere anonima) che viene usata con la seguente logica:
- viene chiamata per ogni elemento di ```array```
- riceve come parametri l'elemento attuale, l'indice dell'elemento e l'array intero (di solito viene usato solo il primo parametro)
- se la funzione eseguita con quei parametri ritorna ```true```, l'elemento verrà aggiunto all'array filtrato, altrimenti verrà scartato  
Esempio
```js
var arr = [7, 2, 9, 8, 4, 4, 3]
var maggiori5 = arr.filter(function(n) {
  return n > 5;
})
console.log(maggiori5) // [7, 9, 8]

var primi3Valori = arr.filter(function(n, index) {
  return index < 3;
})
console.log(primi3Valori) //  [7, 2, 9]
```
In alcuni casi può aumentare la leggibilità usare una funzione con nome invece di una funzione anonima, in particolare se la funzione filtro verrà usata più volte. Inoltre dato che filter ritorna a sua volta un array, è possibile concatenare diversi filter in una sola espressione.
```js
var pari = function(x) { return x % 2 === 0 }
var dispari = function(x) { return !pari(x) }
var maggiore5 = function(x) { return x > 5 }

var arr = [2, 7, 4, 3, 1, 7, 8, 11, 14, 4, 9];
var pariMaggiori5 = arr.filter(pari).filter(maggiore5);
var dispariMaggiori5 = arr.filter(dispari).filter(maggiore5);
console.log(pariMaggiori5); // [8, 14]
console.log(dispariMaggiori5); // [7, 7, 11, 9]
```
Un'altra tecnica utile è quella di avere un generatore di funzione filtro. Mettiamo il caso di voler filtrare una serie di nomi a seconda dell'iniziale scelta dall'utente.  
Potrei costruirmi diversi filtri `inizialeA`, `inizialeB` etc, ma si tratterebbe di un sacco di codice duplicato. Un'alternativa più intelligente consiste nell'avere una funzione che prende come parametro l'iniziale desiderata, e mi ritorna la funzione filtro da usare all'interno di ```filter```
```js
  function iniziale(lettera) {
    // ritorna una funzione filtro, quindi una funzione
    // che prende come parametro l'elemento di un array
    // e che ritorna vero o falso
    return function(el) {
      return el.charAt(0) === lettera;
    }
  }
  var nomi = ["Andrea", "Michele", "Giorgia", "Marco", "Davide", "Daniela", "Alessio"]
  var inizianoPerA = nomi.filter(iniziale("A"));
  var inizianoPerD = nomi.filter(iniziale("D"));
  console.log(inizianoPerA); // ["Andrea", "Alessio"]
  console.log(inizianoPerD); // ["Davide", "Daniela"]
```
## <a name="map"></a>map
Il metodo ```map``` permette di ottenere ottenere un nuovo array generato a partire dagli elementi di un altro. E' molto utile per applicare trasformazioni a tutti gli elementi di un array o per estrare dati specifici da array di oggetti.  
La sintassi è simile a quella di ```filter```
```js
var arrayTrasformato = array.map(trasformazione)
```
La funzione trasformazione si comporta in maniera simile alla funzione filtro:
- viene chiamata su ogni elemento dell'array
- riceve come parametri l'elemento attuale, l'indice dell'elemento e l'array completo
- il valore di ritorno della funzione trasformazione sarà l'elemento del nuovo array trasformato  
Esempi: 
```js
  var arr = [1, 2, 3, 4, 5]
  var arrX2 = arr.map(function(n) {
    return n*2;
  })
  console.log(arrX2) // [2, 4, 6, 8, 10]
  
  var opzioni = ["Casa", "Ufficio", "Altro"]
  var opzioniNumerate = opzioni.map(function(opzione, index) {
    return index + " - " + opzione;
  });
  console.log(opzioniNumerate); // ["0 - Casa", "1 - Ufficio", "2 - Altro"]
  
  var persone = [
    {nome:"Carlo", cognome: "Camusso"}, 
    {nome:"Riccardo", cognome: "Genova"}, 
    {nome:"Marco", cognome:"Lepore"},
    {nome:"Laura", cognome:"Camusso"},
    {nome:"Antonio", cognome:"Tramontana"},
  ]
  var nomi = persone.map(function(persona) {
    return persona.nome;
  })
  console.log(nomi); ["Carlo", "Riccardo", "Marco", "Laura", "Antonio"]
```
Anche nel caso di ```map``` è possibile, e spesso consigliabile, usare funzioni con nomi invece di funzioni anonime, in modo da aumentare la leggibilità. Inoltre essendo il valore di ritorno un array, è possibile concatenare diverse operazioni in una sola espressione.  
Esempi: 
```js
  var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
  var quadrato = function(x) { return x*x };
  var minore50 = function(x) { return x < 50 };
  
  var quadratiMin50 = arr.map(quadrato).filter(minore50);
  console.log(quadratiMin50) // [1, 4, 9, 16, 25, 36, 49]
  
   var persone = [
    {nome:"Gigi", cognome: "Cremeria", nato: 1981}, 
    {nome:"Mastro", cognome: "Lindo", nato: 1958}, 
    {nome:"Mister", cognome:"Muscolo", nato: 1986},
    {nome:"Calimero", cognome:"Nero", nato: 1972},
    {nome:"Susanna", cognome:"Tuttapanna", nato: 1965}
  ]
  function natoPrimaDel(anno) {
    return function(persona) {
      return persona.nato < anno;
    }
  }
  function formattaNome(persona) { return persona.nome + " " + persona.cognome };
  
  var natiPrimaDel1980 = persone.filter(natoPrimaDel(1970)).map(formattaNome);
  console.log(natiPrimaDel1980) // ["Mastro Lindo", "Susanna Tuttapanna"]
```

## <a name="reduce"></a>reduce
Il metodo ```reduce``` serve ad ottenere un singolo valore calcolato progressivamente prendendo in esame tutti gli elementi di un array. Un tipico caso d'uso è ottenere la somma dei valori di un array.  
La sintassi di ```reduce``` è la seguente.
```js
  var valore = array.reduce(trasformazione, valoreIniziale);
```
La funzione trasformazione usa la seguente logica: 
- viene chiamata per ogni elemento dell'array
- riceve come parametri il valore di ritorno della funzione precedente, l'elemento attuale, l'indice, e l'array completo

Il secondo parametro di reduce, valoreIniziale, è opzionale.  
Se specificato, sarà usato alla prima iterazione della funzione trasformazione.  
Se non specificato, il primo elemento dell'array sarà usato come valore iniziale e verrà saltata la prima iterazione.

Esempi:
```js
  var arr = [1,2,3,4,5]
  function somma (totaleParziale, n) {
    return totaleParziale + n;
  }
  var sommaArray = arr.reduce(somma);
  
  var arr = [20, 52, 11, 32, 0, 64, 7];
  function bigger (valorePiuGrande, n) {
    return valorePiuGrande > n ? valorePiuGrande : n;
  }
  var max = arr.reduce(bigger);
  console.log(max) // 64
```

In alcuni casi può essere molto utile usare ```reduce``` per analizzare un array e ricavarne un altro array o un oggetto con caratteristiche diverse.

Esempi: 
```js
  // es: contare le occorrenze di un elemento all'interno di 1 array
  var parole = ["cane", "gatto", "pesce", "cane", "pesce", "lupo", "corvo", "cane"]
  function conta(contatore, parola) {
    // se la parola non è nell'oggetto, inizializzo il valore a 1 per quella parola
    if (!contatore[parola])
      contatore[parola] = 1;
    // altrimenti incremento il contatore relativo a quella parola di 1  
    else
      contatore[parola]++;
    return contatore;
  }
  
  var conteggioParole = parole.reduce(conta, {}); // il valore iniziale è un oggetto vuoto
  console.log(conteggioParole) // {cane: 3, gatto: 1, pesce: 2, lupo: 1, corvo: 1}
```
```js  
  // es: indicizzare un array di oggetti in un dizionario secondo un determinato parametro
  var dati = [
    {id: "a1", value: 1, active: true},
    {id: "a2", value: 2, active: true},
    {id: "a3", value: 5, active: false},
    {id: "b1", value: 7, active: true}
  ]
  function indicizza(dizionario, dato) {
    dizionario[dato.id] = dato;
    return dizionario;
  }
  var datiIndicizzati = dati.reduce(indicizza, {});
  console.log(datiIndicizzati)
  /* 
  {
    a1: {id: "a1", value: 1, active: true},
    a2: {id: "a2", value: 2, active: true},
    a3: {id: "a3", value: 5, active: false},
    b1: {id: "b1", value: 7, active: true}
  }
  */
```
```js
  // es: 'appiattire' un array di array
  var arr2d = [ [1, 2], [3, 4, 5], [6] ];
  function flat (arr, el) {
    return arr.concat(el)
  }
  var flatArray = arr2d.reduce(flat, []);
  console.log(flatArray) // [1, 2, 3, 4, 5, 6]
```
## <a name="forEach"></a>forEach
Il metodo ```forEach``` può sostituire il classico ciclo for nel caso sia necessario eseguire una serie di operazioni prendendo in esame tutti gli elementi di un array. La differenza principale di forEach rispetto a ```map```, ```filter``` e ```reduce``` è di non avere un valore di ritorno.  
Se ho bisogno di ottenere un qualche tipo di valore di ritorno trasformando o lavorando in qualche modo un array, è più sintatticamente corretto usare uno degli altri operatori.  
Alcuni casi d'uso possono essere funzioni di logging o funzioni che chiamano il server per ogni elemento di un array (solo se non ci interessa il valore di ritorno, altrimenti un map è più indicato)
La sintassi è la seguente:
```
array.forEach(funzione);
```
Dove funzione viene chiamata per ogni elemento dell'array, ricevendo come parametri l'elemento corrente, l'indice dell'elemento e l'array completo.
Esempio:
```js
  var events = [ 
    {type:"click", timestamp: 1490965455518}, 
    {type:"click", timestamp: 1490965465722}, 
    {type:"click", timestamp: 1490965265932}
  ]
  function logEventTime(event) {
    console.log(event.timestamp)
  }
  events.forEach(logEventTime);
  /* output:
    1490965455518
    1490965465722
    1490965265932
  */
```
## <a name="es6"></a>ES6
Con l'introduzione del nuovo standard **ECMAScript 6** e in particolare delle [arrow function](es6-basics.md#arrows) si può ottenere una leggibilità ancora migliore nei metodi ```map```, ```reduce``` e ```filter```.
Esempi:
```js
  // estrarre un dato da un array di oggetti
  var persone = [
    {nome:"Carlo", cognome: "Camusso"}, 
    {nome:"Riccardo", cognome: "Genova"}, 
    {nome:"Marco", cognome:"Lepore"},
    {nome:"Laura", cognome:"Camusso"},
    {nome:"Antonio", cognome:"Tramontana"},
  ]
  var nomi = persone.map(persona => persona.nome);
```

```js
  // calcolare la somma di tutti gli anni pari fino ad oggi
  
  // questa funzione ritorna un array con tutti gli anni da annoCorrente fino a 0
  function generaAnni(annoCorrente) {
    if (annoCorrente === 0) return [];
    return [annoCorrente].concat(generaAnni(annoCorrente-1))
  } 
  
  var sommaAnniPari= generaAnni(2017).filter(n => n % 2 === 0).reduce( (tot, anno) => tot + anno)
```


```js
  // eliminare gli spazi in una frase e cifrarla cambiando ogni lettera con la successiva
  
  var frase = "Oggi Carlo ha chiesto a Riccardo di centrare una scritta verticalmente";
  var fraseCifrata = frase.split("") // crea un array di caratteri a partire dalla stringa
    .filter(c => c !== " ") // ignora gli spazi
    .map(c => String.fromCharCode(c.charCodeAt(0)+1) ) // sostituisce ogni carattere con la lettera successiva
    .join("") // unisce nuovamente l'array in una stringa
    
  console.log(fraseCifrata) // "PhhjDbsmpibdijftupbSjddbsepejdfousbsfvobtdsjuubwfsujdbmnfouf"
```


# <a name="funcops"></a>Operazioni sulle funzioni
Di seguito alcuni metodi fondamentali per poter utilizzare all 100% le funzioni in javascript, senza soprese inaspettate.

## <a name="call"></a>call
Il metodo ```call``` permette di chiamare una funzione, specificando di seguito il valore di ```this``` e dei parametri con cui chiamare la funzione.  
Sintassi: 
```js
funzione.call(thisValue, param1, param2, ...)
```
Esempi:
```js
  function sommaNumeri(a,b) {
    return a + b;
  }
  sommaNumeri.call(null, 5, 10) // 15
```
```js
var obj = {
  name: "Mario",
  getName: function() {
    return this.name
  }
}

var obj2 = {
  name: "Nicola"
}

obj.getName(); // "Mario"
obj.getName.call(obj2); // "Nicola"
```
Un possibile caso d'uso di ```call``` è quando abbiamo bisogno di applicare un metodo di un prototipo ad un oggetto che non eredita quel prototipo. Un esempio comune è quando si ha necessità di usare il metodo ```slice``` di ```Array.prototype``` sugli ```arguments``` all'interno di una funzione. ```arguments``` è un oggetto iterabile e si comporta in gran parte come un array, ma non ha il metodo ```slice```. E' possibile usare ```call``` in questo modo:
```js
// stampa in console un array contenente i parametri passati alla funzione, eccetto il primo
function logArguments() {
  var argsExceptFirst = Array.prototype.slice.call(arguments, 1)
  console.log(argsExceptFirst);
}
logArguments(1,2,3,4,5) // output: [2, 3, 4, 5]
```
## <a name="apply"></a>apply
Il metodo ```apply```  funziona in modo molto simile al metodo ```call```, permette anch'esso di chiamare un metodo specificando un valore di ```this``` e i parametri. La differenza con ```call``` è che invece di accettare i parametri separati da virgole, richiede un array contenente i parametri:
```js
  funzione.apply(thisValue, [param1, param2, ...])
```
Esempi:
```js
  var params  = [2, 5];
  function mult(a,b) {
    return a*b;
  }
  mult.apply(null, params) // ouput: 10
```

```js
  // esempio ridottissimo di un sistema ad eventi
  var eventSystem = {
    handlers: [],
    registerHandler: function(handler) {
      this.handlers.push(handler);
    },
    trigger: function() {
      var args = arguments;
      this.handlers.forEach(function(handler) {
        handler.apply(null, args);
      })
    }
  }
  eventSystem.registerHandler(function(a,b) {
    console.log(a + b);
  });
  eventSystem.registerHandler(function(c,d) {
    console.log(c * d);
  });
  eventSystem.trigger(5,10);
  // output: 
  // 15
  // 50
```
Un altro caso in cui ```apply``` torna utile è quando è necessario sostituire un metodo preesistente (o un metodo nativo javascript) aggiungendo qualche tipo di funzionalità all'implementazione originale
```js
// aggiunge un console.log all'esecuzione di ogni window.open

// salvo un riferimento al metodo in una variabile
var originalOpen = window.open; 
// sovrascrivo window.open con la mia implementazione
window.open = function(){
  // il primo parametro è l'url, lo stampo in console
  var url = arguments[0];
  console.log("Opening url: ", url);
  // eseguo il metodo window.open originale, passandogli i parametri 
  // con cui è stato chiamato il nuovo metodo window.open
  originalOpen.apply(this, arguments);
}
```
## <a name="bind"></a>bind
Il metodo ```bind``` serve a creare una nuova funzione specificando il contesto e i parametri con cui verrà chiamata. A differenza di ```call``` e ```apply``` non esegue la funzione ma si limita a ritornarla.  
Sintassi:
```js
  var funzioneBound = funzione.bind(thisValue, param1, param2, ...)
```
Esempio: 
```js
function somma(a,b) {
  return a + b;
}
var somma3 = somma.bind(null, 3) // in somma3 this = null, a = 3
// il valore 3 viene legato al parametro a, somma3 accetta 
// quindi solo il parametro b
somma(10,20) // 30
somma3(10) // 13
```
```bind``` risulta estremamente utile nel caso di funzioni da associare ad eventi o a callback di funzioni asincrone.  
Nel caso degli eventi, internamente la funzione passata a ```addEventListener``` o a ```$().on``` viene eseguita nel contesto del target dell'evento. Ad esempio il valore di ```this``` all'interno di un evento di click è l'elemento del DOM che è stato clickato. A volte questo comportamento non è desiderabile perché si vuole mantenere il contesto originale.
```js
var clickCounter = {
  clicked: 0,
  addClick: function(e) {
    this.clicked++;
  }
}

// esempio NON CORRETTO:
document.body.addEventListener("click", clickCounter.addClick);
// click 4 volte 
console.log(clickCounter.clicked) // 0
// il valore di this all'interno di addClick viene 
// sostituito nel momento in cui usiamo .addEventListener

// esempio CORRETTO:
document.body.addEventListener("click", clickCounter.addClick.bind(clickCounter));
// oppure
var boundAddClick = clickCounter.addClick.bind(clickCounter);
document.body.addEventListener("click", boundAddClick);
// click 4 volte
console.log(clickCounter.clicked) // 4
```
Lo stesso problema si ha con la maggior parte delle implementazioni dei metodi asincroni. Ad esempio i callback associati ad  ```$.ajax()``` vengono eseguiti automaticamente nel contesto (```this```) della risposta ricevuta dal server. Se vogliamo mantenere il contesto originale del metodo possiamo usare ```bind```
```js
  var loader = {
    loading: false,
    setLoading: function(bool) {
      this.loading = bool;
    },
    loadComplete: function() {
      this.setLoading(false);
    },
    // la funzione bindMethods sostituisce setLoading e 
    // loadComplete con funzioni dove this è sempre uguale a loader 
    bindMethods: function() {
      this.setLoading = this.setLoading.bind(this);
      this.loadComplete = this.loadComplete.bind(this);
  }
  
  function getData() {
    return $.ajax({
      url: "http://www.example.com/api",
      type: "GET"
    })
  }
  
  loader.setLoading(true);
  getData().then(loader.loadComplete) // Error: this.setLoading is not a function
  // va in errore perché il valore di this è stato cambiato da .then()
  
  loader.bindMethods(); 
  getData().then(loader.loadComplete) 
  // funziona correttamente e imposta il valore di loader.loading su false
```
Un altro esempio potrebbe essere la creazione di comandi di log personalizzati. I vari ```console.log```, ```console.warn``` etc normalmente sono eseguiti nel contesto ```console```. Se provo ad assegnare un metodo di console ad una variabile e a chiamarlo, ottengo un errore:
```js
  var log = console.log;
  log("test") // error
  ```
  (N.B. questo non succede più nelle ultime versioni di chrome, dove i metodi di consoe sono automaticamente legati alla console)  
  La creazione di un metodo di log personalizzato potrebbe essere realizzata così:
  ```js
  var logTime = console.log.bind(console, "Time: ");
  logTime(10) // ouput in console: Time: 10
  ```
## <a name="functor"></a>Higher-Order Function
Si definisce Higher-Order Function (funzione di ordine superiore) una funzione che prende tra i suoi parametri almeno una funzione e ha come valore di ritorno una nuova funzione. Le funzioni di ordine superiore vengono usate per aggiungere funzionalità a funzioni esistenti.  
Esempi: 
```js
var makeLoggable = function(fn, prefix) {
  prefix = prefix || "";
  // ritorna una funzione
  return function() {
    // la funzione ritornata quando chiamata esegue
    // la funzione originale e ne assegna il risultato
    // alla variabile res
    var res = fn.apply(this, arguments);
    // viene eseguito un console.log con il risultato
    // e l'eventuale prefisso specificato in prefix
    console.log(prefix, res);
    // viene ritornato il valore originale
    return res;
  }
}
var makeDelayed = function(fn, delay) {
  // ritorna una funzione
  return function() {
    // salva i parametri con cui è chiamata la funzione
    var args = arguments;
    // ritorna una Promise che autoesegue la funzione interna
    return new Promise(function(resolve) {
      // esegue la funzione originale con un delay
      setTimeout(function() {
        // all'esecuzione salva il valore della funzione originale
        var res = fn.apply(this, args);
        // risolve la Promise con quel valore
        resolve(res);
      }, delay);
    })
  }
}
function somma(a, b) {
  return a + b;  
}

var loggableSomma = makeLoggable(somma, "Somma:");

var delayedSomma = makeDelayed(somma, 1000);

somma(5, 10) // ritorna 15
loggableSomma(5, 10) // ritorna 15 e scrive in console "Somma: 15"
delayedSomma(5, 10) // esegue somma dopo 1 secondo e ritorna una 
                    // promise che sarà risolta con il valore 15

// è possibile unire i due comportamenti in un unica funzione
var loggableDelayedSomma = makeDelayed(makeLoggable(somma, "Somma: "), 1000)
loggableDelayedSomma(5, 10)
// esegue la funzione dopo 1 secondo e in console scrive "Somma: 15"
```
Come si vede nell'ultimo esempio, combinare più funzioni di ordine superiore è possibile ma la sintassi può diventare complicata. Un modo per aggirare questo problema è di creare funzioni che accettano parametri di configurazione e ritornano funzioni di ordine superiore. Spiegarlo a parole è più complicato che vederlo in azione: 
```js
  function somma(a, b) {
    return a + b;
  }

  // il generatore di funzione di ordine superiore prende come parametri 
  // la configurazione, in questo caso il parametro prefix
  var makeLoggable = function(prefix) {
    // ritorna una funzione di ordine superiore che prende come 
    // parametro la funzione da modificare
    return function(fn) {
      // da qui in poi funziona come nell'esempio precedente
      return function() {
        var res = fn.apply(this, arguments);
        console.log(prefix, res);
        return res;
      }
    }
  }
  
  // si usa in questo modo
  var loggableSomma = makeLoggable("Somma: ")(somma);
  
  // la prossima funzione di ordine superiore non prende configurazione, 
  // ma possiamo comunque decidere di usare un generatore per 
  // mantenere la stessa struttura
  var makeMeasurable = function() {
    return function(fn) {
      return function() {
        var t = Date.now();
        var res = fn.apply(fn, arguments);
        var elapsed = Date.now() - t;
        console.log("Tempo di esecuzione: " + elapsed + "ms");
        return res;
      }
    }
  }
  
  var measurableSomma = makeMeasurable()(somma);
  measurableSomma(10,20) 
  // scrive in console Tempo di esecuzione Xms (molto probabilmente 0ms)
  // e ritorna 30
```
Il vantaggio di questa struttura si vede nel momento in cui vogliamo combinare più funzioni in una sola, approfittando di metodi come `compose` disponibili in varie librerie (lodash, underscore, etc). ```compose``` prende tante funzioni come parametri e restituisce una funzione che esegue consecutivamente le funzioni ricevute (partendo dall'ultima) applicando ogni volta la funzione successiva sul valore di ritorno della funzione precedente.  
Esempio di compose:
```js
function uppercase(str) { return str.toUpperCase() }
function reverse(str) { return str.split("").reverse().join("") }
var f = compose(reverse, uppercase);
f("Test stringa da modificare"); // "ERACIFIDOM AD AGNIRTS TSET"
```
Ora andiamo ad usare ```compose``` con i generatori di funzione di ordine superiore creati nell'esempio precedente:
```js
  var enhance = compose(
    makeLoggable("Somma: "),
    makeMeasurable()
  )
  var enhancedSomma = enhance(somma);
  enhancedSomma(10,20);
  /*
    scrive in console:
    
    Tempo di esecuzione: 0ms
    Somma:  30
    
    e ritorna il valore 30
  */
```
