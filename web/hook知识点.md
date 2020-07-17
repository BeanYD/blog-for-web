### useState的使用

1. 替代class组件中的`this.setState()`，使用时需要`import { useState } from 'react'`

2. 声明一个叫`count`的`state`的变量，`const [count, setCount] = useState(0);`，`count`的初始值为0，通过`setCount`更新`count`的值，使用过程：

    ```react
    import React, { useState } from 'react';
    
    function Example() {
      const [count, setCount] = useState(0);  return (
        <div>
          <p>You clicked {count} times</p>
          <button onClick={() => setCount(count + 1)}>
           Click me
          </button>
        </div>
      );
    }
    ```

    `[count, setCount] = useState('banana');`使用了数组解构，同时创建`count`和`setCount`两个变量

3. 可以使用多个`state`变量，取不同的名称

    ```react
    const [age, setAge] = useState(42);
    const [fruit, setFruit] = useState('banana');
    const [todos, setTodos] = useState([{ text: '学习 Hook' }]);
    ```



### useEffect`的使用

1. 相对于`class`组件，结合了`componentDidMounted`、`componentDidUpdate`、`componentDidUnmounted`方法

2. 可以和`useState`一样，拆分成多个不同业务的`useEffect`进行使用

3. 每次重新渲染后，都会调用该`hook`

4. `react`在组件卸载时执行清除操作，即相对于`class`组件在`componentDidUnmounted`中进行业务处理，如下

```react
useEffect(() => {
    ...;
    return () => {
        ...;
    }
})
```

5. 性能优化方面：在某些特定值相同，渲染后页面未发生改变，可以通过

```react
useEffect(
    () => {
        document.title = `{count}`
    }, [count]
);
```

的方式跳过对`effect`的调用。对于清除操作的`effect`同样适用

```react
useEffect(
    () => {
        document.title = `{count}`
        return () => {
            xxx(props.xxid)
        }
    }, [props.xxid]
);
```

6. 只想执行一次effect（仅在组件挂载和卸载时执行），可以传递一个空数组（`[]`）作为第二个参数

```react
useEffect(() => {
    console.log('我在执行');
}, []);
useEffect(() => {
    return () => {
    	console.log('我在卸载');
    }
}, []);
```

仅在不使用`props`和`state`中的值时使用。如果需要用到组件作用域中的值，应使用上述5中的方式调用，保证安全性

推荐启用 [`eslint-plugin-react-hooks`](https://www.npmjs.com/package/eslint-plugin-react-hooks#installation) 中的 [`exhaustive-deps`](https://github.com/facebook/react/issues/14920) 规则。此规则会在添加错误依赖时发出警告并给出修复建议。