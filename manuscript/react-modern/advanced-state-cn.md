> ## React Advanced State

## React状态进阶

> All state management in this application makes heavy use of React's useState Hook. More sophisticated state management gives you **React's useReducer Hook**, though. Since the concept of reducers in JavaScript splits the community in half, we won't cover it extensively here, but the exercises at the end of this section should give you plenty of practice.

虽然在这个应用中的所有状态管理都大量使用了 React 中的 useState hook。但是更复杂的 state 管理可以用 React 中的 useReducer hook。由于 JavaScript 中的 reducers 概念在社区中已有半壁江山，因此我们不会在这赘述，但在这一节的末尾会给你提供大量的练习。

> We'll move the `stories` state management from the `useState` hook to a new `useReducer` hook. First, introduce a reducer function outside of your components. A reducer function always receives `state` and `action`. Based on these two arguments, a reducer always returns a new state:

我们会用新的 `useReducer` hook 替换 `useState` hook 来管理 `stories` state。首先，在你的组件外部引入一个 reducer 函数。一个 reducer 函数总是接收 `state` 和 `action`。基于这两个参数，reducer 再返回一个新的 state。

{title="src/App.js",lang="javascript"}
~~~~~~~
# leanpub-start-insert
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
  } else {
    throw new Error();
  }
};
# leanpub-end-insert
~~~~~~~

> A reducer `action` is often associated with a `type`. If this type matches a condition in the reducer, do something. If it isn't covered by the reducer, throw an error to remind yourself the implementation isn't covered. The `storiesReducer` function covers one type, and then returns the `payload` of the incoming action without using the current state to compute the new state. The new state is simply the `payload`.

通常来说，一个 reducer 的 `action` 会关联一个 `type`。如果这个 type 与 reducer 中的条件吻合，就可以继续我们的操作。如果 reducer 中并不包含这个条件，那就抛出一个错误提醒自己这个实现没有被覆盖。`storiesReducer` 函数包含一个 type，并且没有使用当前的状态来计算出一个新的 state，而是直接返回入参 action 的 `payload`。那新的 state 就自然是 `payload` 了。

> In the App component, exchange `useState` for `useReducer` for managing the `stories`. The new hook receives a reducer function and an initial state as arguments and returns an array with two items. The first item is the *current state*; the second item is the *state updater function* (also called *dispatch function*):

在 App 组件中，用 `useReducer` 替换 `useState` 来进行 `stories` 的状态管理。新的 hook 接收一个 reducer 函数和一个初始 state 作为参数，并且返回一个包含两项内容的数组。数组的第一项是当前的 state，第二项是 state 的更新函数(也称 dispatch 函数)。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

# leanpub-start-insert
  const [stories, dispatchStories] = React.useReducer(
    storiesReducer,
    []
  );
# leanpub-end-insert

  ...
};
~~~~~~~

> The new dispatch function can be used instead of the `setStories` function, which was returned from `useState`. Instead of setting state explicitly with the state updater function from `useState`, the `useReducer` state updater function dispatches an action for the reducer. The action comes with a `type` and an optional payload:

新的 dispatch 函数可以用来代替 `useState` 中返回的 `setStories` 函数。与 `useState` 中直接设置 state 的更新函数不同，`useReducer` state 更新函数会为 reducer 匹配一个 action。action 会包含一个 `type` 和一个可选的 payload。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  React.useEffect(() => {
    setIsLoading(true);

    getAsyncStories()
      .then(result => {
# leanpub-start-insert
        dispatchStories({
          type: 'SET_STORIES',
          payload: result.data.stories,
        });
# leanpub-end-insert
        setIsLoading(false);
      })
      .catch(() => setIsError(true));
  }, []);

  ...

  const handleRemoveStory = item => {
    const newStories = stories.filter(
      story => item.objectID !== story.objectID
    );

# leanpub-start-insert
    dispatchStories({
      type: 'SET_STORIES',
      payload: newStories,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

> The application appears the same in the browser, though a reducer and React's `useReducer` hook are managing the state for the stories now. Let's bring the concept of a reducer to a minimal version by handling more than one state transition.

尽管我们现在是用 reducer 和 React 中的 `useReducer` hook 来管理 stories 的 state，但是在浏览器中的表现形式还是和之前一样的。现在我们就带入 reducer 的理念来处理多个 state 转换以实现一个轻量级的版本。

> So far, the `handleRemoveStory` handler computes the new stories. It's valid to move this logic into the reducer function and manage the reducer with an action, which is another case for moving from imperative to declarative programming. Instead of doing it ourselves by saying *how it should be done*, we are telling the reducer *what to do*. Everything else is hidden in the reducer.

到目前为止，都是 `handleRemoveStory` 处理函数计算出新的 stories。把这段逻辑移进 reducer 函数，并且以 action 来管理 reducer 是可行的，这是从命令式编程到声明式编程的另一种情况。我们只需告诉 reducer 要做什么功能而不是自己去实现。除此之外的其他内容都封装在 reducer 中。

{title="src/App.js",lang="javascript"}
~~~~~~~
const App = () => {
  ...

  const handleRemoveStory = item => {
# leanpub-start-insert
    dispatchStories({
      type: 'REMOVE_STORY',
      payload: item,
    });
# leanpub-end-insert
  };

  ...
};
~~~~~~~

> Now the reducer function has to cover this new case in a new conditional state transition. If the condition for removing a story is met, the reducer has all the implementation details needed to remove the story. The action gives all the necessary information, an item's identifier`, to remove the story from the current state and return a new list of filtered stories as state.

现在，reducer 函数必须覆盖在新的条件下满足 state 转换这种场景。如果移除 story 的条件符合，reducer 也包含所有移除 story 的实现细节，action 给出所有必要的信息：每一个 story 的标识符，那就可以在当前的 state 中移除对应的 story 并且返回一个新的 stories 筛选列表作为 state。

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
  if (action.type === 'SET_STORIES') {
    return action.payload;
# leanpub-start-insert
  } else if (action.type === 'REMOVE_STORY') {
    return state.filter(
      story => action.payload.objectID !== story.objectID
    );
  } else {
# leanpub-end-insert
    throw new Error();
  }
};
~~~~~~~

> All these if else statements will eventually clutter when adding more state transitions into one reducer function. Refactoring it to a switch statement for all the state transitions makes it more readable:

所有的 if else 语句最终会随着在一个 reducer 函数中添加更多 state 变化而变得杂乱。用 switch 来重构一下 state 转化语句，可读性会比较高： 

{title="src/App.js",lang="javascript"}
~~~~~~~
const storiesReducer = (state, action) => {
# leanpub-start-insert
  switch (action.type) {
    case 'SET_STORIES':
      return action.payload;
    case 'REMOVE_STORY':
      return state.filter(
        story => action.payload.objectID !== story.objectID
      );
    default:
      throw new Error();
  }
# leanpub-end-insert
};
~~~~~~~

> What we've covered is a minimal version of a reducer in JavaScript. It covers two state transitions, shows how to compute current state and action into a new state, and uses some business logic (removal of a story). Now we can set a list of stories as state for the asynchronously arriving data, and remove a story from the list of stories, with just one state managing reducer and its associated `useReducer` hook.
>

我们已经介绍了 JavaScript 中最简版本的 reducer。它涉及了两个 state 的改变，并展示了如何用当前 state 和 action 来计算生成新的 state ，并且使用了一些业务逻辑(删除 story)。现在我们可以将异步获取的 story 列表数据设置为 state，并且只用一个 state 管理的 reducer 结合 `useReducer` hook 就可以删除列表中的story。

> To fully grasp the concept of reducers in JavaScript and the usage of React's useReducer Hook, visit the linked resources in the exercises.

为了全面掌握 JavaScript 中 reducers 的概念以及 React 中 useReducer hook 的用法，可以访问练习中给出的链接。

> ### Exercises:

> * Confirm your [source code for the last section](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Advanced-State).
> * Confirm the [changes from the last section](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Conditional-Rendering...hs/React-Advanced-State?expand=1).
> * Read more about [reducers in JavaScript](https://www.robinwieruch.de/javascript-reducer).
> * Read more about reducers and useReducer in React ([0](https://www.robinwieruch.de/react-usereducer-hook), [1](https://reactjs.org/docs/hooks-reference.html#usereducer)).


### 练习:

* 检查 [上一节的源代码](https://codesandbox.io/s/github/the-road-to-learn-react/hacker-stories/tree/hs/React-Advanced-State).
  * 确认 [上一节之后的变更](https://github.com/the-road-to-learn-react/hacker-stories/compare/hs/React-Conditional-Rendering...hs/React-Advanced-State?expand=1).
* 了解更多[JavaScript 中的 reducers](https://www.robinwieruch.de/javascript-reducer).
* 了解更多 React 中的 reducers 和 useReducer([0](https://www.robinwieruch.de/react-usereducer-hook), [1](https://reactjs.org/docs/hooks-reference.html#usereducer)).