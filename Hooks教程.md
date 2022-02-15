#  Hooks 介绍



## 1. Hooks 是什么 ?

- Hooks 是 React 16.8 新增的特性，它可以让你在不编写 class(类组件) 的情况下使用 state 以及其他的 React 特性
- React 一直都提倡使用**函数组件**的，但是函数组件没有实例不能操作state，也没有生命周期函数，只有类组件才有.

![image-20211210150629840](mg-images/image-20211210150629840.png)

- 如果有一天你在编写函数组件中, 突然意识到需要向其添加一些 state， 怎么办呢?  以前的做法是必须将其它转化为 类组件, 现在呢? Hooks出现之后, 你可以在现有的函数组件中配合 Hooks 直接来操作state了
- 从功能上来看  :   **类组件 ≈ 函数组件 + Hooks**
- 在 React API中,  凡是 use 开头的  基本上都是 Hooks
  - 比如 : useState、useEffect、useCallback、useContext等等



## 2. 为什么要学习 Hooks ? 

- **之前 存在的问题**
  
  - 类组件 : 生命周期多而且难以理解，难熟练运用 this
  - 复用状态的`render-props`和高阶组件(HOC)思想理解起来不容易, 嵌套多容易出现嵌套地域
  - 同一功能,  代码逻辑, 被拆分到不同生命周期函数中 (**项目大了, 不易维护**)
    + componentDidMount ->  window.addEventListener('resize', this.fn)
    + componentWillUnmount -> window.addEventListener('resize', this.fn)
  
- **学习hooks后 :**
  
  - 生命周期可以不用学。react hooks使用全新的理念来管理组件的运作过程。 (useEffect)
  - 高阶组件不用学。React hooks能够完美解决高阶组件想要解决的问题，并且更靠谱。 (自定义hook)
  - **根据功能**而不是基于生命周期方法强制进行**代码分割**
  
  ![image-20211210153827313](mg-images/image-20211210153827313.png)

- 体验一下
  - count + 1
  - count - 1

![image-20211210153501996](mg-images/image-20211210153501996.png)



## 3. 学习建议

- Hooks 和现有代码可以同时工作，你可以渐进式地使用他们。
- 不用急着迁移到 Hooks。因为 Hooks 是在函数组件中使用的, 如果全部迁移就要重构类组件为函数组件了, 工程过大

  + **推荐：新功能用 Hooks，复杂功能 =>  可以继续用 class**



# useState



## 1. 说明

- `useState` 是允许你在 React 函数组件中添加 state 的 Hook (钩子)。
- 使用场景：当你想要在**函数组件中，使用组件状态时**，就要使用 **useState**  了



## 2. 语法

```js
    const [state, setState] = useState(initialValue)
```

- useState 就是一个函数

- 参数  :   state变量的初始值
- 返回值 :  数组, 并且通过解构数组获取仅有的两个元素
  - 第一个元素 : state 就是一个状态变量 
  - 第二个元素 :  `setState` 是一个用于修改状态的 函数



## 3. 基本使用

```js
function App() {
  // 声明变量
  // 参数1 : num 就是我们的第一个变量 state = { num : 10 }
  // 参数2 : setNum 就是修改num的方法, setState({ num : ? })  => 重新渲染App组件
  const [num, setNum] = useState(10)
  const [arr, setArr] = useState(['zs', 'ls'])
  const [obj, setObj] = useState({ name: '老王' })

  return (
    <div>
      <h3>{num}</h3>
      <button onClick={() => setNum(num + 10)}>+10</button>
      <button onClick={() => setNum(num - 10)}>-10</button>
      <button onClick={() => setArr([...arr, 'mage'])}>改数组</button>
      <button onClick={() => setObj({ ...obj, name: '老马' })}>修改对象</button>
      <p>{obj.name}</p>
      <ul>
        {arr.map((v, i) => (
          <li key={i}>{v}</li>
        ))}
      </ul>
    </div>
  )
}
```

- 执行流程

```js
 1:  import React, { useState } from 'react';
 2:  // 对外界来说 : Counter 组件就相当于 render()
 3:  function Counter () {
 4:    const [count, setCount] = useState(10);
 5:
       // 对内部来说 : return 相当于 render函数
 6:    return (
 7:      <div>
 8:        <p>  { count } </p>
 9:        <button onClick={ () => setCount( count + 10 ) }> +10 </button>
10:      </div>
11:    );
12:  }

// Counter 内部代码, 就是函数组件的主体逻辑:
// 1. 首次渲染
// 2. 数据更新, 执行主体逻辑

// 注意: useState中的初始值, 只会在首次渲染时生效, 后续的渲染, 其实拿的是最新的状态
// 说明 react内部, 会帮你记录住 state 数据状态 (闭包)
```



## 4. 注意

- 注意点1 :  初始值为函数形式

```js
 localStorage.setItem('token', '1231231231231231')
  // 声明变量
  // 参数1 : num 就是我们的第一个变量 state = { num : 10 }
  // 参数2 : setNum 就是修改num的方法, setState({ num : ? })  => 重新渲染App组件
  const [num, setNum] = useState(() => {
    return localStorage.getItem('token')
  })
```

- 注意点2 :  使用 多次 setCount

```js
<h3>{count}</h3>
<button onClick={() => add()}>+1</button>

function add() {
    // 1.拿到的是初始值 + 合并
    // setCount(count + 1)
    // setCount(count + 1)
    // 2. 依赖
    setCount(perCount => perCount + 1)
    setCount(perCount => perCount + 1)
  }
```

- 注意点3 : 不要通过 if...else 判断

```js
 let isok = true
  if (isok) {
    const [arr, setArr] = useState(['zs', 'ls'])
  }

<button onClick={() => setArr([...arr, 'mage'])}>添加王五</button>
```





# useEffect

## 1. 说明

- Effect Hook 可以让你在函数组件中执行副作用操作
  - 主作用: 根据state/props渲染
  -  副作用: 发请求, 操作localStorage, 操作dom...
    - 在函数组件中，每当DOM完成一次渲染，都会有对应的副作用执行
- 官网提示 :

```js
如果你熟悉 React class 的生命周期函数，你可以把 useEffect 看做 componentDidMount，componentDidUpdate 和 componentWillUnmount 这三个函数的组合。
```

- 个人建议 :
  - 我们要抛开生命周期的固有思维, 还要注意: 生命周期和useEffect是完全不同的。
- 思想

```js
在function组件中，每当DOM完成一次渲染，都会有对应的副作用执行，useEffect用于提供自定义的执行内容，它的第一个参数（作为函数传入）就是自定义的执行内容。为了避免反复执行，传入第二个参数（由监听值组成的数组）作为比较(浅比较)变化的依赖，比较之后值都保持不变时，副作用逻辑就不再执行。
```



## 2. 语法

### 2.1 语法格式

```js
 useEffect(callback, deps)
  - callback : 回调
  - deps : 依赖数组
```

- 回调执行
  - DOM渲染完, 执行一次 useEffect 的回调
  - 每次更新之后也会执行 useEffect 的回调
  - 总结 : 某种意义上讲，effect 更像是渲染结果的一部分 —— 每个 effect “属于”一次特定的渲染
  
- 依赖数组

  - 可以通过 deps 依赖项 来控制 回调执行的次数

  

## 3. 使用

### 3.1 基本使用 

- 每次更新之后都会调用

```js

function App() {
  let [count, setCount] = useState(10)

  // componentDidMount + componentDidUpdate
  useEffect(() => {
    console.log('-----')
  })

  // 对于外界来说 :  App ~ render
  // 对于内部来说 : return部分 ~ render 的核心
  return (
    <div>
      <h3>{count}</h3>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  )
}
```



### 3.1 修改标题案例

- 设置标题 - 类组件

```js
class App extends React.Component {
  state = {
    count: 0,
  }
  render() {
    return (
      <div>
        <h4>{this.state.count}</h4>
        <button onClick={this.add}>+1</button>
      </div>
    )
  }

  add = () => {
    this.setState({
      count: this.state.count + 1,
    })
  }

  componentDidMount() {
    document.title = `您点击了 ${this.state.count} 次`
  }

  componentDidUpdate() {
    document.title = `您点击了 ${this.state.count} 次`
  }
}
- React 会在每次渲染后都运行 Effect
```

- 设置标题-函数组件

```js
function App() {
  let [count, setCount] = useState(10)

  // componentDidMount + componentDidUpdate
  useEffect(() => {
    console.log('-----')
    document.title = `您点击了 ${count}次`
  })

  return (
    <div>
      <h3>{count}</h3>
      <button onClick={() => setCount(count + 1)}>+1</button>
    </div>
  )
}
```



## 4. 根据依赖项执行 useEffect(性能优化)

 ### 4.1 为什么要有依赖项 ?

```js
function App() {
  let [count, setCount] = useState(10)
  let [num, setNum] = useState(0)

  // componentDidMount + componentDidUpdate
  // 无 num 无关的情况下, 点击修改num, 调用回调就多余了
  useEffect(() => {
    console.log('effect调用了-----')
    document.title = `您点击了 ${count}次`
  }, [count])

  return (
    <div>
      <h3>count : {count}</h3>
      <h3>num : {num}</h3>    +++
      <button onClick={() => setCount(count + 1)}>+1</button>
      <button onClick={() => setNum(num + 1)}>+1</button>      ++++
    </div>
  )
}

```



### 4.2 依赖项的控制

> 原则 : 
>
>  依赖数组就是用来控制是否应该触发 Effect，从而能够减少不必要的计算，从而优化了性能。
>
>  具体而言，只要依赖数组中的每一项与上一次渲染相比都没有改变，那么就跳过本次 Effect 的执行
>
> 控制 ==> 1次
>
> 控制 ==> 多次
>
> 控制 ==> 无限次



- **1. 控制 ==> 1次**

```js
  useEffect(() => {
    console.log('effect调用了-----')
    document.title = `您点击了 ${count} 次`
  }, [])
  // > 不依赖任何变量
  // > 不管点谁都是只执行开始的一次, 不再调用 ==> 完全就是个 componentDidMount  
 
<h3>count : {count}</h3>
<h3>num : {num}</h3>
<button onClick={() => setNum(num + 1)}>+1</button>
<button onClick={() => setCount(count + 1)}>+1</button>
```



- **2. 控制 ==> 多次**

```js
useEffect(() => {
  console.log('effect调用了-----')
  document.title = `您点击了 ${count} 次`
}, [count])
// > 依赖 变化的count 变量     ==> 就是个 componentDidMount  + 就是个 componentDidUpdate
//  点击 count + 变化 调用 
//  点击 num + 变化 不调用

<h3>count : {count}</h3>
<h3>num : {num}</h3>
<button onClick={() => setNum(num + 1)}>+1</button>
<button onClick={() => setCount(count + 1)}>+1</button>
```



- **3. 控制 ==> 无限次**

```js
useEffect(() => {
    console.log('effect调用了-----')
    document.title = `您点击了 ${count} 次`
  })
  // > 没有依赖项
  // >  不管点击谁都会调用  ==> 也是个 componentDidMount  + 就是个 componentDidUpdate

<h3>count : {count}</h3>
<h3>num : {num}</h3>
<button onClick={() => setNum(num + 1)}>+1</button>
<button onClick={() => setCount(count + 1)}>+1</button>
```

- 场景 - 发送请求

```js
// 点击
<button onClick={() => setNum(num + 1)}>num+1</button>

// 请求
  useEffect(() => {
    axios({
      url: 'http://geek.itheima.net/v1_0/channels',
    }).then(res => {
      console.log('结果:', res.data)
    })
  },[])
//1. 不加[] 的时候,点击一次num,都会发送一次请求, 这是无限次的
//2. 回忆一下class组件中的, 发送请求 在 didMount 里面, 只会发送一次
//3. 添加[]
```



## 5.  useEffect 清理副作用

有时候，我们除了想**在 React 更新 DOM 之后运行一些额外的代码** (比如发送网络请求，手动变更 DOM，记录日志，这些都是常见的无需清除的操作)外

还有一些副作用是需要清除的。例如**订阅外部数据源， 开启定时器，注册事件**。这种情况下，清除工作是非常重要的，可以防止引起内存泄露！

问题：如何在组件卸载时，解绑事件？此时，就用到 **effect 的返回值**了

### 5.1 基本使用

```js
// 父
function Father() {
  const [isShow, setisShow] = useState(true)
  return (
    <div>
      父<button onClick={() => setisShow(!isShow)}>显示/隐藏</button>
      {isShow && <App></App>}
    </div>
  )
}

// 子 -----------------------------------------
// componentDidMount
useEffect(() => {
    console.log('订阅消息')
    
   // componentWillUnmount
    return () => {
      console.log('清除消息')
    }
}, [])
```

解释：

- effect 的返回值也是可选的，可省略。也可以返回一个清理函数，用来执行事件解绑等清理操作

- 清理函数的执行时机：

  1. 组件卸载时 
  2. effect 重新执行前 (清除干净上一次的才可以进入下一次, 不加[] )

- 推荐：一个 useEffect 只处理一个功能，有多个功能时，使用多次 useEffect 

- 优势：根据业务逻辑来拆分，相同功能的业务逻辑放在一起，而不是根据生命周期方法名称来拆分代码 

- 编写代码时，关注点集中；而不是上下翻滚来查看代码

### 5.2 多个Effect

```js
// 1. 订阅
  useEffect(() => {
    console.log('订阅消息')
    // componentWillUnmount
    return () => {
      console.log('清除消息')
    }
  }, [])

  //2. 注册事件
  useEffect(() => {
    function handleMove(e) {
      console.log('位置:', e.clientX)
    }

    window.addEventListener('mousemove', handleMove)

    return () => {
      window.removeEventListener('mousemove', handleMove)
    }
  }, [])

  //3. 定时器
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('好嗨哟')
    }, 500)

    return () => {
      clearInterval(timer)
    }
  }, [])
```



## 6. 总结

```js
//1.
useEffect(callback, [])  ==> 类似 : componentDidMount()
//2.
useEffect(callback, [count])  ==> 类似 : componentDidMount + componentDidUpdate()
//3
useEffect(()=>{
  return ()=> {
    // 卸载函数
  }
}, [])  ==> 类似 : componentWillUnmount()
```



## 7. 发送异步请求 + async/await 的 3个套路

在组件中，使用 useEffect Hook 发送请求获取数据（side effect）：

```js
useEffect(() => {
  const loadData = async () => {}
  loadData()
  
  return () => {}
}, [])
```

解释：

- 注意：**effect 只能是一个同步函数，不能使用 async**
- 因为 effect 的返回值应该是一个清理函数，React 会在组件卸载或者 effect 的依赖项变化时重新执行 
- 但如果 effect 是 async 的，此时返回值是 Promise 对象。这样的话，就无法保证清理函数被立即调用

```js
async function fn() {
  return 123
}
console.log(fn())
```

- 如果延迟调用清理函数，也就没有机会忽略过时的请求结果或取消请求
- **为了使用 async/await 语法，可以在 effect 内部创建 async 函数，并调用**

```js
// 错误演示：

// 不要给 effect 添加 async
useEffect(async () => {}, [])
```

- **实战** 

```js
useEffect(() => {
    
    // 1. 子调用 匿名函数自调用
     ;(async () => {
     let res = await axios({
       url: 'http://geek.itheima.net/v1_0/channels',
     })
       console.log(res.data)
     })()

    //2. 上面声明 下面调用
     async function hanlde() {
       let res = await axios({
         url: 'http://geek.itheima.net/v1_0/channels',
       })
       console.log(res.data)
     }
     hanlde()

    // 3.1 提出去
    hanlde()

    return () => {}
  }, [])

//3.2
async function hanlde() {
    let res = await axios({
      url: 'http://geek.itheima.net/v1_0/channels',
    })
    console.log(res.data)
  }
```



## 8.  避免请求死循环问题

- 基本演示

```js
function App() {
  const [channels, setchannels] = useState([])

  useEffect(() => {
    async function hanlde() {
      let res = await axios({
        url: 'http://geek.itheima.net/v1_0/channels',
      })
      setchannels(res.data.data.channels)
      console.log(res.data.data.channels)
    }

    hanlde()
  }, [])

  return (
    <div>
      <ul>
        {channels.map(v => {
          return <li key={v.id}>{v.name}</li>
        })}
      </ul>
    </div>
  )
}
```

- 打印 

```js
useEffect(() => {
    async function hanlde() {
      let res = await axios({
        url: 'http://geek.itheima.net/v1_0/channels',
      })
      setchannels(res.data.data.channels)
      console.log('1:', res.data.data.channels)  ++++
      console.log('2:', channels)     ++++++
    }

    hanlde()
  }, [])

// 1: [{...},{...},{...},{...} ....]
// 2: []
```

- 分析原因

```js
// App 调两次
//1. 自动渲染
//2. set 更新
function App() {
  const [channels, setchannels] = useState([])

  // 1. didMount
  // 2. 副作用
  useEffect(() => {
    // 异步
    async function hanlde() {
      let res = await axios({
        url: 'http://geek.itheima.net/v1_0/channels',
      })
      setchannels(res.data.data.channels)
      console.log('1:', res.data.data.channels)
      // 刚 setState 后 不能立马最新的值
      console.log('2:', channels)
    }

    hanlde()
  }, [])

  // render
  // 主作用
  return (
    <div>
      <ul>
        {channels.map(v => {
          return <li key={v.id}>{v.name}</li>
        })}
      </ul>
    </div>
  )
}
```

- 解决1 : 依赖项 [channels] => 但是会死循环  (或者就不打印)
- 解决2 :  在外面打印

```js
useEffec()

console.log('2:', channels)   // 小心异步顺序

return (<div/>)
```



# useRef

## 1. 说明

- `useRef` 返回一个可变的 ref 对象，返回的 ref 对象在组件的整个生命周期内保持不变。
- 用途  : 
  - 绑定DOM节点，或者React元素
  - 保持可变变量

## 2. 语法

```js
import { useRef } from 'react' // 引入

// 创建ref对象
const myRef = useRef()

// 获取
myRef.current

```

![image-20211211151251488](C:\Users\asus\AppData\Roaming\Typora\typora-user-images\image-20211211151251488.png)

## 3. 使用

### 3.1 用途1- 保持可变变量

> 在react中，无论是使用usestate来保存数据，如果这数据被修改，将会引起页面的重新渲染。
>
> 可有时候，我们想要保存的数据不大，但当数据改变的时候不想页面重新渲染，这份数据在页面多次重新渲染后仍然能保持不变。
>
> 要做到以上条件使用什么方法呢？
> 1 reducer 感觉多少还是有些麻烦，
> 2 保存到浏览器locationstoreage，取的时候也麻烦，不安全。
> 3 保存到服务器，可能你会被骂死，这么一点小数据还要麻烦后端。
> 4 保存到useref里面去。

```js
function App() {
  //1.
  const myRef = useRef(10) // 参数: 初始值

  //3.
  console.log('----', myRef.current)

  return (
    <div>
    //2.
      <button onClick={() => console.log(myRef.current++)}>+1</button>
    //4.
      <button onClick={() => getResult()}>获取</button>
    </div>
  )

  //5. 
  function getResult() {
    console.log('最新结果:', myRef.current)
  }
}
```



### 3.2  场景 : useRef 用在定时器上

- 报警告

````js
let timer;  # 报警告   Line 2:17:   'useRef' is defined but never used     => 使用 useRef
            # 放 state ? ref?   不需要更新 => ref 里面

useEffect(() => {
  timer = setInterval(() => {
    console.log('定时器')
  }, 1000)

  return () => {
    console.log('卸载')
    clearInterval(timer)
  }
}, [])
````

- 使用 useRef 用在定时器timerId 保存变量

```js
function App() {
  
  let timerRef = useRef()

  // 副作用
  useEffect(() => {
    timerRef = setInterval(() => {
      console.log('好嗨哟')
    }, 500)
  }, [])

  // 清除
  function clear() {
    clearInterval(timerRef)
  }

  return (
    <div>
      <button onClick={clear}>清除</button>
    </div>
  )
}
```



### 3.2 用途2- 绑定DOM节点

```js
function App() {
  //1.创建
  let iptRef = useRef()

  // 清除
  function getVal() {
    //3. 获取
    console.log(iptRef.current.value)
  }

  return (
    <div>
    //2. 绑定
      <input ref={iptRef} type="text" />
      <button onClick={getVal}>清除</button>
    </div>
  )
}

```



# useContext

## 1. 说明

- `useContext`，能够让我们在函数组件中使用context的能力, 替换之前的 `context.Consumer`

context: 跨多层组件传递数据

## 2. 语法

```js
const value = useContext(MyContext);
```

- 参数 :  一个 context对象
- 返回值 :  返回 Provider 提供的 value 值

### 2.3 使用说明

- 回忆 : 
  - 之前在类组件件中 , 使用 context实现从上到下跨多层组件传递数据

```js
1. 通过 createContext() 创建 context对象
   const myContext =  React.createContext() 
   
2. 在context对象中，提供了一个自带的 Provider 组件, 用来提供数据。
   <myContext.Provider value={ 'red '}>
      ....
   </myContext.Provider>

3. 在context对象中，提供了一个自带的 Consumer 组件, 用来接收数据。
    <myContext.Consumer>
      {  data => {
         console.log(data 就是传递过来的数据 )
       return <div style={{ color : data }}>Four</div>
      }}
   </myContext.Consumer>
```

 

## 3. 使用 - 跨多层组件传递数据

```js
//1. 创建一个 context  (推荐踢出去)
const context = React.createContext()

function Father() {
  //2. 提供数据

  return (
    <context.Provider value="red">
      <div>
        父<Son></Son>
      </div>
    </context.Provider>
  )
}

function Son() {
  //3. 接收数据
  const value = useContext(context)
  console.log('value:', value)

  return <div>子</div>
}

```

![image-20211211160307863](C:\Users\asus\AppData\Roaming\Typora\typora-user-images\image-20211211160307863.png)

# Todos改造 - Hooks (详见笔记)





# useReducer

## 1. 说明

- useState  的替代方案。
- 它接收一个( 形如 `(oldState, action) => newState` 的)  reducer，并返回当前的 state 以及与其配套的 `dispatch` 方法。



## 2. 语法

```js
const [state, dispatch] = useReducer(reducer, initial);
```

- 参数 :   1-reducer  2-状态初始值
- 返回值 :  当前的 state 以及与其配套的 `dispatch` 方法。



## 3. 使用

### 3.1 重写 useState (计算器示例)

- 之前的 useState

```js
import { useState } from 'react'

//2. 创建组件
const App = () => {
  const [counter, setCounter] = useState(0)

  return (
    <div>
      <div>函数组件 - {counter}</div>
      <button onClick={() => setCounter(counter + 1)}>+1</button>
      <button onClick={() => setCounter(counter - 1)}>+1</button>
    </div>
  )
}
```



- 改造的 useReducer

```js
// reducer : 修改数据的 / 干活的  ++++  ----
// action  : 指令               add   minus   : { type : 'add' }, { type : 'minus' }

// reducer ~~ 多个 setState
const reducer = (state, action) => {
  switch (action.type) {
    case 'increment':
      return state + 1
    case 'decrement':
      return state - 1
    default:
      return state
  }
}

// 2. 创建
function App() {
  // count : 仓库里面的数据
  // dispatch : 用来发号指令  dispatch(指令)  dispatch(action)  dispatch(对象)
  const [count, dispatch] = useReducer(reducer, 100)

  return (
    <div>
      <h4>展示{count}</h4>
      <button onClick={() => dispatch({ type: 'increment' })}>+1</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-1</button>
    </div>
  )
}
```





# 小插曲 : React.memo()  高阶组件

## 1. React.memo 高阶组件的使用场景说明：

React 组件更新机制：只要父组件状态更新，子组件就会无条件的一起更新。

注意：此处更新指的是组件代码执行、JSX 进行 Diff 操作（纯 JS 的操作，速度非常快，不会对性能产生太多影响）。

+ 如果组件 props 改变了，那么，该组件就必须要更新，才能接收到最新的 props。
+ 但是，如果组件 props 没有改变时，组件也要进行一次更新。实际上，这一次更新是没有必要的。

如果要避免组件 props 没有变化而进行的不必要更新，这种情况下，就要使用 React.memo 高阶组件。

```js
类组件    :   PureComponent(自动对比)    shouldComponentUpdate(手动对比)
函数组件  :   React.memo()
```

![](C:/Users/asus/Documents/WeChat Files/wxid_51yv12g6u7z321/FileStorage/File/2021-12/mg-images/组件更新.png) 

## 2. 语法

```js
const MemoChild = React.memo(Child)
```

使用场景：当你想要避免函数组件 props 没有变化而产生的不必要更新时，就要用到 React.memo 了。

作用：**记忆组件上一次的渲染结果，在 props 没有变化时复用该结果，避免函数组件不必要的更新**。

解释：

+ React.memo 是一个高阶组件，用来记忆（memorize）组件。
+ 参数（Child）：需要被记忆的组件，或者说是需要避免不必要更新的组件。
+ 返回值（MemoChild）：React 记住的 Child 组件。

原理：通过对比检查更新前后 props 是否相同，来决定是否复用上一次的渲染结果，

+ 如果相同，复用上一次的渲染结果；
+ 如果不同，重新渲染组件。

```js
import React, { useState } from 'react'
import ReactDOM from 'react-dom'

const Child2 = React.memo(({ count }) => {
  console.log('Child2 子组件代码执行了')
  return <div style={{ backgroundColor: '#abc' }}>子组件2：{count}</div>
})

const Child1 = React.memo(() => {
  console.log('Child1 子组件代码执行了')
  return <div style={{ backgroundColor: '#def' }}>子组件1</div>
})

const App = () => {
  const [count, setCount] = useState(0)

  return (
    <div style={{ backgroundColor: 'pink', padding: 10 }}>
      <h1>计数器：{count}</h1>
      <button onClick={() => setCount(count + 1)}>+1</button>
      <hr />

      {/* 子组件 */}
      <Child1 />
      <br />
      <Child2 count={count} />
    </div>
  )
}

ReactDOM.render(<App />, document.getElementById('root'))

```

![image-20211212131747744](C:/Users/asus/Documents/WeChat Files/wxid_51yv12g6u7z321/FileStorage/File/2021-12/mg-images/image-20211212131747744.png)



## 3. 第二个参数

```js
// 解释：
React.memo(Child, (preProps, nextProps) => {
  if (preProps == nextProps) {
    return false
  } else {
    return true
  }
})

// - React.memo 和 PureComponent 只能做一些浅层对比
// - 第二个参数：用来比较更新前后 props 的函数。
// - 返回值：如果返回 true，表示记住（不重新渲染）该组件；如果返回 false，表示重新渲染该组件。

```

![image-20211212013737010](C:/Users/asus/Documents/WeChat Files/wxid_51yv12g6u7z321/FileStorage/File/2021-12/mg-images/image-20211212013737010.png)

```js
const Child2 = React.memo(
  ({ count }) => {
    console.log('Child2 子组件代码执行了')
    return <div style={{ backgroundColor: '#abc' }}>子组件2：{count}</div>
  },
  (preProps, nextProps) => {
    return true
    return false
    return preProps.count == nextProps.count
  }
)


 <button onClick={() => setCount(count + 1)}>+1</button>
 <button onClick={() => setCount(count)}>+1</button>
```



# useCallback

## 1. 说明

- useCallback 可以让你在函数组件中 缓存计算函数

  

## 2. 语法

```js
const fnA = useCallback(fnB, [a])
```

- useCallback 会将我们传递给它的函数fnB返回，并且将这个结果fnA 缓存；
- 当依赖a变更时，会返回新的函数。

> 把内联回调函数及依赖项数组作为参数传入 `useCallback`，它将返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新。当你把回调函数传递给经过优化的并使用引用相等性去避免非必要渲染（例如 `shouldComponentUpdate`）的子组件时，它将非常有用。

## 3. 使用

```js
// 1. 导入
import React, { useCallback, useState } from 'react'
import ReactDOM from 'react-dom'

// 2. 创建
function App() {
  const [count, setCount] = useState(0)
  const [other, setOther] = useState(0)   

  // const inputChange = e => {
  //   console.log(e.target.value)
  // }

  // 这里面的箭头函数 依赖 count
  // 如果 count 有变化, 就会创建一个新的 ()=>{}
  // 如果 count 没有变化,
  const inputChange = useCallback(() => {
    console.log(count)
  }, [count])

  return (
    <div>
      <div>函数组件 - {count}</div>
      <input type="text" onChange={inputChange} />
      <button onClick={() => setCount(count + 1)}>改count</button>
      <button onClick={() => setOther(other + 1)}>改干扰项</button> // 跟你有啥关系?
    </div>
  )
}

// 3. 渲染
ReactDOM.render(<App></App>, document.getElementById('root'))

```



## 4. 验证

-  返回的是函数，我们无法很好的判断返回的函数是否变更，所以我们可以借助ES6新增的数据类型Set来判断，

```js
// 学习
<button onClick={test}>测试</button>
function test() {
    let set = new Set()
    set.add(1)
    set.add(2)
    set.add([])
    set.add([])
    set.add(() => {})
    set.add(() => {})
    console.log('size:', set.size)
  }

// 演示: 
// 创建一个  全局的  set
let set = new Set()   +++++++

// 2. 创建
function App() {
  const [count, setCount] = useState(0)
  const [other, setother] = useState(0)  // 后来演示干扰

  // 这里面的箭头函数 依赖 count 决定要不要缓存
  // 如果 count 有变化, 就会创建一个新的 ()=>{}
  // 如果 count 没有变化, 就使用缓存的
  let inputChange = useCallback(() => {
    console.log(count)
  }, [count])

  set.add(inputChange)
  console.log('size:', set.size)

  return (
    <div>
      <div>
        函数组件 - {count} - {num}
      </div>
      <input type="text" onChange={inputChange} />
      <button onClick={() => setCount(count + 1)}>改count</button>
      <button onClick={() => setnum(num + 1)}>改num</button>
    </div>
  )
}
```



## 5. 使用场景

```js
//2. 创建组件
const App = () => {
  const [count, setCount] = useState(0)
  // 干扰
  const [other, setOther] = useState(0)

  const inputChange = useCallback(() => {
    console.log(count)
  }, [count])

  set.add(inputChange)
  console.log('set个数', set.size)

  return (
    <div>
      <div>
        函数组件 - {count} - {other}
      </div>
      <input type="text" onChange={inputChange} />
      <button onClick={() => setCount(count + 1)}>改1</button>
      <button onClick={() => setOther(other + 1)}>改1</button>
      <Child f={inputChange}></Child>
    </div>
  )
}

// memo 是缓存外界传来的函数 不变会不更新
// 自己如果不变的话 自己就不更新了
const Child = memo(({ f }) => {
  console.log('child')

  return <div>Child组件</div>
})
```



# useMemo

## 1. 说明

- useMemo 可以让你在函数组件中 缓存计算结果

## 2. 语法

```js
const memoizedValue = useMemo(() => fn(a,b), deps[a,b])
```

- 它接收两个参数，
  -  第一个参数为回调函数  (计算过程fn，必须返回一个结果)，
  -  第二个参数是依赖项(数组)，当依赖项中某一个发生变化，结果将会重新计算。
- 注意 : 因为是缓存计算结果,所以参数1回调函数里面一定要有return返回值

## 2. 使用

- 使用 useMemo 之前
  - 问题 : 每次点击干扰项 other, 都要重新计算一下,所以需要缓存值

```js
const App = () => {
  const [count, setCount] = useState(0)
  const [other, setOther] = useState(0)

  // 累加的方法
  function calcCount(count) {
    console.log('重新计算了一次')
    let _sum = 0
    for (var i = 1; i <= count; i++) {
      _sum += i
    }
    return _sum
  }
  // 结算的结果
  let sum = calcCount(count)

  return (
    <div>
      <div>
        参数 : {count} 累加 : {sum}
      </div>
      <button onClick={() => setCount(count + 1)}>修改参数</button>
      <button onClick={() => setOther(other + 1)}>干扰项 {other}</button>  // 跟你有啥关系
    </div>
  )
}
```

- 使用 useMemo

```js
  // 累加的方法 
  function calcCount(count) {
    console.log('重新计算了一次')
    let _sum = 0
    for (var i = 1; i <= count; i++) {
      _sum += i
    }
    return _sum               #  记忆的是 _sum
  }
  // 结算的结果 
  let sum = useMemo( () => calcCount(count),  [count])      #   使用 useMemo 

  return (
    <div>
      <div>
        参数 : {count} 累加 : {sum}
      </div>
      <button onClick={() => setCount(count + 1)}>修改参数</button>
      <button onClick={() => setOther(other + 1)}>干扰项 {other}</button>
    </div>
  )
```





# 自定义 Hook

## 1. 自定义Hook1 - useMouse

- 封装 useMouse

```js
//1. 引入包
import { useState, useEffect } from 'react'

//2. 创建组件
const useMouse = () => {
  const [mouse, setMouse] = useState({ x: 0, y: 0 })

  const handleMouse = e => {
    setMouse({
      x: e.clientX,
      y: e.clientY
    })
  }

  useEffect(() => {
    window.addEventListener('mousemove', handleMouse)

    return () => {
      window.removeEventListener('mousemove', handleMouse)
    }
  }, [])

  return mouse
}

export default useMouse
```

- 使用 useMouse

```js
// 1. 引入
import useMouse from './useMouse'

const App = () => {
  // 2. 获取 坐标值
  const mouse = useMouse()

  return (
    <div>
      // 3. 使用坐标
      <div> 函数组件 {mouse.x} - {mouse.y} </div>
    </div>
  )
}
```




