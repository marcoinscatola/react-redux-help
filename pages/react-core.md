Una breve guida ad alcuni dei principi base di React

# Componenti
I componenti sono le parti di cui si compongono le app sviluppate in React. Un componente può rappresentare qualsiasi cosa: un semplice div, un gruppo di elementi html complessi, il contenitore dell'intera applicazione, una singola pagina, o anche solo un elemento di logica che non produce nessun codice html ma modifica il comportamento dei componenti ad esso collegati.

Un componente può contenere altri componenti. Dividendo l'UI dell'app in diversi componenti separati, è possibile creare diverse interfacce semplicemente mettendo insieme i componenti creati e definendo la logica con cui devono comunicare tra loro.

## Metodi
React mette a disposizione alcuni metodi predefiniti che vengono automaticamente chiamati in determinati momenti del lifecycle del componente. Di seguito due dei più importanti:
- componentDidMount  
Viene chiamato automaticamente subito dopo che il componente è stato aggiunto all'html della pagina. In questo metodo spesso si eseguono operazioni di inizializzazione del componente o di richiesta di dati dal server  
```js
    componentDidMount() {
        this.init();
        this.getServerData();
    }
```
- render  
Viene chiamato ogni volta che React decide che il componente deve essere aggiornato. Deve ritornare l'html che corrisponde a quel componente. Anche se tecnicamente è possibile scrivere l'html attraverso i metodi React.createElement e React.createComponent, l'unico metodo 'sano' per ritornare qualcosa di leggibile è di usare JSX, che permette di usare una sintassi simile a HTML all'interno di un metodo javascript (vedi paragrafo più avanti)
```jsx
    render() {
        return (
            <div>
                <span>Componente</span>
                <span>Personalizzato</span>
            </div>
        )
    }
```


## JSX
Nei componenti React è possibile usare il linguaggio JSX, che permette di combinare html e javascript per creare template per i componenti. E' preferibile inserire il codice JSX tra parentesi tonde, mentre è *obbligatorio* che le espressioni javascript siano dentro parentesi graffe. 

```jsx
render() {
    var currentDate = new Date().toString();
    return (
        <div>
            {currentDate}
        </div>
    )
}
```

Ci sono due particolarità da tenere a mente. ```class``` e ```for``` sono proprietà degli elementi html, ma sono anche keyword riservate in javascript. 
Solo per queste due proprietà, esistono delle alternative da usare in JSX, rispettivamente ```className``` e ```htmlFor```

```jsx
render() {
    var active = this.props.active;
    var className = active?"active":"inactive";
    return (
        <div className={className}>
            <label htmlFor="username">Esempio</label>
            <input id="username" />
        </div>
    )
}
```
> **Attenzione!**
> All'interno del JSX si possono inserire solo espressioni javascript valutabili. Come regola generale, se non è possibile scriverlo nell'assegnazione di una variabile, non è possibile scriverlo in JSX;
```jsx
// esempio SBAGLIATO, va in errore
render() {
    var active = true;
    return (
        <div>
            { 
                if (active) 
                    return "Attivo!"
                else 
                    return "Non attivo!"
            }
         </div>
     )   
}

// esempio CORRETTO
render() {
    var active = true;
    return (
        <div>
            {active?"Attivo!":"Non attivo!"}
         </div>
     )
    
}
```
```jsx
// esempio SBAGLIATO, va in errore
render() {
    var array = ["Elemento1","Elemento2","Elemento3"];
    return (
        <ul>
            { 
                for (var i = 0; i < array.length; i++)
                    return (<li>{array[i]}</li>);
            }
         </ul>
     )   
}

// esempio CORRETTO
render() {
    var array = ["Elemento1","Elemento2","Elemento3"];
    return (
        <ul>
            {
                array.map(function(el) {
                    return (<li>{el}</li>);
                }
            }
         </ul>
     )
}
```


## Stato e proprietà
Due parti fondamentali dei componenti in react sono lo stato e le proprietà dei componenti, rispettivamente *state* e *props*.

Lo stato del componente è accessibile dal componente stesso in  ```this.state```.
Lo stato viene usato per tutto ciò che riguarda esclusivamente lo stato interno del componente e che non dipende dagli altri componenti. Ad esempio può rappresentare lo stato di focus di un input, o il timer interno di un div che fa un conto alla rovescia. 

Le proprietà sono parametri del componente che vengono ricevuti dall'esterno e sono accessibili dal componente in ```this.props```
Ad esempio poniamo il caso di un componente MyInput che si consiste in una label e un input html. Proprietà come placeholder, type etc andrebbero gestite come *props*, perché non è MyInput a decidere il suo type, il suo placeholder, la sua label etc, ma è il componente che lo contiene a specificare questi parametri per lui al momento del render, passandole come se fossero proprietà html (vedi codice sotto)
Come in html avremmo
```html
    <div>
        <label>Username</label>
        <input type="text" placeholder="Username">
    </div>
    <div>
        <label>Password</label>
        <input type="password" placeholder="Password">
    </div>
```
In react potremmo avere
```jsx
    import React, {Component} from 'react';
    
    class MyInput extends Component {
        render() {
            let props = this.props;
            <div>
                <label>{props.label}</label>
                <input type={props.type} placeholder={props.placeholder} />
            </div>
        }
    }
    
    class MyComponent extends Component{
        render() {
            return (
                <div>
                    <MyInput 
                        type="text" 
                        label="Username" 
                        placeholder="Username" 
                    />
                    <MyInput 
                        type="password" 
                        label="Password" 
                        placeholder="Password" 
                    />
                </div>    
            )
        }
    }
```
###### Attenzione!
props e state non vanno mai modificate direttamente!
Un'istruzione del tipo ```this.props.label = "Testo"``` va assolutamente evitata. Modificando props o state direttamente React non ha modo di sapere che è avvenuta una modifica e non chiamerà il metodo render del componente interessato. Quindi a schermo non vedremo nessun cambiamento.

Per modificare props è necessario che sia il componente *parent* a passare proprietà diverse al component *child*, magari in base a una logica condizionale nel suo metodo render.
Per modificare state, va usato il metodo ```this.setState``` presente in ogni componente React creato a partire dalla classe Component.
setState prende come parametro il nuovo stato del componente, e automaticamente chiama render() in modo da aggiornare la visualizzazione a schermo del componente.
```jsx

render() {
    let active = this.state.active;
    return (
        <div onClick={this.handleToggle}>
            {active?"Elemento Attivo!":"Elemento non attivo"}
        </div>
    )
}

handleToggle() {
    let active = this.state.active;
    this.setState({
        active: !active
    })
}

```


## Come si creano
Esistono due modi principali per creare un componente: 
- Estendere la classe Component dalla libreria React (stateful component)
- Scrivere una semplice funzione che restituisca l'html da renderizzare ("stateless component" detto anche "pure component")

##### Esempio di stateful component
```jsx
// sintassi ES6 di import
import React, {Component} from 'react';

// sintassi ES6, MyComponent è il nome del mio componente, 
// Component è il componente base definito nella libreria
// React di cui estendo il comportamento
class MyComponent extends Component {
    // metodo chiamato quando viene richiesta una nuova istanza del componente
    constructor(props) {
        // 'super' chiama l'implementazione dello stesso metodo nella classe 
        // originale da cui deriva il componente, quindi chiama il metodo constructor
        //  della classe Component. E' obbligatorio che sia la prima istruzione 
        // del metodo constructor di un componente React
        super(props);
    }
    
    render() {
        let label = this.props.label;
        return (
            <div>{label}</div>
        )
    }
}
```
#### Esempio di stateless component
```js
    
```
