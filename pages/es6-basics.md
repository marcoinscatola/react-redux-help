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
  // la funzione. E' un iterable ma non è un array e per convertirlo in array una tecnica molto comune è di 
  // applicare il metodo slice del prototype di Array su arguments, opzionalmente ignorando n valori con il 
  // secondo parametro
  var values = Array.prototype.slice.call(arguments, 1);
  return array.concat(values);
}
```

## Destructuring

## Import / Export
