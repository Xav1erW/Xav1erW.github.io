---
title: React学习笔记
categories:
  - Tech
  - 前端
tags:
  - React
  - javascript
  - 前端
date: 2022.3.25
author: Xav1erW
---

# React

## 组件类型和定义

* react组件首字母应当大写，因为使用时以标签形式使用，如果首字母小写会认为是html的内置标签，报错

```javascript
// 函数式组件
function Welcome(props) {
    // props是传入的参数
    // 如果使用时不传参，也可以没有参数
    return <h1>Hello, {props.name}</h1>;
}

// 使用
ReactDOM.render(
    <Welcome name='wx'/>,document.getElementById('xxx')
)

// 类式组件
class Welcome extends React.Component {
    // 继承 React.Component
  render() {
      //   必须要有render函数，返回需要渲染的内容
    return <h1>Hello, {this.props.name}</h1>;
  }
}
```

## 组件三大属性

### state
* 箭头函数`()=>{}` 和`function` 区别：箭头函数没有`this`，其中出现`this`会去找外侧的`this`，`function`自身有`this`。可以利用该点在类中使用箭头函数，其中用`this`改变类中的属性.

* react自定义方法一般用复制加箭头函数的方法

```javascript
class Foo extends React.Component{
    myMethod = ()=>{
        /*TODO*/
    }
}
```

* 组件对象有`state`属性用于表示组件状态，同时在不同事件后使用`setState`改变state的值实现不同状态的转化。注意`setState`会把相同的键合并，不同的键保留，不会覆盖

```javascript
class Foo extends React.Component{
    constructor(props)
    {
        this.state = {time:111}
        this.myMethod = this.myMethod.bind(this)
        // 由于在调用myMethod 的时候，没用通过实例调用，是直接传入的函数
        // 函数在类的原型对象上，因此其中的this指向不正确
        // 因此使用bind在实例中加入属性myMethod
    }
    myMethod()
    {
        const time = this.state.time
        this.setState({time:time+1})
    }
    render()
    {
        return <h1 onClick={this.myMethod}>现在的时间是{this.state.time}</h1>
    }
}
```

* 同时上述代码可以不需要构造器，简写如下

```javascript
class Foo extends React.Component{
    state = {time:111}
    myMethod = ()=>{
        const time = this.state.time
        this.setState({time:time+1})
    }
    render()
    {
        return <h1 onClick={this.myMethod}>现在的时间是{this.state.time}</h1>
    }
    // 相当于直接在类中追加state和myMethod的属性
}
```

### props

#### 类组件中的props

* react 的props：我们在创建组件对象时不想用constructor，如何给组件传参？利用props属性，在使用对象标签时给标签参数就可以反映到props属性上

```javascript
class Person extends React.Component
{
    render()
    {
        return <p>这个人是{this.props.age}，{this.props.gender==='M'?'他':'她'}的年龄是{this.props.age}岁</p>
    }
}

ReactDOM.render(<Person name='wx' age='18' gender='M'/>, document.getElementById('xxx'))
```

* react批量传props：使用对象传参数

```javascript
class Person extends React.Component
{
    render()
    {
        return <p>这个人是{this.props.age}，{this.props.gender==='M'?'他':'她'}的年龄是{this.props.age}岁</p>
    }
}
let infos = {name:'wx', age:18, gender:'M'}
ReactDOM.render(<Person {...infos}/>, document.getElementById('xxx'))
// 注意此处不是对象的展开，因为外层的{}是表示这是一个js表达式
// 这是Babel+react的特性，不是原生js语法
// 直接console.log(...infos) 不会输出内容
// 仅适用于传标签属性
```

* 拷贝时可以修改其中的某项参数

```javascript
let a = {name:'wx', age:18, gender:'M'}
let copy = {...a, name:'wx2'}
console.log(copy)
// 结果为 {name:'wx2', age:18, gender:'M'}
```

* 当直接使用标签属性的形式传递props时，形式为`key=value`此时value只能时字符串，如果想传递其它类型需要加`{}`表示这是js表达式

```javascript
<Person name='wx' age='19' gender='M'/>
// 此处的age为字符串，但我们想传入数字

// <Person name='wx' age=19 gender='M'/>
// 这样会报错，19仅当为js变量时才具有意义，因此改用
<Person name='wx' age={19} gender='M'/>

// 或者直接使用对象传入参数
let a = {name:'wx', age:19, gender:'M'}
<Person {...a}/>
```

* 接上述内容，传入标签有不同类型，我们对不同类型标签需要有不同的操作，需要对不同标签的数据类型做限制。同时某些参数不传的时候，也需要参数的默认值。（参考函数传参需要限制参数类型和参数默认值）。组件类中使用属性`propTypes` 指定各个属性的限制，使用`defaultProps`指定默认值。具体使用如下

```javascript
import PropTypes from 'prop-types';
// 15.5之前PropTypes在React核心库中，使用React.PropTypes，之后单独写在prop-types.js中
class Person extends React.Component
{
    render()
    {
        return <p>这个人是{this.props.age}，{this.props.gender==='M'?'他':'她'}的年龄是{this.props.age}岁</p>;
    }
}

// 类型和必要性限制
Person.propTypes = {
    name:PropTypes.string.isRequired,
    // 使用React内置的规则PropTypes，限制name为string，同时是必须的参数
    gender:PropTypes.string,
    age:PropTypes.number,
    action:PropTypes.func
    // string、number开头小写是为了不和内部的String、Number冲突
    // 同样的，指定函数类型为了不和function冲突使用func
}

// 默认值的指定
Person.defaultProps = {
    gender:"未知",
    age:18
}


// 给类加入属性，可以在类内定义static属性
class Person extends React.Component
{
    // 类型和必要性限制
    static propTypes = {
        name:PropTypes.string.isRequired,
        // 使用React内置的规则PropTypes，限制name为string，同时是必须的参数
        gender:PropTypes.string,
        age:PropTypes.number,
        action:PropTypes.func
        // string、number开头小写是为了不和内部的String、Number冲突
        // 同样的，指定函数类型为了不和function冲突使用func
    }

    // 默认值的指定
   static defaultProps = {
        gender:"未知",
        age:18
    }
    render()
    {
        return <p>这个人是{this.props.age}，{this.props.gender==='M'?'他':'她'}的年龄是{this.props.age}岁</p>;
    }
}
```

* props是只读的，不能修改

#### 函数组件中的props

* 函数式组件通过参数的形式可以有`props`，但没有`state` 和`refs`，除非使用最新的hooks

```javascript
function Person(props)
{
    const {name, age, gender} = props
    return <p>这个人是{name}，{gender==='M'?'他':'她'}的年龄是{age}岁</p>;
}
// 由于没有了类，只能在外部指定限制和默认值
Person.propTypes = {
        name:PropTypes.string.isRequired,
        // 使用React内置的规则PropTypes，限制name为string，同时是必须的参数
        gender:PropTypes.string,
        age:PropTypes.number,
        action:PropTypes.func
        // string、number开头小写是为了不和内部的String、Number冲突
        // 同样的，指定函数类型为了不和function冲突使用func
}

// 默认值的指定
Person.defaultProps = {
        gender:"未知",
        age:18
}

ReactDOM.render(<Person name='wx'/>, document.getElementById('xxx'))
```

### refs

#### 字符串形式的ref(不推荐)

* 组件内的标签可以通过`ref` 来标识自己，组件会把`ref`和标签组成一对key-value放入属性`refs`中

```javascript
class Person extends React.Component
{
    showData= ()=>
    {
        // 直接通过this.refs获取到<input>标签
        // 注意此时获取到的是真实DOM标签，不是虚拟DOM
        console.log(this.refs.input1.value)
    }
    render()
    {
        return (
            <div>
                <input ref='input1' type='text' placeholder='点击按钮显示内容'/> 
                <button onClick={this.showData}> 点击显示内容 </button>
                <input type='text' placeholder='点击按钮显示内容'/> 
            </div>
        )
    }
}
```

#### 回调函数形式的ref

* 当`ref`为函数时，该函数作为回调函数使用，将`ref`所在节点作为参数传入函数中。一般会利用回调函数，把当前节点赋值给组件属性

```javascript
class Person extends React.Component
{
    showData1= ()=>
    {
        // 直接通过this.refs获取到<input>标签
        // 注意此时获取到的是真实DOM标签，不是虚拟DOM
        console.log(this.refs.input1.value)
    }
    showData2= ()=>
    {
        console.log(this.refs.input2.value)
    }
    render()
    {
        return (
            <div>
                <input ref={(currentNode)=>{this.input1 = currentNode}} type='text' placeholder='点击按钮显示内容'/> 
                <button onClick={this.showData}> 点击显示内容 </button>
                <input onBlur={this.showData2} ref={(currentNode)=>{this.input2 = currentNode}} type='text' placeholder='点击按钮显示内容' /> 
            </div>
        )
        // 由于回调函数简单，只有1个参数和一句，还可简写为 
        // ref = currentNode => this.input1 = currentNode
    }
}
```

* 有关内联函数(函数体直接定义在`ref`后面的函数)的问题：
  * 当组件更新的时候（`state`改变，react重新调用`render`函数，重新渲染），会调用两次`ref`回调函数，第一次传入参数`null` 第二次才真正传入当前节点
  * 原因：每次重新调用`render` 都会重新生成回调函数，不确定之前调用的回调函数有什么影响，为了消除影响，第一次先传入null消除之前可能的效果，之后再次调用，传入当前节点。
  * 只是细节，基本不会产生影响
  * 如果想改，可以把回调函数改为类中定义的函数

```javascript
class Person extends React.Component
{
    state = {s:True}
    showData1= ()=>
    {
        console.log(this.refs.input1.value)
    }
    change= ()=>
    {
        const {s} = this.state
        this.setState({s:!s})
    }
    refCallback=()=>
    {
        this.input1 = currentNode;
        console.log('@', currentNode)
    }
    // 如果使用这个，控制台就只会出现 @ <input ...>
    render()
    {
        return (
            <div>
                <input ref={(currentNode)=>{this.input1 = currentNode;console.log('@', currentNode)}} type='text' placeholder='点击按钮显示内容'/> 
                <button onClick={this.showData1}> 点击显示内容 </button> 
                <button onClick={this.change}> 点击改变状态 </button> 
            </div>
        )
    }
    // 当点击change后，可以看到控制台上输出
    // @ null
    // @ <input ...>
    // 两个结果
}
```

#### createRef

* 可以使用react内部的`createRef`来定义一个`ref`，`createRef`相当于创建一个容器，装ref对应的标签，每个`createRef`对应一个标签，`createRef`创建的ref是一个对象，其中有一个key为`currrent` 通过`myRef.current`获取到对应的标签

```javascript
class Person extends React.Component
{
    state = {s:True}
    myRef1 = React.createRef()
    // ...
    render()
    {
        return (
            <div>
                <input ref={this.myRef1} type='text' placeholder='点击按钮显示内容'/> 
            </div>
        )
    }
```

## React事件处理

* React中的事件都被封装了一层

1. 通过 `onXxx` 指定事件处理函数，如`onClick`、`onBlur`等
   1. 使用的是React自定义事件，而不是原生DOM事件 （为了更好的兼容性）
   2. 事件都是通过事件委托的方式处理的（委托给组件最外层元素） （为了高效）
2. 通过`event.target`得到发生事件的DOM元素对象   （减少ref使用）

* 避免过度使用ref：
  * 发生事件的元素正好是需要操作的元素，可以不用ref
  * 利用事件的回调函数传入的`event`参数直接获取DOM元素对象

例：
```javascript
class Demo extends React.Component
{
    showOnBlur=(event)=>{
        // 回调该函数时会传入event参数，使用event.target获得该事件的DOM对象
        // 这样可以避免ref的频繁使用
        alert(event.target.value)
    }
    render()
    {
        return (
            <div>
                <input onBlur={this.showOnBlur} type='text' placeholder='失去焦点显示内容'/> 
            </div>
        )
    }
```

## React 受控和非受控组件

### 非受控组件

```javascript
class Login extends React.Component
{   
    subCallback=(event)=>
    {
        event.preventDefault()
        const {username, passwd} = this
        alert(`用户名：${username.value}  密码：${passwd.value}`)
    }
    render()
    {
        return(
            <form action="localhost" onSubmit={this.subCallback}>
                用户名：<input ref={(c)=>this.username=c} type="text" name="username"/>
                密码：<input ref={(c)=>this.passwd=c} type="password" name="password"/>
                <button>登录</button>
            </form>
        )
        // form中action表示提交表单数据的地址
    }
}

ReactDOM.render(<Login/>, document.getElementById('root'))
```
* form表单不提供参数时，默认发起GET请求同时带有query参数
* 原生js中表单具有事件 `onsubmit`， React中为 `onSubmit`
* 避免表单提交刷新页面，可以使用 `event.preventDefault()` 阻止默认事件不提交表单
* 非受控组件：页面中所有输入类DOM的值现用现取（点击登录后，回调函数获取表单值）属于非受控组件

### 受控组件

* 原生js 有事件`onchange` 在改变时调用`onchange` 的回调函数，React中同样有 `onChange`
* 使用`onChange` 回调函数将输入内容直接存入状态中

```javascript
class Login extends React.Component
{   
    state = {username:"NoName", passwd:"NoPasswd"}
    readUserName = (event)=>
    {
        this.setState({username:event.target.value})
    }
    readPasswd = (event)=>
    {
        this.setState({passwd:event.target.value})
    }
    subCallback=(event)=>
    {
        event.preventDefault()
        const {username, passwd} = this.state
        alert(`用户名：${username}  密码：${passwd}`)
    }
    render()
    {
        return(
            <form action="localhost" onSubmit={this.subCallback}>
                用户名：<input onChange={this.readUserName} type="text" name="username"/>
                密码：<input onChange={this.readPasswd} type="password" name="password"/>
                <button>登录</button>
            </form>
        )
    }
}

ReactDOM.render(<Login/>, document.getElementById('root'))
```
* 关于受控和非受控：
  * 非受控可以理解为拿到数据的行为不受程序控制，而受用户控制，只有用户进行提交等行为，组件才能最终拿到数据；
  * 受控可以理解为拿数据的行为受程序控制，用户只要输入，不用提交，随着输入组件就能就拿到数据
  * 更建议使用受控组件，因为非受控组件有几个输入，就有几个`ref`，受控组件可以减少`ref` 的使用

* 上述代码有重复实现的功能，可以改善，<a id="柯里化代码">代码如下</a>，具体使用了函数的柯里化

```javascript
class Login extends React.Component
{   
    state = {username:"", passwd:""}
    changeFormData=(dataType)=>
    {
        return (event)=>
        {
            this.setState({[dataType]:event.target.value})
        }
        // 将函数作为返回值供回调使用，同时利用传入参数实现不同功能
    }
    subCallback=(event)=>
    {
        event.preventDefault()
        const {username, passwd} = this.state
        alert(`用户名：${username}  密码：${passwd}`)
    }
    render()
    {
        return(
            <form action="localhost" onSubmit={this.subCallback}>
                用户名：<input onChange={this.changeFormData("username")} type="text" name="username"/>
                密码：<input onChange={this.changeFormData("passwd")} type="password" name="password"/>
                <button>登录</button>
            </form>
        )
    }
}

ReactDOM.render(<Login/>, document.getElementById('root'))
```
* 不用柯里化函数，也可以实现

```javascript
// 修改changeFormData，接受dataType和需要的参数
changeFormData=(dataType, value)=>{/*do something*/}
// ...
return(
    <form action="localhost" onSubmit={this.subCallback}>
        用户名：<input onChange={(event)=>{this.changeFormData("username", event.target.value)}} type="text" name="username"/>
        <button>登录</button>
    </form>
)
```
直接利用回调函数给`changeFormData`传入不同的参数实现代码复用

## 组件生命周期（重要）

### 旧版本

* 组件第一次被渲染到页面上时，React中称之为**挂载 (mount)** ，从页面移除时称之为 **卸载 (unmount)**

* 挂载组件使用`render()` ，卸载使用`ReactDOM.unmountComponentAtNode()`

* 希望在组件挂载到页面上时执行一些操作，比如设置定时器等。可以使用 `componentDidMount()`进行操作，该函数在组件挂载时调用，并仅调用这一次。
* 例：希望组件中的文本周期性的透明度减小，到0时恢复为1。点击按钮使组件消失

```javascript
class Life extends React.Component
{
    state={opacity:1}
    delEle=()=>
    {
        ReactDOM.unmountComponentAtNode(document.getElementById('root'))
        // 删除组件
        // 测试中发现只能删除该组件外部的容器，而不能删除组件内部的标签
    }

    // componentDidMoun调用：只在挂载时调用
    componentDidMount()
    {
        setInterval(
            ()=>
            {
                let {opacity} = this.state
                opacity -= 0.1
                if (opacity<=0)
                {
                    opacity = 1
                }
                this.setState({opacity:opacity})
            },200
        )
        // 设置定时器，每200ms透明度减小0.1
    }

    // render调用：初始化渲染，更新调用
    // 如果定时器设置在render中，每次更新都会创建新的定时器，造成混乱
    render()
    {
        return(
            <div>
                <h2 style={{opacity:this.state.opacity}}>测试文本</h2>
                <button onClick={this.delEle}>点我消失</button>
            </div>
        )
    }
}
```

* 以上代码会出现新的问题，在点击删除节点后，控制台报错：不能更新一个未挂载的组件(unmounted component)的状态
* 解决以上问题：
  1. 先把定时器绑定到`this` 上，即初始定义为`this.timer = setInterval()` ，之后在删除组件时加上删除定时器`clearInterval(this.timer)` 使状态停止更新
  2. 直接使用`componentWillUnmount` 做这项工作，在组件将要被卸载时执行的操作

```javascript
// ...
delEle=()=>
{
    clearInterval(this.timer)
    // 提前清除定时器
    ReactDOM.unmountComponentAtNode(document.getElementById('root'))
}
componentDidMount()
{
    this.timer=setInterval(
        ()=>{
            /*TODO*/
        },200
    )
}
// ...
```

```javascript
componentWillUnmount()
{
    clearInterval(this.timer)
}
```

* 组件的生命周期实际就是组件从创建、挂载到卸载，其中重要的函数（如`render`、`componentDidMount`、`componentWillUnmount` 等）被称为：生命周期钩子函数、生命周期函数等
  * 组件从创建到死亡有一些特定的阶段
  * 组件包含一系列钩子函数会在特定阶段调用
  * 定义组件时会在特定的生命周期回调函数中，做特定的工作。

![](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/React/生命周期流程.svg)

* 对于上图：左侧好理解，不做赘述
* 右侧：
  1. `setState` 流程 
     1. 调用后，首先会通过`shouldComponentUpdate` 阀门，检查是否应当更新当前组件，若返回true则能够进行下去，反之会被阻拦，无法调用后面的函数。如果该函数未定义，永远返回true
     2. 检查返回true后会依次调用`componentWillUpdate` 、 `render` 、`componentDidUpdate` 表示组件更新前、更新、更新后的操作
  2. `forceUpdate` 流程
     * 跳过`shouldComponentUpdate` 强制更新
     * 直接调用`componentWillUpdate` 、 `render` 、`componentDidUpdate`
     * 一般用做不更改状态数据，强制更新一下
     * 通过`this.forceUpdate` 调用
  3. 父组件调用 `render`
     * 首先组件要形成父子关系：在A组件中调用B组件，则A为B的父组件
     * 父组件状态改变，重新render后，调用子组件钩子 `componentWillReceiveProps` （组件将要接收props）
     * **注意！** 上述钩子在第一次传入props 时不会调用！只有更新传入新的props时才会调用（可以认为父组件重新render后调用）

```javascript
// 父组件调用render展示

// 父组件
class A extends React.Component
{
    state = {s:true};

    changeState = () => {
        const {s} = this.state;
        this.setState({s:!s});
    };
    render()
    {
        const {s} = this.state;
        let st = s? "状态1":"状态2";
        return(
            <div>
                <p>这是A组件</p>
                <button onClick={this.changeState}>改变状态</button>
                <B show={st}/>
            </div>
        );
    }
}

// 子组件
class B extends React.Component {
    componentWillReceiveProps()
    {
        console.log("B---componentWillReceiveProps");
    }
    render() { 
        return (
        <div>
            <p>这是B组件，展示状态：{this.props.show}</p>
        </div>);
    }
}

// 在页面初始时，控制台不会显示B---componentWillReceiveProps
// 之后每点击一次button，控制台就多显示一个B---componentWillReceiveProps
```

* 以上都为旧版本的生命周期，做如下总结

1. 初始化阶段，由`ReactDOM.render()` 触发 （初次渲染）
   1. `constructor`
   2. `componentWillMount`
   3. `render`
   4. `componentDidMount`   **常用**，经常在该钩子中进行初始化，如：开启定时器、发起请求、订阅消息
2. 更新阶段，由`this.setState` 或父组件 `render` 触发
   1. `shouldComponentUpdate`
   2. `componentWillUpdate`
   3. `render`
   4. `componentDidUpdate`
3. 卸载组件，通常由 `ReactDOM.unmountComponentAtNode` 触发
   1. `componentWillUnmount` **常用**，通常做一些收尾工作，例：关闭定时器、取消订阅

### 新版本

* `componentWillMount` 、`componentWillReceiveProps` 、 `componentWillUpdate` 前都需要加上`UNSAFE_`
* 记忆：除了 `componentWillUnmount`，所有带will 的钩子都需要加`UNSAFE_`
* unsafe：未来版本后加入异步渲染后，这三个钩子可能带来一些bug（现在不会），因此加入UNSAFE标志，同时提醒减少这三个钩子的使用

* 新生命周期如[下图](https://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
![生命周期新](https://raw.githubusercontent.com/Xav1erW/blog-imgs/master/React/%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E6%96%B0.jpg)

* 对于新生命周期，可以理解为：
  1. 废弃`componentWillMount` 、`componentWillReceiveProps` 、 `componentWillUpdate`三个旧的钩子
  2. 提出 `getDerivedStateFromProps` 、 `getSnapshotBeforeUpdate` 两个新的钩子
  3. 注：实际使用中，两个新的钩子使用情况极其罕见

* 对于`getDerivedStateFromProps`：
  * 定义在类上，因此需要是静态方法 `static getDerivedStateFromProps` ；同时返回值也必须是状态对象或`null`
  * 接受参数`props`和`state` ，得到标签参数
  * 当返回`null`时，不会产生影响
  * 当返回状态对象，会将返回的对象设置为当前的state
  * **理解**：从props中得到派生的状态，即通过props的值得到状态，state的值在任何时候都取决于props，修改就没作用了
  * **注意**：容易造成代码冗余并且难以维护

```javascript
class A extends React.Component
{
    state = {s:0};

    static getDerivedStateFromProps(nextProps, prevState) {
        // return null
        // 返回null不会有任何影响
        console.log(nextProps, prevState);
        // 第一次输出 {s:100}  {s:0}
        // 之后输出 {s:100}  {s:101}
        return {s:nextProps.s}
    }
    // 从props中得到数据，并强行指定state值

    changeState = () => {
        const {s} = this.state;
        this.setState({s:s+1});
        // 每次点击+1
    };
    render()
    {
        const {s} = this.state;
        return(
            <div>
                <p>这是A组件</p>
                <button onClick={this.changeState}>改变状态</button>
                <p>当前状态值为{s}</p>
            </div>
        );
    }
}

ReactDOM.render(
    <A s={100}/>,
    document.getElementById('root')
);
```

* 对于`getSnapshotBeforeUpdate`：
  * 在最近一次渲染输出（提交到 DOM 节点）之前调用。它使得组件能在发生更改之前从 DOM 中捕获一些信息（例如，滚动位置）。此生命周期方法的任何返回值将作为参数传递给 `componentDidUpdate()`。
  * **说明**： `componentDidUpdate` 实际有三个参数 `componentDidUpdate(preProps, preState, snapshot)` 接收更新前的props、更新前的state和`getSnapshotBeforeUpdate` 传递进来的快照 snapshot
  
* 案例说明：
  * 需求：有一新闻列表，长度固定；不断有新的新闻刷新，新的新闻在列表最上方；超出列表长度，使用滚动条。
  * 问题：当内容不断刷新出现时，滚动条相对于最高点位置是固定的，反映到内容上，旧的内容会被新的内容挤到下方
  * 解决：需要固定内容相对不动，即每次更新内容时，需要滚动条相对顶部的高度加上新内容的高度

提前定义好列表和新闻样式为
```css
.newsList{
  width: 200px;
  height:150px;
  background:skyblue;
  overflow: auto;
}

.news{
  height: 30px;
}
```
<a id="indexAsKey">代码</a> 为

```javascript
class NewsList extends React.Component
{
    state = {newsArr:[]}
    // state中保存新闻列表

    componentDidMount() {
        setInterval(() => {
            // 获取原状态
            const {newsArr} = this.state
            // 模拟生成新闻消息
            const news = "新闻" + (newsArr.length+1)
            // 更新状态，把新的新闻信息放到第一个
            this.setState({newsArr:[news,...newsArr]})
        }, 1000);
    }

    getSnapshotBeforeUpdate(prevProps, prevState) {
        return this.NewsList.scrollHeight
        // 拿到更新之前内容区的高度，以和更新后比较，计算更新前后差值
        // 将高度留下快照
        // 快照传给componentDidUpdate
    }

    componentDidUpdate(prevProps, prevState, heightSnapshot) {
        // 获取更新后的高度
        let currentHeight = this.NewsList.scrollHeight
        
        // scrollTop即规定滚动条到顶部的距离
        // 每次距离要加上增加的内容长度
        // 注意不要=，直接=相当于固定滚动条高度，内容还是会被挤下去
        this.NewsList.scrollTop += currentHeight-heightSnapshot
    }

    render() {
        return (
            <div className='newsList' ref={(cNode)=>{this.NewsList = cNode}}>
                {
                    this.state.newsArr.map(
                        (n,index)=>{
                            return <div className='news' key={index}>{n}</div>
                        }
                    )
                    // 回想：react会自动展开可迭代的一系列标签
                }
            </div>
        );
        // 注：中间有key属性是因为要求列表中每一个子节点都有一个unique key
        // 为简单因此将index作为key
        // 实际这样使用会出现一些问题，之后再做探讨
    }
}

```

* 新版本生命周期总结如下

1. 初始化阶段，由`ReactDOM.render()` 触发 （初次渲染）
   1. `constructor`
   2. `getDerivedStateFromProps`
   3. `render`
   4. `componentDidMount`   **常用**，经常在该钩子中进行初始化，如：开启定时器、发起请求、订阅消息
2. 更新阶段，由`this.setState` 或父组件 `render` 触发
   1. `getDerivedStateFromProps`
   2. `shouldComponentUpdate`
   3. `render`
   4. `getSnapshotBeforeUpdate`
   5. `componentDidUpdate`
3. 卸载组件，通常由 `ReactDOM.unmountComponentAtNode` 触发
   1. `componentWillUnmount` **常用**，通常做一些收尾工作，例：关闭定时器、取消订阅


## Diffing算法

* React每次更新不是直接更新真实DOM，而是修改虚拟DOM，比较修改前后的虚拟DOM，只找不同的地方在真实DOM修改，不用真实DOM每次都全部更新，效率高
* Diffing算法最小更新粒度是标签，但是会比较多层，比如
```javascript
<span>
    现在的时间是：{this.state.time}
    <input type="text"/>
</span>
```
其中`this.state.time` 一秒钟更新一次，表示当前时间，`span` 标签内容改变，但是内部的`input`标签并没有改变，react只更新改变的内容，内部的 `input` 不会更新。

* 虚拟DOM中`key`的作用
  * 简单：`key`是虚拟DOM对象的标识，更新显示时`key`起着极其重要的作用
  * 详细：
  1. 当状态数据发生变化时，react会根据新数据生成新的虚拟DOM，随后React将【新虚拟DOM】与【旧虚拟DOM】的diff比较，规则如下：
     * 旧DOM找到了与新DOM相同的`key`，：
        * 若虚拟DOM内容不变，则仍用原本的真实DOM
        * 若虚拟DOM内容改变了，则根据改变的虚拟DOM生成真实DOM，替换页面内容
      * 未找到相同的`key`：根据数据创建真实DOM并渲染到页面

[之前的代码](#indexAsKey) 提到用`index`作为`key` 会产生一些问题，下分析其低效原因
代码示例：

```javascript
class Person extends React.Component {
    state = {
        persons:[
            {id:0, name:"小王", age:18},
            {id:1, name:"小李", age:19},
        ]
    };

    add = () => {
        const {persons} = this.state;
        const p = {id:persons.length+1, name:"小张", age:20}
        this.setState({persons:[p,...persons]})
        // 注意此处将信息系加入到了前面
    };

    render() { 
        return(
            <div>
                <h2>信息展示</h2>
                <button onClick={this.add}>添加小张</button>
                <ul>
                    {
                        this.state.persons.map((personObj, index)=>{
                            return <li key={index}>{personObj.name}---{personObj.age}</li>
                        })
                    }
                </ul>
            </div>
        )
    };
}
```

分析其中的更新步骤：
1. 初始化创建， `state` 内为 

```javascript
[
    {id:1, name:"小王", age:18}, 
    {id:2, name:"小李", age:19}
]
```

那么虚拟DOM中标签为

```html
<li key=0>小王---18</li>
<li key=1>小李---19</li>
```

2. 更新后，数据变为

```javascript
[
    {id:3, name:"小张", age:20},
    {id:1, name:"小王", age:18}, 
    {id:2, name:"小李", age:19}
]
```

新生成的虚拟DOM标签为

```html
<li key=0>小张---20</li>
<li key=1>小王---18</li>
<li key=2>小李---19</li>
```

按照之前的diffing比较步骤：
1. 首先取 `key=0` 旧虚拟和新虚拟比较：有该`key`，但内容不同，则重新渲染到真实DOM中
2. 再取 `key=1` 旧虚拟和新虚拟比较：有该`key`，但内容不同，则重新渲染到真实DOM中
3. 取 `key=2` 旧虚拟和新虚拟比较：无该`key`，直接渲染到真实DOM中

可以发现，实际将三个标签全部重新渲染，但是我们可以看出，两个标签是重复的，不必要再次渲染，这样冗余的渲染造成了效率低下

进一步看造成冗余的原因：使用`index`作为`key`，在数据列表等顺序改变时，会直接改变已有数据的`key`，导致新加入数据后，原本数据对应标签的`key` 也发生变化，造成多余的真实DOM渲染，没有复用之前的标签

**最好不要使用`index`，而是用数据的唯一标识，如`id`等作为`key`的值**

以上的问题，不只会带来效率降低，当有输入框等嵌套结构时，还会造成数据错位：

```javascript
class Person extends React.Component {
    state = {
        persons:[
            {id:0, name:"小王", age:18},
            {id:1, name:"小李", age:19},
        ]
    };

    add = () => {
        const {persons} = this.state;
        const p = {id:persons.length+1, name:"小张", age:20}
        this.setState({persons:[p,...persons]})
        // 注意此处将信息系加入到了前面
    };

    render() { 
        return(
            <div>
                <h2>信息展示</h2>
                <h3>使用index为key</h3>
                <button onClick={this.add}>添加小张</button>
                <ul>
                    {
                        this.state.persons.map((personObj, index)=>{
                            return <li key={index}>{personObj.name}---{personObj.age} <input type="text"/> </li>
                        })
                    }
                </ul>
                <hr/>
                <hr/>
                <h3>使用id为key</h3>
                <ul>
                    {
                        this.state.persons.map((personObj)=>{
                            return <li key={personObj.id}>{personObj.name}---{personObj.age} <input type="text"/> </li>
                        })
                    }
                </ul>
            </div>
        )
    };
}
```

上述代码，上半部分在输入后点击“添加小张” 会造成输入框数据的错位；下半部分则不会

原因：
* 在用index作为key的部分，由于相同的key内部值不同，节点都做了更新，但因为虚拟DOM中 `input` 没有 `value` 属性，直接比较内部 `input` 标签都一样，因此没有更新 `input` 标签，这样相当于保留输入但改变前面内容，造成了最终的错位
* 使用`id`为`key`，比较后id相同不做更新，直接在最前方加入 `li` 标签，输入框和文本信息统一后移，不会有错位

总结：用`index`作为`key`可能的问题
1. 若对数据进行排序、逆序、删除、插入等操作破坏原本顺序：会造成不必要的真实DOM更新，降低效率
2. 如果结构中还包含输入类DOM：会产生错误的DOM更新造成信息的错误显示
3. 如果不对数据进行排序、逆序、删除、插入等操作，仅用于渲染展示信息，可以使用`index`作为`key`

## React脚手架

### 文件介绍

* `punlic/index.html`

head部分内容解释

```html
<!-- 以链接形式指定图标，%PUBLIC_URL%为public文件夹 -->
<link rel="icon" href="%PUBLIC_URL%/favicon.ico" />
<!-- 开启理想视口，适配移动端 -->
<meta name="viewport" content="width=device-width, initial-scale=1" />
<!-- 指定地址栏和标签页的主题颜色（仅针对安卓手机） -->
<meta name="theme-color" content="#000000" />
```

* `punlic/manifest.json`

如果网页用作应用加壳，使用的配置文件（即，直接使用网页套壳作为安卓、iOS应用等）

* `src/App.js`

存放最外层App组件，用脚手架开发之后，所有小组件都作为App的子组件，最后只加载App组件到页面

* `src/App.test.js`

对App组件做测试的脚本，几乎不用

* `src/index.js`

webpack的入口文件
其中加载App组件，用`<React.StrictMode>` 包裹：这样会检查App组件中不合理的部分（比如使用字符串形式的ref会弹出警告等）

* `src/reportWebVital.js`

用于分析、记录、显示页面性能

* `src/setupTests.js`

用于模块的整体测试

### 编写方式

* 不同组件放到 `src` 统一的文件夹下，文件夹内部，一个组件一个文件夹，用于存放组件使用的外部js和样式等
* 将不同组件在App中进行组合
* 区分组件和业务逻辑文件：
  * 组件文件名大写
  * 组件文件后缀可以使用 `jsx`

* 多层文件引入麻烦，可以不同组件文件夹不同，但定义组件的文件都叫 `index.jsx` ，这样只要引入文件夹即可

### 样式模块化

* 避免不同模块之间样式冲突（不同模块指定相同名字的class不同样式，后引入的会覆盖之前引入的），要使用样式的模块化

* 模块化css：文件名改为 `xxx.module.css` 这样引入时可以将样式作为模块引入，使用时按照模块使用（`xxx.class` 等形式）

```css
/* xxx.module.css */
.title{
    background-color:skyblue;
}
```

```javascript
import xxx from './xxx.module.css'

export default class Hello extends React.Component
{
    render() {
        return (
            <h1 className={xxx.title}>Hello</h1>
        );
    }
}
```

### 应用案例 TodoList

* 上方Header组件为一个输入框，接受用户输入需要完成的任务，将任务加入到列表中
* 中间List展示已经有的任务，并且每个任务可以标志是否完成、可以删除
* 下方Footer展示已完成可全部任务数量，可以删除所有已完成任务，可以给所有任务打勾

**困难**：Header和List之间为兄弟，Header的数据无法传给List，也就无法新加入任务

**解决**：Header用某种方式将数据传给父组件App，再由App用props的形式将数据传给List

#### 子组件向父组件传递数据

将父组件类内部的处理、显示数据的函数传给子组件，子组件将数据作为函数参数传入并调用，相当于给父组件传入了参数，代码如下

```javascript
// App.js
class App extends React.Component
{
    state={todos:[
        {id:'001', name:"aaa", done:true},
        {id:'002', name:"bbb", done:false},
        {id:'003', name:"ccc", done:false},
    ]};
    // 添加todo，接受一个todo对象（包含id、name、done等参数）
    addTodo = (todoObj) => {
        const {todos} = this.state;
        // 追加todo
        const newTodos=[todoObj,...todos];
        // 更新状态
        this.setState({todos:newTodos})
    };
    render() {
        return (
             <Header addTodo={this.addTodo}/>
             <List/>
             <Footer/>
        );
    }
}

// =======================================

// Header.js
class Header extends React.Component {
    
    handleKeyUp = (event) => {
        // onKeyUp: 只要键盘按下就调用该事件
        const {keyCode, target} = event;
        // keyCode 为按下去的按键编码，value为输入框内容
        if (keyCode!== 13) return // 当按下的键不是回车直接返回，不处理
        
        // 构造todo对象
        // id需要不同，可以用库构造uuid，uuid库过大，可以使用nanoid
        // 每次调用都生成唯一的id
        // npm i nanoid
        const todoObj={id:nanoid(), name:target.value, done:false}

        // 调用addTodo函数 加入新的任务
        this.props.addTodo(todoObj)
    };

    render() { 
        return (
            <div>
                <input onKeyUp={this.handleKeyUp} type="text" placeholder="请输入任务名，按回车确认"/>
            </div>
        );
    }
}
```

具体实现见：`Todo_src`

## React 和 axios

### 跨域请求和代理配置

* 当出现以下情况，都属于跨域访问

| 跨域原因  |  示例 |
|---|---|
|  域名不同 |  www.jd.com 与 www.taobao.com |
| 域名相同，端口不同  | www.jd.com:8080 与 www.jd.com:8081  |
|  二级域名不同 |  item.jd.com 与 miaosha.jd.com   |

* 我们在测试请求数据时：在不同端口实现前端(3000)和后端服务器(5000)，造成跨域访问，导致数据获取失败。

* Ajax 对于跨域请求：请求可以发出，后端服务器能够接收到，但是后端服务器发往前端的数据会被ajax阻止

* 对于跨域，我们可以设置代理：
  * 代理将前端请求转发给后端，将后端响应返回给前端
  * 代理与前端端口和域名一致：前端仅与代理通信，不会造成跨域，请求不会被阻拦
  * 代理访问后端实际为跨域，但是由于没有ajax阻拦所以可以实现

* 做法：在package.json 中最后加入 `"proxy":"后端服务器地址"`
* 发送数据时就直接给前端端口（不会产生跨域）发送数据，代理自动将数据转发
* 注意：请求会首先在真正的前端端口下寻找资源，如果找到是不会转发给后端服务器的

# 附

## 原生js基础

* 展开运算符：`...`用于展开可迭代的变量，如数组等，但字面量对象(可以理解为字典)无法直接展开

```javascript
let a = [1,2,3,4]
let b = [5,8,7]
console.log(...a)
// 输出1 2 3 4
let c = [...a,...b]
// 用于拼接数组

// let b = {name:'xxx', age:'2'}
// console.log(...b)    // 会报错
```

### 对象相关

* 对象的复制：形式类似于展开运算符，但需要在外层加上`{}`

```javascript
let a = {name:'ccc', age:'3'}
let b = {name:'xxx', age:'2', nigger: true}

let c = {...a}
// 复制a到c
// 注意，直接赋值 c=a 相当于直接返回a的指针，修改a，c也改变

let merged = {...a, ...b}
// 合并a、b，其中相同的key被后者覆盖，不同的key加上
// merged = {name:'xxx', age:'2', nigger: true}
```

* 所有对象中`key:value` 表达式，默认`key` 为字符串，`name:"wx"` 等价于 `"name":"wx"`。如果想使变量作为`key` 可以使用方括号`[]`

```javascript
let dataType = "username";
console.log({dataType:"wx"});
// 输出结果为{dataType:"wx"}

console.log({[dataType]:"wx"});
// 输出结果为{username:"wx"}
```

* 当对象key和value相同时，有简写形式

```javascript
opacity = 0.5
style1 = {opacity:opacity}
style2 = {opacity}
// 两种写法效果相同
// 都会得到 {opacity:0.5}
```

### 常用

* `setInterval` 定时器，两个参数，第一个参数为函数，为执行的操作；第二个参数为执行函数间隔的时间

```javascript
time = 200    //ms
setInterval(()=>{}, time)
```


### 特殊概念

* 高阶函数
  1. 如果一个函数接受一个函数作为参数，那么就是高阶函数
  2. 如果一个函数将一个函数作为返回值，也是高阶函数
* 函数的柯里化：通过函数调用最终返回函数的方式，实现多次接收参数，最后统一处理的函数编码形式。如受控和非受控组件中的[代码](#柯里化代码)

## 资源

* 库的js可以通过官网找到，下载
* 还可以在[BootCDN](https://www.bootcdn.cn/) 找到，该网站提供了常用js库的加速访问