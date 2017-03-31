Rivediamo velocemente alcuni metodi javascript molto utili che diventeranno particolarmente importanti nella gestione dei dati utilizzando framework e architetture moderne.
- [Manipolazione array](#array)
  - [filter](#filter)
  - [forEach](#forEach)
  - [map](#map)
  - [reduce](#reduce)
- [Operazioni sulle funzioni](#funcops)
  - [call](#call)
  - [apply](#apply)
  - [bind](#bind)


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
  
  // es: 'appiattire' un array di array
  var arr2d = [ [1, 2], [3, 4, 5], [6] ];
  function flat (arr, el) {
    return arr.concat(el)
  }
  var flatArray = arr2d.reduce(flat, []);
  console.log(flatArray) // [1, 2, 3, 4, 5, 6]
```
## <a name="forEach"></a>forEach

# <a name="funcops"></a>Operazioni sulle funzioni
## <a name="call"></a>call
## <a name="apply"></a>apply
## <a name="bind"></a>bind
