# Veaury

<div align=center>
  <img src="https://raw.githubusercontent.com/devilwjp/VueReact/master/vuereact-combined.png"/>
</div>  

<div align=center>
  <p>
  <h1>Use React in Vue3 and Vue3 in React, And as perfect as possible!</h1>
  <p>
</div>  

## Do you want to preconfigure your project in advance?  
In theory, you don't need to do additional configuration in a React project to support Vue, nor do you need to do additional configuration in a Vue project to support React.  
If the React or Vue component you want to convert comes from a npm package, or has already been built, you can use `applyReactInVue` or `applyVueInReact` directly.  

If you need to develop both Vue and React in a project, instead of just using an existing npm component, then you should do some configuration, usually configuring `webpack.config.js` and `babel.config.js`.  

The `dev-project-react` and `dev-project-vue3` in the project are the basic projects of the development environment of `veaury`, and they are also the two initial projects created by `create-react-app` and `@vue/cli` respectively. You can refer to How the two projects are configured to support the other framework.

## Use cases  
- 👨‍👩‍👧 Using both Vue and React in one app
- 🏃 Migrating from React to Vue or from Vue to React
- 📲 Using third-party Vue and React Components, such as `antd`, `element-ui`, `vuetify`

## Installation
```sh
# Install with yarn:
$ yarn add veaury
# or with npm:
$ npm i veaury -S
```
## Usage
### Vue in React - Basic usage
```jsx
import { applyVueInReact } from 'veaury'
// This is a Vue component
import BasicVueComponent from './Basic.vue'
import { useState } from 'react'
// Use HOC 'applyVueInReact'
const Basic = applyVueInReact(BasicVueComponent)
export default function () {
  const [foo] = useState('Hello!')
  return <Basic foo={foo}>
    <div>
      the default slot
    </div>
  </Basic>
}
```  
### React in Vue - Basic usage
```vue
<template>
  <Basic :foo="foo">
    <div>
      the children
    </div>
  </Basic>
</template>
<script>
import { applyReactInVue } from 'veaury'
// This is a React component
import BasicReactComponent from './react_app/Basic.jsx'
import { ref } from 'vue'
export default {
  components: {
    // Use HOC 'applyReactInVue'
    Basic: applyReactInVue(BasicReactComponent)
  },
  setup() {
    return {
      foo: ref('Hello!')
    }
  }
}
</script>
```
### Vue in React - Usage of events
```jsx
import {applyVueInReact} from 'veaury'
import BasicVue from './Basic.vue'
import {useState} from 'react'

const Basic = applyVueInReact(BasicVue)
export default function () {
  function onClickForVue() {
    console.log('clicked!')
  }
  
  return <div>
    {/*Trigger with $emit('click') in Vue component*/}
    <Basic onClick={onClickForVue} />
  </div>
}
```
### React in Vue - Usage of events
```vue
<template>
  <!-- Trigger with 'props.onClick()' in React component -->
  <ReactButton @click="onClickForReact" />
</template>

<script>
import { ref } from 'vue'
import { applyReactInVue } from 'veaury'
// This is a React Component
import ReactButton from "./react_app/Button.jsx"

export default {
  components: {
    ReactButton: applyReactInVue(ReactButton)
  },
  setup() {
    function onClickForReact() {
      console.log('clicked!')
    }
    return {
      onClickForReact,
    }
  }
}
</script>
```
### Vue in React - Usage of slots
The usage of 'slots' is similar to the usage of 'v-slots' of Vue's jsx.  
```jsx
import {applyVueInReact} from 'veaury'
import BasicVue from './Basic.vue'

const Basic = applyVueInReact(BasicVue)
export default function () {
  return <div>
    {/*just send children*/}
    <Basic>
      {/* Render with '<slot/>' in Vue Component */}
      <div>this is children</div>
    </Basic>
    {/*send v-slots*/}
    <Basic v-slots={{
      // Render with '<slot name="slot1" />' in Vue Component
      slot1: <div>this is slot1(namedSlot)</div>,
      // Render with '<slot name="slot2" value="xxxxxx"/>' in Vue Component
      slot2: ({value}) => <div>this is slot2(scopedSlot), and receive value: {value}</div>,
      // Render with '<slot/>' in Vue Component
      default: <div>this is children</div>
    }}/>
    {/*another usage*/}
    <Basic>
      {{
        slot1: <div>this is slot1(namedSlot)</div>,
        slot2: ({value}) => <div>this is slot2(scopedSlot), and receive value: {value}</div>,
        default: <div>this is children</div>
      }}
    </Basic>
  </div>
}
```
### React in Vue - Usage of render props and React node
Named slots & scoped slots of Vue = React render props.  
Default slots $ children of Vue = React props.children.  
Name a named slot of Vue starting with 'node:' = React Node.
```vue
<template>
  <Basic>
    <!--  Render with 'props.slot1()' in React component  -->
    <template v-slot:slot1>
      <div>
        this is slot1 (render props)
      </div>
    </template>
    <!--  Render with 'props.slot2("xxxxx")' in React component  -->
    <template v-slot:slot2="bar">
      <div>
        this is slot2 (render props)<br/>
        this content is passed from React: {{bar}}
      </div>
    </template>
    <!--  Render with 'props.slot3' in React component  -->
    <template v-slot:node:slot3>
      <div>
        this is slot3 (react node)
      </div>
    </template>
    <!--  Render with 'props.children' in React component  -->
    <div>
      this is children (react node)
    </div>
  </Basic>
</template>

<script>
import { applyReactInVue } from 'veaury'
// This is a React Component
import ReactBasic from "./react_app/Slots.jsx"

export default {
  components: {
    Basic: applyReactInVue(ReactBasic)
  }
}
</script>
```
### Context
Veaury will judge that if there is a wrapper layer of the same framework in the outer layer, Veaury will use React's `Portal` and Vue's `Teleport` instead of creating a new application instance every time.   
It's a really awesome! Veaury can well pass the root node context to the child nodes, regardless of whether the node is wrapped or not.  
This means that a React component is used in a Vue component, and then another Vue subcomponent is used in this React component. This Vue subcomponent can get the context of the outer Vue component.  

#### Vue in React - usage of Provider/useContext
```jsx
export function 
```