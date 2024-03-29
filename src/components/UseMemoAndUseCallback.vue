<template>
  <section>
    <section class="usememo">
      <h3 style="text-transform: none">useMemo</h3>
      <ul style="font-size: 0.8em">
        <li class="fragment" v-for="(e, i) in whatUseMemo" :key="i">{{ e }}</li>
      </ul>
    </section>
    <section class="usecallback">
      <h3 style="text-transform: none">useCallback</h3>
      <ul style="font-size: 0.8em">
        <li class="fragment" v-for="(e, i) in whatUseCallback" :key="i">
          {{ e }}
        </li>
      </ul>
    </section>
    <section class="demo">
      <p style="text-transform: none">
        Demo: useCallback and useMemo<a :href="sandboxlink">[code]</a><a :href="gitlink">[github]</a>
      </p>
      <pre class="prettyprint language-javascript srcCode">
        <template v-for="(d, i) in demo" :key=i>
          <code :class="{fragment: i !== 0 }" data-trim :data-line-numbers=d.lineNumbers>{{ d.code }}</code>
          <span :class="{fragment: true, 'fade-in-then-out': true, comment: true }" >- {{d.description}}</span>
        </template>
      </pre>
    </section>
    <section class="demo">
      <p style="text-transform: none">
        Demo: Fixing infinite loop<a :href="infinidemoLink">[code]</a>
      </p>
      <pre class="prettyprint language-javascript srcCode">
        <template v-for="(d, i) in infinidemo" :key=i>
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
      codeSlideNumber: 0,
      whatUseMemo: [
        "useMemo returns a memoized value, caching a value so it isn't recalculated if dependencies don't change",
        "Addresses problems with expensive computations",
        "Avoid using useMemo on cheap computations to avoid excessive memory use",
        "Memoized computations are not guaranteed to cache values between renders",
        "Overuse can make an app slower since allocations need to happen, even for empty array dependencies",
        "Performance loss with useMemo happens on initial render, but improves subsequent renders",
      ],
      whatUseCallback: [
        "Returns a memoized version of the callback that only changes if the dependencies changed",
        "Useful when passing callbacks to optimized child components that rely on reference equality \
            to prevent unnecessary renders (or infinite loops)",
        "useCallback(fn, deps) is equivalent to useMemo(() => fn, deps)",
      ],
      sandboxlink:
        "https://codesandbox.io/s/usememo-and-usecallback-qvehl?file=/src/App.js",
      gitlink:
        "https://github.com/deanagan/react-snippets/tree/main/usememo-color",
      demo: [
        {
          lineNumbers: "",
          description:
            "ColorAppV1 is a component that has number increment, and color changing buttons and drop down form. Observe TopColors always re-rendering anytime the app's local state changes. ",
          sandboxlink: "",
          code: `
          export default function ColorAppV1() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color);

            const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

            const colors = [
              { name: "red", uniqueId: 1 },
              { name: "green", uniqueId: 2 },
              { name: "blue", uniqueId: 3 },
              { name: "orange", uniqueId: 4 },
              { name: "yellow", uniqueId: 5 },
              { name: "violet", uniqueId: 6 },
              { name: "pink", uniqueId: 7 },
              { name: "gray", uniqueId: 8 },
              { name: "brown", uniqueId: 9 },
              { name: "black", uniqueId: 10 },
              { name: "white", uniqueId: 11 },
            ];

            const colorChoices = colors.filter((c) => c.name !== currentColor);
            const favoriteColors = ['red', 'green', 'blue'];

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
        {
          lineNumbers: "34-37,40-44, 46",
          description:
            "Changing the color or incrementing the count will cause the top colors table to re-render which we don't want. This happens because the color app component changes\
            state, and re-renders, which re-creates favoriteColors. Since favoriteColors is a new object, thus top colors re-renders too.",
          sandboxlink: "",
          code: `
          export default function ColorAppV1() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color);

            const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

            const colors = [
              { name: "red", uniqueId: 1 },
              { name: "green", uniqueId: 2 },
              { name: "blue", uniqueId: 3 },
              { name: "orange", uniqueId: 4 },
              { name: "yellow", uniqueId: 5 },
              { name: "violet", uniqueId: 6 },
              { name: "pink", uniqueId: 7 },
              { name: "gray", uniqueId: 8 },
              { name: "brown", uniqueId: 9 },
              { name: "black", uniqueId: 10 },
              { name: "white", uniqueId: 11 },
            ];

            const colorChoices = colors.filter((c) => c.name !== currentColor);
            const favoriteColors = ['red', 'green', 'blue'];

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/> //-->> Re-renders each time color is changed or counter is incremented
              </div>
            );
          }
          `,
        },
        {
          lineNumbers: "1-18",
          description:
            "Move constants and pure functions out of the component. This will fix the TopColors table from unnecessarily being rendered by increment or color change.",
          sandboxlink: "",
          code: `
          const colors = [
            { name: "red", uniqueId: 1 },
            { name: "green", uniqueId: 2 },
            { name: "blue", uniqueId: 3 },
            { name: "orange", uniqueId: 4 },
            { name: "yellow", uniqueId: 5 },
            { name: "violet", uniqueId: 6 },
            { name: "pink", uniqueId: 7 },
            { name: "gray", uniqueId: 8 },
            { name: "brown", uniqueId: 9 },
            { name: "black", uniqueId: 10 },
            { name: "white", uniqueId: 11 },
          ];

          const favoriteColors = ['red', 'green', 'blue'];
          const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

          export default function ColorAppV2() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color);

            const colorChoices = colors.filter((c) => c.name !== currentColor);

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
        {
          lineNumbers: "27",
          description:
            "Further improvement, we should prevent the dropdown from re-rendering if increment happens. First step is to memoize colorChoices. If the increment happens, \
            the ColorApp state changes and causes the colorChoices to be recreated as a new object, which causes the dropdown to re-render.",
          sandboxlink: "",
          code: `
          const colors = [
            { name: "red", uniqueId: 1 },
            { name: "green", uniqueId: 2 },
            { name: "blue", uniqueId: 3 },
            { name: "orange", uniqueId: 4 },
            { name: "yellow", uniqueId: 5 },
            { name: "violet", uniqueId: 6 },
            { name: "pink", uniqueId: 7 },
            { name: "gray", uniqueId: 8 },
            { name: "brown", uniqueId: 9 },
            { name: "black", uniqueId: 10 },
            { name: "white", uniqueId: 11 },
          ];

          const favoriteColors = ['red', 'green', 'blue'];
          const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

          export default function ColorAppV2() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color);

            const colorChoices = colors.filter((c) => c.name !== currentColor); // --> We need to memoize to not recalculate each time

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
        {
          lineNumbers: "27-29",
          description:
            "Apply useMemo to colorChoices to prevent drop down from re-rendering when increment is clicked. Now it only re-calculates if currentColor changes.",
          sandboxlink: "",
          code: `
          const colors = [
            { name: "red", uniqueId: 1 },
            { name: "green", uniqueId: 2 },
            { name: "blue", uniqueId: 3 },
            { name: "orange", uniqueId: 4 },
            { name: "yellow", uniqueId: 5 },
            { name: "violet", uniqueId: 6 },
            { name: "pink", uniqueId: 7 },
            { name: "gray", uniqueId: 8 },
            { name: "brown", uniqueId: 9 },
            { name: "black", uniqueId: 10 },
            { name: "white", uniqueId: 11 },
          ];

          const favoriteColors = ['red', 'green', 'blue'];
          const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

          export default function ColorAppV3() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color);

            const colorChoices = useMemo(() => {
              return colors.filter((c) => c.name !== currentColor);
            }, [currentColor]);

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
         {
          lineNumbers: "25, 27-29",
          description:
            "Even though we've memoized, notice that the dropdown still re-renders, because setToOtherColor recalculates each time.",
          sandboxlink: "",
          code: `
          const colors = [
            { name: "red", uniqueId: 1 },
            { name: "green", uniqueId: 2 },
            { name: "blue", uniqueId: 3 },
            { name: "orange", uniqueId: 4 },
            { name: "yellow", uniqueId: 5 },
            { name: "violet", uniqueId: 6 },
            { name: "pink", uniqueId: 7 },
            { name: "gray", uniqueId: 8 },
            { name: "brown", uniqueId: 9 },
            { name: "black", uniqueId: 10 },
            { name: "white", uniqueId: 11 },
          ];

          const favoriteColors = ['red', 'green', 'blue'];
          const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

          export default function ColorAppV3() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = (color) => setCurrentColor(color); //-->> this causes a re-render as it is recalculated.

            const colorChoices = useMemo(() => {
              return colors.filter((c) => c.name !== currentColor);
            }, [currentColor]);

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
        {
          lineNumbers: "25,45",
          description:
            "Apply useCallback to setToOtherColor to prevent the setColor function from re-rendering the dropdown.",
          sandboxlink: "",
          code: `
          const colors = [
            { name: "red", uniqueId: 1 },
            { name: "green", uniqueId: 2 },
            { name: "blue", uniqueId: 3 },
            { name: "orange", uniqueId: 4 },
            { name: "yellow", uniqueId: 5 },
            { name: "violet", uniqueId: 6 },
            { name: "pink", uniqueId: 7 },
            { name: "gray", uniqueId: 8 },
            { name: "brown", uniqueId: 9 },
            { name: "black", uniqueId: 10 },
            { name: "white", uniqueId: 11 },
          ];

          const favoriteColors = ['red', 'green', 'blue'];
          const getColorType = (color) => (['red', 'blue', 'green']).includes(color) ? 'PRIMARY' : 'NON PRIMARY';

          export default function ColorAppV4() {
            const [currentColor, setCurrentColor] = useState("blue");
            const [counter, setCounter] = useState(0);
            const setToGreen = () => setToOtherColor("green");
            const setToRed = () => setToOtherColor("red");
            const setToBlue = () => setToOtherColor("blue");
            const incrementCount = () => setCounter(counter + 1);
            const setToOtherColor = useCallback((color) => setCurrentColor(color), []);

            const colorChoices = useMemo(() => {
              return colors.filter((c) => c.name !== currentColor);
            }, [currentColor]);

            return (
              <div className="App">
                <ColoredHeader color={currentColor}>I change color. Counter: {counter}</ColoredHeader>
                <h2>Change the color using the buttons or the drop down!</h2>

                <Button onClick={setToRed}>Red</Button>
                <Button onClick={setToGreen}>Green</Button>
                <Button onClick={setToBlue}>Blue</Button>
                <Button onClick={incrementCount}>Increment Counter</Button>
                <div>Color Type: {getColorType(currentColor)}</div>

                <ColorDropDown
                  colorChoices={colorChoices}
                  currentColor={currentColor}
                  setToColor={setToOtherColor}
                />

                <TopColors colors={favoriteColors}/>
              </div>
            );
          }
          `,
        },
      ],
      infinidemoLink: "https://codesandbox.io/s/usememo-and-usecallback-prevent-infinite-loop-kg7zo?file=/src/ColorDropdown.js",
      infinidemo: [
        {
          lineNumbers: "",
          description:
            "ColorDropDown component above has a problem with infinite looping.",
          code: `
          import { useCallback, useEffect, useRef, useState } from "react";
          import {
            Dropbtn,
            DropDownContent,
            DropDownItem,
            DropDownLi
          } from "./StyledComponents";

          const excludedColor = ["red", "green", "blue"];
          export default function ColorDropDown({
            colorChoices,
            currentColor,
            setToColor
          }) {
            const [currentEntry, setCurrentEntry] = useState(currentColor);
            const [clickedOutside, setClickedOutside] = useState(true);
            const [excludedColorChoices, setExcludedColorChoices] = useState(
              colorChoices
            );
            const currentComponentRef = useRef(null);

            const infiniguard = useRef(0);

            const excludeColors = () => {
              setExcludedColorChoices((c) => c.filter((d) => !excludedColor.includes(d)));
            };

            const handleClickOutside = (e) => {
              const current = currentComponentRef.current;
              if (!current?.contains(e.target)) {
                setClickedOutside(true);
              }
            };

            useEffect(() => {
              console.log("render drop down");
              infiniguard.current += 1;
              if (infiniguard.current < 20) {
                console.log("exclude colors with buttons", infiniguard.current);
                excludeColors();
              }
            }, [colorChoices, currentColor, setToColor, excludeColors]);

            useEffect(() => {
              setCurrentEntry(currentColor);
            }, [currentColor]);

            const onSelectHandler = (entry) => {
              setCurrentEntry(entry.name);
              setToColor(entry.name);
            };

            useEffect(() => {
              document.addEventListener("mousedown", handleClickOutside);
              return () => document.removeEventListener("mousedown", handleClickOutside);
            }, []);

            const handleClickInside = () => setClickedOutside(false);
            return (
              <DropDownLi>
                <Dropbtn onClick={handleClickInside}>{currentEntry}</Dropbtn>
                {!clickedOutside ? (
                  <DropDownContent ref={currentComponentRef}>
                    {excludedColorChoices.map((pe) => (
                      <DropDownItem
                        key={pe.uniqueId.toString()}
                        onClick={() => {
                          onSelectHandler(pe);
                          setClickedOutside(true);
                        }}
                      >
                        {pe.name}
                      </DropDownItem>
                    ))}
                  </DropDownContent>
                ) : null}
              </DropDownLi>
            );
          }


          `
        },
        {
          lineNumbers: "24-26, 35-42",
          description:
            "The function 'excludeColors' is a dependency in useEffect below. When useEffect runs the first time, it wil change this component's state (excludedColorChoices),\
            which will then re-render the component, which re-creates excludedColors. With a new reference to the function object, useEffect runs again...",
          code: `
          import { useCallback, useEffect, useRef, useState } from "react";
          import {
            Dropbtn,
            DropDownContent,
            DropDownItem,
            DropDownLi
          } from "./StyledComponents";

          const excludedColor = ["red", "green", "blue"];
          export default function ColorDropDown({
            colorChoices,
            currentColor,
            setToColor
          }) {
            const [currentEntry, setCurrentEntry] = useState(currentColor);
            const [clickedOutside, setClickedOutside] = useState(true);
            const [excludedColorChoices, setExcludedColorChoices] = useState(
              colorChoices
            );
            const currentComponentRef = useRef(null);

            const infiniguard = useRef(0);

            const excludeColors = () => {
              setExcludedColorChoices((c) => c.filter((d) => !excludedColor.includes(d)));
            };

            const handleClickOutside = (e) => {
              const current = currentComponentRef.current;
              if (!current?.contains(e.target)) {
                setClickedOutside(true);
              }
            };

            useEffect(() => {
              console.log("render drop down");
              infiniguard.current += 1;
              if (infiniguard.current < 20) {
                console.log("exclude colors with buttons", infiniguard.current);
                excludeColors();
              }
            }, [colorChoices, currentColor, setToColor, excludeColors]);

            useEffect(() => {
              setCurrentEntry(currentColor);
            }, [currentColor]);

            const onSelectHandler = (entry) => {
              setCurrentEntry(entry.name);
              setToColor(entry.name);
            };

            useEffect(() => {
              document.addEventListener("mousedown", handleClickOutside);
              return () => document.removeEventListener("mousedown", handleClickOutside);
            }, []);

            const handleClickInside = () => setClickedOutside(false);
            return (
              <DropDownLi>
                <Dropbtn onClick={handleClickInside}>{currentEntry}</Dropbtn>
                {!clickedOutside ? (
                  <DropDownContent ref={currentComponentRef}>
                    {excludedColorChoices.map((pe) => (
                      <DropDownItem
                        key={pe.uniqueId.toString()}
                        onClick={() => {
                          onSelectHandler(pe);
                          setClickedOutside(true);
                        }}
                      >
                        {pe.name}
                      </DropDownItem>
                    ))}
                  </DropDownContent>
                ) : null}
              </DropDownLi>
            );
          }
          `
        },
        {
          lineNumbers: "24-26",
          description:
            "This can be fixed by memoizing the the excludeColor function so it doesn't change if it doesn't have to.",
          code: `
          import { useCallback, useEffect, useRef, useState } from "react";
          import {
            Dropbtn,
            DropDownContent,
            DropDownItem,
            DropDownLi
          } from "./StyledComponents";

          const excludedColor = ["red", "green", "blue"];
          export default function ColorDropDown({
            colorChoices,
            currentColor,
            setToColor
          }) {
            const [currentEntry, setCurrentEntry] = useState(currentColor);
            const [clickedOutside, setClickedOutside] = useState(true);
            const [excludedColorChoices, setExcludedColorChoices] = useState(
              colorChoices
            );
            const currentComponentRef = useRef(null);

            const infiniguard = useRef(0);

            const excludeColors = useCallback(() => {
              setExcludedColorChoices((c) => c.filter((d) => !excludedColor.includes(d)));
            }, []);

            const handleClickOutside = (e) => {
              const current = currentComponentRef.current;
              if (!current?.contains(e.target)) {
                setClickedOutside(true);
              }
            };

            useEffect(() => {
              console.log("render drop down");
              infiniguard.current += 1;
              if (infiniguard.current < 20) {
                console.log("exclude colors with buttons", infiniguard.current);
                excludeColors();
              }
            }, [colorChoices, currentColor, setToColor, excludeColors]);

            useEffect(() => {
              setCurrentEntry(currentColor);
            }, [currentColor]);

            const onSelectHandler = (entry) => {
              setCurrentEntry(entry.name);
              setToColor(entry.name);
            };

            useEffect(() => {
              document.addEventListener("mousedown", handleClickOutside);
              return () => document.removeEventListener("mousedown", handleClickOutside);
            }, []);

            const handleClickInside = () => setClickedOutside(false);
            return (
              <DropDownLi>
                <Dropbtn onClick={handleClickInside}>{currentEntry}</Dropbtn>
                {!clickedOutside ? (
                  <DropDownContent ref={currentComponentRef}>
                    {excludedColorChoices.map((pe) => (
                      <DropDownItem
                        key={pe.uniqueId.toString()}
                        onClick={() => {
                          onSelectHandler(pe);
                          setClickedOutside(true);
                        }}
                      >
                        {pe.name}
                      </DropDownItem>
                    ))}
                  </DropDownContent>
                ) : null}
              </DropDownLi>
            );
          }
          `
        }
      ]
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
