> React Side-Effects

## React 副作用

>  Next we'll add a feature to our Search component in the form of another React hook. We'll make the Search component remember the most recent search interaction, so the application opens it in the browser whenever it restarts.

接下来我们要给 Search 组件增加一个新的功能，让 Search 组件记住最近一次搜索操作，这样每次重启应用之后，应用就会在浏览器里把它打开。

> First, use the local storage of the browser to store the `searchTerm` accompanied by an identifier. Next, use the stored value, if there a value exists, to set the initial state of the `searchTerm`. Otherwise, the initial state defaults to our initial state (here "React") as before:

首先，用浏览器的本地存储来存储 `searchTerm` 和它的标识符。然后，用存储的值，如果存在的话，作为 `searchTerm` 的初始 state。否则就和之前一样，使用我们的初始 state（这里指 "React" ）作为默认的初始值：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

 const [searchTerm, setSearchTerm] = React.useState(
# leanpub-start-insert
   localStorage.getItem('search') || 'React'
# leanpub-end-insert
 );

 const handleSearch = event => {
   setSearchTerm(event.target.value);

# leanpub-start-insert
   localStorage.setItem('search', event.target.value);
# leanpub-end-insert
 };

 ...
);
~~~~~~~

> When using the input field and refreshing the browser tab, the browser should remember the latest search term. Using the local storage in React can be seen as a **side-effect** because we interact outside of React's domain by using the browser's API.

当用户使用输入框然后刷新浏览器标签页的时候，浏览器应该可以记住最后一个搜索项。在 React 里使用本地存储可以被看作是一种**副作用**，因为我们跨出了 React 的领域去和浏览器的 API 产生了互动。

> There is one flaw, though. The handler function should mostly be concerned about updating the state, but now it has a side-effect. If we use the `setSearchTerm` function elsewhere in our application, we will break the feature we implemented because we can't be sure the local storage will also get updated. Let's fix this by handling the side-effect at a dedicated place. We'll use **React's useEffect Hook** to trigger the side-effect each time the `searchTerm` changes:

不过这里还有个问题。处理函数应该只关心如何更新 state，但它现在还有一个副作用。如果我们在应用的其他地方调用 `setSearchTerm` 就可能会破坏已有功能，因为无法确定本地缓存是否也会被更新。我们可以通过把对副作用的处理固定在某个地方来解决这个问题。这里将在每次 `searchTerm` 发生变化的时候，用到 **React 的 useEffect Hook** 来触发副作用：

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
 ...

 const [searchTerm, setSearchTerm] = React.useState(
   localStorage.getItem('search') || 'React'
 );

# leanpub-start-insert
 React.useEffect(() => {
   localStorage.setItem('search', searchTerm);
 }, [searchTerm]);
# leanpub-end-insert

# leanpub-start-insert
 const handleSearch = event => {
   setSearchTerm(event.target.value);
 };
# leanpub-end-insert

 ...
);
~~~~~~~

> React's useEffect Hook takes two arguments: The first argument is a function where the side-effect occurs. In our case, the side-effect is when the user types the `searchTerm` into the browser's local storage. The second argument is a dependency array of variables. If one variable changes, the function for the side-effect is called. In our case, the function is called every time the `searchTerm` changes; it's called initially when the component renders for the first time.

React 的 useEffect Hook 需要两个参数：第一个参数是一个会产生副作用的函数。在我们这个例子里，副作用是指当用户输入搜索词 `searchTerm` 并存进浏览器的本地存储。第二个参数是所依赖的变量数组。如果任何一个依赖的变量发生变化，包含副作用的这个函数就会被调用。对于我们来说，每次 `searchTerm` 改变都会调用这个函数；当组件第一次渲染的时候，它也会被初始化调用。

> If the dependency array of React's useEffect is an empty array, the function for the side-effect is only called once, after the component renders for the first time. The hook lets us opt into React's component lifecycle. It can be triggered when the component is first mounted, but also one of its dependencies are updated.

如果 useEffect 的依赖数组是个空数组，那么这个管理副作用的函数只会被执行一次，也就是在组件第一次渲染之后。这个 hook 使我们可以有选择性地使用 React 的组件生命周期。它会在组件第一次挂载的时候被触发，也可以在它的依赖更新时被触发。

> Using React `useEffect` instead of managing the side-effect in the handler has made the application more robust. *Whenever* and *wherever* `searchTerm` is updated via `setSearchTerm`, local storage will always be in sync with it.

使用 React 的 `useEffect` 而不是处理函数来管理副作用，会让我们的应用更加健壮。无论*何时何地* 使用 `setSearchTerm` 更新 `searchTerm`，本地存储都会同步更新。



### 练习:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Side-Effects).

> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Props-Handling...hs/React-Side-Effects?expand=1).

> * Read more about React's useEffect Hook ([0](https://reactjs.org/docs/hooks-effect.html), [1](https://reactjs.org/docs/hooks-reference.html#useeffect)).

> * Give the first argument's function a `console.log()` and experiment with React's useEffect Hook's dependency array. Check the logs for an empty dependency array too.



* 检查[上一节的源码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Side-Effects)。
	* 确认[上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/Props-Handling...hs/React-Side-Effects?expand=1)。
* 阅读更多关于 React 的 useEffect Hook 的文章：([0](https://reactjs.org/docs/hooks-effect.html), [1](https://reactjs.org/docs/hooks-reference.html#useeffect))。
* 在第一个函数参数里使用 `console.log()` 来测试 React useEffect Hook 的依赖数组。也看一下依赖数组为空的情况下，日志是什么样的。

