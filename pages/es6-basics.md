Di seguito alcuni esempi di sintassi ES6 che si incontrerà sicuramente lavorando in React.  
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

## Arrow functions
L'arrow function è un tipo particolare di funzione con una dichiarazione più concisa della function tradizionale.
La sintassi dell'arrow function è ```(param1, param2, ...) => returnExpression``` oppure ```(param1, param2, ...) => { statements }``` se è necessario eseguire operazioni non sintetizzabili in un'espressione. Nel caso di un solo parametro si possono omettere le parentesi tonde. 
Esempi: 
```js
  var sum = (a, b) => a + b;
  sum(1,3) // output: 4
  
  var double = x => x * 2;
  double(5) //output: 10
  
  var handleClick = event => {
    var target = event.target;
    target.style.color = "red"
  }
```
L'arrow function ha la particolarità di non accettare binding di  ```this``` e di ```arguments```. Questo significa che dichiarando un arrow function all'interno di un'altro metodo potremo mantenere il valore di ```this``` senza che questo venga sovrascritto da ```bind```, ```call``` o ```apply```.
Questo è un enorme vantaggio che risolve il problema del mantenere il contesto nelle funzioni anonime ad esempio nel caso di gestione di eventi, callback su chiamate ajax, map/filter/reduce, o in generale qualsiasi sistema che internamente usi ```bind```, ```call``` o ```apply```. 
Esempi: 
```js
  // sintassi ES6
  var TranslationApp = {
    dictionary: {
      cane: "dog",
      gatto: "cat",
      mucca: "cow"
    },
    translate: function(parole) {
      return parole.map( parola => this.dictionary[parola] );
    }
  }
  
  // equivalente ES5
  var TranslationApp = {
    dictionary: {
      cane: "dog",
      gatto: "cat",
      mucca: "cow"
    },
    translate: function(parole) {
      var that = this;
      return parole.map(function(parola){
        return that.dictionary[parola]
      });
    }
  }
```


```js
  // sintassi ES6
  var InputHandler = {
    clicked: 0,
    bindEvents: function() {
      document.body.addEventListener("click", () => {
          this.clicked += 1;
      });
    }
  }
  
  // equivalente ES5
  var InputHandler = {
    clicked: 0,
    bindEvents: function() {
      var that = this;
      document.body.addEventListener("click", function() {
          that.clicked += 1;
      });
    }
  }
```
La brevità della sintassi inoltre migliora la leggibilità nel caso ci siano da concatenare diverse funzioni anonime, ad esempio:
```js
  var dati = [
  {name: "John", age: 31}, 
  {name: "Gary", age: 25}, 
  {name: "Jane", age: 23}, 
  {name: "Leon", age: 18}
]
// obiettivo: estrai i contatti con età superiore a 20 anni e crea 
// un array con i loro nomi in ordine alfabetico

// sintassi ES6
  dati
    .filter(person => person.age > 20)
    .map(person => person.name)
    .sort();
  
// sintassi ES5
  dati
  .filter(function(person) {
    return person.age > 20;
  })
  .map(function(person) {
    return person.name;
  })
  .sort();

// alternativa ES5 senza filter/map
  var datiFinali = [];
  for (var i = 0; i < dati.length; i++) {
    var person = dati[i];
    if (person.age > 20)
      datiFinali.push(person.name)
  }
  datiFinali.sort();

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
> **Attenzione!** E' preferibile modificare gli oggetti creando sempre un nuovo oggetto con i nuovi valori desiderati. 
Architetture come Redux impongono questo limite e anche internamente React ragiona in questo modo per la gestione dello stato. 
Modificare un oggetto direttamente porta spesso a dei bug e dei comportamenti inattesi. 
Per via di come sono gestiti gli oggetti in Javascript, si ha questo comportamento
```js
function setReady (appState) {
  appState.ready = true;
  return appState;
}

var applicationState = {prop1: 1, ready: false };
var newApplicationState = setReady(applicationState)
applicationState === newApplicationState // true, sembra che lo stato dell'app sia rimasto uguale
```
In questo modo mi è impossibile paragonare il vecchio stato dell'applicazione con il nuovo, per verificare se è cambiato qualcosa. Un'alternativa corretta può essere questa: 
```js
function setReady (appState) {
  var newState = {
    ...appState,
    ready: true
  }
  return newState;
}

var applicationState = {prop1: 1, ready: false };
var newApplicationState = setReady(applicationState)
applicationState === newApplicationState // false
```

## Destructuring

```js
// oggetto di prova
let obj = {
	text: "babbo",
	number: 42
}

// questo
let {text, number} = obj;
// è uguale a scrivere
let text = obj.text, number = obj.number;

//complichiamoci la vita
let obj = {
	text: "babbo",
	number: 9000,
	nested: {
		stuff: true
	}
}
// ti mostro gli esempi in espressioni separate 
//ma puoi fare anche un'unica espressione
let {text:label} = obj;
// è uguale a 
let label = obj.text;

let {nested:{stuff}} = obj
// è uguale a
let stuff = obj.nested.stuff

let {nonesiste=false} = obj
// è uguale a 
let nonesiste = typeof obj.nonesiste !== "undefined"?obj.nonesiste:false;
```

```js
// mischiamoci spread e rest
let obj = {
	main: 1,
	other: 2,
	stuff: 3
}

let {main, ...bleh} = obj;
// è uguale a
let main = obj.main;
let bleh = {
	other: obj.other,
	stuff: obj.stuff
}

// caso tipico, hai un elemento che rimpiazza un input di testo e 
// ha delle props aggiuntive che gestisci tu (nell'esempio, primary e theme), ma vuoi che cose tipo
// type, placeholder, onChange etc vengano trasferite direttamente
// sull'input (ti metto solo il render)

render() {
	let {primary, theme, ...otherProps} = this.props;
	// gestisci primary e theme come vuoi, es
	let style = primary?{color: red}:{color:black}
	return (
		<input style={style} {...otherProps} />
	)
}

// gestendolo così se passo altre proprietà al mio input custom 
// oltre a quelle due vanno direttamente sull'input

```
```js
//il destructuring puoi usarlo anche nei parametri di una funzione
// and it's really cool

let action = {
	type: "EXAMPLE_ACTION",
	payload: {
		id: 1,
		boh: 2
	}
}

let handleAction = ({type, payload, meta={}) => {
	// l'oggetto che prende come parametro mi arriva
	// già destructurato
	console.log(type, payload, meta)
	let {id, boh} = payload;
	// e da qui in poi lavoro con id e boh
}

handleAction(action)
```
```js
// funziona anche con gli array
let arr = [1,2,3,4]
let [head, ...rest] = arr;
// head = 1, rest = [2,3,4]

// bonus: sort in javascript "a la haskell"
let sort = arr => {
    if (!arr.length) return [];
    let [head, ...tail] = arr;
    let bigger = tail.filter(el => el > head);
    let smaller = tail.filter(el => el <= head);
    return [...sort(smaller), head, ...sort(bigger)];
}
```

## Import / Export
