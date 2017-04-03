- [Higher-Order Components (HOC)](#hoc)

## <a name="hoc"></a>Higher-Order Components
Un Higher-Order Component (componente di ordine superiore, spesso abbreviato in HOC) è concettualmente molto simile alla [funzione di ordine superiore](es5-advanced.md#functor).  
Come la funzione di ordine superiore è una funzione che prende tra i parametri una funzione e ritorna una nuova funzione, un componente di ordine superiore è una funzione che prende tra i parametri un componente e ne ritorna uno nuovo, implementando qualche funzionalità.  
Gli HOC sono il modo migliore per creare comportamenti o funzionalità che abbiamo bisogno di utilizzare su più componenti.  
Ad esempio poniamo di voler costruire un HOC che permette di nascondere il componente se gli viene passata la proprietà ```hidden```:
```jsx
  // file HOC/hideable.js
  import React, {Component} from 'react';
  
  // la funzione prende come parametro un componente
  function makeHideable(WrappedComponent) {
    // ritorna un nuovo componente
    return class HOC extends Component {
      render() {
        // separo la prop hidden dalle altre usando destructuring
        let {hidden, ...otherProps} = this.props;
        // se la proprietà è presente non renderizzo nulla
        if (hidden)
          return null;
        // altrimenti renderizzo il componente originale, passandogli
        // tutte le altre props che avevo ricevuto
        else
          return <WrappedComponent {...otherProps} />
      }
    }
  }
  
```
Ora creiamo un componente che vogliamo poter nascondere
```jsx
// file: components/Test.js
import React, {Component} from 'react';
import makeHideable from 'HOC/hideable'
class Test extends Component {
  render() {
    return (
      <div>Componente di test</div>
    )
  }
}

// applichiamo il HOC creato precedentemente
var enhanchedTest = makeHideable(Test);
// esportiamo il componente arricchito della nuova implementazione
export default enchancedTest;

// oppure esportiamo direttamente il componente mentre gli applichiamo il HOC
export default makeHideable(Test);
```
Una volta impostato possiamo richiamare il nostro componente di Test e reagirà alla proprietà ```hidden```:
```jsx
  //file index.js
  import React, {Component} from 'react';
  import Test from 'components/Test'
  /*[...]*/
  class App extends Component {
    render() {
      // questo non renderizza niente
      return ( <Test hidden /> )
    }
  }
```
Una volta creato un HOC si può applicare allo stesso modo a diversi componenti, riducendo il codice duplicato.  
Come con le funzioni di ordine superiore, i componenti di ordine superiore si possono comporre nel caso si abbia necessità di applicarne più di uno ad un singolo componente. La sintassi è identica a quella vista in [funzioni di ordine superiore](es5-advanced.md#functor).  
