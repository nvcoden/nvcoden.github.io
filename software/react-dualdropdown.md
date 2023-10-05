---
layout: default
---

## How to update a second dropdown box, w.r.t values of the first dropdown box in React?

✍🏻Written/Revised : October 2023

*Disclaimer: I am fairly very noob to React. So there might be a better way to do this, and if so, please do share it with me in the ['issues' in my github repo](https://github.com/nvcoden/nvcoden.github.io/issues).*

Consider a scenario in your screen where you have 2 dropdowns. 
- One is a showing a list of media type. Eg :- Tv Shows, Movies, Anime, Animated... <br>
- Other is showing a list of specific items in those types.<br>

Now you want the user to have a better navigation in which, **if the user selects an option from the first dropdown, it curates the things in the second dropdown accordingly**.<br>
For better navigation, lets assume I have a [nested `json` object](https://www.ibm.com/docs/no/db2/11.5?topic=documents-json-nested-objects) with the exact mappings as I need.<br>

```jsx
const itemMapping = {
    "All":[
        "Severance", "loki", "Ahsoka", "The Expanse","Avengers", "Justice League", "Star Wars","Bleach", "Naruto", "One Piece"
    ],
    "Tv Shows":[
        "Severance", "loki", "Ahsoka", "The Expanse"
    ],
    "Movies": [
        "Avengers", "Justice League", "Star Wars"
    ],
    "Anime":[
        "Bleach", "Naruto", "One Piece"
    ]
}
```

Lets get the first list sorted
```jsx
const categories = Object.keys(itemMapping);
```

To get this in the UI, we can add (inside the react-function of your choosing)

```jsx
const showCategories = (
    <select onChange={handleChange}>
        {categories.map((cat)=>(
            <option value={cat}>{cat}</option>
        ))}
    </select>
)
```
we can change `handleChange` and replace it with the action of choosing the array of the selected category and assigning it to an array.<br> 
Here is where things get tricky. ***This will not work, if the second array - the dynamic one - is a static*** `const` ***or*** `let` ***or*** `var` ***It should be a*** `state`. i.e
```jsx
const wrongArray = []; // initializing an empty array
wrongArray = itemMapping['All'] // populating it with the default value of 'all the items'
//👆 This is the wrong way as the react screen will not re-render 
// if there is a change to a static array.
// It needs to detect a change in state to do that. Fortunately, we have the `useState()` react hook
​
​
//👇 This way, we can initialize an array as a react-state. (and also fill it up
// with all the items )
const [catItems, setCatItems] = useState(itemMapping['All']);
```
***Note : [Read more about array as react states here](https://react.dev/learn/updating-arrays-in-state)***

Now, we can use the `handleChange` to be such that, it updates the state of the `catItems` array with the corresponding items
```jsx
const showCategories = (
    <select onChange={(cat) => setCatItems(itemMapping[cat.target.value])}>
        {categories.map((cat)=>(
            <option value={cat}>{cat}</option>
        ))}
    </select>
)
```
This updated array will be used for the dropdown elements of the second dropdown
```jsx
  const showCatItems = (
    <select
      onChange={
        // getting a console output so that we know this is working as expected
        (item) => console.log(item.target.value)
      }
    >
      {catItems.map((items) => (
        <option value={items}>{items}</option>
      ))}
    </select>
  );
```
This should do it.

Now your second dropdown dox elements will be dynamically updated with the selections on the first box.<br>
Thanks for reading👋

* * *

*If you still find the above explanation a bit unsatisfactory, the full code is given below, run that with debug points to understand how it works.*
```jsx
import { useState } from "react";

// A generic react function (now read #56 and come back)
function DependantDropDowns() {
  
  // All the items mapped in a json array for easy usage
  const itemMapping = {
      "All":[
          "Severance", "loki", "Ahsoka", "The Expanse","Avengers", "Justice League", "Star Wars","Bleach", "Naruto", "One Piece"
      ],
      "Tv Shows":[
          "Severance", "loki", "Ahsoka", "The Expanse"
      ],
      "Movies": [
          "Avengers", "Justice League", "Star Wars"
      ],
      "Anime":[
          "Bleach", "Naruto", "One Piece"
      ]
  }
  
  // list of categories
  const categories = Object.keys(itemMapping);
  // default catItems array containing all the items
  const [catItems, setCatItems] = useState(itemMapping["All"]);

  const showCategories = (
    <select onChange={(cat) => setCatItems(itemMapping[cat.target.value])}>
      {categories.map((cat) => (
        <option>{cat}</option>
      ))}
    </select>
  );

  const showCatItems = (
    <select
      onChange={
        // getting a console output so that we know this is working as expected
        (item) => console.log(item.target.value)
      }
    >
      {catItems.map((items) => (
        <option value={items}>{items}</option>
      ))}
    </select>
  );

  return (
    <>
      {showCategories}
      {showCatItems}
    </>
  );
}

export default function App() {
  return <DependantDropDowns />;
}
```