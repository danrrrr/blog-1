# 框架面试题

## 1、React Vue 使用感受？

遇到这个问题，我也是楞了一下，要是之前能稍微总结一下就能回答得更好。

相同点：

1.  虚拟 DOM
2.  组件化
3.  保持对视图的关注
4.  数据驱动视图
5.  都有支持 native 的方案

不同点：

1.  state 状态管理 vs 对象属性 get，set
2.  vue 实现了数据的双向绑定 v-model,而组件之间的 props 传递是单向的，react 数据流动是单向的。

### 运行时优化

在 React 应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树，开发者不得不手动使用 shouldComponentUpdate 去优化性能。

在 Vue 组件的依赖是在渲染过程中自动追踪的，开发者不再需要考虑此类优化。另外 Vue 还做了很多其他方面的优化，例如：标记静态节点，优化静态循环等。

> 总结：Vue 在运行时帮我们做了很多优化了处理，开发者可以直接上手，React 则是由开发者自己去处理优化，让程序有更多的定制化。

### JSX vs Templates

JSX 中你可以使用完整的编程语言 JavaScript 功能来构建你的视图页面。比如你可以使用临时变量、JS 自带的流程控制、以及直接引用当前 JS 作用域中的值等等。

Templates 对于很多习惯了 HTML 的开发者来说，模板比起 JSX 读写起来更自然。基于 HTML 的模板使得将已有的应用逐步迁移到 Vue 更为容易。你甚至可以使用其他模板预处理器，比如 Pug 来书写 Vue 的模板。

> 总结：Vue 在模板上实现定制化，可以使用类 HTML 模板，以及可以使用 JSX，React 则是推荐 JSX。

### 生态圈

Vue 的路由库和状态管理库都是由官方维护支持且与核心库同步更新的。

React 的路由库和状态管理库是由社区维护，因此创建了一个更分散的生态系统。React 的生态系统相比 Vue 更加繁荣。

## 2、单向数据流和双向数据流有什么区别？

### 单向数据流

1.  所有状态的改变可记录、可跟踪，源头易追溯;
2.  所有数据只有一份，组件数据只有唯一的入口和出口，使得程序更直观更容易理解，有利于应用的可维护性;
3.  一旦数据变化，就去更新页面(data-页面)，没有(页面-data);
4.  如果用户在页面上做了变动，需要手动更新数据。

双向绑定 = 单向绑定 + UI 事件监听

缺点：

1.  HTML 代码渲染完成，无法改变，新数据到来时，就会整合新数据和模板重新渲染。
2.  代码量上升，数据流转过程变长，需要进行统一的数据流管理，例如：redux。

### 双向数据流

1.  用户在视图上的修改会自动同步到数据模型中去，数据模型中值的变化也会立刻同步到视图中去。
2.  在表单交互较多的场景下，会简化大量业务无关的代码。

缺点：

1.  无法追踪局部状态的变化。
2.  “暗箱操作”，增加了出错时 debug 的难度。
3.  由于组件数据变化来源入口变得不止一个，数据流转方向易混乱，如果不加以控制，容易出错。

## 3、React 优点？

1.  函数式编程思想，无状态组件，同样的 prop 对应同样的输出。
2.  虚拟 dom，firber，底层优化，提高渲染效率。
3.  模块化思想，复用性更强。
4.  单向数据流，让事情一目了然。
5.  以 JS 为中心，使用 JSX 开发页面，CSS In JS 书写样式。
6.  支持服务器端渲染。
7.  一套代码多端运行。

缺点：

1.  只是视图层，构建大型项目的话，需要引入 Redux 和 React-Router 等相关的东西。
2.  不好控制 DOM

## 4、Vue 优点？

1.  类 HTML 模板语法，更容易上手。
2.  模块化思想，复用性强。
3.  虚拟 dom，运行时优化，提高渲染效率。
4.  支持双向数据绑定，易用性强。
5.  支持服务器端渲染。

缺点：

1.  社区不如 react，大多是中国开发者。
2.  生态圈不够，vue 全家桶都是 vue 官方自己出的东西。

## 5、webpack 插件怎么编写？

webpack 就像是一条生产线，要经过一系列流程处理之后才能将源文件转换成输出结果。

webpack plugin 实质就是一个类，在被创建的时候，会监听 webpack 生产线上的事件，然后加入生产线中，改变生产线输出。

webpack 启动后，会读取配置中的 plugins，并创建对应实例，在 webpack 初始化 compiler 对象之后，再调用 plugin 中的 aplpy 方法，把 compiler 注入到插件中。

```js
class HelloAsyncPlugin {
  apply(compiler) {
    // tapAsync() 基于回调(callback-based)
    compiler.hooks.emit.tapAsync('HelloAsyncPlugin', function(
      compilation,
      callback
    ) {
      setTimeout(function() {
        console.log('Done with async work...');
        callback();
      }, 1000);
    });

    // tapPromise() 基于 promise(promise-based)
    compiler.hooks.emit.tapPromise('HelloAsyncPlugin', compilation => {
      return doSomethingAsync().then(() => {
        console.log('Done with async work...');
      });
    });

    // 原先基本的 tap() 也在这里列出：
    compiler.hooks.emit.tap('HelloAsyncPlugin', () => {
      // 这里没有异步任务
      console.log('Done with sync work...');
    });
  }
}

module.exports = HelloAsyncPlugin;
```

## 6、webpack loader 编写？

webpack loader 实质就是一个`function`，function 中会被注入需要被处理的资源，然后加以处理。

```js
import { getOptions } from 'loader-utils';
import validateOptions from 'schema-utils';

const schema = {
  type: 'object',
  properties: {
    test: {
      type: 'string'
    }
  }
};

export default function(source) {
  const options = getOptions(this);

  validateOptions(schema, options, 'Example Loader');

  // 对资源应用一些转换……

  return `export default ${JSON.stringify(source)}`;
}
```

## 7、webpack 插件之间怎么互相通信？

在 webpack 中，通过 tapable 管理运行时的各种事件流，不同的 webpack plugins 可以通过自定义事件相互通信。

例如：在 `Bplugin` 中监听 `Aplugin` 中的事件

```js
// A 插件
const SyncHook = require('tapable').SyncHook;
class APlugin {
  apply(compiler) {
    if (compiler.hooks.myCustomHook) throw new Error('Already in use');
    // 声明一个自定义事件，并初始化需要传递的参数。
    compiler.hooks.myCustomHook = new SyncHook(['参数1', '参数2']);
    // 在当前插件中监听自定义事件
    compiler.hooks.myCustomHook.tap('APlugin', (a, b) =>
      console.log('获取到参数：', a, b)
    );
    // 可以在任意的钩子函数中去触发自定义事件
    compiler.hooks.compilation.tap('APlugin', compilation => {
      compilation.hooks.afterOptimizeChunkAssets.tap('APlugin', chunks => {
        compiler.hooks.myCustomHook.call('a', 'b');
      });
    });
  }
}

// A 插件
class BPlugin {
  apply(compiler) {
    // 监听A组件定义的事件
    compiler.hooks.myCustomHook.tap('BPlugin', (a, b) =>
      console.log('获取到参数：', a, b)
    );
  }
}
```

## 8、React 中是否必须将所有状态都放入 Redux？有什么优缺点？

我们都知道，如果把状态都存在 redux 中，就可以编写出更多无状态的组件。

无状态组件优点：

- 代码整洁、可读性高
- 无状态组件的性能好

个人理解，并不是所有的 state 都存放在 redux 里比较好，应该考虑一下几点：

该应用程序的其他部分是否关心此数据？

您是否需要能够根据此原始数据创建更多派生数据？

是否使用相同的数据来驱动多个组件？

能够将此状态恢复到给定时间点？

你想缓存数据？

如果你没有以上的需要，就不要把 state 放入 redux 中。
