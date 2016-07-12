# css-practice-demos
a collection of simple demos of CSS practice

## Sticky Footer

## Holy-Grail-Layout

## CSS-Modules

### Demo01: 局部作用域

css的规则都是全局的，任何一个组件的样式规则都对整个页面有效。产生局部作用域唯一方法，就是使用一个独一无二的class的名字，不会与其他选择器重名。这就是CSS Modules 的做法。下面是一个React组件APP.js

```javascript
import React from 'react';
import style from './App.css';

export default () => {
  return (
    <h1 className={style.title}>
      Hello World
    </h1>
  );
};
```

上面代码中，我们将样式文件App.css输入到style对象，然后引用style.title代表一个class。

```css
.title {
  color: red;
}
```

构建工具会将类名style.title编译成一个哈希字符串。

```html
<h1 class="_3zyde4l1yATCOkgn-DBWEL">
  Hello World
</h1>
```

App.css 也会同时被编译。

```css
._3zyde4l1yATCOkgn-DBWEL {
  color: red;
}
```

这样一来，这个类名就变成独一无二了，只对App组件有效。CSS Modules提供各种[插件](https://github.com/css-modules/css-modules/blob/master/docs/get-started.md)，支持不同的构建工具。本文使用的是Webpack的[css-loader插件](https://github.com/webpack/css-loader#css-modules)，因为它对CSS Modules的支持最好，而且很容易使用。顺便说一下，如果你想学Webpack，可以阅读我的教程[Webpack-Demos](https://github.com/ruanyf/webpack-demos)。
下面是这个示例的webpack.config.js。

```javascript
module.exports = {
  entry: __dirname + '/index.js',
  output: {
    publicPath: '/',
    filename: './bundle.js'
  },
  module: {
    loaders: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        loader: 'babel',
        query: {
          presets: ['es2015', 'stage-0', 'react']
        }
      },
      {
        test: /\.css$/,
        loader: "style-loader!css-loader?modules"
      },
    ]
  }
};
```

上面代码中，关键的一行是style-loader!css-loader?modules，它在css-loader后面加了一个查询参数modules，表示打开CSS Modules 功能。
现在，运行这个Demo。

```bash
$ npm run demo01
```

打开 http://localhost:8080， 可以看到结果，h1标题显示红色。

### Demo02: 全局作用域

CSS Modules 允许使用:global(.className)的语法，声明一个全局规则。凡是这样声明的class，都不会被编译成哈希字符串。App.css加入一个全局class。

```css
.title {
  color: red;
}

:global(.title) {
  color: green;
}
```

App.js使用普通的class的写法，就会引用全局class。

```javascript
import React from 'react';
import styles from './App.css';

export default () => {
  return (
    <h1 className="title">
      Hello World
    </h1>
  );
};
```

运行这个示例。

```bash
$ npm run demo02
```

打开 http://localhost:8080，应该会看到h1标题显示为绿色。

CSS Modules还提供一种显示的局部作用域语法:local(.className)，等同于.className，所以上面的App.css也可以写成下面这样。

```css
:local(.title) {
  color: red;
}

:global(.title) {
  color: green;
}
```

### Demo03 定制哈希类名

css-loader默认的哈希算法是[hash:base64]，这会将.title编译成._3zyde4|1yATCOkgn-DBWEL这样的字符串。
webpack.config.js里面可以定制哈希字符串的格式。

```javascript
module: {
  loaders: [
    // ...
    {
      test: /\.css$/,
      loader: "style-loader!css-loader?modules&localIdentName=[path][name]---[local]---[hash:base64:5]"
    },
  ]
}
```

运行这个示例

```bash
$ npm run demo03
```

你会发现.title被编译成了demo03-components-App---title---GpMto

### Demo04 Class的组合

在CSSModule中，一个选择器可以继承另一个选择器的规划，这称为“组合”（“composition”）。
在App.css中，让.title继承.className。

```css
.className {
  background-color: blue;
}

.title {
  composes: className;
  color: red;
}
```

App.js不用修改。

```javascript
import React from 'react';
import style from './App.css';

export default () => {
  return (
    <h1 className={style.title}>
      Hello World
    </h1>
  );
};
```

运行这个示例。

```bash
$ npm run demo04
```

你应该看到红色的h1在蓝色的背景上。App.css编译成下面的代码。

```css
._2DHwuiHWMnKTOYG45T0x34 {
  color: red;
}

._10B-buq6_BEOTOl9urIjf8 {
  background-color: blue;
}
```

相应地，h1的class也会编译成`<h1 class="_2DHwuiHWMnKTOYG45T0x34 _10B-buq6_BEOTOl9urIjf8">`,
