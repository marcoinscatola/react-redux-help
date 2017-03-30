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

## let e const
Queste due nuove parole chiave vanno ad aggiungersi a ```var``` nella dichiarazione delle variabili e delle costanti.

```const``` definisce una constante, il suo uso è praticamente identico a ```var``` con la differenza che l'interprete javascript andrà in errore se provo a cambiare il valore assegnato alla costante, o a fare più di un'assegnazione.
```js
	const TEST = 1;
	TEST = 2; // errore
	const TEST = 2 // errore
```

```let``` funziona in maniera diversa da ```var``` nella gestione della visibilità della variabile. Mentre una variabile definita con ```var``` è visibile in tutta la funzione in cui la variabile è stata definita, ```let``` limita la visibilità al blocco di codice in cui si trova. 
```js
	var x = 20;
	if (true) {
		let x = 10;
		let y = 100;
		console.log(x); // output: 10;
		console.log(y); // output: 100;
	}
	console.log(x); // output: 20;
	console.log(y); // output: undefined
```
Tendenzialmente ```let``` può sostituire var in ogni suo uso e rende più difficile che si creino errori per via di variabili impostate inavvertitamente con lo stesso nome. 
```const``` viene usato per tutti quei valori che non variano all'interno del blocco di codice in cui sono definiti, e ha il vantaggio di produrre errori se si prova a cambiarne il valore.

## Arrow functions
L'arrow function è un tipo particolare di funzione con una dichiarazione più concisa della function tradizionale.
La sintassi dell'arrow function è ```(param1, param2, ...) => returnExpression``` oppure ```(param1, param2, ...) => { statements }``` se è necessario eseguire operazioni non sintetizzabili in un'espressione. Nel caso di un solo parametro si possono omettere le parentesi tonde. 
Esempi: 
```js
  const sum = (a, b) => a + b;
  sum(1,3) // output: 4
  
  const double = x => x * 2;
  double(5) //output: 10
  
  const handleClick = event => {
    let target = event.target;
    target.style.color = "red"
  }
```
L'arrow function ha la particolarità di non accettare binding di  ```this``` e di ```arguments```. Questo significa che dichiarando un arrow function all'interno di un'altro metodo potremo mantenere il valore di ```this``` senza che questo venga sovrascritto da ```bind```, ```call``` o ```apply```.
Questo è un enorme vantaggio che risolve il problema del mantenere il contesto nelle funzioni anonime ad esempio nel caso di gestione di eventi, callback su chiamate ajax, map/filter/reduce, o in generale qualsiasi sistema che internamente usi ```bind```, ```call``` o ```apply```. 
Esempi: 
```js
  // sintassi ES6
  const TranslationApp = {
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
  const InputHandler = {
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
  const people = [
  {name: "John", age: 31}, 
  {name: "Gary", age: 25}, 
  {name: "Jane", age: 23}, 
  {name: "Leon", age: 18}
]
// obiettivo: estrai i contatti con età superiore a 20 anni e crea 
// un array con i loro nomi in ordine alfabetico

// sintassi ES6
  people
    .filter(person => person.age > 20)
    .map(person => person.name)
    .sort();
  
// sintassi ES5
  people
  .filter(function(person) {
    return person.age > 20;
  })
  .map(function(person) {
    return person.name;
  })
  .sort();

// alternativa ES5 senza filter/map
  var res = [];
  for (var i = 0; i < dati.length; i++) {
    var person = people[i];
    if (person.age > 20)
      res.push(person.name)
  }
  res.sort();

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

let arr = [1,2,3]
let arr2 = addValuesToArray(arr, 4, 5, 6, 7)
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
  let modulo1 = { prop1: 10, prop2: 20 };
  let modulo2 = {
    ...modulo1,
    prop3: 30
  }
  // output: modulo2 = { prop1: 10, prop2: 20, prop3: 30 };
  let array1 = [3,4,5];
  let array2 = [1,2, ...array1, 6,7]
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
  let options = { font: "big", theme: "dark", admin: false }
  let newOptions = { font: "small" }
  let updatedOptions = {
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

let applicationState = {prop1: 1, ready: false };
let newApplicationState = setReady(applicationState)
applicationState === newApplicationState // true, sembra che lo stato dell'app sia rimasto uguale
```
In questo modo mi è impossibile paragonare il vecchio stato dell'applicazione con il nuovo, per verificare se è cambiato qualcosa. Un'alternativa corretta può essere questa: 
```js
function setReady (appState) {
  let newState = {
    ...appState,
    ready: true
  }
  return newState;
}

let applicationState = {prop1: 1, ready: false };
let newApplicationState = setReady(applicationState)
applicationState === newApplicationState // false
```

## Destructuring
Con questa sintassi è possibile semplificare l'estrazione dei dati dagli oggetti o dagli array. Viene usato spesso per rendere più leggeri e leggibili i setter iniziali dei metodi che ricevono i parametri in oggetti complessi.
Esempi base:
```js
// oggetto di prova
let obj = {
	text: "testo",
	number: 42
}

// sintassi ES6
let {text, number} = obj;  // risultato: text = "testo", number = 42
// sintassi ES5
var text = obj.text, number = obj.number;

// array di prova
let arr = [1,2,3]

// sintassi ES6
let [uno, due, tre] = arr // risultato: uno = 1, due = 2, tre = 3
// sintassi ES5
var uno = arr[0], due = arr[1], tre = arr[2]

```
Come si vede dagli esempi si tratta di un assegnazione di variabile che nella parte sinistra (prima dell'uguale) replica la struttura della sorgente da cui andrà ad estrarre i dati, e crea delle variabili a partire da questa struttura.
Nel caso del destructuring degli oggetti, le variabili prendono di default il nome e  i valori dei parametri dell'oggetto sorgente.
Nel caso del destructuring degli array quello che conta è l'ordine delle variabili, e prendono il valore dell'array corrispondente alla posizione in cui compaiono.

E' inoltre possibile assegnare dei valori di default e cambiare il nome delle variabili nel caso del destructuring degli oggetti.
Esempi
```js
//complichiamoci la vita
let obj = {
	text: "testo",
	number: 9000,
	nested: {
		data: true
	}
}
// per semplicità gli esempi delle diverse funzionalità sono in espressioni separate, 
// ma si possono usare anche diverse tecniche nella stessa espressione

// sintassi ES6
let {text:label} = obj; // risultato: label = "testo"
// sintassi ES5
let label = obj.text;

// sintassi ES6
let {nested:{data}} = obj
// sintassi ES5
let data = obj.nested.data

// sintassi ES6
let {nonesiste=false} = obj // risultato: nonesiste = false, invece di undefined
// sintassi ES5
let nonesiste = typeof obj.nonesiste !== "undefined"?obj.nonesiste:false;
```
Il destructuring diventa particolarmente potente unità agli operatori spread e rest, permettendo di separare i dati che ci servono in maniera molto più pulita

```js
let obj = {
	id: 1,
	val1: "test",
	val2: true,
	val3: null
}

// sintassi ES6
let {id, ...values} = obj;  // risultato: id = 1 values = { val1: "test", val2: true, val3: null };
// sintassi ES5
let id = obj.id;
let values = {
	val1: obj.val1,
	val2: obj.val2,
	val3: obj.val3,
}
```
Un caso tipico che vorrei analizzare (ma richiede di aver letto prima l'introduzione a react) riguarda la gestione di un componente che sostituisce, ad esempio, un input di testo per aggiungere delle funzionalità aggiuntive corrispondenti a delle proprietà personalizzate (nell'esempio che segue, ```primary``` e ```theme```). Però è necessario che qualsiasi altra proprietà io imposti sul mio componente venga trasferita all'input che verrà renderizzato, in modo da poter continuare ad usare proprietà come ```type```, ```placeholder```, ```onChange``` etc.
Usando il destructuring con il rest operator posso isolare le proprietà che voglio gestire e mettere tutte le altre in un oggetto separato (nell'esempio, ```otherProps```). 
Nella fase di render posso poi usare lo spread operator per 'distribuire' tutti i valori di ```otherProps``` sull'input.
```jsx
render() {
	let {primary, theme, ...otherProps} = this.props;
	// gestisci primary e theme come vuoi, es
	let style = primary?{color: red}:{color:black}
	return (
		<input style={style} {...otherProps} />
	)
}

```
Il destructuring può essere usato anche nei parametri di una funzione, e estrae automaticamente le variabili dai parametri che riceverà.
Esempio:
```js
// questa è la struttura dell'oggetto che mi aspetto venga passato 
// alla funzione handleAction
let action = {
	type: "EXAMPLE_ACTION",
	payload: {
		id: 1,
		value: "test"
	}
}

let handleAction = ({type, payload, meta={} }) => {
	// l'oggetto che prende come parametro mi arriva
	// già scomposto in type, payload, e meta. Non ho bisogno di 
	// dichiararle nuovamente. la variabile meta avrà anche un valore di default
	// impostato su un oggetto vuoto, in modo da non andare in errore se provo
	// ad accedere, per esempio, a meta.qualcosa
	console.log(type, payload, meta); // scriverà in console EXAMPLE_ACTION {id: 1, value: "test"} {}
	let {id, value} = payload;
	// e da qui in poi lavoro con id e value
	console.log(id, value); // scriverà in console 1 "test"
}

handleAction(action)
```


## Import / Export
[Da completare]
