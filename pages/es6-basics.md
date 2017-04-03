Di seguito alcuni esempi di sintassi ES6 che si incontrerà sicuramente lavorando in React.  
La maggior parte della sintassi che segue non aggiunge nuove funzionalità, ma permette di ottenere gli stessi risultati utilizzando meno boilerlplate e aumentando la leggibilità.  
La compatibilità è garantità dal transpilatore (che andremo ad usare in ogni caso per JSX) che converte il tutto in codice valido anche su browser datati come IE8.  
A prescindere che si voglia utilizzare o meno la sintassi che segue, è importante conoscerla perché è costantemente presente nei tutorial e nelle librerie dell'ecosistema React.  

- [Parametri con valori predefiniti](#default)
- [let e const](#letconst)
- [Arrow Functions](#arrows)
- [Spread e rest operator](#spreadrest)
- [Destructuring](#destruct)
- [Nomi di proprietà computate](#computed)
- [Template](#template)
- [Import/Export](#importexport)
## <a name="default"></a>Parametri con valori predefiniti
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

## <a name="letconst"></a>let e const
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

## <a name="arrows"></a>Arrow functions
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

## <a name="spreadrest"></a>Spread e Rest operator
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
Da notare che a seconda dell'ordine con cui viene usato lo spread operator in un oggetto è possibile sovrascrivere / aggiornare determinati parametri, ottenendo come risultato un nuovo oggetto.
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

## <a name="destruct"></a>Destructuring
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

## <a name="computed"></a>Nomi di proprietà computate
In ES6 è possibile definire il nome di una proprietà di un oggetto in maniera dinamica anche al momento della dichiarazione dell'oggetto stesso. La sintassi è simile all'accesso dinamico alle proprietà di un oggetto (```obj[prop]```).  
Esempio: 
```js
// sintassi ES6
let base = "propNum";
let obj = {
  [base + 1]: 10,
  [base + 2]: 20,
  [base + 3]: 30
}
console.log(obj) // {propNum1: 10, propNum2: 20, propNum3: 30}

// sintassi ES5
// in ES5 non è possibile dichiarare una proprietà dinamica contestualmente
// all'inizializzazione di un oggetto. E' necessario creare prima l'oggetto
let base = "propNum";
var obj = {};
obj[base + 1] = 10;
obj[base + 2] = 20;
obj[base + 3] = 30;
```

## <a name="template"></a>Template
I template sono una modalità avanzata di gestione delle stringhe. Permettono di scrivere stringhe multilinea e di inserire variabili nel testo in maniera molto più semplice rispetto alla gestione tradizionale.  
I template sono delimitati dal carattere *backtick* (\`), che su OSX è ottenibile con la combinazione alt+\.  
Esempio: 
```js
// sintassi ES6
let str = `Questa è una 
stringa 
multilinea`;

// sintassi ES5
var str = "Questa è una\n stringa\n multilinea";
// oppure volendola scrivere su più righe per leggibilità
var str = "Questa è una\n"+
"stringa\n"+
"multilinea";
```
Per inserire variabili o altro codice javascript all'interno del testo si usa la sintassi ```${}```, ad esempio:  
```js
// sintassi ES6
let username = "Darth Vader"
let benvenuto = "Benvenuto, ${username}!"
console.log(benvenuto) // Benvenuto, Darth Vader!

// sintassi ES5
var username = "Anakin Skywalker"
var benvenuto = "Benvenuto, " + username + "!";
```
Un altro esempio un po' più complesso.
```js
// sintassi ES6
let active = false;
let info = `Data: ${new Date().toLocaleString()}
Il tuo account ${active?'è attivo':'non è attivo'}. 
Ti auguriamo una buona giornata!`

console.log(info) 
/*
Data: 3/4/2017, 11:07:45
Il tuo account non è attivo. 
Ti auguriamo una buona giornata!
*/

// sintassi ES5
var active = false;
var info = "Data: " + new Date().toLocaleString() + "\n" +
"Il tuo account " + (active?'è attivo':'non è attivo') + ". \n" +
"Ti auguriamo una buona giornata!"
```

E' utile sapere che apporre un template dopo il nome di una funzione chiama automaticamente la funzione passandogli come parametri i vari elementi del template (un array delle parte statiche e un array delle parti dinamiche).  
Anche se raramente andremo a usare questa tecnica, alcune librerie ne fanno uso per permettere allo sviluppatore di scrivere liberamente un qualche tipo di sintassi e convertirla internamente in altro codice.  
Un esempio è la libreria styled-components per React:
```js
// libreria styled-components
import styled from 'styled-components';

// Immaginiamo di avere dei file .js nella cartella style
// con variabili contenenti informazioni sulla tipografia,
// colori dell'app, dimensioni standard del padding etc
import font from 'style/font'; 
import layout from 'style/layout';

var styledDiv = styled.div`
  position: absolute;
  left: 0;
  top: 0;
  padding: ${layout.paddingBase}px;
  color: ${font.mainColor};
  font-size: ${font.big}
`

// in styledDiv avrò un componente react corrispondente ad
// un elemento DIV con applicato lo stile specificato
```


## <a name="importexport"></a>Import / Export
Import e Export permettono rispettivamente di importare ed esportare variabili e metodi da moduli javascript.
Questo permette di separare il codice in diversi file, importare librerie esterne solo dove servono e di condividere dati senza usare variabili con visibilità globale.

Esistono due tipi di Export: export con nome e export di default. Ogni file può avere **un solo** export di default, ed un qualsiasi numero di export con nome. Di solito il valore o il metodo più importante del file si esporta come default, perché ne rende più facile l'import.  
Esempio default export
```js
  // lib/modulo.js
 function metodo(x) {
    return x + 1;
 };
  export default metodo;  
  
  // posso anche esportare direttamente la funzione
  export default function(x) {
    return x + 1;
  }
```
```js  
  // index.js
  import metodo from 'lib/modulo';
  metodo(3) // output 4
  
  // essendo un export di default, posso specificare qualsiasi nome
  // al momento dell'import
  
  import sommaUno from 'lib/modulo';
  sommaUno(3) // output 4  
```

Senza la keyword ```default``` gli export vengono considerati named export, e al momento dell'importo sarà necessario specificare il nome del valore o del metodo da importare. La sintassi prevede di fare l'export direttamente nella dichiarazione della variabile o del metodo, oppure di usare le parentesi graffe se fatto successivamente (è possibile anche combinare più export in questo modo).  
Esempio export con nome
```js
  // lib/modulo.js
 export function metodo(x) {
    return x + 1;
 };
 
 // oppure
 function metodo(x) {
  return x + 1;
 }
 export {metodo};
 
 // nel caso di più valori esportati
 
 function metodo1(x) { /*...*/ }
 function metodo2(x) { /*...*/ }
 
 export {metodo1, metodo2}
```
```js  
  // index.js
  import {metodo} from 'lib/modulo';
  metodo(3) // output 4
  
<a  // se voglio cambiare il nome con cui viene importato il metodo
  // posso usare la keyword as
  
  import {metodo as sommaUno} from 'lib/modulo';
  sommaUno(3) // output 4  
```
E' possibile (e nella pratica succede spesso) avere sia export di default che export con nome all'interno dello stesso file. E' possibile combinare le sintassi viste finora in un unico import:
```js
// lib/modulo.js
export const VAL_MIN = 10, VAL_MAX = 20;
export default function valuta(x) {
  return x > VAL_MIN && x < VAL_MAX
}
```
```js
// index.js
import metodo, {VAL_MIN, VAL_MAX as max} from 'lib/modulo'
// metodo conterrà la funzione valuta, VAL_MIN conterrà
// il valore di VAL_MIN e max conterrà il valore di VAL_MAX
```
Inoltre è possibile importare tutti gli export con nome all'interno di un unico oggetto usando la sintassi ```import * as nomeOggetto```
```js
//lib/modulo.js
export var val1 = 10, val2 = 20;
var text = "Testo";
export {text}
```
```js
// index.js
import * as mod from 'lib/modulo'
mod.val1 === 10 // true
mod.val2 === 20 // true
mod.text === "Testo" // true
```

