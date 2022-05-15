---
title: JS高阶函数
date: 2020-06-15 10:49:49
tags: 高阶函数 react-redux connect
---

## 可以接收另一个参数作为参数的函数，就叫做高阶函数。

### 常见高阶函数

1. Array.prototype.map,
2. Array.prototype.filter,
3. Array.prototype.reduce,

既然提到了这几个数组方法，那也就顺便说一说哈

- map 是啥，就不在这里说了，不知道就百度去。map() 方法的回调函数接受 3 个参数。 item index arraySelf 。 item 是数组的每一个元素 ，index 是该元素的下标（数组中的第几个元素），arraySelf 是调用 map 方法的这个数组，当然了，这些参数不是固定叫 item，index 啥的，这是入参，叫啥都行，位置对的上相应的位置就行。比如：

```
[1,2,3].map((one,two,three)=>{
  // 这里的one就对应上面我说的item ，two就是index，这样说就能明白吧？
  return one + two + three.length
})
```

map 有返回值，会返回一个新的数组，而类似的 forEach（）方法，就没有返回值，所以哟，用 forEach 的时候经常会搭配 Array.push() 😁 还有啊，写 jsx 的时候，map 遍历出来的玩意你得加个 key 值，通常可以是 index，比如哈，我举个栗子 🌰

```
<ul>
[1,2,3].map((item, index) => {
  return <li key = {index} value = {item} >item</li>
})
</ul>
```

这玩意写的好像有点多余。。。好像会 map 的都能知道，不会 map 看了我这也看不懂。😂

```
  [1,2,3].filter(item=>item > 1)

  [1,2,3].reduce((x,y)=>{
  return x + y
  })

```

react-redux

connect 方法 嘻嘻 弄一波这个～

connect 方法做的事情是将 state 和 dispatch 绑定到 Connect 组件的参数上，然后 Connect 组件将你当前的 App 组件封装起来，使得 App 组件可以通过 props 获取到父组件 Connect 传递的 state 和 props。

这也就是为什么你可以在自己写的组件上面直接通过 this.props 访问到 state 和 action。有的人是通过 store 去读取 state 和 dispatch action，也是一样的道理。

从 connect 方法的实现，我们看到了非常多 react 组件的影子，生命周期，props 传递，context 上下文。

对比 Provider 组件：

Provider 是顶层组件的作用，将 store 作为上下文提供给全局共享，而 Connect 组件是局部组件，将某个 react 组件包装起来，传递指定的 state 和 props 给该组件访问。

### 非常遗憾，我没有彻底看懂 connect 的源码，所以暂时不写什么感想了，留个链接后续再试试继续理解

[这是别人博客，connect 解析源码的](https://www.cnblogs.com/williamjie/p/9591826.html)

#### 发现了另一个博客，简化版的，大概的逻辑就看懂了，但是那个 480 行的源码还是不明白具体咋回事。

```
  export default function connect(mapStateToProps, mapDispatchToProps, mergeProps, options = {}) {
  return function wrapWithConnect(WrappedComponent) {
    class Connect extends Component {
      constructor(props, context) {
        // 从祖先Component处获得store
        this.store = props.store || context.store
        this.stateProps = computeStateProps(this.store, props)
        this.dispatchProps = computeDispatchProps(this.store, props)
        this.state = { storeState: null }
        // 对stateProps、dispatchProps、parentProps进行合并
        this.updateState()
      }
      shouldComponentUpdate(nextProps, nextState) {
        // 进行判断，当数据发生改变时，Component重新渲染
        if (propsChanged || mapStateProducedChange || dispatchPropsChanged) {
          this.updateState(nextProps)
            return true
          }
        }
        componentDidMount() {
          // 改变Component的state
          this.store.subscribe(() = {
            this.setState({
              storeState: this.store.getState()
            })
          })
        }
        render() {
          // 生成包裹组件Connect
          return (
            <WrappedComponent {...this.nextState} />
          )
        }
      }
      Connect.contextTypes = {
        store: storeShape
      }
      return Connect;
    }
  }

```
