# L'environnement RRRR
React ramda reselect redux

confoo_mtl_2017

---

# React

* librairie de vue
* incite au fonctionnel
* JSX
* Pas de gestion de l'état
* Pas de communication serveur

---

```javascript
const redButton = ({label, onClick}) => {
  return <button style="backgroundColor: red" onClick={onClick}>
    {label}
  </button>
}
```

---

# Redux

* Gestion centralisé l'état
* Conçu pour React
* update => rendu de tout les composants (par défaut)
* possibilité de couper des branches de rendu
* état ===  une grosse map


---

````js
const reducer = (state, action) => {
  switch(action.type) {
    case "ADD_CONTACT":
      return addContact(state, action);
  }
}
````

---


````js
const component = ({name, age}) => {...}

const stateToProps = (state) => {
  return {
    name: state.name,
    age: nbYearSince(state.birthdate, new Date())
  }
}
const dispatchToProps = (dispatch) => {
  return {
    onClick: _ => dispatch({type:"gotoPage", page:"profile" })
  }
}
export default connect(stateToProps, dispatchToProps)(component)
````

---

# Ramda
* Manipulation de donnée
* type de donnée natif (Map, Array, String)
* non-destructive
* API très fonctionnel (map, flatten, reduce)
* Structure toujours dernier paramètre (!= lodash)
* Currifié

---

Manipulation de base
```js
const m = {k: "v"}
const n = R.assoc("j", "w", m)
//=> n == {k: "v", j: "w"}
```

Manipulation avancé
````js
const m = {a: b: {c: d: {cmpt: 10}}}
const update = {a: b: {c: d: {cmpt: R.inc}}}
const m2 = R.evolve(update, m)
// => m2 = {a: b: {c: d: {cmpt: 11}}}
````

---

# Reselect

* Permet de mémoiser  des valeur calculés

---

````js
let count = 0
const factoriel = (n) => {...}
const fact = n => { count++; return factoriel(n) }
const fact_mem = R.memoize(fact)
fact_mem(3) //=> 6
fact_mem(3) //=> 6
count //=> 1

````

---

```javascript
const state = {
  lines: [
    {desc: "Milk", price: 3.50},
    {desc: "Bread", price: 11.33}
  ],
  taxe: 0.12
};

const priceWT = createSelector((state) => state.lines.price))
const taxe = createSelector((state) => state.taxe)
const taxeAmount = createSelector(priceWT, taxe,
  (priceWT, taxe) => priceWT * taxe
)
const total = createSelector(priceWT, taxAmount,
  (priceWT, taxAmount) => priceWT + taxeAmount
)
```


---

# Problèmes

* Problématiques courantes ou nouvelles


---

# Routage

```
render((
  <Router history={browserHistory}>
    <Route path="/" component={App}>
      <Route path="about" component={About}/>
      <Route path="users" component={Users}>
        <Route path="/user/:userId" component={User}/>
      </Route>
      <Route path="*" component={NoMatch}/>
    </Route>
  </Router>
), document.getElementById('root'))
```

@from react-router's readme


---

## Quel composant updater ?

AUCUN: _manuellement_

TOUS: _(du moins mentalement)_

---

## Quand updater un composant ?

Via dépendance:

* Redux.connect
* Reselect.createSelector

---

## Qui doit avoir accès au store ?

TOUS

* Composition de composant plus simple
* Branche d'update coupé rapidement

---

## Comment gérer le store ?

* Pas de mutation
* Redux aide facilite

---

##  Afficher une image avec un défaut en cas de problèmes

````js
currentUrl(){
  if(this.state.imgStatus === "ERROR"|| !this.props.imgSrc) {
    return this.props.defaultSrc
  }
  return this.props.imgSrc
}

render(){
  const currentUrl = this.currentUrl()
  if(["ERROR", "LOADED"].includes(this.state.imgStatus)){
    return <img src={currentUrl}/>
  }
  return <img src={currentUrl}
              onLoad={this.onLoad} onError={this.onError}/>
}
````

---

## Affichage de grande liste

recherce => [ids]

[ids] => sommaire([ids])

id => détails(id)

---

Stratégie

````js
state = {
  itemsById: {
    42: {
      détails: {...},
      sommaire: {...}
    }
  },
  showableItems: [42, 10,...],

}
````

---

````js
//Items sans sommaire:

R.find(
  (id) => R.isNil(state.itemsById[id].sommaire),
  R.keys(state.itemsById)
)

// Items sans détails:
R.find(
  (id) => R.isNil(state.itemsById[id].details),
  R.keys(state.itemsById)
)

// Nouvel item:
(id) => R.not(R.contains(R.keys(state.itemsById), id))
````

---

## Affichage de liste à retardement

````js
render(){
  const itemsToRender = R.slice(0,
                                this.props.lastVisibleIndex,
                                this.props.list)
  const completed = R.equals(this.props.lastVisibleIndex,
                             R.length(this.props.list))
  const visibilityChange = shown => shown ? this.loadMore() : null     
  return <div>
      {R.map(this.props.renderItem, itemsToRender)}
      {!completed ?
        <span>
          <VisibilitySensor onChange={visibilityChange}/> }
          <LoadMore/>
        </span>: null }
    </div>
}

````

---


## Animation interruptible

```js
class Demo extends Component {
  handleMouseDown() {
    this.setState({open: !this.state.open})
  }
  render() {
    return <div>
      <button onMouseDown={this.handleMouseDown}>Toggle</button>
      <Motion style={{x: spring(this.state.open ? 400 : 0)}}>
        {({x}) =>
          <div className="demo">
            <div className="demo-block" style={{
              transform: `translate3d(${x}px, 0, 0)`
            }} />
          </div>}
      </Motion>
    </div>
  }
}
```

@voir React-Motion

---

## Traduction

```js

const TrMessage = () => {
  const msg = `Hello {name}, you have {unreadMsg, number} {unreadMsg,
    plural,
    one {message}
    other {messages}
  }`

  return  <FormattedMessage
      id="welcome"
      defaultMessage={msg}
      values={user}
  />

}
```

@voir React-intl

---

Extraction autmatique via Babel-react-intl

Format JSON
````json
[
  {
    "id": "welcome",
    "defaultMessage": "Hello {name}, you have {unreadMsg, number} {unreadMsg, plural, one {message} other {messages}"
  }
]
````

1 json / composant


---

quelques coup  de ༼∩ •́ ヮ •̀ ༽⊃━☆ﾟ. * ･ ｡ﾟ

````json
{
  "welcome": "Bonjour {name}, you have {unreadMsg, number} {unreadMsg, plural, zero {message}, one {message} other {messages}}"
}
````

1 json / app

---

<!-- .slides: width: 1300px -->
## Modification d'un formulaire

````js
const form = ({firstname, lastname, invalidFields, onChange})=> {
  return <form>
      <input type="text" value={firstname} placeholder="firstname"
             onChange={onChange("firstname")}
             className={{invalid: invalidFields["firstname"]}}>

      <input type="text" value={lastname} placeholder="lastname"
             onChange={onChange("lastname")}
             className={{invalid: invalidFields["lastname"]}}>
  </from>
}
````

````js
const stateToProps = (state) => {
  return {firstname, lastname, invalidFields} = state
}
const dispatchToProps = (dispatch) => ({
  onChange: (fieldName) => (value) => {
    dispatch({type: "change_field", fieldName, value})
}})
````

---

````js
const onChangeField = (state, action) => {
  const invalidFields = !validate(action.value) ?
    R.push(action.fieldName) :
    R.remove(action.fieldName)

  const tr = {
    invalidFields: invalidFields,
    [action.firstname]: R.always(action.value)
  }
  return R.evolve(tr, state)

}
````
