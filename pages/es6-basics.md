Di seguito alcuni esempi di sintassi es6 che si incontrerà sicuramente lavorando in React.  
La maggior parte della sintassi che segue non aggiunge nuove funzionalità, ma permette di ottenere gli stessi risultati utilizzando meno boilerlplate e aumentando la leggibilità.  
La compatibilità è garantità dal transpilatore (che andremo ad usare in ogni caso per JSX) che converte il tutto in codice valido anche su browser datati come IE8.  
A prescindere che si voglia utilizzare o meno la sintassi che segue, è importante conoscerla perché è costantemente presente nei tutorial e nelle librerie dell'ecosistema React.  

## Parametri con valori predefiniti
Permette di specificare un valore di default per un parametro di una funzione, scrivendolo nel formato ```nomeParametro=valoreDefault```
```js
// Sintassi ES6
function creaIndice(index, label="Non definito") {
  return index + " - " + label
}

creaIndice(0, "Test");
// output: "0 - Test"
creaIndice(1);
// output: "1 - Non definito"


// Equivalente ES5
function creaIndice(index, label) {
  label = typeof label === "undefined" ? "Non definito" : label;
  return index + " - " + label;
}
```

## Spread e Rest operator
Sia lo spread che il rest operator sono identificati dalla sintassi ```...nomeVariabile```, è il contesto a definire quale dei due si sta usando.  

Rest operator raggruppa le 'rimanenze' di una serie di valori in un nuovo array o in un nuovo oggetto. 
Un esempio tipico di rest operator è per gestire le funzioni con un numero non predeterminato di parametri.
```js
// Sintassi ES6
function addValuesToArray(array, ...values) {
  // la variabile values sarà un array contenente tutti i parametri dopo il primo
  return array.concat(values);
}

var arr = [1,2,3]
var arr2 = addValuesToArray(arr, 4, 5, 6, 7)
// arr2 = [1,2,3,4,5,6,7]

// Equivalente ES5
function addValuesToArray(array) {
  // arguments è una keyword riservata che contiene i parametri con cui è stata chiamata
  // la funzione. E' un iterable ma non è un array e per convertirlo in array una tecnica 
  // molto comune è di applicare il metodo slice del prototype di Array su arguments, 
  // opzionalmente ignorando n valori specificando un secondo parametro
  var values = Array.prototype.slice.call(arguments, 1);
  return array.concat(values);
}
```

Spread operator è un po' il contrario del rest operator, usato di fronte ad una collection di valori (i.e. array o object) ne 'espande' il contenuto all'interno del contesto in cui è stato usato. Un esempio tipico di uso è nel caso in cui sia necessario copiare i valori di un oggetto all'interno di un altro.
```js
  // sintassi ES6
  var modulo1 = { prop1: 10, prop2: 20 };
  var modulo2 = {
    ...modulo1,
    prop3: 30
  }
  // output: modulo2 = { prop1: 10, prop2: 20, prop3: 30 };
  var array1 = [3,4,5];
  var array2 = [1,2, ...array1, 6,7]
  // output: array2 = [1,2,3,4,5,6,7]
  
  // Equivalente ES5
  var modulo1 = { prop1: 10, prop2: 20 };
  var modulo2 = { prop3: 30 };
  Object.keys(modulo1).forEach(function(prop) { 
    modulo2[prop] = modulo1[prop] 
  })
  // una possibile alternativa è usare una libreria esterna come jquery, lodash, 
  // underscore etc che offrono metodi tipo $.extend
  
  var array1 = [3,4,5];
  var array2 = [1,2].concat(array1).concat([6,7])
```
Da notare che a seconda dell'ordine con cui viene usato lo spread operator in un oggetto è possibile usarlo per aggiornare determinati parametri o per creare una copia di un oggetto con dei parametri modificati
```js
  var options = { font: "big", theme: "dark", admin: false }
  var newOptions = { font: "small" }
  var updatedOptions = {
    ...options,
    ...newOptions
  }
  // output: updatedOptions = {font: "small", theme: "dark", admin: false }
  
  // Equivalente ES5
  var options = { font: "big", theme: "dark", admin: false }
  var newOptions = { font: "small" }
  var updatedOptions = {};
  Object.keys(options).forEach(function(prop) { 
    updatedOptions[prop] = options[prop] 
  })
  Object.keys(newOptions).forEach(function(prop) { 
    updatedOptions[prop] = newOptions[prop] 
  })
  
```
E' preferibile modificare gli oggetti creando sempre un nuovo oggetto con i nuovi valori desiderati. Architetture come Redux impongono questo limite e anche internamente React ragiona in questo modo per la gestione dello stato. 
Modificare un oggetto direttamente porta spesso a dei bug e dei comportamenti inattesi. 
Per via di come sono gestiti gli oggetti in Javascript, si ha questo comportamento
```js
var obj = { test: 1 }
var obj2 = obj;
obj2.test = 2;
obj === obj2 // true
```

## Destructuring

## Import / Export
