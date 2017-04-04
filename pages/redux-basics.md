- [Principi Generali](#principi)
- [Actions](#actions)
- [Reducers](#reducers)
- [Store](#store)
- [Come funzionano insieme](#recap)
- [react-redux](#reactredux)
- [Middleware](#middleware)
## <a name="principi"></a>Principi Generali
Redux è un'architettura software per sviluppare applicazioni che devono gestire stati e dati complessi riducendo al minimo la possibilità di errori da "distrazione".  
Le idee alla base di redux sono le seguenti:
- Tutto lo stato dell'applicazione risiede in un unico oggetto, con struttura ad albero. Questo oggetto è read-only.
- L'unico modo per cambiare lo stato dell'applicazione consiste nell'emettere una *action*. Un'*action* è un semplice oggetto javascript che contiene il tipo di azione eseguita ed eventuali parametri con cui è stata eseguita.
- le *action* una volta emesse vengono elaborate dai *reducer*, funzioni pure che prendono come parametri lo stato dell'applicazione e l'azione emessa, e restituiscono in output il nuovo stato.  
In pratica ad azioni dell'utente e alle comunicazioni con il server corrisponderanno delle *action*, e si andranno a scrivere dei *reducer* che definiscono come si comporta l'applicazione all'occorrenza di ogni action.

## <a name="actions"></a>Actions
Un action è un semplice oggetto javascript. L'action potenzialmente può avere qualsiasi struttura ma generalmente ci si attiene ad uno standard ([Flux Standard Action](https://github.com/acdlite/flux-standard-action), o FSA).  
L'action come minimo deve contenere la proprietà `type` che identifica univocamente il tipo di action. Ad esempio questa è un'action valida per lo standard FSA:
```js
var action = { type: "INIT_APP" }
```
Nel caso l'action contenga anche dei dati che la descrivono, vanno inseriti nella proprietà `payload`:
```js
var action = {
  type: "SAVE_CONTATTO_RUBRICA",
  payload: {
    id: 562722,
    idTipoContatto: 1
    ragSociale: "BruttureInCloud.it"
  }
}
```
Se l'action rappresenta un errore, può essere aggiunta la proprietà `error` con valore `true`:
```js
var action = {
  type: "LOAD_DOCUMENTO",
  payload: {
    return_mes: "Documento non trovato"
  },
  error: true
}
```
Infine la proprietà `meta` può contenere informazioni aggiuntive che descrivono la action ma non fanno parte del payload. Ad esempio informazioni da usare nelle analytics, o proprietà che individuano una gestione particolare di quell'action (spesso in combinazione con i [middleware](#middleware)).
```js 
var action = {
  type: "LOAD_DOCUMENTO",
  payload: {
    id: 727388,
    idTipoDoc: 1,
    inOrOut: "out"
  },
  meta: {
    API: API.Documenti.caricaDocumento
  }
}
```
Tecnicamente è possibile scrivere le action a mano, ma è quasi sempre più comodo usare una funzione che le restituisca già pronte:
```js
  function saveContatto(datiContatto) {
    return {
      type: "SAVE_CONTATTO",
      payload: datiContatto
    }
  }
```
## <a name="reducers"></a>Reducers
I reducer contengono la logica dell'applicazione e descrivono come lo stato dell'applicazione varia in seguito ad ogni azione.  
I reducer hanno le seguenti caratteristiche:
- Prendono come parametro lo stato dell'applicazione e un action, e ritornano il nuovo stato dell'applicazione. Si chiamano reducer perché hanno la stessa struttura di una funzione accettata dal metodo [Array.prototype.reduce](es5-advanced.md#reduce)
- Sono funzioni pure, ovvero :
  - non modificano e non referenziano variabili al di fuori del proprio scope, e non modificano i parametri che hanno ricevuto (ovvero non hanno *side-effects*)
  - non chiamano API
  - non usano funzioni che a loro volta hanno *side-effects* (es: new Date()) 
  - **dati gli stessi parametri in input, restituiscono sempre lo stesso output**  

Un esempio di reducer che cambia il valore di una variabile in base al tipo di action emessa:
```js
const initialState = { 
  altriValori: "Test",
  documentiAperti: 0 
}

// se il primo parametro è undefined usa lo stato iniziale dichiarato prima
function reducer(state=initialState, action) {
  if (action.type == "OPEN_DOCUMENT") {
    // se l'action è di tipo OPEN_DOCUMENT ritorna un nuovo
    // stato con la variabile documentiAperti incrementata di 1
    return {
      ...state,
      documentiAperti: state.documentiAperti + 1
    }
  }
  else if (action.type == "CLOSE_DOCUMENT") {
    // se l'action è di tipo CLOSE_DOCUMENT ritorna un nuovo
    // stato con la variabile documentiAperti diminuita di 1
    return {
      ...state,
      documentiAperti: state.documentiAperti - 1
    }
  }
  else {
    // se l'action non è nessuno dei due tipi ritorna lo stato 
    // senza modificarlo (ovvero non gestisce l'action)
    return state
  }
}

// generatori di azioni
function openDocument() {
  return { type: "OPEN_DOCUMENT" };
}
function closeDocument() {
  return { type: "CLOSE_DOCUMENT" };
}

// redux inizialmente parte con uno stato dell'applicazione = undefined
var state = reducer() 
// in questo momento state = initialState
state = reducer(state, openDocument())
// in questo momento state === { altriValori: "Test", documentiAperti: 1 }
state = reducer(state, closeDocument())
// in questo momento state === { altriValori: "Test", documentiAperti: 0 }
// Attenzione: state !== initialState, perché è sempre stato ritornato un nuovo oggetto
```
Idealmente andremo a riorganizzare il codice separando i reducer in diverse funzioni e utilizzando costanti per le tipologie di actions, in modo da avere qualcosa di più leggibile:
```js
const initialState = { 
  altriValori: "Test",
  documentiAperti: 0 
}
// action constant
const OPEN_DOCUMENT = "OPEN_DOCUMENT", 
      CLOSE_DOCUMENT = "CLOSE_DOCUMENT";

// action generator
function openDocument() {
  return { type: OPEN_DOCUMENT };
}
function closeDocument() {
  return { type: CLOSE_DOCUMENT };
}

// reducer
function openDocumentReducer(state, action) {
  return {
    ...state,
    documentiAperti: state.documentiAperti + 1
  }
}

function closeDocumentReducer(state, action) {
  return {
    ...state,
    documentiAperti: state.documentiAperti - 1
  }
}

function reducer(state=initialState, action) {
  switch(action.type) {
    case OPEN_DOCUMENT: 
      return openDocumentReducer(state, action);
    case CLOSE_DOCUMENT:  
      return closeDocumentReducer(state, action);
    default:
      return state;
  }
}
```
#### Vantaggi
Il vantaggio principale dei reducer deriva dagli stessi limiti che impongono: **dati gli stessi parametri in input ritornano sempre lo stesso valore**.  
Questo ha conseguenze importanti:
- I reducer sono testabili: visto che a parità di stato e action un reducer deve ritornare sempre lo stesso valore, è possibile scrivere test automatizzati che verificano il comportamento dei reducer.
- Dato uno stato di partenza e una serie di action è possibile ricostruire la storia dello stato dell'applicazione in seguito a quelle modifiche. Ciò permette di riprodurre bug con su macchine diverse dall'originale, automatizzare processi test sull'interfaccia, etc
- Mantenendo in memoria la serie di actions eseguite, è possibile ricalcolare lo stato attuale escludendo alcune di esse o riapplicandole all'occorrenza. In questo modo è possibile implementare funzioni di undo/redo senza effettuare grosse modifiche.
- Si è obbligati a separare la logica di business dalle interazioni con il server e con l'utente, creando codice più pulito e riutilizzabile.
## <a name="store"></a>Store
## <a name="recap"></a>Come funzionano insieme
## <a name="reactredux"></a>react-redux
## <a name="middleware"></a>Middleware
