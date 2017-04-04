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
- Tutto lo stato dell'applicazione risiede in un unico oggetto, con struttura ad albero
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
## <a name="reducers"></a>Reducers
## <a name="store"></a>Store
## <a name="recap"></a>Come funzionano insieme
## <a name="reactredux"></a>react-redux
## <a name="middleware"></a>Middleware
