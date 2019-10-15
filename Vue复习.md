*    Vue.js 组件其实都是被扩展的 Vue 实例 
*    可以扩展 `Vue` 构造器，从而用预定义选项创建可复用的**组件构造器**： 
*   `var MyComponent = Vue.extend({  // 扩展选项})`
*   ` 所有的 MyComponent 实例都将以预定义的扩展选项被创建` 
*   `var myComponentInstance = new MyComponent()`
*    每个 Vue 实例都会**代理**其 `data` 对象里所有的属性： 
*    注意只有这些被代理的属性是**响应的**。如果在实例创建之后添加新的属性到实例上，它不会触发视图更新。 
*    除了 data 属性， Vue 实例暴露了一些有用的实例属性与方法。这些属性与方法都有前缀 `$`，以便与代理的 data 属性区分。 
*    ==注意==，不要在实例属性或者回调函数中（如 `vm.$watch('a', newVal => this.myMethod())`）使用[箭头函数](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。因为箭头函数绑定父上下文，所以 `this` 不会像预想的一样是 Vue 实例，而是 `this.myMethod` 未被定义。
*    每个 Vue 实例在被创建之前都要经过一系列的初始化过程。例如，实例需要配置数据观测(data observer)、编译模版、挂载实例到 DOM ，然后在数据变化时更新 DOM 。在这个过程中，实例也会调用一些 **生命周期钩子** ，这就给我们提供了执行自定义逻辑的机会。例如，`created` 这个钩子在实例被创建之后被调用：  

 ![img](http://doc.vue-js.com/images/lifecycle.png)

*   数据绑定最常见的形式就是使用 “Mustache” 语法（双大括号）的文本插值：

    ```
    <span>Message: {{ msg }}</span>
    ```

    Mustache 标签将会被替代为对应数据对象上 `msg` 属性的值。无论何时，绑定的数据对象上 `msg` 属性发生了改变，插值处的内容都会更新。

    通过使用 [v-once 指令](http://doc.vue-js.com/v2/api/#v-once)，你也能执行一次性地插值，当数据改变时，插值处的内容不会更新。但请留心这会影响到该节点上所有的数据绑定：

*   ` <span v-once>This will never change: {{ msg }}</span> `

*   双大括号会将数据解释为纯文本，而非 HTML 。为了输出真正的 HTML ，你需要使用 `v-html` 指令：

    ```
    <div v-html="rawHtml"></div>
    ```

    被插入的内容都会被当做 HTML —— 数据绑定会被忽略。注意，你不能使用 `v-html` 来复合局部模板，因为 Vue 不是基于字符串的模板引擎。组件更适合担任 UI 重用与复合的基本单元。

*   Mustache 不能在 HTML 属性中使用，应使用 [v-bind 指令](http://doc.vue-js.com/v2/api/#v-bind)：

    ```
    <div v-bind:id="dynamicId"></div>
    ```

    这对布尔值的属性也有效 —— 如果条件被求值为 false 的话该属性会被移除：

    ```
    <button v-bind:disabled="someDynamicCondition">Button</button>
    ```

*   迄今为止，在我们的模板中，我们一直都只绑定简单的属性键值。但实际上，对于所有的数据绑定， Vue.js 都提供了完全的 JavaScript 表达式支持。

    ```
    {{ number + 1 }}{{ ok ? 'YES' : 'NO' }}{{ message.split('').reverse().join('') }}<div v-bind:id="'list-' + id"></div>
    ```

    这些表达式会在所属 Vue 实例的数据作用域下作为 JavaScript 被解析。有个限制就是，每个绑定都只能包含**单个表达式**，所以下面的例子都**不会**生效。

    ```
    <!-- 这是语句，不是表达式 -->{{ var a = 1 }}<!-- 流控制也不会生效，请使用三元表达式 -->{{ if (ok) { return message } }}
    ```

    模板表达式都被放在沙盒中，只能访问全局变量的一个白名单，如 `Math` 和 `Date` 。你不应该在模板表达式中试图访问用户定义的全局变量。

*   Vue.js 允许你自定义过滤器，被用作一些常见的文本格式化。过滤器应该被添加在 **mustache 插值**的尾部，由“管道符”指示：

    ```
    {{ message | capitalize }}
    ```

    Vue 2.x 中，过滤器只能在 mustache 绑定中使用。为了在指令绑定中实现同样的行为，你应该使用[计算属性](http://doc.vue-js.com/v2/guide/computed.html)。

*   过滤器函数总接受表达式的值作为第一个参数。

    ```
    new Vue({  // ...  filters: {    capitalize: function (value) {      if (!value) return ''      value = value.toString()      return value.charAt(0).toUpperCase() + value.slice(1)    }  }})
    ```

*   过滤器可以串联：

    ```
    {{ message | filterA | filterB }}
    ```

    过滤器是 JavaScript 函数，因此可以接受参数：

    ```
    {{ message | filterA('arg1', arg2) }}
    ```

 这里，字符串 `'arg1'` 将传给过滤器作为第二个参数， `arg2` 表达式的值将被求值然后传给过滤器作为第三个参数。 

*    指令（Directives）是带有 `v-` 前缀的特殊属性。指令属性的值预期是**单一 JavaScript 表达式**（除了 `v-for`，之后再讨论）。指令的职责就是当其表达式的值改变时相应地将某些行为应用到 DOM 上。让我们回顾一下在介绍里的例子： 

*   ```
    <p v-if="seen">Now you see me</p>
    ```

    这里， `v-if` 指令将根据表达式 `seen` 的值的真假来移除/插入 <p>元素。

*   一些指令能接受一个“参数”，在指令后以冒号指明。例如， `v-bind` 指令被用来响应地更新 HTML 属性：

    ```
    <a v-bind:href="url"></a>
    ```

    在这里 `href` 是参数，告知 `v-bind` 指令将该元素的 `href` 属性与表达式 `url` 的值绑定。

*   另一个例子是 `v-on` 指令，它用于监听 DOM 事件：

    ```
    <a v-on:click="doSomething">
    ```

    在这里参数是监听的事件名。我们也会更详细地讨论事件处理。

*   修饰符（Modifiers）是以半角句号 `.` 指明的特殊后缀，用于指出一个指定应该以特殊方式绑定。例如，`.prevent` 修饰符告诉 `v-on` 指令对于触发的事件调用 `event.preventDefault()`：

    ```
    <form v-on:submit.prevent="onSubmit"></form>
    ```

    之后当我们更深入地了解 `v-on` 与 `v-model`时，会看到更多修饰符的使用。

*   在模板中绑定表达式是非常便利的，但是它们实际上只用于简单的操作。在模板中放入太多的逻辑会让模板过重且难以维护。例如：

    ```
    <div id="example">  {{ message.split('').reverse().join('') }}</div>
    ```

    在这种情况下，模板不再简单和清晰。在实现反向显示 `message` 之前，你应该确认它。这个问题在你不止一次反向显示 message 的时候变得更加糟糕。

    这就是为什么任何复杂逻辑，你都应当使用**计算属性**。

*   [观察 Watchers](http://doc.vue-js.com/v2/guide/computed.html#观察-Watchers)

    虽然计算属性在大多数情况下更合适，但有时也需要一个自定义的 watcher 。这是为什么 Vue 提供一个更通用的方法通过 `watch` 选项，来响应数据的变化。当你想要在数据变化响应时，执行异步操作或昂贵操作时，这是很有用的。

    例如：

    ```
    <div id="watch-example">  <p>    Ask a yes/no question:    <input v-model="question">  </p>  <p>{{ answer }}</p></div>
    <!-- Since there is already a rich ecosystem of ajax libraries    --><!-- and collections of general-purpose utility methods, Vue core --><!-- is able to remain small by not reinventing them. This also   --><!-- gives you the freedom to just use what you're familiar with. --><script src="https://unpkg.com/axios@0.12.0/dist/axios.min.js"></script><script src="https://unpkg.com/lodash@4.13.1/lodash.min.js"></script><script>var watchExampleVM = new Vue({  el: '#watch-example',  data: {    question: '',    answer: 'I cannot give you an answer until you ask a question!'  },  watch: {    // 如果 question 发生改变，这个函数就会运行    question: function (newQuestion) {      this.answer = 'Waiting for you to stop typing...'      this.getAnswer()    }  },  methods: {    // _.debounce 是一个通过 lodash 限制操作频率的函数。    // 在这个例子中，我们希望限制访问yesno.wtf/api的频率    // ajax请求直到用户输入完毕才会发出    // 学习更多关于 _.debounce function (and its cousin    // _.throttle), 参考: https://lodash.com/docs#debounce    getAnswer: _.debounce(      function () {        var vm = this        if (this.question.indexOf('?') === -1) {          vm.answer = 'Questions usually contain a question mark. ;-)'          return        }        vm.answer = 'Thinking...'        axios.get('https://yesno.wtf/api')          .then(function (response) {            vm.answer = _.capitalize(response.data.answer)          })          .catch(function (error) {            vm.answer = 'Error! Could not reach the API. ' + error          })      },      // 这是我们为用户停止输入等待的毫秒数      500    )  }})</script>
    ```

    结果：

    Ask a yes/no question: 

    Error! Could not reach the API. Error: Network Error

    在这个示例中，使用 `watch` 选项允许我们执行异步操作（访问一个 API），限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态。这是计算属性无法做到的。

    除了 `watch` 选项之外，您还可以使用 [vm.$watch API](http://doc.vue-js.com/v2/api/#vm-watch) 命令。

*   组件在注册之后，便可以在父实例的模块中以自定义元素 `` 的形式使用。要确保在初始化根实例 **之前** 注册了组件：

    ```
    <div id="example">  <my-component></my-component></div>
    // 注册Vue.component('my-component', {  template: '<div>A custom component!</div>'})// 创建根实例new Vue({  el: '#example'})
    ```

    渲染为：

    ```
    <div id="example">  <div>A custom component!</div></div>
    ```

*    我们知道，父组件是使用 props 传递数据给子组件，但如果子组件要把数据传递回去，应该怎样做？那就是自定义事件！

     

     ### 使用 `v-on` 绑定自定义事件

     每个 Vue 实例都实现了[事件接口(Events interface)](http://doc.vue-js.com/v2/api/#Instance-Methods-Events)，即：

*    使用 `$on(eventName)` 监听事件

*    使用 `$emit(eventName)` 触发事件

Vue的事件系统分离自浏览器的[EventTarget API](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget)。尽管它们的运行类似，但是`$on`和 `$emit` **不是**`addEventListener` 和 `dispatchEvent` 的别名。

另外，父组件可以在使用子组件的地方直接用 `v-on` 来监听子组件触发的事件。

*   ```js
    <div id="counter-event-example">  <p>{{ total }}</p>  <button-counter v-on:increment="incrementTotal"></button-counter>  <button-counter v-on:increment="incrementTotal"></button-counter></div>
        
    Vue.component('button-counter',
                  {  template: '<button v-on:click="increment">{{ counter }}</button>', 
                   data: function () {    return {      counter: 0    }  },
                   methods: {    increment: function () {      this.counter += 1      this.$emit('increment')    } 
                  },
                  })
    
    new Vue({  el: '#counter-event-example',  data: {    total: 0  },  methods: {    incrementTotal: 
                                                                                 function () {      this.total += 1  
                                                                                                                 }  
                                                                                }
            })
    ```

*   



