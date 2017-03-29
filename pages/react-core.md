Una breve guida ad alcuni dei principi base di React

# Componenti
I componenti sono le parti di cui si compongono le app sviluppate in React. Un componente può rappresentare qualsiasi cosa: un semplice div, un gruppo di elementi html complessi, il contenitore dell'intera applicazione, una singola pagina, o anche solo un elemento di logica che non produce nessun codice html ma modifica il comportamento dei componenti ad esso collegati.

Un componente può contenere altri componenti. Dividendo l'UI dell'app in diversi componenti separati, è possibile creare diverse interfacce semplicemente mettendo insieme i componenti creati e definendo la logica con cui devono comunicare tra loro.

## JSX
Nei componenti React è possibile usare il linguaggio JSX, che permette di combinare html e javascript per creare template per i componenti.
```jsx
    var currentDate = new Date().toString();
    return (
        <div>
            {currentDate}
        </div>
    )
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


## Come si creano
Esistono due modi principali per creare un componente: 
- Estendere la classe Component dalla libreria React (stateful component)
- Scrivere una semplice funzione che restituisca l'html da renderizzare ("stateless component" detto anche "pure component")

##### Esempio di stateful component
#
```js
// sintassi ES6 di import
import React, {Component} from 'react';

// sintassi ES6, MyComponent è il nome del mio componente, Component è il componente base definito nella libreria
// React di cui estendo il comportamento
class MyComponent extends Component {
    // metodo chiamato quando viene richiesta una nuova istanza del componente
    constructor(props) {
        // super chiama l'implementazione dello stesso metodo nella classe originale da cui deriva il componente,
        // quindi chiama il metodo constructor della classe Component. E' obbligatorio che sia la prima istruzione 
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



  

# New Features!

  - Import a HTML file and watch it magically convert to Markdown
  - Drag and drop images (requires your Dropbox account be linked)


You can also:
  - Import and save files from GitHub, Dropbox, Google Drive and One Drive
  - Drag and drop markdown and HTML files into Dillinger
  - Export documents as Markdown, HTML and PDF

Markdown is a lightweight markup language based on the formatting conventions that people naturally use in email.  As [John Gruber] writes on the [Markdown site][df1]

> The overriding design goal for Markdown's
> formatting syntax is to make it as readable
> as possible. The idea is that a
> Markdown-formatted document should be
> publishable as-is, as plain text, without
> looking like it's been marked up with tags
> or formatting instructions.

This text you see here is *actually* written in Markdown! To get a feel for Markdown's syntax, type some text into the left window and watch the results in the right.

### Tech

Dillinger uses a number of open source projects to work properly:

* [AngularJS] - HTML enhanced for web apps!
* [Ace Editor] - awesome web-based text editor
* [markdown-it] - Markdown parser done right. Fast and easy to extend.
* [Twitter Bootstrap] - great UI boilerplate for modern web apps
* [node.js] - evented I/O for the backend
* [Express] - fast node.js network app framework [@tjholowaychuk]
* [Gulp] - the streaming build system
* [Breakdance](http://breakdance.io) - HTML to Markdown converter
* [jQuery] - duh

And of course Dillinger itself is open source with a [public repository][dill]
 on GitHub.

### Installation

Dillinger requires [Node.js](https://nodejs.org/) v4+ to run.

Install the dependencies and devDependencies and start the server.

```sh
$ cd dillinger
$ npm install -d
$ node app
```

For production environments...

```sh
$ npm install --production
$ npm run predeploy
$ NODE_ENV=production node app
```

### Plugins

Dillinger is currently extended with the following plugins. Instructions on how to use them in your own application are linked below.

| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md] [PlDb] |
| Github | [plugins/github/README.md] [PlGh] |
| Google Drive | [plugins/googledrive/README.md] [PlGd] |
| OneDrive | [plugins/onedrive/README.md] [PlOd] |
| Medium | [plugins/medium/README.md] [PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md] [PlGa] |


### Development

Want to contribute? Great!

Dillinger uses Gulp + Webpack for fast developing.
Make a change in your file and instantanously see your updates!

Open your favorite Terminal and run these commands.

First Tab:
```sh
$ node app
```

Second Tab:
```sh
$ gulp watch
```

(optional) Third:
```sh
$ karma test
```
#### Building for source
For production release:
```sh
$ gulp build --prod
```
Generating pre-built zip archives for distribution:
```sh
$ gulp build dist --prod
```
### Docker
Dillinger is very easy to install and deploy in a Docker container.

By default, the Docker will expose port 80, so change this within the Dockerfile if necessary. When ready, simply use the Dockerfile to build the image.

```sh
cd dillinger
docker build -t joemccann/dillinger:${package.json.version}
```
This will create the dillinger image and pull in the necessary dependencies. Be sure to swap out `${package.json.version}` with the actual version of Dillinger.

Once done, run the Docker image and map the port to whatever you wish on your host. In this example, we simply map port 8000 of the host to port 80 of the Docker (or whatever port was exposed in the Dockerfile):

```sh
docker run -d -p 8000:8080 --restart="always" <youruser>/dillinger:${package.json.version}
```

