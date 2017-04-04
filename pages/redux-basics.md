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
- Tutto lo stato dell'applicazione risiede in un unico oggetto, con struttura ad albero. Questo oggetto è read-only ed è contenuto nello *store*.
- L'unico modo per cambiare lo stato dell'applicazione consiste nell'emettere una *action*. Un'*action* è un semplice oggetto javascript che contiene il tipo di azione eseguita ed eventuali parametri con cui è stata eseguita.
- le *action* una volta emesse vengono elaborate dai *reducer*, funzioni pure che prendono come parametri lo stato dell'applicazione e l'azione emessa, e restituiscono in output il nuovo stato.  

In pratica ad azioni dell'utente e alle comunicazioni con il server corrisponderanno delle *action*, e si andranno a scrivere dei *reducer* che definiscono come si comporta l'applicazione all'occorrenza di ogni action.  
A collegare il tutto ci pensa lo store, che contiene lo stato dell'applicazione e permette di emettere *actions* e di ricevere i cambiamenti dello stato dopo ogni elaborazione.

## <a name="store"></a>Store
Lo store è creato a partire dai reducer ed espone tre metodi:
- `dispatch`, che permette di emettere un'azione e di ottenere un nuovo stato dell'applicazione a partire dallo stato attuale e dall'action appena emessa
- `getState`, che permette di accedere allo stato dell'applicazione
- `subscribe`, che permette di eseguire un callback ogni volta che cambia lo stato dell'applicazione  
Sostanzialmente si comporta come uno store nell'architettura Flux, ma in Redux esiste un unico store che contiene tutto lo stato dell'applicazione.  

Per creare uno store si usa il metodo `createStore` di redux e si passano come parametro i reducer:
```js
// importo createStore da redux
import {createStore} from 'redux';
// importo i reducer che andremo a scrivere 
// dalla cartella reducers
import reducers from 'reducers';
var store = createStore(reducers);
```

Per vedere come funziona lo store è necessario introdurre prima le actions e i reducer, ma per il momento possiamo riassumere il comportamento generale:
Vari componenti dell'applicazione possono impostare dei callback sull'aggiornamento dello stato usando il metodo `subscribe`, e ottenere il nuovo stato chiamando `getState`.
```js
  function listener() {
    var newState = store.getState();
    Component.setState(newState) // logica di update del componente
  }
  store.subscribe(listener) // listener viene chiamata ogni volta che lo stato dell'app cambia
```
In corrispondenza di interazioni dell'utente o con il server verranno emesse delle *actions* attraverso il metodo `dispatch`:
```js 
  store.dispatch(action)
```
Le action emesse verranno elaborate dai reducer, ottenendo alla fine il nuovo stato dell'applicazione. Ottenuto il nuovo stato dell'applicazione verranno chiamati i callback associati in precedenza con `subscribe`, così che React possa aggiornare la visualizzazione della pagina.

## <a name="actions"></a>Actions
Un action è un semplice oggetto javascript. L'action potenzialmente può avere qualsiasi struttura ma generalmente ci si attiene ad uno standard ([Flux Standard Action](https://github.com/acdlite/flux-standard-action), o FSA).  
L'action come minimo deve contenere la proprietà `type` che identifica univocamente il tipo di action. Ad esempio questa è un'action valida per lo standard FSA:
```js
var action = { type: "INIT_APP" }

store.dispatch(action)
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

store.dispatch(action)
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

store.dispatch(action)
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

store.dispatch(action)
```
Tecnicamente è possibile scrivere le action a mano, ma è quasi sempre più comodo usare una funzione che le restituisca già pronte:
```js
  function saveContatto(datiContatto) {
    return {
      type: "SAVE_CONTATTO",
      payload: datiContatto
    }
  }
  
  // diventa più comodo usare dispatch più volte sulla stessa 
  // action con parametri diversi
  var datiContatto = { nome: "Daniel", cognome: "Rats" }
  store.dispatch(saveContatto(datiContatto))
  
  var datiContatto2 = { nome: "Danieru", cognome: "Nezumi" }
  store.dispatch(saveContatto(datiContatto2))
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

// esempio d'uso - attenzione è solo un esempio. Nella pratica 
// si occupa redux di chiamare i reducer, non vengono mai chiamati manualmente

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

// è cambiata l'organizzazione ma non è cambiato il modo di usare il reducer
var state = reducer() 
state = reducer(state, openDocument())
state = reducer(state, closeDocument())
```

#### Vantaggi
Il vantaggio principale dei reducer deriva dagli stessi limiti che impongono: **dati gli stessi parametri in input ritornano sempre lo stesso valore**.  
Questo ha conseguenze importanti:
- I reducer sono testabili: visto che a parità di stato e action un reducer deve ritornare sempre lo stesso valore, è possibile scrivere test automatizzati che verificano il comportamento dei reducer.
- Dato uno stato di partenza e una serie di action è possibile ricostruire la storia dello stato dell'applicazione in seguito a quelle modifiche. Ciò permette di riprodurre bug con su macchine diverse dall'originale, automatizzare processi test sull'interfaccia, etc
- Mantenendo in memoria la serie di actions eseguite, è possibile ricalcolare lo stato attuale escludendo alcune di esse o riapplicandole all'occorrenza. In questo modo è possibile implementare funzioni di undo/redo senza effettuare grosse modifiche.
- Si è obbligati a separare la logica di business dalle interazioni con il server e con l'utente, creando codice più pulito e riutilizzabile.

#### Reducer per stati complessi
Nel caso di applicazioni che devono gestire un grande numero di reducer e uno stato molto complesso, si renderà necessario separare i reducer in più metodi (e possibilmente in più file).  

Poniamo di avere un'applicazione che mostra una lista di documenti e ha la possibilità di aprire il dettaglio del documento in una modale.  Iniziamo dal pensare alla struttura dello stato della nostra applicazione. Sappiamo che dobbiamo mostrare una lista di documenti, quindi avremo bisogno di un array o di un oggetto che ne contenga i dati. Inoltre abbiamo la possibilità di aprire e chiudere una modale (proprietà booleana) e di mostrare un documento all'interno (serve un riferimento ad un elemento della lista).  

Una possibile struttura potrebbe essere questa:
```js
var state = {
  documents: [{
    id: 217,
    cliente: "RottureInCloud",
    clienteID: 567,
    importo: 2000
  }, {
    id: 122,
    cliente: "EvaGestionale",
    clienteID: 331,
    importo: 1000
  }],
  modal: {
    open: true,
    documentID: 217
  }
}
```

A questo punto consideriamo di che tipo di actions avremo bisogno. Sicuramente dovremo poter ricevere i dati con cui popolare la lista di documenti. Inoltre avremo bisogno di poter aprire un documento in una modale, e chiudere la modale stessa.  
Questi potrebbero essere i generatori di action corrispondenti:
```js
  const RECEIVE_DOCUMENTS = "RECEIVE_DOCUMENTS",
        OPEN_DOCUMENT_MODAL = "OPEN_DOCUMENT_MODAL",
        CLOSE_DOCUMENT_MODAL = "CLOSE_DOCUMENT_MODAL";

  const receiveDocuments = documents => ({
    type: RECEIVE_DOCUMENTS,
    payload: { 
      documents: documents
    }
  })
  
  const openDocumentModal = documentID => ({
    type: OPEN_DOCUMENT_MODAL,
    payload: {
      documentID: documentID
    }
  })
  
  const closeDocumentModal = () => ({
    type: CLOSE_DOCUMENT_MODAL
  })
```

Passiamo a scrivere i reducer.  Abbiamo bisogno di un reducer che gestisca la lista dei documenti, e due reducer che gestiscano l'apertura e la chiusura della modale. 
```js
  const initialState = {
    documents: [],
    modal: {
      open: false,
      documentID: null
    }
  }

  function receiveDocumentsReducer(state, action) {
    let documents = actions.payload.documents;
    return {
      ...state,
      documents
    }
  }
  
  function openDocumentModalReducer(state, action) {
    let documentID = action.payload.documentID;
    return {
      ...state,
      modal: {
        open: true,
        documentID
      }
    }
  }
  
  function closeDocumentModalReducer(state, action) {
    return {
      ...state,
      modal: {
        open: false,
        documentID: null
      }
  }
  
  function reducer(state=initialState, action) {
    switch (action.type) {
      case RECEIVE_DOCUMENTS:
        return receiveDocumentsReducer(state, action);
      case OPEN_DOCUMENT_MODAL:
        return openDocumentModalReducer(state, action);
      case CLOSE_DOCUMENT_MODAL:
        return closeDocumentModalReducer(state, action);
      default: 
        return state;
    }
  }
  
  // esempio d'uso esplicito, in realtà è lo store che
  // si occupa di chiamare i reducer
  var state = reducer();  // state === initialState

  // poniamo che ci siano arrivati i dati dei documenti dal server
  var documentsFromServer = [{ id: 1, cliente: "MyPenna", clienteID: 211, importo: 1200}]
  state = reducer(state, receiveDocuments(documentsFromServer));
  /* 
  state === {
    documents: [{ id: 13, cliente: "MyPenna", clienteID: 211, importo: 1200}],
    modal: {
      open: false,
      documentID: null
    }
  } */
  
  // al click dell'utente sulla riga facciamo elaborare quest'altra action
  state = reducer(state, openDocumentDialog(13));
  /* 
  state === {
    documents: [{ id: 13, cliente: "MyPenna", clienteID: 211, importo: 1200}],
    modal: {
      open: true,
      documentID: 13
    }
  } */
```
Una cosa che si nota è che i reducer modificano solo una parte dello stato (per esempio `state.documents` o `state.modal`) ma si complicano un po' perché ricevono come parametro l'intero stato dell'applicazione e devono di nuovo restituire l'intero stato. Questa complessità aumenta all'aumentare della complessità dello stato dell'applicazione.  
Una soluzione consiste nel passare ad ogni reducer solo la parte di stato di cui si occupa, e gestire l'aggiornamento dello stato complessivo dell'applicazione nel reducer principale. Esempio:
```js
// file redux/documents.js
// scriviamo un reducer che gestisca unicamente la parte state.documents

// lo stato iniziale di state.documents è un array vuoto
const initialState = [];

// costanti relative a state.documents
const RECEIVE_DOCUMENTS = "RECEIVE_DOCUMENTS";

// questo reducer in state riceve state.documents
// e il valore di ritorno andrà in state.documents
function receiveDocumentsReducer(state, action) {
  return actions.payload.documents;
}

// questo è il reducer principale che esportiamo
export default function reducer(state=initialState, action) {
  // gestisce solo le action relative a state.documents
  switch(action.type) {
    case RECEIVE_DOCUMENTS:
      return receiveDocumentsReducer(state, action)
    default: 
      return state;
  }
}
```
```js
// file reducers/modal.js
// scriviamo un reducer che gestisca unicamente la parte state.modal

// lo stato iniziale di state.modal è un oggetto con questa struttura
const initialState = {
  open: false,
  documentID: null
};

// costanti relative a state.documents
const OPEN_DOCUMENT_MODAL = "OPEN_DOCUMENT_MODAL";
const CLOSE_DOCUMENT_MODAL = "CLOSE_DOCUMENT_MODAL";

// questi reducer in state ricevono state.modal
// e il valore di ritorno andrà in state.modal
function openDocumentModalReducer(state, action) {
  return {
    open: true,
    documentID: actions.payload.documentID
  }
}
function closeDocumentModalReducer(state, action) {
  return {
    open: false,
    documentID: null
  }
}

// questo è il reducer principale che esportiamo
export default function reducer(state=initialState, action) {
  // gestisce solo le action relative a state.modal
  switch(action.type) {
    case OPEN_DOCUMENT_MODAL:
      return openDocumentModalReducer(state, action);
    case CLOSE_DOCUMENT_MODAL:
      return closeDocumentModalReducer(state, action);
    default: 
      return state;
  }
}
```
```js
// file redux/index.js
// scriviamo un reducer che metta insieme i reducer precedenti

// importiamo gli altri due reducer
import documentsReducer from 'redux/documents';
import modalReducer from 'redux/modal';

// non c'è bisogno di stato iniziale perché gestito dai 
// reducer creati prima
export default function reducer(state={}, action) {
  return {
    documents: documentsReducer(state.documents, action),
    modal: modalReducer(state.modal, action)
  }
}

// in alternativa, il metodo combineReducers di redux 
// ci semplifica un po' quest'ultima parte
import {combineReducers} from 'redux'

export default combineReducers({
  documents: documentsReducer,
  modal: modalReducer
})
```
## <a name="recap"></a>Come funzionano insieme
Ora che abbiamo visto le varie parti che compongono redux, diamo di nuovo un'occhiata d'insieme per vedere come il tutto funziona in un'applicazione. Prenderemo ad esempio l'applicazione di cui abbiamo costruito i reducer nell'esempio precedente.  
Struttura dei file:
```
|-- components
|  |-- App.js
|  |-- Modal.js
|
|-- redux
|  |-- documents.js
|  |-- modal.js
|  |-- index.js
|
|-- index.js
|-- store.js
```
```js
// file store.js
import {createStore} from 'redux';
import {reducers} from 'reducers';

// crea ed esporta lo store
export default createStore(reducers)
```

```js
// file reducers/index.js
// questo file è identico a quello creato nell'esempio precedente
import documentsReducer from 'redux/documents';
import modalReducer from 'redux/modal';
import {combineReducers} from 'redux';

export default combineReducers({
  documents: documentsReducer,
  modal: modalReducer
})
```

```js
// file redux/documents.js
const initialState = [];
const RECEIVE_DOCUMENTS = "RECEIVE_DOCUMENTS";

// action generator
function receiveDocuments(documents) {
  return {
    type: RECEIVE_DOCUMENTS,
    payload: { documents }
  }
}
// esporto l'action generator
export { receiveDocuments };

// reducers
function receiveDocumentsReducer(state, action) {
  return actions.payload.documents;
}

export default function reducer(state=initialState, action) {
  switch(action.type) {
    case RECEIVE_DOCUMENTS:
      return receiveDocumentsReducer(state, action)
    default: 
      return state;
  }
}
```

```js
// file redux/modal.js
const initialState = {
  open: false,
  documentID: null
};

const OPEN_DOCUMENT_MODAL = "OPEN_DOCUMENT_MODAL",
      CLOSE_DOCUMENT_MODAL = "CLOSE_DOCUMENT_MODAL";

// action generator
function openDocumentModal(documentID) {
  return {
    type: OPEN_DOCUMENT_MODAL,
    payload: { documentID }
  }
}
function closeDocumentModal() {
  return {
    type: OPEN_DOCUMENT_MODAL
  }
}
// esporto gli action generator
export { openDocumentModal, closeDocumentModal };

// reducers
function openDocumentModalReducer(state, action) {
  return {
    open: true,
    documentID: actions.payload.documentID
  }
}
function closeDocumentModalReducer(state, action) {
  return {
    open: false,
    documentID: null
  }
}

export default function reducer(state=initialState, action) {
  switch(action.type) {
    case OPEN_DOCUMENT_MODAL:
      return openDocumentModalReducer(state, action);
    case CLOSE_DOCUMENT_MODAL:
      return closeDocumentModalReducer(state, action);
    default: 
      return state;
  }
}
```

```js
```

```js
```

```js
```

## <a name="reactredux"></a>react-redux
## <a name="middleware"></a>Middleware
