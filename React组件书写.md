# React组件书写

## 类型检查

使用prop-types做类型检查，prop-types会在运行时检查传入给React组件的props是否与定义的一致，不匹配的话会在开发模式下会显示警告

### 安装

```sh
npm install --save prop-types
```

### 引入

```js
import PropTypes from 'prop-types'; // ES6
var PropTypes = require('prop-types'); // ES5 with npm
```

### 使用

```js
import PropTypes from 'prop-types';

class Greeting extends React.Component {
  render() {
    return (
      <h1>Hello, {this.props.name}</h1>
    );
  }
}

Greeting.propTypes = {
  name: PropTypes.string
};
```

[prop-types文档](https://www.npmjs.com/package/prop-types)

## fetch使用

Fetch API提供了一个JavaScript接口，用于访问和操作HTTP管道的各个部分，例如请求和响应。 它还提供了一个全局fetch（）方法，该方法提供了一种在网络上异步获取资源的简单逻辑方法。

之前使用XMLHttpRequest实现了此类功能。 Fetch提供了一个更好的替代方案，可以很容易地被其他技术使用，例如Service Workers。 Fetch还提供了一个逻辑位置来定义其他与HTTP相关的概念，例如CORS和HTTP扩展。

fetch规范与jQuery.ajax()的不同之处主要有两点：

- 即使响应是HTTP 404或500，fetch()返回的Promise也不会是拒绝HTTP错误状态。相反，它将正常解析（ok状态设置为false），它只会拒绝网络故障或者有任何事情妨碍了请求的完成。
- 默认情况下，fetch不会从服务器发送或接收任何cookie，如果站点依赖于cookie维护用户会话，则会导致未经身份验证的请求（要发送cookie，必须设置init选项的credentials属性）

基本的fetch请求设置起来非常简单。 看看下面的代码：

```js
fetch('http://example.com/movies.json')
  .then(function(response) {
    return response.json();
  })
  .then(function(myJson) {
    console.log(myJson);
  });
```

这里我们通过网络获取JSON文件并将其打印到控制台。 fetch()的最简单用法是使用一个参数 - 要获取的资源的路径 - 并返回包含响应的promise（`Response`对象）

这当然只是一个HTTP响应，而不是实际的JSON。 要从响应中提取JSON主体内容，我们使用`json()`方法（在`Body` mixin上定义，它由`Request`和`Response`对象实现。）

### 加入请求选项

`fetch()`方法可以选择接受第二个参数，一个允许您控制许多不同设置的`init`对象：

```js
// Example POST method implementation:

postData(`http://example.com/answer`, {answer: 42})
  .then(data => console.log(data)) // JSON from `response.json()` call
  .catch(error => console.error(error));

function postData(url = ``, data = {}) {
  // Default options are marked with *
    return fetch(url, {
        method: "POST", // *GET, POST, PUT, DELETE, etc.
        mode: "cors", // no-cors, cors, *same-origin
        cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
        credentials: "same-origin", // include, same-origin, *omit
        headers: {
            "Content-Type": "application/json; charset=utf-8",
            // "Content-Type": "application/x-www-form-urlencoded",
        },
        redirect: "follow", // manual, *follow, error
        referrer: "no-referrer", // no-referrer, *client
        body: JSON.stringify(data), // body data type must match "Content-Type" header
    })
    .then(response => response.json()); // parses response to JSON
}
```

### fetch上传文件

可以使用HTML `<input type =“file”/>` input元素，`FormData()`和`fetch()`上传文件

```js
var formData = new FormData();
var fileField = document.querySelector("input[type='file']");

formData.append('username', 'abc123');
formData.append('avatar', fileField.files[0]);

fetch('https://example.com/profile/avatar', {
  method: 'PUT',
  body: formData
})
.then(response => response.json())
.catch(error => console.error('Error:', error))
.then(response => console.log('Success:', response));
```

`create-react-app`默认会提供`fetch`的polyfill：

```js
if (typeof Promise === 'undefined') {
  // Rejection tracking prevents a common issue where React gets into an
  // inconsistent state due to an error, but it gets swallowed by a Promise,
  // and the user has no idea what causes React's erratic future behavior.
  require('promise/lib/rejection-tracking').enable();
  window.Promise = require('promise/lib/es6-extensions.js');
}

// fetch() polyfill for making API calls.
require('whatwg-fetch');

```

[Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)

## 高阶组件

**高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。**

```js
const EnhancedComponent = higherOrderComponent(WrappedComponent);
```

antd中，form就是一个典型的高阶组件，例：

```js
import { Form, Icon, Input, Button, Checkbox } from 'antd';

const FormItem = Form.Item;

class NormalLoginForm extends React.Component {
  handleSubmit = (e) => {
    e.preventDefault();
    this.props.form.validateFields((err, values) => {
      if (!err) {
        console.log('Received values of form: ', values);
      }
    });
  }

  render() {
    const { getFieldDecorator } = this.props.form;
    return (
      <Form onSubmit={this.handleSubmit} className="login-form">
        <FormItem>
          {getFieldDecorator('userName', {
            rules: [{ required: true, message: 'Please input your username!' }],
          })(
            <Input prefix={<Icon type="user" style={{ color: 'rgba(0,0,0,.25)' }} />} placeholder="Username" />
          )}
        </FormItem>
        <FormItem>
          {getFieldDecorator('password', {
            rules: [{ required: true, message: 'Please input your Password!' }],
          })(
            <Input prefix={<Icon type="lock" style={{ color: 'rgba(0,0,0,.25)' }} />} type="password" placeholder="Password" />
          )}
        </FormItem>
        <FormItem>
          {getFieldDecorator('remember', {
            valuePropName: 'checked',
            initialValue: true,
          })(
            <Checkbox>Remember me</Checkbox>
          )}
          <a className="login-form-forgot" href="">Forgot password</a>
          <Button type="primary" htmlType="submit" className="login-form-button">
            Log in
          </Button>
          Or <a href="">register now!</a>
        </FormItem>
      </Form>
    );
  }
}

const WrappedNormalLoginForm = Form.create()(NormalLoginForm);

ReactDOM.render(<WrappedNormalLoginForm />, mountNode);
```

在书写antd form（re-form）表单的时候，使用的是高阶组件提供的 `prop.form` 属性，其中包含：
- `getFieldDecorator` 为表单控件提供 `value` 和 `onChange` 的状态管理（react中表单域一般由组件state进行管理）
- `validateFields` 验证表单域，回调中包含错误信息和表单域的值
- `getFieldInstance` 获取表单域的实例，可以对表单域进行focus等操作
- `setFieldsValue` 设置表单域的值，参数是表单域的键值对

[rc-form详细文档](https://github.com/react-component/form#option-object)

rc-form会传递子组件的引用，所以给添加的 `ref` 会自动传递到子组件上

`Form.create` 可以传递 `option` ，将form的表单域传递给全局的状态管理（`state manage`）

## 子组件引用

使用ref获取子组件的引用，在设置焦点、modal确定按钮提交内部表单等操作时非常必要。

ref设置有2种语法：
1. `React.createRef`

```js
class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    this.myRef = React.createRef();
  }
  render() {
    return <div ref={this.myRef} />;
  }
}
```

使用时：

```js
const node = this.myRef.current;
```

2. ref回调

```js
function CustomTextInput(props) {
  return (
    <div>
      <input ref={props.inputRef} />
    </div>
  );
}

class Parent extends React.Component {
  render() {
    return (
      <CustomTextInput
        inputRef={el => this.inputElement = el}
      />
    );
  }
}
```

注意：
- 在函数组件上不要使用`ref`属性，因为函数型组件没有实例
- 如果使用行内`ref`回调设置`ref`，在组件更新时回调会调用2次，第一次为`null`，然后是DOM节点，这是因为每一次`render`都会创建一个新的实例，所以React需要清除旧的`ref`，再设置新的`ref`，可以通过类的绑定方法替换回调来避免这种情况的发生。

## 状态管理

Redux或MobX

### Redux

Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

它可以帮助您编写行为一致的应用程序，在不同的环境（客户端，服务器和本机）中运行，并且易于测试。 最重要的是，它提供了出色的开发人员体验，例如实时代码编辑与时间旅行调试器相结合。

#### 要点

应用中所有的 state 都以一个对象树的形式储存在一个单一的 *store* 中。 惟一改变 state 的办法是触发 *action*，一个描述发生什么的对象。 为了描述 action 如何改变 state 树，你需要编写 *reducers*。

例：

```js
import { createStore } from 'redux';

/**
 * 这是一个 reducer，形式为 (state, action) => state 的纯函数。
 * 描述了 action 如何把 state 转变成下一个 state。
 *
 * state 的形式取决于你，可以是基本类型、数组、对象、
 * 甚至是 Immutable.js 生成的数据结构。惟一的要点是
 * 当 state 变化时需要返回全新的对象，而不是修改传入的参数。
 *
 * 下面例子使用 `switch` 语句和字符串来做判断，但你可以写帮助类(helper)
 * 根据不同的约定（如方法映射）来判断，只要适用你的项目即可。
 */
function counter(state = 0, action) {
  switch (action.type) {
  case 'INCREMENT':
    return state + 1;
  case 'DECREMENT':
    return state - 1;
  default:
    return state;
  }
}

// 创建 Redux store 来存放应用的状态。
// API 是 { subscribe, dispatch, getState }。
let store = createStore(counter);

// 可以手动订阅更新，也可以事件绑定到视图层。
store.subscribe(() =>
  console.log(store.getState())
);

// 改变内部 state 惟一方法是 dispatch 一个 action。
// action 可以被序列化，用日记记录和储存下来，后期还可以以回放的方式执行
store.dispatch({ type: 'INCREMENT' });
// 1
store.dispatch({ type: 'INCREMENT' });
// 2
store.dispatch({ type: 'DECREMENT' });
// 1
```

[Redux官网](https://redux.js.org)
