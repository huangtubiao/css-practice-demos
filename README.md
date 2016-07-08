# css-practice-demos
a collection of simple demos of CSS practice

## Sticky Footer

## holy-grail-layout

## css-modules

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

这样一来，这个类名就变成独一无二了，只对App组件有效。CSS Modules提供各种插件(https://github.com/css-modules/css-modules/blob/master/docs/get-started.md)，支持不同的构建工具。本文使用的是Webpack的css-loader插件(https://github.com/webpack/css-loader#css-modules)，因为它对CSS Modules的支持最好，而且很容易使用。顺便说一下，如果你想学Webpack，可以阅读我的教程[Webpack-Demos](https://github.com/ruanyf/webpack-demos)。
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
