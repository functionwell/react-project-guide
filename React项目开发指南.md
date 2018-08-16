# React项目开发指南

React可以不使用构建工具[直接以script标签引用](https://reactjs.org/docs/add-react-to-a-website.html#add-react-in-one-minute)，[JSX语法也不是必选项](https://reactjs.org/docs/add-react-to-a-website.html#optional-try-react-with-jsx)。

推荐使用的React项目工具链：

- 学习React或创建一个单页应用，使用 **[Create React App](https://reactjs.org/docs/create-a-new-react-app.html#create-react-app)**
- 创建一个Node.js的服务端渲染的网站，推荐[Next.js](https://reactjs.org/docs/create-a-new-react-app.html#nextjs)
- 创建一个静态内容网站，推荐[Gatsby](https://reactjs.org/docs/create-a-new-react-app.html#gatsby)
- 构建一个组件库或者与现有的代码做集成，推荐[更加灵活的工具链](https://reactjs.org/docs/create-a-new-react-app.html#more-flexible-toolchains)

自已从头创建工具链的话，一般包含以下部分：

- 包管理器：[Yarn](https://yarnpkg.com/)或[npm](https://www.npmjs.com/)，可以使用丰富的第三方库，便于第三方库的安装和升级
- 打包器：[webpack](https://webpack.js.org/)或[Parcel](https://parceljs.org/)之类，可以让你书写模块化的代码，并打包在一起优化加载时间
- 编译器：[Babel](http://babeljs.io/)，可以让你书写现代化的JavaScript代码，并依然能在旧的浏览器里运行

这里使用Create React App来构建React应用

## Create React App

Create React App是官方推荐的学习React和构建单页应用的工具

    npx create-react-app my-app
    cd my-app
    npm start

需要Node >= 6（推荐使用[nvm](https://github.com/creationix/nvm)安装node）， npm >= 5.2  
npx是npm5.2+引入的工具，需要全局安装

    npm install -g npx

npx可以免于全局安装create-react-app，并且保证使用create-react-app创建的package配置是最新的，并会自动下载第三方包

my-app内部结构为：

    my-app
    ├── README.md
    ├── node_modules
    ├── package.json
    ├── .gitignore
    ├── public          # 静态文件目录
    │   ├── favicon.ico
    │   ├── index.html
    │   └── manifest.json
    └── src
        ├── App.css
        ├── App.js
        ├── App.test.js
        ├── index.css
        ├── index.js
        ├── logo.svg
        └── registerServiceWorker.js

/node_modules 目录和 /build 目录可以加入到ignore list中，代码同步的时候不需要同步这些文件。

进入my-app项目路径下后  

    npm start

app就会以开发模式运行于[http://localhost:3000](http://localhost:3000/)

    npm test

[运行测试](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#running-tests)

    npm run build

在 `build` 文件夹下构建生产环境的应用，用于部署

React默认支持所有流行的浏览器，包含IE9及以上，[部分功能需要polyfills](https://reactjs.org/docs/javascript-environment-requirements.html)

create-react-app创建的项目包含以下功能：

- React，JSX，ES6，Flow语法支持
- ES6以外的语法支持，如对象展开符
- 自动对CSS增加前缀，你不再需要 `-webkit-` 或者其他前缀修饰
- 一个快速交互式单元测试运行器，内置支持覆盖率报告
- 一个实时开发服务器，用于警告常见错误
- 一个用于生成JS，CSS和图像的构建脚本，包含哈希和sourcemap
- 一个离线优先的[service worker](https://developers.google.com/web/fundamentals/getting-started/primers/service-workers)和[web app manifest](https://developers.google.com/web/fundamentals/engage-and-retain/web-app-manifest/)，满足所有[Progressive Web App](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#making-a-progressive-web-app)标准

create-react-app提供的工具默认是预先配置好的，我们需要进行 ["eject"](https://github.com/facebook/create-react-app/blob/master/packages/react-scripts/template/README.md#npm-run-eject) 来进行自定义配置

    npm run eject

项目结构会变成如下：

    ├── config/           # Jest和Webpack的配置文件，以及一些polyfills
    │   ├── env.js
    │   ├── jest/
    │   │   ├── cssTransform.js
    │   │   └── fileTransform.js
    │   ├── paths.js
    │   ├── polyfills.js
    │   ├── webpack.config.dev.js
    │   └── webpack.config.prod.js
    ├── node_modules/
    ├── package.json
    ├── public/
    │   ├── favicon.ico
    │   └── index.html
    ├── scripts/          # 暴露出来的React Scripts
    │   ├── build.js
    │   ├── start.js
    │   └── test.js
    ├── src/
    │   ├── components/
    │   │   ├── board.js
    │   │   ├── game.js
    │   │   └── square.js
    │   ├── index.css
    │   ├── index.js
    │   ├── logo.svg
    │   └── utils/
    │       └── index.js
    └── yarn.lock

## 引入第三方React组件库

以[antd](https://ant.design/docs/react/introduce-cn)为例

    npm install antd --save

在`index.js`中就可以使用antd了：

```js
    import { DatePicker } from 'antd';
    ReactDOM.render(<DatePicker />, mountNode);
```

引入样式：

```js
    import 'antd/dist/antd.css';  // or 'antd/dist/antd.less'
```

### 按需加载

如果你在开发环境的控制台看到下面的提示，那么你可能使用了 `import { Button } from 'antd';` 的写法引入了 antd 下所有的模块，这会影响应用的网络性能。

    You are using a whole package of antd, please use https://www.npmjs.com/package/babel-plugin-import to reduce app bundle size.

使用 babel-plugin-import 来进行按需加载

    npm install babel-plugin-import --save-dev

在 `/config/webpack.config.dev.js` 中：
 ~~`module.exports.module.rules[2].oneOf[2].options.plugins` 数组~~ Babel loader 配置中增加 

    ["import", {
        "libraryName": "antd",
        "libraryDirectory": "lib",
        "style": "css",
    }]

更改后，处理 js 文件的 `loader` 类似如下：

```js
    // Process JS with Babel.
    {
        test: /\.(js|jsx|mjs)$/,
        include: paths.appSrc,
        loader: require.resolve('babel-loader'),
        options: {
            cacheDirectory: true,
            "plugins": [
                ["import", {
                    "libraryName": "antd",
                    "libraryDirectory": "lib",
                    "style": "css",
                }]
            ]
        },
    }
```

`/config/webpack.config.prod.js` 中也做相同处理

[babel-plugin-import详细文档](https://github.com/ant-design/babel-plugin-import)

然后只需从 antd 引入模块即可，无需单独引入样式：

```js
    // babel-plugin-import 会帮助你加载 JS 和 CSS
    import { DatePicker } from 'antd';
```

## 热模块替换HMR

项目默认会在文件更新后自动刷新页面，但无法保留页面状态（如弹窗），热模块替换可以在修改文件后重新加载更新后的模块，并保留页面的当前状态

    npm install --save-dev react-hot-loader

在 `config/webpack.config.dev.js` 中，Babel loader 配置中增加 `'react-hot-loader/babel'` ，增加后 `loader` 配置类似如下：

```js
    // Process JS with Babel.
    {
        test: /\.(js|jsx|mjs)$/,
        include: paths.appSrc,
        loader: require.resolve('babel-loader'),
        options: {
            cacheDirectory: true,
            "plugins": [
                ["import", {
                    "libraryName": "antd",
                    "libraryDirectory": "lib",
                    "style": "css",
                }],
                'react-hot-loader/babel'
            ]
        },
    }
```

然后把根组件 `(src/index.js)` 标记为 *hot-exported* ：

```js
    // ./containers/App.js
    import React from 'react'
    import { hot } from 'react-hot-loader'
    
    const App = () => <div>Hello World!</div>
    
    export default hot(module)(App)
```

现在你就可以在开发模式下热重载所有组件了

[React Hot Loader文档](https://www.npmjs.com/package/react-hot-loader)

## 开发中代理

如果你有单独的后端开发服务器 API，并且希望在同域名下发送 API 请求 ，那么代理某些 URL 会很有用。

[webpack-dev-server 可以配置 proxy 来代理请求](https://webpack.docschina.org/configuration/dev-server/#devserver-proxy)

在 http://192.168.10.221:8090 上有后端服务的话，你可以这样在package.json文件中添加如下配置启用代理：

```json
    // package.json
    //...
    "proxy": {
        "/api": "http://192.168.10.221:8090"
    }
```

请求到 /api/users 现在会被代理到请求 http://192.168.10.221:8090/api/users

默认情况下，不接受运行在 HTTPS 上，且使用了无效证书的后端服务器。如果你想要接受，修改配置如下：

```json
    // package.json
    //...
    "proxy": {
        "/api":{
            "target": "http://192.168.10.221:8090",
            "secure": false
        }
    }
```

更多配置选项参考[http-proxy-middleware文档](https://github.com/chimurai/http-proxy-middleware#http-proxy-options)

## 应用构建

### 生成的图片文件

`npm run build` 后会在 /build 文件加下生成用于生产环境的文件，默认的图片文件 `[/\.bmp$/, /\.gif$/, /\.jpe?g$/, /\.png$/]` 会使用 url-loader 处理，当图片小于10000 bytes时，会返回一个DataURL，大于10000 bytes时，会使用 file-loader 处理图片，图片的引用路径默认为 `/static/media/[name].[hash:8].[ext]` ，要更改此配置，需要在 file-loader 中配置：

```js
    options: {
        name: '[name].[hash:8].[ext]',
        publicPath: 'http://a.f265.com/project/canon-chain-manager/img/'
    }
```

这样输出的代码中引用图片的位置就是 `http://a.f265.com/project/canon-chain-manager/img/[name].[hash:8].[ext]`

[url-loader文档](https://webpack.docschina.org/loaders/url-loader)  
[file-loader文档](https://webpack.docschina.org/loaders/file-loader)

### 生成的js文件和css文件

默认构建好的js和css以 `main.[hash:8]` 命名，要更改的话，需要配置 entry ，默认为

```js
    entry: [require.resolve('./polyfills'), paths.appIndexJs]
```

把 entry 更改为一个对象：

```js
    entry: {
        login: [require.resolve('./polyfills'), paths.appIndexJs]
    }
```

这样打包好的js、css文件就会以 `login.[hash:8]` 命名

如果在测试环境上不想使用带文件版本号(`[hash:8]`)的文件，可以在 `webpack.config.prod.js` 中找到

```js
    const cssFilename = 'static/css/[name].[contenthash:8].css';
```

更改为

```js
    const cssFilename = 'static/css/[name].css';
```

在 `output.filename` 和 `output.chunkFilename` 中去掉 `[chunkhash:8]`

```js
    output: {
        // ...
        filename: 'static/js/[name].[chunkhash:8].js',
        chunkFilename: 'static/js/[name].[chunkhash:8].chunk.js',
    }
```

改为：

```js
    output: {
        // ...
        filename: 'static/js/[name].js',
        chunkFilename: 'static/js/[name].chunk.js',
    }
```

## 路由 react-router

[react-router](https://reacttraining.com/react-router/core) 用于针对不同的路由渲染不同的React组件，并能通过 [HTML5 History API](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Adding_and_modifying_history_entries) 实现前端路由跳转（并不发送请求给后台）

安装：

    npm install react-router-dom --save

基础案例：

```js
    import React from 'react'
    import {
        BrowserRouter as Router,
        Route,
        Link
    } from 'react-router-dom'

    const Home = () => (
    <div>
        <h2>Home</h2>
    </div>
    )

    const About = () => (
    <div>
        <h2>About</h2>
    </div>
    )

    const Topic = ({ match }) => (
    <div>
        <h3>{match.params.topicId}</h3>
    </div>
    )

    const Topics = ({ match }) => (
    <div>
        <h2>Topics</h2>
        <ul>
        <li>
            <Link to={`${match.url}/rendering`}>
            Rendering with React
            </Link>
        </li>
        <li>
            <Link to={`${match.url}/components`}>
            Components
            </Link>
        </li>
        <li>
            <Link to={`${match.url}/props-v-state`}>
            Props v. State
            </Link>
        </li>
        </ul>

        <Route path={`${match.path}/:topicId`} component={Topic}/>
        <Route exact path={match.path} render={() => (
        <h3>Please select a topic.</h3>
        )}/>
    </div>
    )

    const BasicExample = () => (
    <Router>
        <div>
        <ul>
            <li><Link to="/">Home</Link></li>
            <li><Link to="/about">About</Link></li>
            <li><Link to="/topics">Topics</Link></li>
        </ul>

        <hr/>

        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
        <Route path="/topics" component={Topics}/>
        </div>
    </Router>
    )
    export default BasicExample
```

主要使用的组件为 `<Router>`， `<Route>`， `<Link>`，`<Switch>`

### `<Router>`

父组件，用来包裹所有不同路由对应的子组件，只能含有一个子元素

### `<Link>`

跳转到对应的路由

### `<Route>`

当路由匹配时，渲染对应的组件

三种方式渲染子组件：属性`component`，`render`，`children`，子组件会获得 `match`，`location`，`history` 三个属性

属性：

- path：string，对应的路由
- component：React Element，路由匹配时渲染的组件
- render：func，路由匹配时调用该函数
- children：func，无论路由是否匹配，都调用该函数，参数对象props中的match属性在未匹配时为null

### <Switch>

用 `<Switch>` 包裹一组 `<Route>`，这样只会渲染匹配到的第一个 `<Route>`，否则的话其它未匹配的 `<Route>` 仍会渲染一个 `null`

[react-router详细文档](https://reacttraining.com/react-router/web)

配合react-router，可以使一个js文件供一组页面使用，okex使用此方法将前端拆分为多个子模块如 `account`，`fiat`，`spot`，便于开发和维护
