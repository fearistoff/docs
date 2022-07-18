# Application API

## createApp()

Создаёт экземпляр приложения.

- **Тип**

  ```ts
  function createApp(rootComponent: Component, rootProps?: object): App
  ```

- **Подробности**

  Первый аргумент - это корневой компонент. Второй аргумент - это параметры, которые применятся к корневому компоненту.

- **Пример**

  Со встроенным корневым компонентом:

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* root component options */
  })
  ```

  С импортируемым компонентом:

  ```js
  import { createApp } from 'vue'
  import App from './App.vue'

  const app = createApp(App)
  ```

- **См. также:** [Гайд - создание Vue Приложения](/guide/essentials/application.html)

## createSSRApp()

Создаёт экземпляр приложения для режима [SSR Гидратации](/guide/scaling-up/ssr.html#client-hydration). Использование полностью повторяет использование `createApp()`.

## app.mount()

Навешивает экземпляр приложения в элемент-контейнер.

- **Тип**

  ```ts
  interface App {
    mount(rootContainer: Element | string): ComponentPublicInstance
  }
  ```

- **Подробности**

  Аргумент может быть как самим элементом DOM-дерева или CSS-селектором на него (использоваться будет первое совпадение). Возвращает экземпляр корневого приложения.

  Если у компонента есть шаблон или объявлена функция рендера, он заменит всё содержимое DOM-дерева внутри контейнера. Иначе, если доступен рантайм компилятор, за шаблон контейнера будет использован шаблон из `innerHTML`.

  В режиме SSR Гидратации, он гидрирует существующие элементы DOM-дерева внутри контейнера. Если будут некоторые [различия](/guide/scaling-up/ssr.html#hydration-mismatch), то существующие элементы DOM-дерева будут трансформированы, чтобы совпадать с ожидаемым результатом.

  Для каждого экземпляра приложения, `mount()` может быть вызван лишь один раз.

- **Пример**

  ```js
  import { createApp } from 'vue'
  const app = createApp(/* ... */)

  app.mount('#app')
  ```

  Также можно указать сам элементом DOM-дерева:

  ```js
  app.mount(document.body.firstChild)
  ```

## app.unmount()

Снимает навешивание экземпляр приложения, вызывая соответствующее событие жизненного цикла для всех компонентов этого внутри приложения.

- **Тип**

  ```ts
  interface App {
    unmount(): void
  }
  ```

## app.provide()

Указывает значение, которое будет встроено во все дочерние компоненты приложения.

- **Тип**

  ```ts
  interface App {
    provide<T>(key: InjectionKey<T> | symbol | string, value: T): this
  }
  ```

- **Подробности**

  Первым параметром ожидается ключ встраиваемого значения, а вторым - само значение. Возвращает сам экземпляр приложения.

- **Пример**

  ```js
  import { createApp } from 'vue'

  const app = createApp(/* ... */)

  app.provide('message', 'hello')
  ```

  Использование внутри компонента:

  <div class="composition-api">

  ```js
  import { inject } from 'vue'

  export default {
    setup() {
      console.log(inject('message')) // 'hello'
    }
  }
  ```

  </div>
  <div class="options-api">

  ```js
  export default {
    inject: ['message'],
    created() {
      console.log(this.message) // 'hello'
    }
  }
  ```

  </div>

- **См. также:**
  - [Provide / Inject](/guide/components/provide-inject.html)
  - [Provide на уровне приложения](/guide/components/provide-inject.html#app-level-provide)

## app.component()

Регистрирует глобальный компонент если переданы и название в виде строки и описание компонента, или извлекает уже существующий компонент если отправлено только имя. 

- **Тип**

  ```ts
  interface App {
    component(name: string): Component | undefined
    component(name: string, component: Component): this
  }
  ```

- **Пример**

  ```js
  import { createApp } from 'vue'

  const app = createApp({})

  // регистрация параметров объекта
  app.component('my-component', {
    /* ... */
  })

  // извлечение уже существующего компонента
  const MyComponent = app.component('my-component')
  ```

- **См. также:** [Регистрация компонентов](/guide/components/registration.html)

## app.directive()

Регистрирует глобальную стороннюю директиву если переданы и название в виде строки и описание директивы, или извлекает уже существующую директиву если отправлено только имя.

- **Тип**

  ```ts
  interface App {
    directive(name: string): Directive | undefined
    directive(name: string, directive: Directive): this
  }
  ```

- **Пример**

  ```js
  import { createApp } from 'vue'

  const app = createApp({
    /* ... */
  })

  // регистрация (объект директивы)
  app.directive('my-directive', {
    /* события жизненного цикла директивы */
  })

  // регистрация (сокращение до функции директивы)
  app.directive('my-directive', () => {
    /* ... */
  })

  // извлечение уже существующей директивы
  const myDirective = app.directive('my-directive')
  ```

- **См. также:** [Сторонние директивы](/guide/reusability/custom-directives.html)

## app.use()

Устанавливает [плагин](/guide/reusability/plugins.html).

- **Тип**

  ```ts
  interface App {
    use(plugin: Plugin, ...options: any[]): this
  }
  ```

- **Подробности**

  Первым аргументом принимает сам плагин, а во втором опциональном - его параметры.

  Плагин может быть объектом с методом `install()` внутри или просто функцией, которая будет использована как метод `install()`. Параметры (второй аргумент `app.use()`) будут вставлены рядом с методом `install()` плагина.

  Когда вызывается `app.use()` для одного и того же плагина он будет установлен только один раз.

- **Пример**

  ```js
  import { createApp } from 'vue'
  import MyPlugin from './plugins/MyPlugin'

  const app = createApp({
    /* ... */
  })

  app.use(MyPlugin)
  ```

- **См. также:** [Плагины](/guide/reusability/plugins.html)

## app.mixin()

Применяет глобальный миксин (в области видимости приложения). Глобальные миксины применяются к встроенным параметрам для каждого компонента в приложении.

:::warning Не рекомендован
Mixins are supported in Vue 3 mainly for backwards compatibility, due to their widespread use in ecosystem libraries. Use of mixins, especially global mixins, should be avoided in application code.
Миксины поддерживаются во Vue 3 в основном для обратной совместимости из-за их широкого использования в экосистемных библиотеках. Использование миксинов, особенно глобальных миксинов, рекомендуем избегать.

Для повторного использования логики, пользуйтесь [Составными](/guide/reusability/composables.html) функциями.
:::

- **Тип**

  ```ts
  interface App {
    mixin(mixin: ComponentOptions): this
  }
  ```

## app.version

Provides the version of Vue that the application was created with. This is useful inside [plugins](/guide/reusability/plugins.html), where you might need conditional logic based on different Vue versions.

- **Type**

  ```ts
  interface App {
    version: string
  }
  ```

- **Example**

  Performing a version check inside a plugin:

  ```js
  export default {
    install(app) {
      const version = Number(app.version.split('.')[0])
      if (version < 3) {
        console.warn('This plugin requires Vue 3')
      }
    }
  }
  ```

- **See also:** [Global API - version](/api/general.html#version)

## app.config

Every application instance exposes a `config` object that contains the configuration settings for that application. You can modify its properties (documented below) before mounting your application.

```js
import { createApp } from 'vue'

const app = createApp(/* ... */)

console.log(app.config)
```

## app.config.errorHandler

Assign a global handler for uncaught errors propagating from within the application.

- **Type**

  ```ts
  interface AppConfig {
    errorHandler?: (
      err: unknown,
      instance: ComponentPublicInstance | null,
      // `info` is a Vue-specific error info,
      // e.g. which lifecycle hook the error was thrown in
      info: string
    ) => void
  }
  ```

- **Details**

  The error handler receives three arguments: the error, the component instance that triggered the error, and an information string specifying the error source type.

  It can capture errors from the following sources:

  - Component renders
  - Event handlers
  - Lifecycle hooks
  - `setup()` function
  - Watchers
  - Custom directive hooks
  - Transition hooks

- **Example**

  ```js
  app.config.errorHandler = (err, instance, info) => {
    // handle error, e.g. report to a service
  }
  ```

## app.config.warnHandler

Assign a custom handler for runtime warnings from Vue.

- **Type**

  ```ts
  interface AppConfig {
    warnHandler?: (
      msg: string,
      instance: ComponentPublicInstance | null,
      trace: string
    ) => void
  }
  ```

- **Details**

  The warning handler receives the warning message as the first argument, the source component instance as the second argument, and a component trace string as the third.

  It can be used to filter out specific warnings to reduce console verbosity. All Vue warnings should be addressed during development, so this is only recommended during debug sessions to focus on specific warnings among many, and should be removed once the debugging is done.

  :::tip
  Warnings only work during development, so this config is ignored in production mode.
  :::

- **Example**

  ```js
  app.config.warnHandler = (msg, instance, trace) => {
    // `trace` is the component hierarchy trace
  }
  ```

## app.config.performance

Set this to `true` to enable component init, compile, render and patch performance tracing in the browser devtool performance/timeline panel. Only works in development mode and in browsers that support the [performance.mark](https://developer.mozilla.org/en-US/docs/Web/API/Performance/mark) API.

- **Type**: `boolean`

- **See also:** [Guide - Performance](/guide/best-practices/performance.html)

## app.config.compilerOptions

Configure runtime compiler options. Values set on this object will be passed to the in-browser template compiler and affect every component in the configured app. Note you can also override these options on a per-component basis using the [`compilerOptions` option](/api/options-rendering.html#compileroptions).

::: warning Important
This config option is only respected when using the full build (i.e. the standalone `vue.js` that can compile templates in the browser). If you are using the runtime-only build with a build setup, compiler options must be passed to `@vue/compiler-dom` via build tool configurations instead.

- For `vue-loader`: [pass via the `compilerOptions` loader option](https://vue-loader.vuejs.org/options.html#compileroptions). Also see [how to configure it in `vue-cli`](https://cli.vuejs.org/guide/webpack.html#modifying-options-of-a-loader).

- For `vite`: [pass via `@vitejs/plugin-vue` options](https://github.com/vitejs/vite/tree/main/packages/plugin-vue#options).
  :::

### app.config.compilerOptions.isCustomElement

Specifies a check method to recognize native custom elements.

- **Type:** `(tag: string) => boolean`

- **Details**

  Should return `true` if the tag should be treated as a native custom element. For a matched tag, Vue will render it as a native element instead of attempting to resolve it as a Vue component.

  Native HTML and SVG tags don't need to be matched in this function - Vue's parser recognizes them automatically.

- **Example**

  ```js
  // treat all tags starting with 'ion-' as custom elements
  app.config.compilerOptions.isCustomElement = (tag) => {
    return tag.startsWith('ion-')
  }
  ```

- **See also:** [Vue and Web Components](/guide/extras/web-components.html)

### app.config.compilerOptions.whitespace

Adjusts template whitespace handling behavior.

- **Type:** `'condense' | 'preserve'`

- **Default:** `'condense'`

- **Details**

  Vue removes / condenses whitespace characters in templates to produce more efficient compiled output. The default strategy is "condense", with the following behavior:

  1. Leading / ending whitespace characters inside an element are condensed into a single space.
  2. Whitespace characters between elements that contain newlines are removed.
  3. Consecutive whitespace characters in text nodes are condensed into a single space.

  Setting this option to `'preserve'` will disable (2) and (3).

- **Example**

  ```js
  app.config.compilerOptions.whitespace = 'preserve'
  ```

### app.config.compilerOptions.delimiters

Adjusts the delimiters used for text interpolation within the template.

- **Type:** `[string, string]`

- **Default:** `{{ "['\u007b\u007b', '\u007d\u007d']" }}`

- **Details**

  This is typically used to avoid conflicting with server-side frameworks that also use mustache syntax.

- **Example**

  ```js
  // Delimiters changed to ES6 template string style
  app.config.compilerOptions.delimiters = ['${', '}']
  ```

### app.config.compilerOptions.comments

Adjusts treatment of HTML comments in templates.

- **Type:** `boolean`

- **Default:** `false`

- **Details**

  By default, Vue will remove the comments in production. Setting this option to `true` will force Vue to preserve comments even in production. Comments are always preserved during development. This option is typically used when Vue is used with other libraries that rely on HTML comments.

- **Example**

  ```js
  app.config.compilerOptions.comments = true
  ```

## app.config.globalProperties

An object that can be used to register global properties that can be accessed on any component instance inside the application.

- **Type**

  ```ts
  interface AppConfig {
    globalProperties: Record<string, any>
  }
  ```

- **Details**

  This is a replacement of Vue 2's `Vue.prototype` which is no longer present in Vue 3. As with anything global, this should be used sparingly.

  If a global property conflicts with a component’s own property, the component's own property will have higher priority.

- **Usage**

  ```js
  app.config.globalProperties.msg = 'hello'
  ```

  This makes `msg` available inside any component template in the application, and also on `this` of any component instance:

  ```js
  export default {
    mounted() {
      console.log(this.msg) // 'hello'
    }
  }
  ```

## app.config.optionMergeStrategies

An object for defining merging strategies for custom component options.

- **Type**

  ```ts
  interface AppConfig {
    optionMergeStrategies: Record<string, OptionMergeFunction>
  }

  type OptionMergeFunction = (to: unknown, from: unknown) => any
  ```

- **Details**

  Some plugins / libraries add support for custom component options (by injecting global mixins). These options may require special merging logic when the same option needs to be "merged" from multiple sources (e.g. mixins or component inheritance).

  A merge strategy function can be registered for a custom option by assigning it on the `app.config.optionMergeStrategies` object using the option's name as the key.

  The merge strategy function receives the value of that option defined on the parent and child instances as the first and second arguments, respectively.

- **Example**

  ```js
  const app = createApp({
    // option from self
    msg: 'Vue',
    // option from a mixin
    mixins: [
      {
        msg: 'Hello '
      }
    ],
    mounted() {
      // merged options exposed on this.$options
      console.log(this.$options.msg)
    }
  })

  // define a custom merge strategy for `msg`
  app.config.optionMergeStrategies.msg = (parent, child) => {
    return (parent || '') + (child || '')
  }

  app.mount('#app')
  // logs 'Hello Vue'
  ```

- **See also:** [Component Instance - `$options`](/api/component-instance.html#options)
