# Props深入

## children

- 作用 : 获取**组件标签**的 **子节点** , 本质上也是props上的属性,  类似与Vue中的插槽
- 获取方式 : `this.props.children`

```html
<div>
  {/* 1. 类似与普通的插槽 */}
  <MgButton name="login">登录</MgButton>
  <MgButton name="register">注册</MgButton>
  
  {/* 2. 具名插槽  只能通过属性来模拟具名插槽了*/}
  {/* <Child>
  <div #top>我是头部</div>
  <div #bottom>我是底部</div>
  </Child> */}
  <Child top={<div>我是头部</div>} bottom={<div>我是底部</div>}></Child>

{/* 3. 作用于插槽 */}
<MLHeader>
  {title => {
  return <h1>w shi {title}</h1>
  }}
</MLHeader>
</div>
```



## props 校验

- 作用：规定组件props的类型约束, 减少开发中的错误

- [prop-types 校验规则](https://zh-hans.reactjs.org/docs/typechecking-with-proptypes.html)

  

- 导入 : `import PropTypes from 'prop-types'`

- 给组件添加校验规则

  ```js
  // 要对某个组件里的某个属性进行校验
  Child.propTypes = {
  	// age 表示要校验的属性名称
  	// number 表示类型是数字
  	// isRequied 表示必填项
  	age : PropTypes.number.isRequired,
    name : PropTypes.string,
    arr : PropTypes.array,
    fn : PropTypes.func,
    isOK : PropTypes.bool,
    // 你可以让你的 prop 只能是特定的值，指定它为 枚举类型。
    num : PropTypes.oneOf(['News', 'Photos']).isRequired,
    // 一个对象可以是几种类型中的任意一个类型
    optionalUnion: PropTypes.oneOfType([
      PropTypes.string,
      PropTypes.number
    ]).isRequired,
  }
  ```
  
  

## props的默认值

- 可以通过 `组件.defaultProps = {}` 这样的方式来给组件添加默认值
- 默认值在用户没有传递该组件时会生效，如果用户传递了该属性，那么就会使用用户传递的属性了

```js
// 默认值
Child.defaultProps = {
  age : 10
}
```



## 类的静态属性static

+ 实例成员: 通过实例调用的属性或者方法，叫做实例成员（属性或者方法）

+ 静态成员：通过类或者构造函数本身才能访问的属性或者方法

```jsx
class Person {
   name = 'zs',
   static age = 18
   sayHi() {
       console.log('哈哈')
   }
   static goodBye() {
       console.log('byebye')
   }
}

const p = new Person()

console.log(p.name)
p.sayHi()

console.log(Person.age)
Person.goodBye()
```





# 生命周期

- [生命周期图谱](http://projects.wojtekmaj.pl/react-lifecycle-methods-diagram/)
- 图示: 顺序很重要, 对你后面的性能优化有帮助

![](mg-images/生命周期-8273213.png)

## 第一阶段 :  挂载阶段

- **触发时机** : 组件第一次被渲染的时候, 进入页面的时候触发
- **三个钩子函数**
  - **constructor()** : 初始化state 
  - **render()** : 渲染UI 
  - **componentDidMount()** : 1.操作DOM  2. 发送ajax请求
- **触发顺序** :  **constructor -->  render --> componentDidMount** 

```js
  // 类组件
  class Child extends React.Component {
    
    //1.1. 构造函数
    // 初始化数据
    constructor() { 
      super()
      this.state = {
        name :'zs'
      }
      console.warn('constructor');
    }

    //1.2. 渲染UI 
    render () {
      console.warn('render');
      
      return (
        <div>
          <p>哈哈 {this.state.name}</p>
        </div>
      )
    }

    //1.3. 挂载之后  渲染之后
    // 操作DOM ,发送ajax
    componentDidMount () {
      console.warn('componentDidMount');
    }
  }
```



## 第二阶段 :  更新阶段

- **执行顺序** :  render --> componentDidUpdate
- 三种**触发组件更新**的方式
  - **setState()**  :  修改 state 的值
  - **new Props** :  表示组件接收到一个新的 props 值。在父子组件关系中，当父组件中状态更新时，最新的 props 会自动传入到子组件中，从而触发子组件的重新渲染。不管是函数组件还是class组件，都会触发重新渲染
  - **forceUpdate()** :  表示强制组件重新渲染 (一般不会用 知道就好)
- **注意点**：
- 注意1 :  componentDidUpdate 钩子函数中获取到的是 更新后的 DOM 内容
  - 想获取之前的, 使用 `componentDidUpdate (preProps,preState) {`  
  - 参数是`之前的props 和 state`
- 注意2 : 在 componentDidUpdate 钩子函数中，不能直接调用 setState() 否则，会递归渲染，造成死循环。如果要调用 setState()，应该放在一个 条件判断 中

```js
class Parent extends React.Component { 
  state = {
    pmsg :'撩妹'
  }
  render () { 
    return <div>
      <button onClick={this.updateProps2Child}>按钮修改props</button>
      <Child msg={ this.state.pmsg }></Child>
    </div>
  }

  updateProps2Child = () => { 

    // 演示2 : 修改父的state 其实就是修改 child 的props , 重新调用 child 的render
    this.setState({
      pmsg : '撩汉'
    })
  }
}
  
  //2. 类组件
  class Child extends React.Component {
    
    state = {
        name :'zs'
     }

    //2.1. 渲染UI 
    render () {
      console.warn('render');
      
      return (
        <div>
          <p>哈哈 {this.state.name}</p>
          <p>父传过来的 : { this.props.msg }</p>
          <button onClick={this.updateName}>按钮</button>
        </div>
      )
    }

    //更新state 的name 数据
    updateName = () => { 
       // 演示1 : setState 重新调用 render
       this.setState({
         name : 'ls'
       })

      // 演示3 : forceUpdate 重新调用 render
      this.forceUpdate()
    }

    // 2.2 组件更新
    // 重复调用就会重新赋值, 赋值多了就会赋重样的值, 如果判断处理就是判断不一样的值
    // 上一次的 props 和  上一次的state 
    componentDidUpdate (preProps,preState) { 
      console.warn('组件更新之前的数据', preState);
      // this.state 当前最新的state 
      console.warn('组件更新之后的数据',this.state); 
      // 有条件的渲染 不然会造成死循环
      if(this.state.name !== preState.name) {
        this.setState({
          name : 'xx'
        })
      }
    }
  }
```



## 第三阶段 : 卸载阶段

- `componentWillUnmount`：在组件卸载时会触发，也就是 组件从页面中消失的时候
- **作用**：执行清理工作
  - 比如：清理掉 定时器、解绑手动绑定的事件 等

```js
// 第一步 : 在父组件里 点击按钮, 把 子组件给销毁
class Parent extends React.Component { 
	...
  render () { 

    return <div>
      <button onClick={this.changeShow}>按钮修改props</button>
      { this.state.isShow && (<Child msg={ this.state.pmsg }></Child>) }
    </div>
  }

  changeShow = () => { 
      // 点击按钮 子组件被销毁卸载
    this.setState({
      isShow : false
    })
  }
}

// 第二步 :  在 Child 组件里 
componentDidMount () {
	
    //1. 开始定时器
    this.timerId = setInterval(() => {
        console.log('好嗨哟');
    }, 1000);
	//2. 给window注册鼠标触摸事件
    window.addEventListener('mousemove', this.handleMouseMove)
}

// 触摸事件处理函数
handleMouseMove = (e) => { 
  console.log(e.clientX);
}

// 第三步 :  在将要卸载的钩子函数里 清除定时器和 移除鼠标移动事件
// 将要卸载
componentWillUnmount () { 
    //1. 清除定时器
    clearInterval(this.timerId)
    //2. 移除鼠标触摸事件
    window.removeEventListener('mousemove',this.handleMouseMove)
}
```



## 改造Todos

```js
  componentDidMount(){
    this.setState({
      list : JSON.parse(localStorage.getItem('todos_loc'))
    })
  }

  componentDidUpdate(){
    console.log('变化了');
    localStorage.setItem('todos_loc', JSON.stringify(this.state.list))
  }
```



 	

# render-props  和 高阶组件

- 什么情况下 会使用这两种模式 ? **复用**
- 当两个组件或者多个组件有一部分` state `   和  `操作 state 的方法`  相同或者相似的时候, 就可以将这些代码逻辑使用这两种模式来实现复用

```
const [num,setNum] = useState(0)
```

- 目的 : **状态逻辑复用**    ==> 通俗点说 :  另一种封装形式
- 要复用的内容为 : 
  - state 
  - 操作 state 的方法
- 注意 
  - 不管是高阶组件还是 render-props 模式, 都要将`状态逻辑`封装在一个组件中 
  - 并且这个组件只提供状态和操作状态的方法逻辑
  - 这个组件不提供要渲染的UI结构,  因为要渲染的内容不确定
- **学习注意 :** 
  - **思想重于代码**



## render-props 的使用

#### 拷贝 Mouse.js 并简单介绍

- state : {x,y} 鼠标坐标
- 注册鼠标移动事件和移除 事件
- 移动事件吹函数 保存鼠标位置

#### 演示1 : 鼠标位置

```js
// 位置
// 1. 使用 mouse组件 添加一个render属性, 值类型为一个函数
// 2. 可以得到一个mouse 位置坐标
// 3. 通过return 什么, 页面就会显示什么
ReactDOM.render(<Mouse 
                render={(mouse) => { 
                  console.log(mouse);
                  return <h1>{mouse.x} - {mouse.y}</h1>
}}/>,document.getElementById('root'))
```



#### 演示2 :移动猫

```js
// 引入图片 
import cat from './images/cat.png'

// 渲染
ReactDOM.render(<Mouse 
                render={mouse => { 

  return <img style={{ position : "absolute", left:mouse.x-64, top:mouse.y-64 }} src={cat} alt=''/>

} }/>,document.getElementById('root'))
```



#### 总结使用共同步骤

- 1.给 Mouse 组件传递 `render 属性`    ( render属性的值 是一个函数 )
- 2.通过 render 函数属性的**参数** 来获取组件内部复用的状态 (比如 : 鼠标位置数据)
- 3 通过render函数属性的**返回值**来指定最终要渲染在 页面中的内容



#### 分析Mouse 组件内容

- Mouse 组件 只负责 
  - 提供 鼠标位置 state
  - 提供操作数据位置的逻辑代码
- 注意 :  Mouse 组价 自身不指定要渲染的内容, 因为 Mouse 组件自身要渲染的什么内容
  - 实际上是由用户在使用该组件时指定的
- **思想 :**  Mouse 组件通过调用 props.render() 方法,  将组件内部的状态暴露到组件外部, 这样, 用户在使用该组件时, 就可以通过 render 属性的参数来获取到组件内部的状态了

```js
// 封装 Mouse 组件 Mouse.js，
// 实现鼠标位置的复用
// 状态逻辑复用：1 state 2 操作状态
class Mouse extends React.Component {
  // 提供鼠标位置的状态
  state = {
    x: 0,
    y: 0
  }

  // 监听鼠标位置
  componentDidMount() {
    window.addEventListener('mousemove', this.handleMouseMove)
  }

  // 组件卸载时执行清理工作：解绑事件
  componentWillUnmount() {
    window.removeEventListener('mousemove', this.handleMouseMove)
  }

  // 更新鼠标位置
  handleMouseMove = e => {
    this.setState({
      x: e.clientX,
      y: e.clientY
    })
  }

  render() {
    // 通过 props 来获取到传递给组件的属性 render
    // 因为 render 是一个函数，所以，就可以调用
    // 在调用 render 函数时，将组件内部的状态，传递给 render 函数
    // 最终，通过 render 的形参，就可以在组件外部获取到组件内部的状态了
    // this.props.render(this.state)
    // return null

    // 通过 render 函数的返回值来指定要渲染的内容
    // 所以，在组件内部直接使用 render 函数的返回值来作为该组件要渲染的内容
    return this.props.render(this.state)
  }
}
```



#### 使用 children 代替 render 属性

- 注意 : 不是该模式叫 render-props 模式, 就必须使用render 属性
  - 实际上, 只要有一个属性来告诉组件要渲染什么内容, 其实这就是 render-props 模式了
- **推荐 : 使用 children 代替 render 属性**
- **使用 children 演示位置和猫**

```js
// 1 . 位置
ReactDOM.render(<Mouse>
  {(mouse) => { 
    return <p>{mouse.x} - { mouse.y }</p>        # +
   } }
</Mouse>, document.getElementById('root'))
// 2 . 猫
ReactDOM.render(<Mouse>
  {mouse => { 
    return <img style={{ position:'absolute', left:mouse.x-64, top:mouse.y-64 }} src={cat} alt=""/>  											        # +
  }  }
</Mouse>, document.getElementById('root'))

// 3. Mouse.js 内部
render() {
  // 改为 children 
  return this.props.children(this.state)
}
```



#### 使用场景

- 场景1 :  之前的 Context 使用的就是这个模式 

```js
const { Provider, Consumer } = React.createContext()

// 提供数据
<Provider value={ 'red' } ></Provider>

// 消费数据/使用数据
<Consumer>
{ data => {
   return <div style={{ color : data }}>Four</div>
}
</Consumer>
```



- 场景2 : 动画插件 **react-spring**
- [github地址](https://github.com/mawenxing/react-spring)
- 安装 :  `yarn add react-spring`
- 使用 :  `Render-props api ==> spring`

```js
// 引入
import {Spring} from 'react-spring/renderprops'

// 使用
ReactDOM.render(<Spring
  config={{ duration:4000 }}
  from={{ opacity: 0 }}
  to={{ opacity: 1 }}>
  {props => { 
    console.log(props);
    
    return <div style={props}>hello</div>
  }}
</Spring>, document.getElementById('root'))
```



## 高阶组件的使用

- render-props 模式 和  高阶组件 都是 用来做复用的  : state 和 操作 state 的方法

#### 高阶组件介绍

- **高阶组件 :**  HOC : High-Order Component
- 实际上**就是一个函数**,  这个函数能够接受一个**参数组件**, 然后,返回一个**增强后的组件**
- **参数组件** : 就是需要被包装的组件
- **返回的组件** : 增强后的组件, 这个组件中就是通过Props来接收到复用的状态逻辑的

- **思想 : 就是组件在增强的过程中, 传入了一些数据给 组件的 props**

```js
const 增强后的组件 = 高阶组件(被包装组件)
```



#### 高阶组件使用演示

- 高阶组件的代码

```js
// 这就是一个高阶组件
// 职责 : 1 提供鼠标位置状态 2 提供鼠标位置的方法 
const withMouse = WrappedComponent => {
  class Mouse extends React.Component {
    // 鼠标位置状态
    state = {
      x: 0,
      y: 0
    }
    // 进入页面时，就绑定事件
    componentDidMount() {
      window.addEventListener('mousemove', this.handleMouseMove)
    }
    // 鼠标移动的事件处理程序
    handleMouseMove = e => {
      this.setState({
        x: e.clientX,
        y: e.clientY
      })
    }
    // 移除事件
    componentWillUnmount() {
      window.removeEventListener('mousemove', this.handleMouseMove)
    }
    render() {
      return <WrappedComponent {...this.state} />
      // return <WrappedComponent x={this.state.x} y={this.state.y} />
    }
  }
  return Mouse
}
```



#### 演示1:创建位置组件

```js
//1. 演示1 位置组件
const Position = props => { 
  console.log(props) // 增强之前props是没有值的
  return <p>x:{props.x} y:{props.y}</p>
}

// 如何使用? 
// 增强后 = withMouse(增强前)
HOC_Position =   withMouse(Position)

// 渲染 
ReactDOM.render(<HOC_Position/>, document.getElementById('root'))
```

#### 演示2 : 创建移动猫组件

```js
//2.演示2 : 移动猫
const Cat = props => {
  console.log(props);
  
  return <img style={{ position:"absolute", left:props.x-64, top:props.y-64 }} src={cat} alt=""/>
}

// 使用高阶组件增强一下
HOC_Cat = withMouse(Cat)

// 渲染 
ReactDOM.render(<HOC_Cat/>, document.getElementById('root'))
```



#### 高阶组件分析

- 高阶组件名称 约定 **以 with 开头**
- 指定函数参数，参数应该以大写字母开头（作为要被包装的组件）
- 在函数内部创建一个类组件，提供复用的状态逻辑代码，并返回
- 在该组件中，渲染参数组件，同时将状态通过prop传递给参数组件
- 调用该高阶组件，传入要增强的组件，通过返回值拿到增强后的组件，并将其渲染到页面中

```js
const withMouse = (WrappedComponent) => {
  class Mouse extends React.Component {    
    ... 省略鼠标位置状态 和 操作鼠标位置的方法逻辑
    render() {
      return <WrappedComponent {...this.state} />      #  核心
    }
  }
  return Mouse
}
```



# 原理揭秘

## setState 的[说明](https://zh-hans.reactjs.org/docs/react-component.html#setstate)

#### 1.异步更新数据

- setState()  是异步更新数据的
- 为什么是异步的 ? 
  - 因为 setState() 可能同时被调用多次，如果是同步的话，状态就会更新多次，
  - 也就是页面要发生多次渲染，也就是发生多次重绘和重排，这样的话，会降低应用的性能。

```js
state = { 
    count: 0
}  

 handle2 = () => {
    console.log(this.state.count)  // 0
   
    this.setState({
      count: this.state.count + 1,
    })
    // for (var i = 0; i < 1000; i++) {
    //   this.setState({
    //     count: i
    //   })
    // }

    console.log(this.state.count)  // 0
  }
```



#### 2. 合并更新

- 当你调用 setState 的时候，React.js 并不会马上修改 state （为什么）

- 而是把这个对象放到一个更新队列里面

- 稍后才会从队列当中把新的状态提取出来合并到 state 当中，然后再触发组件更新。
  可以多次调用 setState() ，只会触发一次重新渲染

```js
handle = () => {
    this.setState({
      name: 'ls',
    })

    this.setState({
       name: 'zl',
      age: 90,
    })
  }
  
  componentDidUpdate() {
    console.warn('更新了')   // 这里会调几次呢????
  }
```



> 如果我们想要立马拿到最新的数据怎么办呢?

#### 3. setState 的第一种格式 :  setState(对象, [callback])

- **格式 :  setState( 对象, 回调 )**

- [**官**] : callback 它将在 `setState` 完成合并并重新渲染组件后执行。
- 通常，我们建议使用 `componentDidUpdate()` 来代替此方式。

```js
this.setState({
    count : this,state.count + 1
},() => {
    console.log('这个回调函数会在状态更新后立即执行',this.state.count)
})
```



#### 4. 演示问题

- **代码**

```js
console.log('前', this.state.count) // 0
// 异步更新
this.setState({
  count: this.state.count + 1  // 以为是 1
})
// 异步更新  + 获取 结果
this.setState(
  {
    count: this.state.count + 1 // 以为是 2
  },
  () => {
    console.log(this.state.count) // 以为是 2 但是结果是1
  }
)
// 两次更新 发现结果还都是 1 , 
```

- **分析**

```js
# [官] 这种形式的 setState() 也是异步的，并且在同一周期内会对多个 setState 进行批处理。
# [官] 后调用的 setState() 将覆盖同一周期内先调用 setState 的值，因此商品数仅增加一次。
let newObj = Object.assign({}, obj, { age: 20 }, { age: 30 })

# [官] 如果后续状态取决于当前状态，我们建议使用 updater 函数的形式代替：
```



#### 5. setState 的第二种格式 :  setState(updater, [callback])

- **格式 :  setState(函数式, 回调)**
- **函数式** : 函数里面返回一个对象

```js
 // 异步更新 
// 这个为什么就可以了, 因为是通过参数获取的, 是 React 控制 的,,返回的就是上次更新的
this.setState((state, props) => {
    return {
        count: state.count + 1  // 拿到最新的 0 + 1 = 1
    }
})
// 异步更新  + 获取 结果
// 这个为什么就可以了, 因为是通过参数获取的, 是 React 控制 的,,返回的就是上次更新的
this.setState(
    (state, props) => {
        return {
            count: state.count + 1 //拿到最新的1 + 1 = 2
        }
    },
    () => {
        console.log(this.state.count) // 结果是2
    }
)
```

- 简写

```js
// 也可以简写
this.setState((state) => ({
        count: state.count + 1  // 以为是 1
      })
 )
```



#### 总结

```js
// 第一种格式 :  setState(stateChnage, [callback])  ★
               setSrare(对象, 回调)

// 第二种格式 :setState(updater, [callback])  ★
              setSrare(函数式, 回调)

// 最常用的还是 第一种格式的简化操作  setState(stateChange)   ★★★
this.setState({
    count : this.state.count + 1
})
```



## JSX 语法的转化过程 (了解)

> 演示  : babel中文网试一试 let h1 = <div></div>

- JSX 仅仅是createElement() 方法的语法糖 (简化语法)
- JSX 语法 被 @babel/preset-react  插件编译为 createElement() 方法
- React 元素：是一个对象，用来描述你希望在屏幕上看到的内容
- React 元素 最后 被  `ReactDOM.render(<Child/>,document.getElementById('root'))` 渲染显示到页面

![](mg-images/jsx.png)

- 演示:

```js
render () {
    
    const el = <h1 className='greeting'>Hello JSX</h1>
   
    console.log(el);
    
    return el
}

react更新机制 
简化 : 虚拟DOM
然后呢?? 
1. jsx => 虚拟DOM  => 真实的DOM
2. jsx数据发生变化 => 新的虚拟DOM
3. 新的虚拟DOM 和 旧的虚拟DOM对比 => 通过Diff算法找到有差异的地方 => 更新1步骤的真实的DOM


```



# 性能优化

## 组件更新机制

- **父组件重新渲染时, 也会重新染当前组件子树** 

![](mg-images/更新机制.png)

#### 演示代码 : 

- App > P1+P2> C1+C2

```js
import React from 'react'
import ReactDOM from 'react-dom'

//2. 类组件
class App extends React.Component {
  state = {}
  render() {
    return (
      <div style={{ background: 'pink', display: 'flex' }}>
        <P1></P1>
        <P2></P2>
      </div>
    )
  }
}
class P1 extends React.Component {
  render() {
    return (
      <div style={{ background: 'red', flex: 1 }}>
        <p>P1</p>
      </div>
    )
  }
}
class P2 extends React.Component {
  state = {
    name: 'zs',
  }
  render() {
    return (
      <div style={{ background: 'skyblue', flex: 1 }}>
        <p onClick={this.handle}>P2- {this.state.name}</p>
        <C1></C1>
        <C2></C2>
      </div>
    )
  }
  handle = () => {
    this.setState({
      name: 'ls',
    })
  }
}
class C1 extends React.Component {
  render() {
    console.log('child1 渲染了')
    return (
      <div style={{ background: 'yellow' }}>
        <p>child1</p>
      </div>
    )
  }
}
class C2 extends React.Component {
  render() {
    console.log('child2 渲染了')
    return (
      <div style={{ background: 'lime' }}>
        <p>child2</p>
      </div>
    )
  }
}

ReactDOM.render(<App />, document.getElementById('root'))

```

- 这样效果是出来了,但是它确实有很严重的性能问题, 因为子组件都没有任何变化,如果重新渲染,那么就会重新调用render() 渲染页面, 有损性能
- 所以需要进行处理 组件性能优化



## 组件性能优化

#### 优化1: 减轻 state 

- **原则** : state 中 只存储跟组件渲染相关的数据 (比如 : count / 列表数据 等) 
- 不用做渲染的数据 不要放在 state 中,  (比如  定时器 id ) 

> 渲染有关的 : 在 render() 里面用到的

```js
class Hello extends React.Component {
  state = {
    // id: 100,
    name: 'zs',
  }
  id = 100
  render() {
    console.warn('render调用了')
    return (
      <div>
        <p>{this.state.name}</p>
        <button onClick={this.handle}>按钮</button>
      </div>
    )
  }
  handle = () => {
    
    this.id = 300
    
    // this.setState({  造成多余的重新渲染
    //   id : 300
    // })
    // this.setState({   合情合理
    //   name: 'ls',
    // })

    clearInterval(this.timer)
  }

  componentDidMount() {
    this.timer = setInterval(() => {
      console.log('好嗨哟')
    }, 500)
  }
}

```



#### 优化2 :  避免不必要的重新渲染

- **组件更新机制** : 父组件更新会引起子组件也被更新
- **问题** : 子组件没有任何变化时, 也会重新渲染, 这就造成了不必要的重新渲染
- **如何避免**不必要的重新渲染呢 ? 
- **解决方式** : 使用 钩子函数` **shouldComponentUpdate(nextProps, nextState)**
  - nextProps  : 最新的属性
  - nextState : 最新的状态
  - **场景** : 比较更新前后的 state 或者 props 是否相同, 来决定是否 更新组件
- **作用** :  通过返回值 决定该组件是否需要重新渲染, 返回true ,表示重新渲染, false 表示不重新渲染
- **触发时机** : 更新阶段的钩子函数, 组件重新渲染   **前**   执行 
  - 顺序 :  shouldComponentUpdate()   ==> render()  ==>  componentDidMount()
- **演示** : 点击父组件的计算器的数据count

```js
// 演示1 : 子组件里 通过 shouldComponentUpdate()  { return true or false } 
	- true-更新
	- false-不更新

// 演示2 : 获取 父传过来的属性, 奇数更新,偶数不更新,  
  shouldComponentUpdate(nextProps,nextState)
	  - nextProps : 最新的属性
    - nextState : 最新的状态
    if(nextState.count % 2 == 0) {
        return false    
     }else { 
        return true
     }

// 演示3 : 就一个组件里有 number 值, 点击产生随机值, 比较前后生成的随机值是否一致,不一致就更细, 一致就不需要更新
// count : Math.floor(Math.random()*3)
 shouldComponentUpdate(nextProps,nextState) {
     
     //          上一个的值                  最新的值
     console.log(this.state.count === nextState.count)
     
     // 本次的 nextState.number
     // 上一次的 this.state.number
     //  或者 通过 if..else..判断 
     return this.state.count !== nextState.count
 }
```



#### 优化3 : 纯组件 - PureComponent

- 纯组件
- **作用** : 自动实现了 shouldComponentUpdate() 钩子函数, 不需要再手动对比更新前后的props 或者 state , 来阻止不必要的更新了
- **原理** : PureComponent 内部, 会别对比更新前后的props 以及更新前后的state , 只要有一个不同, 就会让组件更新, 只有在两者都相同的情况下, 才会阻止组件更新

```js
class Hello extends React.PureComponent {}
// 把那个方法 shouldComponentUpdate() 删除掉, 已经 PureComponent已经封装好了
```



#### PureComponent 内部原理

> 参考 : API Reference => React => React.PureComponent

- [PureComponent](https://react-1251415695.cos-website.ap-chengdu.myqcloud.com/docs/react-api.html#reactpurecomponent)
- 说明 : PureComponent 内部会比较更新前后的 props 和 state 分别进行浅对比

- **对于简单/值类型来说, 比较两个值是否相同 (直接赋值即可, 没有坑)**

```js
// 将 React.Component 替换为： React.PureComponent
class Hello extends React.PureComponent {
  state = {
    // number 就是一个普通的值类型的数据
    number: 0
  }

  handleClick = () => {
    const newNumber = Math.floor(Math.random() * 3)
   console.log(newNum);
    // 对于 值类型 来说，没有任何坑，直接使用即可
    // 更新前的 number：0
    // 更新后的 number：2
    // PureComponent 内部会进行如下对比：
    // 更新前的number === 更新后的number
    //    如果这两个值相同，内部，相当于在 shouldComponentUpdate 钩子函数中返回 false ，阻止组件更新。
    //    如果这两个值不同，内部，相当于在 shouldComponentUpdate 钩子函数中返回 true ，更新组件。
    this.setState({
      number: newNumber
    })
  }

  render() {
    return (
      <div>
        <h1>随机数：{this.state.number}</h1>
        <button onClick={this.handleClick}>随机生成</button>
      </div>
    )
  }
}
```

- **对于引用类型来说, 只比较对象的引用(地址) 是否相同**
  - **造成的结果** : 对象里 的数据变化,不更新

```js
const { obj } = this.state

let newNum = Math.floor(Math.random() * 3)
console.log(newNum);

// 虽然value 值 也可以从 react-dev-tools 里调试 value值确实都变了,但是 obj 一直没有变
obj.value = newNum

this.setState({
  obj 
})

```

- **正确做法** :   根据现有状态生成一个新对象, 然后再更新状态

```js
const newObj = {...this.state.obj} // 创建一个新对象
newObj.value = Math.floor(Math.random() * 3)
this.setState({
    obj: newObj
})
```

- **正确做法说明:** 
  - 在 PureComponent 中 使用引用类型 的状态时, 应该每次都创建一个新的状态, 而不是直接修改当前状态
  - 因为PureComponent 是浅对比, 所以,如果直接修改当前对象中的属性, 会造成: 对象中的值变了, 但是引用地址没有改变, 而导致组件不会被更新, 这样的话就出现bug了
- **注意 , 在 React 中, ( 不管是PureComponent 还是 Component ) ,  都不要直接修改引用类型的状态值, 而是要创建一个新的状态, 修改新的状态,然后再更新**



#### 在 React 组件 中更新应用类型的状态

- 文档 :  [不可变数据的力量](https://react.docschina.org/docs/optimizing-performance.html)
- 注意 : 对于**引用类型的状态**来说, 应该**创建新的状态**, 而**不要直接修改当前状态**
- **原则 : 状态不可变!!!!  数据不要修改, 直接创建新的**
- **对象状态 :** 

```js
// 对象
state = {
	obj : {
		value : 123
	}
}

//ES5
//                            新加     之前的值      修改内容
const newObj = Object.assign( {}, this.state.obj, {value : '新的值'} )
this.setState({
    obj:newObj
})
// es6 
this.setState({
    obj : {...this.state.obj, value: '新的值'}
})
```

- **数组状态** 

```js
// 数组：
state = {
  list: ['a', 'b']
}

// ES5：
this.setState({
  list: this.state.list.concat([ 'c' ]) // ['a', 'b', 'c']
})

// ES6：
this.setState({
  list: [...this.state.list, 'c']
})

// 删除数组元素：[].filter()
```



# 路由

#### 路由介绍

- 路由 :  就是一套映射规则, 是url中 `哈希值` 与 `展示视图` 之间的一种对应关系
- 为什么要学习路由 ? 
  - 现代的前端应用大多都是 SPA（单页应用程序），也就是只有一个 HTML 页面的应用程序。
  - 因为它的用户体验更好、对服务器的压力更小，所以更受欢迎。
  - 为了有效的使用单个页面来管理原来多页面的功能，前端路由 应运而生。
- 使用React路由简单来说，就是配置 **路径**   和  **组件**（配对）



````
spa缺点
 1. 学习成本大 学习路由
 2. 不利于SEO
````



#### 基本使用

- 安装 : `yarn add react-router-dom@5.3.0`

```js
// 1 导入路由中的三个组件
import { BrowerRouter , Link, Route } from 'react-router-dom'

const Hello = () => {
  // 2 使用 BrowerRouter 组件包裹整个应用（才能使用路由）
  return (
    <BrowerRouter>
      <div>
        <h1>React路由的基本使用：</h1>

        {/* 3 使用 Link 组件，创建一个导航菜单（路由入口） */}
        <Link to="/first">页面一</Link>

        {/* 4 使用 Route 组件，配置路由规则以及要展示的组件（路由出口） */}
        <Route path="/first" component={First} />
      </div>
    </BrowerRouter>
  )
}
```



#### 常用组件的使用介绍

- 引入的三个组件

```js
// HashRouter => 哈希模式 => 带 #  => 修改的是 location.hash
import { HashRouter , Link, Route } from 'react-router-dom'  //带#
// BrowserRouter => history模式 => 不带 #  => 修改的是 location.pathname
import { BrowserRouter , Link, Route } from 'react-router-dom'  //不带#
```

- BrowserRouter 组件 :  使用 Router 组件包裹整个应用 (才能使用路由)
- Link 组件 : 创建一个导航菜单 (路由入口)
  - 最终会生成一个a标签, 通过 to 属性指定 pathname（history /） 或 hash（哈希模式 #）
- Route 组件 : 用来配置路由规则和要展示的组件 (路由出口)
  - path : 配置路由规则
  - component : 指定当前路由 规则匹配时要展示的组件
  - Route 组件放在哪, 组件内容就展示在哪, 并且每一个路由都是一个单独的Route组件



#### 路由的执行过程

- 当点击 Link  的时候，就会修改浏览器中的 pathname
- 只要 浏览器地址栏中的 pathname 发生改变，React 路由就会监听到这个改变
- React 路由监听到 pathname 改变后，就会遍历所有 Route 组件，分别使用 Route 组件中的 path 路由规则，与当前的 浏览器地址栏中的pathname进行匹配
- 只要匹配成功，就会把当前 Route 对应的组件，展示在页面中
- 注意：匹配时，不是找到第一个匹配的路由就停下来了。而是： 所有的 Route 都会进行匹配，只要匹配就会展示该组件。
  - 也就是说：在一个页面中，可以有多个 Route 同时被匹配



#### 使用 Switch 组件 ,匹配一个

```js
{/* Switch 只会让 组件显示出来一个 */}
<Switch>
  <Route path="/one" component={One}></Route>
	<Route path="/two" component={Three}></Route>
	<Route path="/two" component={Two}></Route>
</Switch>
```



#### 编程式导航

-  **改变入口的三种方式 :**
-  手动输入

-  声明式导航 :  <Link to='/one'/>  (html)
   - 编程式导航 : 通过js代码来实现的跳转/返回 (js)

-  **编程式导航 :**
-  可以通过props 拿到 跳转和返回的方法

-  正常的组件, 打印 props  => 默认是 一个空对象 {}

-  凡是参与`路由匹配`出来的组件 , 路由都会给他们传入三个属性 history, location, match

-  **history : (主要用来编程式导航)**

   - push()  跳转到另外一个页面   push(path,state)
   - goBack() 返回上一个页面
   - replace() 跳转到另外一个页面

-  **location : (位置路径的)**
-  pathname : 路径
   - state : 通过跳转传递的数据

-  **match : 获取参数**

   -  params : 可以拿到动态路由里的参数  params : {id : 123}

```js
 * - push()  跳转到另外一个页面
 * - goBack() 返回上一个页面
 * - replace() 跳转到另外一个页面
 *
 * - push 和 replace 区别
 * - 	push()    跳转 - 记录访问的历史  (可逆)
 * - 	replace() 跳转 - 不记录访问的历史 (不可逆)

 * One :  <button onClick={this.jump}>跳转到two</button> 演示
 * Two :  <button onClick={this.back}>返回到One</button> 返回
```

#### 备

```js
HashRouter 传参的方式和 BrowserRouter 传参的方式不一样
 this.props.history.push({
      pathname: '/pay',
      state: {
        name: 'zs'
      }
    })
```



#### 默认路由 - 根路径  /

- 默认路由地址为：`/`
- 默认路由在进入页面的时候，就会自动匹配

```js
{/* / 表示默认路由规则 */}
<Route path="/" component={Home} />
```



#### 匹配模式

>  问题：当 Link组件的 to 属性值为 “/login”时，为什么 默认路由`/` 也被匹配成功？ 

- 默认情况下，React 路由是: **模糊匹配**模式 
- **模糊匹配**：只要 pathname 以 path 开头就会匹配成功
  - path 代表Route组件的path属性 
  - pathname 代表Link组件的to属性（也就是url中  location.pathname） 
- **精确匹配**：只有当 path 和 pathname 完全匹配时才会展示该路由
- **解决办法**  :  给 Route 组件添加 exact 属性，让其变为精确匹配模式

```js
// 添加  exact 之后, 此时，该组件只能匹配 pathname=“/” 这一种情况
<Route exact path="/" component=... />

// 再演示 : /one/two/three 
```



#### 重定向

- 需求 : `使用 重定向  '/' => '/one'`
- **方式1 : render-props**

 ```js
<Route exact path='/'
  render={ () => {
    return <Redirect to='/one' />
	 }
}></Route>
 ```



- **方式2 - children**

```js
<Route exact path='/'>
  <Redirect to='/one' />
</Route>
```



## 路由两种模式的说明

### 哈希模式

```js
 1. 访问路径 :   http://localhost:8080/#/one 
                http://localhost:8080/#/two

 2. 服务器接收到的 (服务器是不会 接收 # 后面的内容的)
 3. 不管访问的路径是什么样的 
     http://localhost:8080  ==> 服务器返回的默认 的就是  index.html

4. 后面的 /one 和 /two 由路由来使用, 根据路由匹配规则找到对应的组件显示
5. 哈希模式 不管是 开发阶段还是发布阶段,都是没有问题的
```



### history 模式

```js
1. 访问路径 :  http://localhost:8080/one 
              http://localhost:8080/two

2. 服务器接收到的 http://localhost:8080/one  和 http://localhost:8080/two
 		但是，/one 和 /two 这个路径是不需要服务器端做任何处理的。
3.  http://localhost:8080/getNews
    http://localhost:8080/detail 
    它们都是 接口地址  , 后面遇到 类似 /one 和 /two 都会以为是接口 是要返回数据的呢?
3. 所以，应该在服务器端添加一个路由配置，直接返回 SPA 的 index.html 页面就行啦。
4. 类似处理
    app.get('/getNews', (req,res) => {
    // 根据 res 返回 对应的数据
      res.json { .....  }
    })
    app.get('/detail', (req,res) => {
     // 根据 res 返回 对应的数据
      res.json { .....  }
    })

    // 最后 额外再多加一个, 专门用来返回 index.html
    app.use('*', (req,res) => {
      res.sendFile('index.html')
    })
    
```

#### 总结 : 

```js
history模式 : 
- 开发阶段 :  webpack脚手架已经处理好了, 
- 发布阶段 : 服务器是公司的服务器, 可能就会报错
- 我们要做的就是`告诉后台`,我们使用的 是 history模式,让他专门处理一下,就可以了
- 如果后台不给处理,或者处理不好, 我们就使用 `哈希模式`
```

