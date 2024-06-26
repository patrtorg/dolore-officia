# @patrtorg/dolore-officia
Fela toolkit for Vue designed for flexibility yet team-oriented. [website wip.](https://houd1ni.github.io/@patrtorg/dolore-officia/)

[![Build Status](https://circleci.com/gh/houd1ni/@patrtorg/dolore-officia/tree/master.svg?style=shield)](https://circleci.com/gh/houd1ni/@patrtorg/dolore-officia/tree/master) [![codecov](https://codecov.io/gh/houd1ni/@patrtorg/dolore-officia/branch/master/graph/badge.svg)](https://codecov.io/gh/houd1ni/@patrtorg/dolore-officia) [![bundlephobia](https://badgen.net/bundlephobia/minzip/@patrtorg/dolore-officia)](https://bundlephobia.com/result?p=@patrtorg/dolore-officia)  [![npm](https://badgen.net/npm/v/@patrtorg/dolore-officia)](https://www.npmjs.com/package/@patrtorg/dolore-officia) [![Deps](https://david-dm.org/houd1ni/@patrtorg/dolore-officia.svg)](https://david-dm.org/houd1ni/@patrtorg/dolore-officia) [![DevDeps](https://david-dm.org/houd1ni/@patrtorg/dolore-officia/dev-status.svg)](https://david-dm.org/houd1ni/@patrtorg/dolore-officia)
(tree-shaking friendly!)

**[Fela](https://github.com/rofrischmann/fela) does the great job, but it has no idea how to cook it with Vue.
This is what I've created after combining vue's :style and :class attributes to make apps dynamically configured and easiest to write and maintain.**

Included as deps:
- [fela](https://github.com/rofrischmann/fela)
- [fela-dom](https://github.com/rofrischmann/fela/tree/master/packages/fela-dom)
- [fela-plugin-embedded](https://github.com/rofrischmann/fela/tree/master/packages/fela-plugin-embedded)
- [fela-plugin-prefixer](https://github.com/rofrischmann/fela/tree/master/packages/fela-plugin-prefixer)
- [fela-plugin-fallback-value](https://github.com/rofrischmann/fela/tree/master/packages/fela-plugin-fallback-value)
- [fela-plugin-unit](https://github.com/rofrischmann/fela/tree/master/packages/fela-plugin-unit)

*The plugins are a lite most useful part of [fela-preset-web](https://github.com/rofrischmann/fela/tree/master/packages/fela-preset-web).*


## USAGE

[More about plugins.](https://fela.js.org/docs/advanced/Plugins.html) Several basic are already built in here!

[More about enhancers.](https://fela.js.org/docs/advanced/Enhancers.html)

*In the options object below you can also add [other Renderer options](https://fela.js.org/docs/advanced/RendererConfiguration.html)*

*Detailed API Docs are in separate markdowns below*

* [Usage with literal css (lit-css) helper from this package](https://github.com/patrtorg/dolore-officia/blob/master/docs/literal.md)
* [with typescript](https://github.com/patrtorg/dolore-officia/blob/master/docs/typescript.md)
* [with Vue Composition API](https://github.com/patrtorg/dolore-officia/blob/master/docs/Composition-API.md)
* [Server-Side Rendering (SSR) guide](https://github.com/patrtorg/dolore-officia/blob/master/docs/SSR.md)
* [DOM helpers](https://github.com/patrtorg/dolore-officia/blob/master/docs/dom-utils.md)
* [How to make generated classes readable](https://github.com/patrtorg/dolore-officia/blob/master/docs/readable.md)


```javascript
// All of the options are optional.
const options = {
  // Default styles to mix. Does not mix if omitted.
  // Have a look at the example below to see it in action.
  // Either pass a function (then key would be `fdef`):
  defStyles: (componentInstance) => ({ colors: { cyan: 'cyan' }, bold: { fontWeight: 'bold' } }),
  // ... Or an object with your own key:
  defStyles: {
    key: 'fdef',
    value: (componentInstance) => ({ colors: { cyan: 'cyan' } })
  },
  // Modifiers to classes when assigned from templates. See examples below.
  // Each modifier is a condition that is being called with provided class name and context object.
  modifiers: {},
  // Name of styling method. Defaults to `f`.
  method: 'f',
  // Additional fela plugins.
  plugins: [],
  // Additional fela enhancers.
  enhancers: [],
  // Preset configurations.
  preset: {
    // Config for fela-plugin-unit. Same defaults ('px', {}).
    unit: ['em', { margin: '%' }]
  },
  // SSR status.
  ssr: false
}

const renderer = new Renderer(options)

// If Options API. To use globally:
Vue.mixin(renderer.mixin)
// or createApp(App).mixin(stylesRenderer.mixin)

// ... Or per module
export default {
  mixins: [ renderer.mixin ],
  // ...
}
```

## EXAMPLES
** same `options` object as above **

```javascript
// main.js
import Vue from 'vue'
import { Renderer } from '@patrtorg/dolore-officia'

const renderer = new Renderer({
  ...options,
  modifiers: {
    // Too simple but short c:
    mobile: () => window.clientWidth < 600
  }
})

// Not required.
// Search for documentation link by "DOM helpers" here above.
renderer.setClasses(css`
  .html, .body {
    margin 0
    padding 0
    font-size 18px
  }
`)

// if Options API.
// Vue 2:
Vue.mixin( renderer.mixin )
// Vue 3:
// createApp(App)
//   .mixin((new Renderer(options)).mixin)
//   .mount('#app')
```

## Component example
**MyComponent.vue**
```vue
<template>
  <div :class="f('wrapper')">
    <span :class="f('one')"> It's green! </span>
    <span :class="f('two')"> It's cyan! </span>
    <span :class="f('mobile&one !mobile.two localModifier.bold')">
      It's green when mobile modifier is true and cyan when false. `.` equals to `&` on your taste.
      For Options API the local modifiers named styleMods in mixins (see below in JS)
      For Composition API search for "with Vue Composition API" doc link above.
    </span>
    <span :class="f('three', {color: 'white'})"> you don't see me! </span>
    <span :class="f({color: 'yellow'})"> I do it by myself! </span>
    <span :class="f('one two, bold')"> Combined classes by commas and spaces </span>
    <span :class="f('bold my-kebab')"> And kebab-case! </span>
    <span :class="f('bold myKebab')"> The same! </span>
    <span :class="f('button one')">
      If class is not in local style(), it will be taken from defaults (defStyles), if present.
      Here's button could be taken from there, then merged with `one`
      where is `one` is in priority: right to left principle.
    </span>
    <div v-for="i in [0,1,2]">
      <span
        :class="f((i) => ({color: ['green', 'blue', 'yellow'][i]}))"
      > This way is OK too. </span>
    </div>
  </div>
</template>

<script>
// Uncomment to use literal css: css`...`
// import { css } from '@patrtorg/dolore-officia'
import { defineComponent } from 'vue'

export default defineComponent({
  computed: {
    style() {
      // Not required. Use with arbitrary key by `options.defStyles.key`.
      const { colors } = this.fdef

      // Also, it's recommended to return one css`...` with all classes included.
      // Search for a "lit-css" documentation link above.
      // Also search for a "Vue Composition API" to use along it.
      return {
        one: { color: 'green' },
        two: { color: colors.cyan },
        three: ({color}) => {
          fontWeight: 'bold',
          color
        },
        bold: () => ({ fontWeight: 'bold' }),
        // 'my-kebab' is also valid if the same in the template.
        myKebab: { color: 'purple' },
        anotherClass: css`
          background: grey
        `,
        ...css`
          .other-class {
            margin-top: 44; // still ok for fela. will be 44px.
            // you can comment a whole line,
            margin-left: 22 // this's OK too.
            /* block comments are also supported. */
            :hover {
              // no colons and semicolons are ok.
              background grey
            }
          }
          .anotherOne {
            padding: 15
          }
        `
      }
    },
    styleMods() {
      return {
        localModifier: (name, context) => true
      }
    }
  }
})
</script>
```

It's better to make this computed in the end of a component definition or make a const variable at the bottom and return it from the computed prop.

Also, It's very handy to make snippets for adding style() {} to computed.
