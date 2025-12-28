# React State Management: Zero to Hero

A comprehensive guide to mastering state management in React, from basic concepts to advanced patterns.

---

## 1. State Concept

State represents data that changes over time in your application. When state changes, React re-renders the component to reflect the new data.

**Key principles:**

- State is local to a component by default
- Changing state triggers a re-render
- State updates may be asynchronous
- Never mutate state directly

```jsx
// State is data that changes
// When count changes, the UI updates
function Example() {
  const [count, setCount] = useState(0);

  return <div>{count}</div>; // Shows current count
}
```

---

## 2. useState

The fundamental hook for managing state in functional components.

```jsx
import { useState } from "react";

function Counter() {
  // Declare state variable with initial value
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount((c) => c - 1)}>
        Decrement (functional update)
      </button>
    </div>
  );
}
```

**Advanced useState patterns:**

```jsx
// Complex state object
const [user, setUser] = useState({
  name: "Alice",
  age: 25,
  email: "alice@example.com",
});

// Update nested state (always create new object)
const updateEmail = (newEmail) => {
  setUser((prev) => ({
    ...prev,
    email: newEmail,
  }));
};

// Lazy initialization (runs only once)
const [data, setData] = useState(() => {
  return expensiveComputation();
});

// Array state
const [items, setItems] = useState([]);

const addItem = (item) => {
  setItems((prev) => [...prev, item]);
};

const removeItem = (id) => {
  setItems((prev) => prev.filter((item) => item.id !== id));
};
```

---

## 3. Controlled Components

Components where form data is handled by React state rather than the DOM.

```jsx
function LoginForm() {
  const [email, setEmail] = useState("");
  const [password, setPassword] = useState("");

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log({ email, password });
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="password"
        value={password}
        onChange={(e) => setPassword(e.target.value)}
        placeholder="Password"
      />
      <button type="submit">Login</button>
    </form>
  );
}
```

**Multiple inputs pattern:**

```jsx
function FormWithMultipleInputs() {
  const [formData, setFormData] = useState({
    username: "",
    email: "",
    age: "",
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData((prev) => ({
      ...prev,
      [name]: value,
    }));
  };

  return (
    <form>
      <input
        name="username"
        value={formData.username}
        onChange={handleChange}
      />
      <input name="email" value={formData.email} onChange={handleChange} />
      <input name="age" value={formData.age} onChange={handleChange} />
    </form>
  );
}
```

---

## 4. Lifting State Up

Moving state to the closest common ancestor when multiple components need to share it.

```jsx
// Parent component holds shared state
function ParentComponent() {
  const [sharedValue, setSharedValue] = useState("");

  return (
    <div>
      <ChildA value={sharedValue} onChange={setSharedValue} />
      <ChildB value={sharedValue} />
    </div>
  );
}

// Child A can modify the state
function ChildA({ value, onChange }) {
  return <input value={value} onChange={(e) => onChange(e.target.value)} />;
}

// Child B reads the same state
function ChildB({ value }) {
  return <p>You typed: {value}</p>;
}
```

**Real-world example:**

```jsx
function TemperatureConverter() {
  const [celsius, setCelsius] = useState("");

  const fahrenheit = celsius ? ((celsius * 9) / 5 + 32).toFixed(2) : "";

  return (
    <div>
      <TemperatureInput
        scale="Celsius"
        temperature={celsius}
        onTemperatureChange={setCelsius}
      />
      <TemperatureInput
        scale="Fahrenheit"
        temperature={fahrenheit}
        onTemperatureChange={(f) => setCelsius((((f - 32) * 5) / 9).toFixed(2))}
      />
    </div>
  );
}

function TemperatureInput({ scale, temperature, onTemperatureChange }) {
  return (
    <fieldset>
      <legend>{scale}</legend>
      <input
        value={temperature}
        onChange={(e) => onTemperatureChange(e.target.value)}
      />
    </fieldset>
  );
}
```

---

## 5. Prop Drilling Pain

The problem of passing props through multiple layers of components.

```jsx
// ❌ Bad: Prop drilling through multiple levels
function App() {
  const [user, setUser] = useState({ name: "Alice" });

  return <Layout user={user} setUser={setUser} />;
}

function Layout({ user, setUser }) {
  return (
    <div>
      <Header user={user} setUser={setUser} />
    </div>
  );
}

function Header({ user, setUser }) {
  return (
    <nav>
      <UserMenu user={user} setUser={setUser} />
    </nav>
  );
}

function UserMenu({ user, setUser }) {
  return (
    <div>
      <span>{user.name}</span>
      <button onClick={() => setUser({ name: "Bob" })}>Change User</button>
    </div>
  );
}

// Problems:
// 1. Layout and Header don't use user/setUser but must pass them
// 2. Hard to maintain and refactor
// 3. Components are tightly coupled
```

---

## 6. Context

React's built-in solution for sharing state across the component tree without prop drilling.

```jsx
import { createContext, useContext, useState } from "react";

// 1. Create context
const UserContext = createContext();

// 2. Create provider component
function UserProvider({ children }) {
  const [user, setUser] = useState({ name: "Alice", role: "admin" });

  const value = {
    user,
    setUser,
    updateName: (name) => setUser((prev) => ({ ...prev, name })),
  };

  return <UserContext.Provider value={value}>{children}</UserContext.Provider>;
}

// 3. Create custom hook for consuming context
function useUser() {
  const context = useContext(UserContext);
  if (!context) {
    throw new Error("useUser must be used within UserProvider");
  }
  return context;
}

// 4. Use in components
function App() {
  return (
    <UserProvider>
      <Layout />
    </UserProvider>
  );
}

function Layout() {
  return <Header />; // No props needed!
}

function Header() {
  return <UserMenu />;
}

function UserMenu() {
  const { user, updateName } = useUser();

  return (
    <div>
      <span>{user.name}</span>
      <button onClick={() => updateName("Bob")}>Change Name</button>
    </div>
  );
}
```

**Multiple contexts pattern:**

```jsx
function App() {
  return (
    <UserProvider>
      <ThemeProvider>
        <CartProvider>
          <Layout />
        </CartProvider>
      </ThemeProvider>
    </UserProvider>
  );
}
```

**Context with useReducer:**

```jsx
import { createContext, useContext, useReducer } from "react";

const CartContext = createContext();

function cartReducer(state, action) {
  switch (action.type) {
    case "ADD_ITEM":
      return { ...state, items: [...state.items, action.payload] };
    case "REMOVE_ITEM":
      return {
        ...state,
        items: state.items.filter((item) => item.id !== action.payload),
      };
    case "CLEAR":
      return { ...state, items: [] };
    default:
      return state;
  }
}

function CartProvider({ children }) {
  const [state, dispatch] = useReducer(cartReducer, { items: [] });

  return (
    <CartContext.Provider value={{ state, dispatch }}>
      {children}
    </CartContext.Provider>
  );
}
```

---

## 7. Zustand

A lightweight, modern state management library with minimal boilerplate.

**Installation:**

```bash
npm install zustand
```

**Basic usage:**

```jsx
import { create } from "zustand";

// Create store
const useStore = create((set) => ({
  // State
  count: 0,
  user: null,

  // Actions
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  setUser: (user) => set({ user }),
  reset: () => set({ count: 0, user: null }),
}));

// Use in components
function Counter() {
  const count = useStore((state) => state.count);
  const increment = useStore((state) => state.increment);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>Increment</button>
    </div>
  );
}

function UserProfile() {
  const user = useStore((state) => state.user);
  const setUser = useStore((state) => state.setUser);

  return <div>{user ? <p>Hello, {user.name}</p> : <p>Not logged in</p>}</div>;
}
```

**Advanced patterns:**

```jsx
// Async actions
const useStore = create((set, get) => ({
  posts: [],
  loading: false,
  error: null,

  fetchPosts: async () => {
    set({ loading: true, error: null });
    try {
      const res = await fetch("https://api.example.com/posts");
      const posts = await res.json();
      set({ posts, loading: false });
    } catch (error) {
      set({ error: error.message, loading: false });
    }
  },

  addPost: (post) =>
    set((state) => ({
      posts: [...state.posts, post],
    })),

  // Access current state with get()
  getCurrentCount: () => get().posts.length,
}));

// Slices pattern (organize large stores)
const createUserSlice = (set) => ({
  user: null,
  setUser: (user) => set({ user }),
  logout: () => set({ user: null }),
});

const createCartSlice = (set) => ({
  items: [],
  addItem: (item) =>
    set((state) => ({
      items: [...state.items, item],
    })),
  removeItem: (id) =>
    set((state) => ({
      items: state.items.filter((i) => i.id !== id),
    })),
});

const useStore = create((...args) => ({
  ...createUserSlice(...args),
  ...createCartSlice(...args),
}));

// Persist middleware
import { persist } from "zustand/middleware";

const useStore = create(
  persist(
    (set) => ({
      user: null,
      token: null,
      setUser: (user) => set({ user }),
      setToken: (token) => set({ token }),
    }),
    {
      name: "auth-storage", // localStorage key
    }
  )
);

// Immer middleware (easier immutable updates)
import { immer } from "zustand/middleware/immer";

const useStore = create(
  immer((set) => ({
    user: { name: "Alice", settings: { theme: "dark" } },
    updateTheme: (theme) =>
      set((state) => {
        // Mutate draft directly (immer handles immutability)
        state.user.settings.theme = theme;
      }),
  }))
);
```

---

## 8. Redux Toolkit

The official, opinionated toolset for Redux that simplifies setup and common patterns.

**Installation:**

```bash
npm install @reduxjs/toolkit react-redux
```

**Setup:**

```jsx
// store.js
import { configureStore } from "@reduxjs/toolkit";
import counterReducer from "./counterSlice";
import userReducer from "./userSlice";

export const store = configureStore({
  reducer: {
    counter: counterReducer,
    user: userReducer,
  },
});

// App.jsx
import { Provider } from "react-redux";
import { store } from "./store";

function App() {
  return (
    <Provider store={store}>
      <YourApp />
    </Provider>
  );
}
```

**Creating slices:**

```jsx
// counterSlice.js
import { createSlice } from "@reduxjs/toolkit";

const counterSlice = createSlice({
  name: "counter",
  initialState: {
    value: 0,
    history: [],
  },
  reducers: {
    increment: (state) => {
      // Redux Toolkit uses Immer, so we can "mutate" state
      state.value += 1;
      state.history.push(state.value);
    },
    decrement: (state) => {
      state.value -= 1;
      state.history.push(state.value);
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload;
      state.history.push(state.value);
    },
    reset: (state) => {
      state.value = 0;
      state.history = [];
    },
  },
});

export const { increment, decrement, incrementByAmount, reset } =
  counterSlice.actions;
export default counterSlice.reducer;

// Using in components
import { useSelector, useDispatch } from "react-redux";
import { increment, decrement, incrementByAmount } from "./counterSlice";

function Counter() {
  const count = useSelector((state) => state.counter.value);
  const history = useSelector((state) => state.counter.history);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch(increment())}>+</button>
      <button onClick={() => dispatch(decrement())}>-</button>
      <button onClick={() => dispatch(incrementByAmount(5))}>+5</button>
      <p>History: {history.join(", ")}</p>
    </div>
  );
}
```

**Async thunks:**

```jsx
// userSlice.js
import { createSlice, createAsyncThunk } from "@reduxjs/toolkit";

// Async action
export const fetchUser = createAsyncThunk("user/fetchUser", async (userId) => {
  const response = await fetch(`https://api.example.com/users/${userId}`);
  return response.json();
});

const userSlice = createSlice({
  name: "user",
  initialState: {
    data: null,
    status: "idle", // 'idle' | 'loading' | 'succeeded' | 'failed'
    error: null,
  },
  reducers: {
    clearUser: (state) => {
      state.data = null;
      state.status = "idle";
    },
  },
  extraReducers: (builder) => {
    builder
      .addCase(fetchUser.pending, (state) => {
        state.status = "loading";
      })
      .addCase(fetchUser.fulfilled, (state, action) => {
        state.status = "succeeded";
        state.data = action.payload;
      })
      .addCase(fetchUser.rejected, (state, action) => {
        state.status = "failed";
        state.error = action.error.message;
      });
  },
});

export const { clearUser } = userSlice.actions;
export default userSlice.reducer;

// Using in component
function UserProfile({ userId }) {
  const dispatch = useDispatch();
  const { data, status, error } = useSelector((state) => state.user);

  useEffect(() => {
    dispatch(fetchUser(userId));
  }, [userId, dispatch]);

  if (status === "loading") return <p>Loading...</p>;
  if (status === "failed") return <p>Error: {error}</p>;
  if (!data) return null;

  return (
    <div>
      <h2>{data.name}</h2>
      <p>{data.email}</p>
    </div>
  );
}
```

---

## 9. RTK Query

Redux Toolkit's data fetching and caching solution, eliminating most data-fetching boilerplate.

```jsx
// api.js
import { createApi, fetchBaseQuery } from "@reduxjs/toolkit/query/react";

export const api = createApi({
  reducerPath: "api",
  baseQuery: fetchBaseQuery({ baseUrl: "https://api.example.com" }),
  tagTypes: ["Post", "User"],
  endpoints: (builder) => ({
    // GET request
    getPosts: builder.query({
      query: () => "/posts",
      providesTags: ["Post"],
    }),

    // GET with params
    getPost: builder.query({
      query: (id) => `/posts/${id}`,
      providesTags: (result, error, id) => [{ type: "Post", id }],
    }),

    // POST request
    createPost: builder.mutation({
      query: (newPost) => ({
        url: "/posts",
        method: "POST",
        body: newPost,
      }),
      invalidatesTags: ["Post"],
    }),

    // PUT request
    updatePost: builder.mutation({
      query: ({ id, ...patch }) => ({
        url: `/posts/${id}`,
        method: "PUT",
        body: patch,
      }),
      invalidatesTags: (result, error, { id }) => [{ type: "Post", id }],
    }),

    // DELETE request
    deletePost: builder.mutation({
      query: (id) => ({
        url: `/posts/${id}`,
        method: "DELETE",
      }),
      invalidatesTags: ["Post"],
    }),
  }),
});

export const {
  useGetPostsQuery,
  useGetPostQuery,
  useCreatePostMutation,
  useUpdatePostMutation,
  useDeletePostMutation,
} = api;

// store.js
import { configureStore } from "@reduxjs/toolkit";
import { api } from "./api";

export const store = configureStore({
  reducer: {
    [api.reducerPath]: api.reducer,
  },
  middleware: (getDefaultMiddleware) =>
    getDefaultMiddleware().concat(api.middleware),
});

// Using in components
function PostsList() {
  const { data: posts, error, isLoading, refetch } = useGetPostsQuery();

  if (isLoading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <button onClick={refetch}>Refresh</button>
      {posts.map((post) => (
        <PostItem key={post.id} post={post} />
      ))}
    </div>
  );
}

function PostItem({ post }) {
  const [deletePost, { isLoading }] = useDeletePostMutation();

  const handleDelete = async () => {
    try {
      await deletePost(post.id).unwrap();
      alert("Post deleted!");
    } catch (err) {
      alert("Failed to delete post");
    }
  };

  return (
    <div>
      <h3>{post.title}</h3>
      <p>{post.body}</p>
      <button onClick={handleDelete} disabled={isLoading}>
        {isLoading ? "Deleting..." : "Delete"}
      </button>
    </div>
  );
}

function CreatePost() {
  const [title, setTitle] = useState("");
  const [body, setBody] = useState("");
  const [createPost, { isLoading }] = useCreatePostMutation();

  const handleSubmit = async (e) => {
    e.preventDefault();
    try {
      await createPost({ title, body }).unwrap();
      setTitle("");
      setBody("");
      alert("Post created!");
    } catch (err) {
      alert("Failed to create post");
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={title}
        onChange={(e) => setTitle(e.target.value)}
        placeholder="Title"
      />
      <textarea
        value={body}
        onChange={(e) => setBody(e.target.value)}
        placeholder="Body"
      />
      <button type="submit" disabled={isLoading}>
        {isLoading ? "Creating..." : "Create Post"}
      </button>
    </form>
  );
}
```

**Advanced features:**

```jsx
// Polling (auto-refetch)
const { data } = useGetPostsQuery(undefined, {
  pollingInterval: 3000, // Refetch every 3 seconds
});

// Skip query conditionally
const { data } = useGetUserQuery(userId, {
  skip: !userId, // Don't fetch if no userId
});

// Transforming responses
getPosts: builder.query({
  query: () => "/posts",
  transformResponse: (response) => {
    return response.map((post) => ({
      ...post,
      formattedDate: new Date(post.createdAt).toLocaleDateString(),
    }));
  },
});

// Optimistic updates
updatePost: builder.mutation({
  query: ({ id, ...patch }) => ({
    url: `/posts/${id}`,
    method: "PUT",
    body: patch,
  }),
  async onQueryStarted({ id, ...patch }, { dispatch, queryFulfilled }) {
    // Optimistically update cache
    const patchResult = dispatch(
      api.util.updateQueryData("getPost", id, (draft) => {
        Object.assign(draft, patch);
      })
    );
    try {
      await queryFulfilled;
    } catch {
      // Revert on error
      patchResult.undo();
    }
  },
});
```

---

## 10. Decision Making

Choose the right state management solution for your needs:

### useState

**Use when:**

- State is local to a single component
- Simple form inputs
- Toggle states, modals
- Small component-level data

**Example:** Button click counter, form validation, modal open/close state

### Lifting State Up

**Use when:**

- 2-3 sibling components need shared state
- Parent-child communication is simple
- State logic is straightforward

**Example:** Form with multiple inputs, filtering a list with controls in separate components

### Context API

**Use when:**

- State needs to be accessed by many components at different nesting levels
- You want to avoid prop drilling
- State changes infrequently (theme, auth, locale)
- Small to medium apps

**Pros:** Built into React, simple API, no extra dependencies  
**Cons:** Can cause unnecessary re-renders, not optimized for frequent updates

**Example:** User authentication, theme settings, language preferences

### Zustand

**Use when:**

- Medium-sized apps with moderate state complexity
- You want simplicity without Redux boilerplate
- Frequent state updates across multiple components
- You need middleware (persist, devtools)

**Pros:** Minimal boilerplate, great DX, fast, built-in middleware  
**Cons:** Smaller ecosystem than Redux

**Example:** Shopping cart, global UI state, user preferences, notifications

### Redux Toolkit

**Use when:**

- Large, complex applications
- Need time-travel debugging
- Complex state logic with many actions
- Team is familiar with Redux patterns
- Need robust middleware ecosystem

**Pros:** Predictable state updates, excellent devtools, large ecosystem  
**Cons:** More boilerplate than Zustand, steeper learning curve

**Example:** Enterprise apps, apps with complex business logic, apps needing undo/redo

### RTK Query

**Use when:**

- Your app heavily relies on server data
- You want automatic caching and synchronization
- Need optimistic updates
- Want to eliminate data-fetching boilerplate

**Pros:** Automatic caching, refetching, garbage collection  
**Cons:** Tightly coupled with Redux Toolkit

**Example:** CRUD applications, dashboards fetching API data, real-time data apps

---

## Quick Decision Tree

```
Does only one component need this state?
├─ YES → useState
└─ NO ↓

Do only 2-3 nearby components need it?
├─ YES → Lift state up
└─ NO ↓

Is it mostly server data (CRUD operations)?
├─ YES → RTK Query
└─ NO ↓

Is it infrequently changing global state?
├─ YES → Context API
└─ NO ↓

Is your app large and complex?
├─ YES → Redux Toolkit
└─ NO → Zustand
```

---

## Best Practices

1. **Start simple:** Always start with useState and lift state up. Only add complexity when needed.

2. **Collocate state:** Keep state as close as possible to where it's used.

3. **Avoid premature optimization:** Don't reach for global state management until prop drilling becomes painful.

4. **Separate server state from client state:** Consider using RTK Query or React Query for server data, and Zustand/Context for UI state.

5. **Use TypeScript:** All modern state management libraries have excellent TypeScript support.

6. **Keep actions simple:** Each action should do one thing well.

7. **Normalize complex state:** Store data in a flat structure with IDs rather than nested objects.

8. **Use selectors:** Create reusable selectors to compute derived state.

---

## Conclusion

State management in React is a journey. Start with useState, embrace lifting state up, use Context for simple global state, and graduate to Zustand or Redux Toolkit as your application grows. Remember: the best state management solution is the simplest one that solves your problem.
