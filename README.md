##  Todos App - API calling in react-redux

- step 1: setup project & install packages `npm install redux react-redux redux-thunk axios`
- step 2: define constants-> src/services/constants/todosConstant.js

```js
export const GET_TODOS_REQUEST = "GET_TODOS_REQUEST";
export const GET_TODOS_SUCCESS = "GET_TODOS_SUCCESS";
export const GET_TODOS_FAILED = "GET_TODOS_FAILED";
```

- step 3: create async action creator -> src/services/actions/todosAction.js

  ```js
  import {
    GET_TODOS_FAILED,
    GET_TODOS_REQUEST,
    GET_TODOS_SUCCESS,
  } from "../constants/todosConstant";

  import axios from "axios";

  export const getAllTodos = () => async (dispatch) => {
    dispatch({ type: GET_TODOS_REQUEST });
    try {
      const res = await axios.get("https://jsonplaceholder.typicode.com/posts");
      dispatch({ type: GET_TODOS_SUCCESS, payload: res.data });
    } catch (error) {
      dispatch({ type: GET_TODOS_FAILED, payload: error.message });
    }
  };
  ```

- step 4: create reducer -> src/services/reducers/todosReducer.js

  ```js
  import {
    GET_TODOS_FAILED,
    GET_TODOS_REQUEST,
    GET_TODOS_SUCCESS,
  } from "../constants/todosConstant";

  const initialState = {
    isLoading: false,
    todos: [],
    error: null,
  };

  export const todosReducer = (state = initialState, action) => {
    switch (action.type) {
      case GET_TODOS_REQUEST:
        return {
          ...state,
          isLoading: true,
        };
      case GET_TODOS_SUCCESS:
        return {
          isLoading: false,
          todos: action.payload,
          error: null,
        };
      case GET_TODOS_FAILED:
        return {
          isLoading: false,
          todos: [],
          error: action.payload,
        };

      default:
        return state;
    }
  };
  ```

- step 5: create store -> src/store.js

  ```js
  import { applyMiddleware, createStore } from "redux";
  import thunk from "redux-thunk";
  import { todosReducer } from "./services/reducers/todosReducer";

  const store = createStore(todosReducer, applyMiddleware(thunk));
  export default store;
  ```

- step 6: provide store -> src/index.js

  ```js
  import React from "react";
  import ReactDOM from "react-dom/client";
  import "./index.css";
  import App from "./App";
  import reportWebVitals from "./reportWebVitals";

  import { Provider } from "react-redux";
  import store from "./store";

  const root = ReactDOM.createRoot(document.getElementById("root"));

  root.render(
    <Provider store={store}>
      <App />
    </Provider>
  );
  reportWebVitals();
  ```

- step 7: use store -> src/components/Todos.js

  ```js
  import React, { useEffect } from "react";
  import { useDispatch, useSelector } from "react-redux";
  import { getAllTodos } from "../services/actions/todosAction";

  const Todos = () => {
    const { isLoading, todos, error } = useSelector((state) => state);

    console.log(isLoading);

    const dispatch = useDispatch();
    useEffect(() => {
      dispatch(getAllTodos());
    }, []);

    return (
      <div>
        <h2>Todos App</h2>
        {isLoading && <h3>Loading ...</h3>}
        {error && <h3>{error.message}</h3>}
        <section>
          {todos &&
            todos.map((todo) => {
              return (
                <article key={todo.id}>
                  <h4>{todo.id}</h4>
                  <p>{todo.title}</p>
                </article>
              );
            })}
        </section>
      </div>
    );
  };
  export default Todos;
  ```

- step 8: adding style -> src/App.css

  ```css
  section {
    display: grid;
    grid-gap: 0.5rem;
    padding: 1rem;
  }

  article {
    background-color: #293462;
    color: white;
    padding: 0.5rem;
  }

  @media (min-width: 600px) {
    section {
      grid-template-columns: auto auto;
    }
  }
  @media (min-width: 768px) {
    section {
      grid-template-columns: auto auto auto;
    }
  }
  ```