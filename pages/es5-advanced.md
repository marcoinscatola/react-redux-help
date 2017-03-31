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
var arrayFiltrato = array.filter(funzioneFiltro)
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
    // ritorna una funzione filtro, quindi che prende
    // come parametro l'elemento di un array
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
var arrayTrasformato = array.map(funzioneTrasformazione)
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
## <a name="forEach"></a>forEach

# <a name="funcops"></a>Operazioni sulle funzioni
## <a name="call"></a>call
## <a name="apply"></a>apply
## <a name="bind"></a>bind
