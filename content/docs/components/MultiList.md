---
id: multilist
title: "MultiList"
layout: docs
sectionid: docs
permalink: basic-components/multilist.html
prev: basic-components/singlelist.html
prevTitle: "SingleList"
next: basic-components/singlerange.html
nextTitle: "SingleRange"
---

![Image to be displayed](https://i.imgur.com/KuSUJyX.png)

`MultiList` creates a multiple selection based list UI component that is connected to a database field. It is similar to a [SingleList](/basic-components/singlelist.html) except it can support multiple item selections.

Example uses:

* select category items from a list of categories in an e-commerce website.
* selecting airlines to fly by in a flight booking experience.

## Usage

### Basic Usage

```js
<MultiList
  componentId="CitySensor"
  dataField="group_city.raw"
  title="Cities"
/>
```

### Usage With All Props

```js
<MultiList
  componentId="CitySensor"
  dataField="group_city.raw"
  title="Cities"
  size={100}
  sortBy="asc"
  defaultSelected={["San Francisco"]}
  queryFormat="or"
  selectAllLabel="All Cities"
  showCheckbox={true}
  showCount={true}
  showSearch={true}
  placeholder="Search City"
  initialLoader="Loading cities list.."
  react={{
    and: ["CategoryFilter", "SearchFilter"]
  }}
  showFilter={true}
  filterLabel="City"
  URLParams={false}
/>
```

## Props

- **componentId** `String`  
    unique identifier of the component, can be referenced in other components' `react` prop.
- **dataField** `String`  
    data field to be connected to the component's UI view.
- **title** `String or HTML` [optional]  
    title of the component to be shown in the UI. Defaults to no title being shown.
- **size** `Number` [optional]  
    number of list items to be displayed. Defaults to showing a `100` items. Max value for this prop can be `1000`.
-  **sortBy** `String` [optional]  
    sort the list items by one of `count`, `asc`, or `desc`. Defaults to `count`, which sorts the list by the frequency of count value, most first.
- **defaultSelected** `Array` [optional]  
    pre-select one or more list items. Accepts an `Array` object containing the items that should be selected. It is important for the passed value(s) to exactly match the field value(s) as stored in the DB.
- **queryFormat** `String` [optional]  
    queries the selected items from the list in one of two modes: `or`, `and`.
    * Defaults to `or` which queries for results where any of the selected list items are present.
    * In `and` mode, the applied query filters results where all of the selected items are present.
- **selectAllLabel** `String` [optional]  
    add an extra `Select all` item to the list with the provided label string.
- **showCheckbox** `Boolean` [optional]  
    show checkbox icon for each list item. Defaults to `true`.
- **showCount** `Boolean` [optional]  
    show a count of the number of occurences besides each list item. Defaults to `true`.
- **showSearch** `Boolean` [optional]  
    whether to show a searchbox to filter the list items locally. Defaults to true.
- **placeholder** `String` [optional]  
    placeholder to be displayed in the searchbox, only applicable when the `showSearch` prop is set to `true`. When applicable, the default placeholder value is set to "Search".
- **initialLoader** `String or HTML` [optional]  
    display text while the data is being fetched, accepts `String` or `HTML` markup.
- **showFilter** `Boolean` [optional]  
    show as filter when a value is selected in a global selected filters view. Defaults to `true`.
- **filterLabel** `String` [optional]  
    An optional label to display for the component in the global selected filters view. This is only applicable if `showFilter` is enabled. Default value used here is `componentId`.
- **URLParams** `Boolean` [optional]  
    enable creating a URL query string parameter based on the selected value of the list. This is useful for sharing URLs with the component state. Defaults to `false`.

## Syntax

<br>

<iframe height='500' scrolling='no' title='MultiList docs example' src='//codepen.io/sids-aquarius/embed/GveKLV/?height=500&theme-id=light&default-tab=js&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/sids-aquarius/pen/GveKLV/'>MultiList docs example</a> by Siddharth Kothari (<a href='https://codepen.io/sids-aquarius'>@sids-aquarius</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

## Styles

All reactivebase components are `rbc` namespaced.

![Annotated image](https://i.imgur.com/qJZgfvI.png)

## Extending

`MultiList` component can be extended to

1. customize the look and feel with `className`, `style`,
2. update the underlying DB query with `customQuery`,
3. connect with external interfaces using `beforeValueChange`, `onValueChange` and `onQueryChange`,
4. specify how options should be filtered or updated using `react` prop.

```js
<MultiList
  ...
  className="custom-class"
  style={{"paddingBottom": "10px"}}
  customQuery={
    function(value) {
      return {
        match: {
          data_field: "this is a test"
        }
      }
    }
  }
  beforeValueChange={
    function(value) {
      // called before the value is set
      // returns a promise
      return new Promise((resolve, reject) => {
        // update state or component props
        resolve()
        // or reject()
      })
    }
  }
  onValueChange={
    function(value) {
      console.log("current value: ", value)
      // set the state
      // use the value with other js code
    }
  }
  onQueryChange={
    function(prevQuery, nextQuery) {
      // use the query with other js code
      console.log('prevQuery', prevQuery);
      console.log('nextQuery', nextQuery);
    }
  }
  // specify how and which options are filtered using `react` prop.
  react={
    "and": ["pricingFilter", "dateFilter"],
    "or": ["searchFilter"]
  }
/>
```

- **className** `String`  
    CSS class to be injected on the component container.
- **style** `Object`  
    CSS styles to be applied to the **MultiList** component.
- **customQuery** `Function`  
    takes **value** as a parameter and **returns** the data query to be applied to the component, as defined in Elasticsearch v2.4 Query DSL.
    `Note:` customQuery is called on value changes in the **MultiList** component as long as the component is a part of `react` dependency of at least one other component.
    `Note:` When extending with customQuery, the `queryFormat` prop has no affect.
- **beforeValueChange** `Function`  
    is a callback function which accepts component's future **value** as a parameter and **returns** a promise. It is called every time before a component's value changes. The promise, if and when resolved, triggers the execution of the component's query and if rejected, kills the query execution. This method can act as a gatekeeper for query execution, since it only executes the query after the provided promise has been resolved.
- **onValueChange** `Function`  
    is a callback function which accepts component's current **value** as a parameter. It is called everytime the component's value changes. This prop is handy in cases where you want to generate a side-effect on value selection. For example: You want to show a pop-up modal with the valid discount coupon code when list item(s) is/are selected in a "Discounted Price" MultiList.
- **onQueryChange** `Function`  
    is a callback function which accepts component's **prevQuery** and **nextQuery** as parameters. It is called everytime the component's query changes. This prop is handy in cases where you want to generate a side-effect whenever the component's query would change.
- **react** `Object`  
    specify dependent components to reactively update **MultiList's** options.
    - **key** `String`  
        one of `and`, `or`, `not` defines the combining clause.
        - **and** clause implies that the results will be filtered by matches from **all** of the associated component states.
        - **or** clause implies that the results will be filtered by matches from **at least one** of the associated component states.
        - **not** clause implies that the results will be filtered by an **inverse** match of the associated component states.
    - **value** `String or Array or Object`  
        - `String` is used for specifying a single component by its `componentId`.
        - `Array` is used for specifying multiple components by their `componentId`.
        - `Object` is used for nesting other key clauses.

## Examples

<br>

<iframe height='500' scrolling='no' title='MultiList docs example' src='//codepen.io/sids-aquarius/embed/GveKLV/?height=500&theme-id=light&default-tab=result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/sids-aquarius/pen/GveKLV/'>MultiList docs example</a> by Siddharth Kothari (<a href='https://codepen.io/sids-aquarius'>@sids-aquarius</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

1. [List with all the default props](https://opensource.appbase.io/playground/?knob-title=&selectedKind=map%2FMultiList&selectedStory=Basic&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)

2. [List without search](https://opensource.appbase.io/playground/?knob-title=&knob-showCount=false&knob-showSearch=false&selectedKind=map%2FMultiList&selectedStory=Without%20Search&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)

3. [List with pre-selected items](https://opensource.appbase.io/playground/?knob-title=&knob-showCount=false&knob-showSearch=false&knob-defaultSelected%5B0%5D=London&knob-defaultSelected%5B1%5D=Sydney&selectedKind=map%2FMultiList&selectedStory=Default%20Selected&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)

4. [List with a custom sort order](https://opensource.appbase.io/playground/?knob-title=&knob-showCount=false&knob-showSearch=false&knob-defaultSelected%5B0%5D=London&knob-defaultSelected%5B1%5D=Sydney&knob-sortBy=count&selectedKind=map%2FMultiList&selectedStory=Custom%20Sort&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)

5. [List with a 'Select All' item](https://opensource.appbase.io/playground/?knob-title=&knob-showCount=false&knob-showSearch=false&knob-defaultSelected%5B0%5D=London&knob-defaultSelected%5B1%5D=Sydney&knob-sortBy=count&knob-selectAllLabel=All%20cities&selectedKind=map%2FMultiList&selectedStory=With%20Select%20All&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)

6. [Playground (with all knob actions)](https://opensource.appbase.io/playground/?knob-title=MultiList%3A%20City%20Filter&knob-defaultSelected%5B0%5D=London&knob-defaultSelected%5B1%5D=Sydney&knob-selectAllLabel=All%20cities&knob-queryFormat=or&knob-sortBy=count&knob-showCheckbox=true&knob-size=10&knob-showCount=true&knob-placeholder=Search%20City&knob-showSearch=true&selectedKind=map%2FMultiList&selectedStory=Playground&full=0&down=1&left=1&panelRight=0&downPanel=storybooks%2Fstorybook-addon-knobs)
