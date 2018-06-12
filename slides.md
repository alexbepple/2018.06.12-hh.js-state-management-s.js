
background-image: url(/assets/hugh-stevenson-596597-unsplash.jpg)
background-size: cover

class: middle
# Simpler state management with S.js
### Alex Bepple
##### @alexbepple

---

# Getting started with [S.js](https://www.npmjs.com/package/s-js)

```javascript
import s from 's-js'

// create signal
const a = s.value(0)

// construct computation ('derived signal')
const b = s(() => a() + 1)
console.log(b()) // => 1

// change signal value
a(1)
// computation updates automatically
console.log(b()) // => 2
```

---

background-image: url(/assets/markus-petritz-135033-unsplash.jpg)
background-size: cover

class: bottom
# Let’s explore S.js together

---

# Functional Reactive Programming

Reactive + Functional = 
S.js + pick you poison 
(mine is [Ramda](http://ramdajs.com/))

No need to reinvent the wheel!  
~~RxJS~~, ~~Bacon~~, ~~Kefir~~, ~~most~~ etc.

They might have super powers.  
But sometimes, we just need wheels.

---

class: middle

# Simpler state management
### (with React)

.footnote[
  [*] Gernot Höflechner’s JSUnconf talk “You don’t need state management” really struck a chord with me. He is onto something. Maybe we really can push all state to the boundaries.
]


---


class: inverse

# How do you <br/> implement <br/> this with <br/> Redux?

.right[
  ![](/assets/3-counters.png)
]

--

* Duplicate everything?
* Parameterize everything?
  * Separation of concerns?
* Two different reducers?


---


# Simplest approach: <br/> just rerender everything

```jsx
const s1 = s.data(0)
const s2 = s.data(1)

const Counter = ({ signal }) => <div>
  <h2>Count: {signal()}</h2>
  <button onClick={() => signal(signal() + 1)}>+</button>
</div>

const App = () => <div>
  <Counter signal={s1} />
  <Counter signal={s2} />
  <Counter signal={s2} />
</div>

*s.root(() =>
*  s.on([s1, s2], () => render(<App />, 
*    document.getElementById('root')))
*)
```

---


# Extract presentation component

```jsx
const CounterPresentation = ({ value, onIncrement }) => <div>
  <h2>Count: {value}</h2>
  <button onClick={onIncrement}>+</button>
</div>

const Counter = ({ signal }) =>
  <CounterPresentation
*    value={signal()}
*    onIncrement={() => signal(signal() + 1)}
  />
```


---


## Optimize rendering by only rerendering when necessary
### … think `connect` in _react-redux_ …

```javascript
const connect = mapOwnProps => re.compose(
  re.lifecycle({
*    componentDidMount() {
*      s(() => this.setState(mapOwnProps(this.props)))
    }
  }),
  re.mapProps(mapOwnProps)
)

const Counter = connect(own => ({
  value: own.signal(),
  onIncrement: () => z.over(r.inc, own.signal)
}))(CounterPresentation)
```


---


# Add saving and restoring from `localStorage`
## without touching existing code

```javascript
if (localStorage.counter) 
  s1(localStorage.counter)

s.root(() => 
  s(() => (localStorage.counter = s1())))
```


---


# Bottomline

We lose

* dev tools
* middleware
    * Although, how much of it do we still need?

But we gain

* no cruft
* degrees of freedom for simplicity/optimized performance
* modularity
* keep our familiar tools, no need to learn entirely new API
    * S.js API is tiny

---


background-image: url(/assets/hugh-stevenson-596597-unsplash.jpg)
background-size: cover


---
# Photo credits

* [Hugh Stevenson](https://unsplash.com/@hughstevensonn)
* [Markus Petritz](https://unsplash.com/@petritzdesigns)
