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
Il metodo filter permette di filtrare un array secondo un criterio di nostra scelta ed ottenere come risultato un nuovo array contenente solo gli elementi dell'array originale che soddisfano quel criterio.
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
## <a name="forEach"></a>forEach
## <a name="map"></a>map
## <a name="reduce"></a>reduce

# <a name="funcops"></a>Operazioni sulle funzioni
## <a name="call"></a>call
## <a name="apply"></a>apply
## <a name="bind"></a>bind
