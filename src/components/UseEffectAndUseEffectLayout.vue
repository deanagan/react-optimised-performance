<template>
  <section>
    <section class="useeffect">
      <h3 style="text-transform: none">useEffect and useEffectLayout</h3>
      <ul style="font-size: 0.8em">
        <li class="fragment" v-for="(e, i) in whatUseEffect" :key="i">{{ e }}</li>
      </ul>
    </section>

     <section class="demo">
      <p style="text-transform: none">
        Demo: useMemoObjCompare - a custom useMemo for useEffect<a :href="sandboxlink">[code]</a><a :href="gitlink">[github]</a>
      </p>
      <pre class="prettyprint language-javascript srcCode">
        <template v-for="(d, i) in useMemoObjCompare" :key=i>
          <code :class="{fragment: i !== 0 }" data-trim :data-line-numbers=d.lineNumbers>{{ d.code }}</code>
          <span :class="{fragment: true, 'fade-in-then-out': true, comment: true }" >- {{d.description}}</span>
        </template>
      </pre>
    </section>
  </section>
</template>

<script>
import { dedentStrUsing1stLineIndent } from "../mixins/codetrim";

export default {
  mixins: [dedentStrUsing1stLineIndent],
  data() {
    return {
      whatUseEffect: [
        "The Effect Hook lets you perform side effects in function components",
        "Is equivalent to componentDidMount, componentDidUpdate, and componentWillUnmount combined from class based components",
        "useEffect hooks are executed after render, when DOM updates have been done",
        "useEffect is designed to handle one concern, so it is best to use multiple useEffect to separate concerns",
        "useEffect runs both after the first render (regardless of dependency) and after every update",
        "Because useEffect runs when its dependencies change, care must be take to consider Javascript's referential equality rules to avoid infinite loops",
      ],
      sandboxlink:
        "",
      useMemoObjCompare: [
        {
          lineNumbers: "",
          description: "useEffect can have a memoized object as a dependency. In this example, we don't want ColorComponent's useEffect to run if count2 changes.",
          sandboxlink: "",
          code: `
          const colorChoices = [
            {uniqueId: 1, name: 'red'},
            {uniqueId: 2, name: 'green'},
            {uniqueId: 3, name: 'blue'},
          ]

          export default function App() {
            const [count, setCount] = useState(0);
            const [count2, setCount2] = useState(0);
            const [color, setColor] = useState('blue');
            const colorAndCount = {color, count};
            const changeCurrentColor = useCallback((c) => {
              setColor(c)
            }, [color]);

            return (
              <div className="App">
                <header className="App-header">
                  <p>Hello Vite + React!</p>
                  <ColorComponent colorAndCount={colorAndCount}/>
                  <button type="button" onClick={() => setCount((count) => count + 1)}>
                    count is: {count}
                  </button>
                  <button type="button" onClick={() => setCount2((count) => count + 1)}>
                    count2 is: {count2}
                  </button>
                  <ColoredHeader color={color}>Current Color: {color}</ColoredHeader>
                  <ColorDropDown colorChoices={colorChoices} currentColor={color} setToColor={changeCurrentColor}/>
                </header>
              </div>
            )
          }

          const colorToCountry = [
            {color: 'blue', country: 'America'},
            {color: 'red', country: 'China'},
            {color: 'green', country: 'Australia'}
          ]

          function ColorComponent({colorAndCount}) {
            const [country, setCountry] = useState('')

            useEffect(() => {
              const result = colorToCountry.find(d => d.color === colorAndCount.color)
              setCountry(result.country)
            }, [colorAndCount])

            return (
              <div>
                Country: {country}
              </div>
            )
          }
          `,
        },
        {
          lineNumbers: "36-46,55,60",
          description: "useMemoObjCompare can memoize an object within ColorComponent so referential equality works properly as dependency to useEffect.",
          sandboxlink: "",
          code: `
          const colorChoices = [
            {uniqueId: 1, name: 'red'},
            {uniqueId: 2, name: 'green'},
            {uniqueId: 3, name: 'blue'},
          ]

          function App() {
            const [count, setCount] = useState(0)
            const [count2, setCount2] = useState(0)
            const [color, setColor] = useState('blue')
            const colorAndCount = {color, count}
            const changeCurrentColor = useCallback((c) => {
              setColor(c)
            }, [color])

            return (
              <div className="App">
                <header className="App-header">
                  <p>Hello Vite + React!</p>
                  <ColorComponent colorAndCount={colorAndCount}/>
                  <button type="button" onClick={() => setCount((count) => count + 1)}>
                    count is: {count}
                  </button>
                  <button type="button" onClick={() => setCount2((count) => count + 1)}>
                    count2 is: {count2}
                  </button>
                  <ColoredHeader color={color}>Current Color: {color}</ColoredHeader>
                  <ColorDropDown colorChoices={colorChoices} currentColor={color} setToColor={changeCurrentColor}/>
                </header>
              </div>
            )
          }

          export default App

          const useMemoObjCompare = (value) => {
            const prevRef = useRef()
            const previous = prevRef.current
            const isObjEqual = isEqual(previous, value)
            useEffect(() => {
              if (!isObjEqual) {
                prevRef.current = value;
              }
            })
            return isObjEqual ? previous : value
          };

          const colorToCountry = [
            {color: 'blue', country: 'America'},
            {color: 'red', country: 'China'},
            {color: 'green', country: 'Australia'}
          ]
          function ColorComponent({colorAndCount}) {
            const [country, setCountry] = useState('')
            const colorAndCountMemoized = useMemoObjCompare(colorAndCount)

            useEffect(() => {
              const result = colorToCountry.find(d => d.color === colorAndCount.color)
              setCountry(result.country)
            }, [colorAndCountMemoized])
            return (
              <div>
                Country: {country}
              </div>

            )
          }
          `,
        },
      ],
    };
  },
};
</script>

<style>
.prettyprint.language-javascript.srcCode {
  margin-top: 0;
  margin-bottom: 0;
  font-size: 0.25em;
  /* width: 100vw; */
}

.comment {
  height: 10px;
  position: absolute;
  top: 450px;
}

.codeheight {
  height: 75%;
}

.container {
  display: flex;
  /* flex-direction: column; */
}
</style>
