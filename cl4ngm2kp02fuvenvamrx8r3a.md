## Level Up Your Full-Stack Development with React Query

# Introduction

This guide will cover why React Query is an excellent tool to take your code to the next level when building full-stack applications.

Recently, I joined [thirdweb](https://thirdweb.com/) and the React SDK uses React Query all over the place! But Why? What's all the hype about?

This guide will cover:
- Why use React Query
- Reading data with React Query queries
- Writing data using React Query mutations
- Using React Query in a Next.js project

## Why use React Query?

If you've ever built an application that involves some kind of API or requesting data before, you're probably familiar with this pattern:

1. Create some stateful variables using `useState`, like `data`, `isLoading`, `isError`.
2. A `useEffect` hook that fetches some data from an API, and updates these stateful variables accordingly
3. On the UI, show a loading state while the loading flag is true, or show the data or an error when it is available.
4. Another `useEffect`, that listens for updates and keeps the state variables up-to-date.

Before you know it, you've got 50-100 lines of React code just to keep track of data for one page!

This still hasn't taken into account caching, memoization or performance optimizations like pagination!

What if you could use one hook, `useQuery` that handles all of this for you? And another hook, `useMutation` to keep your read and write operations in sync!

React Query is designed to help resolve these issues, and claims to "defeat and overcome the tricky challenges and hurdles of server state".

Let's explore it together and learn how we can improve our apps with this helpful library!


## Installation

For this project I'll be using Next.js and TypeScript:

```bash
npx create-nexct-app@latest --ts
```

Then I'll install the react-query library

```bash
yarn add react-query
```

## Making Queries

Querying refers to the process of **fetching** or "reading" data.

In React Query, a `query` is defined as:

> A declarative dependency on an asynchronous source of data that is tied to a **unique key**.


![i know some.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1655623197635/C_bIz0Te1.gif align="left")

To break that sentence down:

1. A query uses an asynchronous function that fetches some data, (e.g. `fetchPosts`)
2. Each query has a unique key that identifies itself, which is used for re-fetching and caching, (e.g. `posts`)

For example, in step 1, you might have a function that fetches a list of todos from your database called `fetchTodoList`.

In step 2, you can use any name you like to define this query, let's say `todos` is the key to fetch all todos.

Combining them together, you end up with this:

```jsx
const result = useQuery('todos', fetchTodoList)
```

Here, the `result` value contains a tonne of info, which you can de-structure as you please. It contains:

**Flags:**
- `isLoading`
- `isError`
- `isSuccess`
- `isIdle`
- `isFetching`

**Data**
- `error`
- `data` 👈If your data fetching function ran successfully, the result is in here.

Typically you might de-structure this `result` to look like this:

```jsx
const { isLoading, isError, data, error } = useQuery('todos', fetchTodoList)
```

And to take it a step further, you can re-name the `data` variable to be something meaningful:

```jsx
const { data: todos } = useQuery('todos', fetchTodoList)
```

Then, you can reflect all of these states on the UI, and show the data once it's loaded:

```jsx
 function Todos() {
   const { isLoading, isError, data, error } = useQuery('todos', fetchTodoList)
 
   if (isLoading) {
     return <span>Loading...</span>
   }
 
   if (isError) {
     return <span>Error: {error.message}</span>
   }
 
   // We can assume by this point that `isSuccess === true`
   return (
     <ul>
       {data.map(todo => (
         <li key={todo.id}>{todo.title}</li>
       ))}
     </ul>
   )
 }
```

### Query Keys

Okay, but why did we need that `key` called `todos`? Couldn't we have just used the function itself?

Here's where **caching** comes into play.

React Query is essentially building an object that contains a key-value pair for each query you make.

The key is the name of the query you made, `todos`. 

The value is the result of that query (the data).

Let's break it down step-by-step and see why this is useful:

1. You make a query to fetch all of your `todos` using the `fetchTodoList` function. Let's say you get two todos back in the result of this function.
2. React Query creates an entry into its mapping, saying "todo" is equal to those two queries.
3. Next time you need this `todos` data, you run the exact same query, using `fetchTodoList`, and the same key, `todos`.
4. React Query checks its mapping to see if it knows about this `todos` key, because *if* it does, then it can just return the data it kept knowledge of in step 2, rather than re-running the query again.

This saves your application from re-fetching data that has already been calculated, because that data has been stored in a **cache**.

Now let's imagine the query to get fetch todos takes 2 seconds.

Your app just got a whole lot faster, saving 2 seconds every time you would have re-fetched these todos from the database.


### Technically...

But wait, there's more!

Now let's imagine you have multiple users in your application.

The result of the `todos` query will be different for each user, this is why you can define the `key` to be an array, rather than just a string.

For example, currently, the key to identify our query is just "todos".

Behind the scenes, React query is actually converting this key to an array: `['todos']`.

If we wanted to have different cached results for different user we can use an array as a key instead:

**User One**:

```jsx
useQuery(['todos', 1], fetchTodoListForUser(1))
```

**User Two**:

```jsx
useQuery(['todos', 2], fetchTodoListForUser(2))
```

The array value here is serialized and used as the key value of the cache. The result? Different cached values for different users.



### Dependent Queries

Often in our applications, we might have this pattern:

1. Fetch some info
2. Fetch more info, but info that depends on info from step 1

For example:

1. Fetch a post by its ID
2. Fetch the user info for the user who posted this, using the result of step 1

Without React Query, you'd likely:

1. Fetch the post
2. Store it in a state variable, along with some loading and error flags, 
3. Create a `useEffect` to listen for when that post has been loaded
4. If that post data came back successfully, *and* has a userId field, *then*, inside another `useEffect`; fetch the user. 
5. Store the user in another state field (again, with loading and error flags)

By this point, you've easily got 50 lines of code for two read operations!

Let's compare that to React Query's **Dependent Queries**:

```jsx
 // Get the post by it's id
 const { data: post } = useQuery(['post', id], getUserByPost()
 
// Get the user id of the user that posted this post
 const userId = post?.ownerId
 
 // Then get the user information
 const { isIdle, data: user } = useQuery(
   ['user', userId],
   getUser,
   {
     // The query will not execute until the userId is available
     enabled: !!userId,
   }
 )
```

We've converted it down to less than 10 lines of code, gained caching, re-fetching, and a more error-safe way of reading multiple data sources just by implementing React Query!

## Mutations

Mutations are "write" operations (as opposed to "read" operations). 

They're used to create, update, or delete data.

The real power of React Query's mutations comes with the `onSuccess` operation.

In a real-world application (without React Query), you might do something like this:

1. Call a function that adds a piece of data
2. Once that function ran (if it succeeded), manually update the state with the new data
3. Let React re-render the page with the updated state

Let's take a look at how we can solve this pattern using React Query.

A typical mutation in React Query looks like this:

```jsx
const mutation = useMutation(postTodo)
```

Nothing special, but again, we can de-structure `mutation` to get meaningful flags and information about how our write operation is performing and where it's at.

- `isIdle`
- `isLoading`
- `isError`
- `isSuccess`
- `error`
- `data`

Let's say we have a function that allows a user to create a todo in the database called `addTodo`.

Whenever they add a todo, we need to update the query we have that fetches those todos, to let it know that there are new todos to fetch and that the result it currently has is invalid now.

To achieve this, we can combine:

- `useQueryClient`
- `invalidateQueries`
- the mutation's `onSuccess` event.

```jsx
 import { useMutation, useQueryClient } from 'react-query'
 
 const queryClient = useQueryClient()
 
 // When this mutation succeeds, invalidate any queries with the `todos` query key
 const mutation = useMutation(addTodo, {
   onSuccess: () => {
     queryClient.invalidateQueries('todos')
   },
 })
```

That's one way to do it. But this way, we're re-fetching ALL of that data again, when we really just want to let the query know that one new thing was added, so it's pretty inefficient.

For this case, we can use `setQueryData` instead, to tell our query that there has been an update, rather than telling it that the whole thing is invalid and re-fetch.

```jsx
 const queryClient = useQueryClient()
 
 const mutation = useMutation(editTodo, {
   onSuccess: data => {
     queryClient.setQueryData(['todo', { id: 5 }], data)
   }
 })
 
 mutation.mutate({
   id: 5,
   name: 'Do the laundry',
 })
 
 // The query below will be updated with the response from the
 // successful mutation
 const { status, data, error } = useQuery(['todo', { id: 5 }], fetchTodoById)
```


## Using Next.js

We've only talked about client-side querying so far.

What about server-side rendering and static site generation?

Should you use React Query for that?

Short answer: yes!

Long answer: Yes, using `prefetchQuery`, `dehydrate`, and wrapping your application inside a `QueryClientProvider` a `Hydrate` components.


**Wrapping your `_app.tsx` file**:

```jsx
import React, { useState } from "react";
import { Hydrate, QueryClient, QueryClientProvider } from "react-query";
import type { AppProps } from "next/app";
import "../styles/globals.css";

function MyApp({ Component, pageProps }: AppProps) {
  const [queryClient] = useState(() => new QueryClient());

  return (
    <QueryClientProvider client={queryClient}>
      <Hydrate state={pageProps.dehydratedState}>
        <Component {...pageProps} />
      </Hydrate>
    </QueryClientProvider>
  );
}

export default MyApp;
```

**Fetch some data on the server-side**

```jsx

 // pages/posts.jsx
 import { dehydrate, QueryClient, useQuery } from 'react-query';
 
 export async function getStaticProps() {
   const queryClient = new QueryClient()
 
   await queryClient.prefetchQuery('posts', getPosts)
 
   return {
     props: {
       dehydratedState: dehydrate(queryClient),
     },
   }
 }
```

**Hydrate the data on the client-side**:

```jsx
const Home: NextPage = () => {
  // This useQuery could just as well happen in some deeper child to
  // the "Posts"-page, data will be available immediately either way
  const { data } = useQuery("posts", getPosts);

  console.log("Data:", data);

  return <div>hello world</div>;
};
```

This uses some intimidating terms like `dehydrate` and `prefetch`, but let's break it down.

Next.js pre-renders the DOM so that the user can view the information on the page quickly, without having to wait for the query to run (we do that at build-time when we statically generate the page).

At this point in time, we have a "dehydrated" state for React Query.

> `dehydrate` creates a frozen representation of a cache that can later be hydrated with Hydrate, useHydrate, or hydrate. This is useful for passing prefetched queries from server to client or persisting queries to localStorage or other persistent locations. It only includes currently successful queries by default.

You'll notice we are returning a `dehydratedState` variable on the server-side as props to our component. This is important for the next step.

We `hydrate` the query on the client-side, using the queries inside of the `dehydratedState` variable, and hydrate them.

> hydrate adds a previously dehydrated state into a cache. If the queries included in dehydration already exist in the queryCache, hydrate does not overwrite them.

This way, we get all the benefits of the React Query library, like caching and re-fetching, but with data that originally came from the server-side.


# Conclusion

If you find yourself using `useState` and `useEffect` to manage your data, I'd suggest checking out this amazing tool, React Query!

React Query is an exciting, easy-to-implement library with excellent documentation to match. 

It achieves its goal of overcoming the tricky challenges and hurdles of managing state on both client and server, and I will be integrating it into all of my projects going forward!

This guide discusses the basics of React Query and why you might want to use it, including:
- Why use React Query
- Reading data using React Query's `queries`
- Writing data using React Query's `mutation`s
- Real-world examples of server-side rendering and client-side hydration.

# Follow Me

If you enjoyed this article, follow me on Twitter for more thoughts on full-stack development particularly in the web3 space!

%[https://twitter.com/jarrodWattsDev]




















