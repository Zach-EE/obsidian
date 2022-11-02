## Blogs
- [Mastering Mutations in React Query](https://tkdodo.eu/blog/mastering-mutations-in-react-query)

## [Queries](https://tanstack.com/query/v4/docs/guides/queries)
```tsx
import { QueryClient, QueryClientProvider, useQuery } from '@tanstack/react-query'  

const queryClient = new QueryClient()

function Example() {
const query = useQuery(['todos'], fetchTodos) 

return (

	<div>
		{query.isLoading
		? 'Loading...'
		: query.isError
		? 'Error!'
		: query.data
		? query.data.map((todo) => <div key={todo.id}>{todo.title}</div>)
		: null}
	</div>
	)
} 

function App () {
	return (
		<QueryClientProvider client={queryClient}>
			<Example />
		</QueryClientProvider>
	)
}
```

### Query Basics:

A declarative dependency on an asynchronous source of data that is tied to a **unique key**, and can be used with any Promise based method. If Method modifies server data it is recommended to using Mutations

Subscribe to query components and custom hooks call `useQuery` with:
- a unique query key
- a function that resolves data, and throws an error
```tsx
const result = useQuery(['todos'], fetchTodoList)
```

The query can only be in one of the following states at a given moment:
- `isLoading` or `status === isLoading` - the query has no data yet
- `isError` or `status === isError` - the query encountered an error
- `isSuccess` or `status === isSuccess` - the query was successful and data is available
- `error` - if query is an `isError`, the error is available via `error` prop
- `data` - if query is `success` state, data available in `data` prop.

For most queries, it is sufficient to check for `isLoading` state, then check `isError` state, and finally assume the data is available and render successful state.

*Note: TypeScript will narrow the type of `data` correctly if you'vs checked for `loading` & `error` before accessing it*

### FetchStatus:

In addition to `status` field, the `result` object, will also get an additional `fetchStatus` prop with the following options:
- `fetchStatus === 'fetching'` - the query is currently fetching.
- `fetchStatus === 'paused'` - The query wanted to fetch, but is paused. Read more in [Network Mode Guide](https://tanstack.com/query/v4/docs/guides/network-mode)
- `fetchStatus === 'idle'` - the query is not doing anything at the moment. 
### Why two different States?

Background refetches and stale-while-revalidate logic make all combinations for `status` and `fetchStatus` possible. For example:
- a query in `success` status will usually be in `idle` fetchStatus, but it could also be in `fetching` if a background refetch is happening.
- a query that mounts and has no data will usually be in `loading` status and `fetching` fetchStatus, but it could also be `paused` if there is no network connection.

So keep in mind that a query can be in `loading` state without actually fetching data. As a rule of thumb:
- The `status` gives information about the `data`: Do we have any or not?
- The `fetchStatus` gives information about the `queryFn`: Is it running or not

## [Mutations](https://tanstack.com/query/v4/docs/guides/mutations)
Unlike queries, mutations are typically used to create/update/delete data or perform server side-effects. For this purpose, React Query exports a `useMutation` hook.

*In example below, you pass variables to the mutation function by calling the `mutate` function with a **single variable or object**
```tsx
function App() {
  const mutation = useMutation(newTodo => {
    return axios.post('/todos', newTodo)
  })

  return (
    <div>
      {mutation.isLoading ? (
        'Adding todo...'
      ) : (
        <>
          {mutation.isError ? (
            <div>An error occurred: {mutation.error.message}</div>
          ) : null}

          {mutation.isSuccess ? <div>Todo added!</div> : null}

          <button
            onClick={() => {
              mutation.mutate({ id: new Date(), title: 'Do Laundry' })
            }}
          >
            Create Todo
          </button>
        </>
      )}
    </div>
  )
}				
```

A mutation can only be in one of the following states at any given moment:

- `isIdle` of `status === 'idle'` - The mutation is currently idle or in a fresh/reset state
- `isLoading` or `status === 'loading'` - The mutation is currently running
- `isError` or `status === 'error'` - The mutation encountered error
- `isSuccess` or `status === 'success'` - The mutation was successful and mutation data is available
- `error` - prop available in event of `isError` state
- `data` - prop available in a `success` state

Alone the mutations aren't all too special but when used with `onSuccess` option, the Query Client's `invalidateQueries` and `setQueries` methods mutations become a powerful tool.

>**IMPORTANT: The `mutate` function is an asynchronous function, which means you cannot use it directly in an event callback in React 16 and earlier. If you need to access the event in `onSubmit` you need to wrap `mutate` in another function. This is due to [React event pooling.](https://reactjs.org/docs/legacy-event-pooling.html)
```tsx
// This will not work in React 16 and earlier
const CreateTodo = () => {
  const mutation = useMutation(event => {
    event.preventDefault()
    return fetch('/api', new FormData(event.target))
  })

  return <form onSubmit={mutation.mutate}>...</form>
}

// This will work
const CreateTodo = () => {
  const mutation = useMutation(formData => {
    return fetch('/api', formData)
  })
  const onSubmit = event => {
    event.preventDefault()
    mutation.mutate(new FormData(event.target))
  }

  return <form onSubmit={onSubmit}>...</form>
}
```

### Resetting Mutation State

It is sometimes necessary to clear the `error` or `data` of a mutations request. The `reset` function handles this:

```tsx
const CreateTodo = () => {
  const [title, setTitle] = useState('')
  const mutation = useMutation(createTodo)

  const onCreateTodo = e => {
    e.preventDefault()
    mutation.mutate({ title })
  }

  return (
    <form onSubmit={onCreateTodo}>
      {mutation.error && (
        <h5 onClick={() => mutation.reset()}>{mutation.error}</h5>
      )}
      <input
        type="text"
        value={title}
        onChange={e => setTitle(e.target.value)}
      />
      <br />
      <button type="submit">Create Todo</button>
    </form>
  )
}
```

### Mutation Side Effects

`useMutation` comes with helper options that allow quick and easy side-effects at any stage during the mutation life cycle. These are handy for both [invalidating and refetching queries after mutations](https://tanstack.com/query/v4/docs/guides/invalidations-from-mutations) and even [Optimistic Updates](https://tanstack.com/query/v4/docs/guides/optimistic-updates)
```tsx
useMutation(addTodo, {
  onMutate: variables => {
    // A mutation is about to happen!

    // Optionally return a context containing data to use when for example rolling back
    return { id: 1 }
  },
  onError: (error, variables, context) => {
    // An error happened!
    console.log(`rolling back optimistic update with id ${context.id}`)
  },
  onSuccess: (data, variables, context) => {
    // Boom baby!
  },
  onSettled: (data, error, variables, context) => {
    // Error or success... doesn't matter!
  },
})
```

When returning a promise in any of the callback functions it will first be awaited before the next callback is called:

```tsx
useMutation(addTodo, {
  onSuccess: async () => {
    console.log("I'm first!")
  },
  onSettled: async () => {
    console.log("I'm second!")
  },
})
```

In the event you want to **trigger additional callbacks** beyond the ones defined on `useMutation` when calling `mutate`. This can be used to trigger component-specific side effects. To do that, you can provide any of the same callback options to the `mutate` function after your mutation variable.

Supported Overrides include: `onSuccess`, `onError`, and `onSettled`. *Note that these additional callbacks will not run if the target component unmounts **before** the mutation finishes.
```tsx
useMutation(addTodo, {
  onSuccess: (data, variables, context) => {
    // I will fire first
  },
  onError: (error, variables, context) => {
    // I will fire first
  },
  onSettled: (data, error, variables, context) => {
    // I will fire first
  },
})

mutate(todo, {
  onSuccess: (data, variables, context) => {
    // I will fire second!
  },
  onError: (error, variables, context) => {
    // I will fire second!
  },
  onSettled: (data, error, variables, context) => {
    // I will fire second!
  },
})
```

### Consecutive Mutations 

There is a slight difference in handling `onSuccess`, `onError`, `onSettled` callbacks when it comes to consecutive mutations. When passed to the `mutate` function, they will be fired up only *once* and only if the component is still mounted. This is due to the fact that mutations observer is removed and resubscribed every time the `mutate` function is called. On the contrary, `useMutation` handlers execute for each `mutate` call.

>**Be aware that most likely, `mutationFn` passed to `useMutation` is asynchronous. In that case, the order in which mutations are fulfilled may differ from the order of `mutate` function calls.

```tsx
useMutation(addTodo, {
  onSuccess: (data, error, variables, context) => {
    // Will be called 3 times
  },
})

['Todo 1', 'Todo 2', 'Todo 3'].forEach((todo) => {
  mutate(todo, {
    onSuccess: (data, error, variables, context) => {
      // Will execute only once, for the last mutation (Todo 3),
      // regardless which mutation resolves first
    },
  })
})
```

### Promises

Use `mutateAsync` instead of `mutate` to get a promise which will resolve on success or throw on an error. *Example below can be used to compose side effects*
```tsx
const mutation = useMutation(addTodo)

try {
  const todo = await mutation.mutateAsync(todo)
  console.log(todo)
} catch (error) {
  console.error(error)
} finally {
  console.log('done')
}
```

### Retry

By default React Query will not retry a mutation on error, but it is possible with the `retry` option:
```tsx
const mutation = useMutation(addTodo, {
  retry: 3,
})
```

In the event the mutations fail because the device is offline, they will be retried in the same order when the device reconnects.

### Persist Mutations

Mutations can be persisted to storage if needed and resumed at a later point. This can be done with the hydration functions:
```tsx
const queryClient = new QueryClient()

// Define the "addTodo" mutation
queryClient.setMutationDefaults(['addTodo'], {
  mutationFn: addTodo,
  onMutate: async (variables) => {
    // Cancel current queries for the todos list
    await queryClient.cancelQueries(['todos'])

    // Create optimistic todo
    const optimisticTodo = { id: uuid(), title: variables.title }

    // Add optimistic todo to todos list
    queryClient.setQueryData(['todos'], old => [...old, optimisticTodo])

    // Return context with the optimistic todo
    return { optimisticTodo }
  },
  onSuccess: (result, variables, context) => {
    // Replace optimistic todo in the todos list with the result
    queryClient.setQueryData(['todos'], old => old.map(todo => todo.id === context.optimisticTodo.id ? result : todo))
  },
  onError: (error, variables, context) => {
    // Remove optimistic todo from the todos list
    queryClient.setQueryData(['todos'], old => old.filter(todo => todo.id !== context.optimisticTodo.id))
  },
  retry: 3,
})
// Start mutation in some component:
const mutation = useMutation(['addTodo'])
mutation.mutate({ title: 'title' })

// If the mutation has been paused because the device is for example offline,
// Then the paused mutation can be dehydrated when the application quits:
const state = dehydrate(queryClient)

// The mutation can then be hydrated again when the application is started:
hydrate(queryClient, state)

// Resume the paused mutations:
queryClient.resumePausedMutations()
```

### Persisting Offline Mutations

If you persist offline mutations with the [persistQueryClient plugin](https://tanstack.com/query/v4/docs/plugins/persistQueryClient) mutations can't be resumed when the page is reloaded unless you provide a default mutation function.

This is a technical limitation. When persisting to an external storage, only the state of mutations is persisted, as functions cannot be serialized. After hydration, the component that triggers the mutation might not be mounted, so calling `resumePausedMutaions` may yield an error: `No mutationFn found`.
```tsx
const persister = createSyncStoragePersister({
  storage: window.localStorage,
})
const queryClient = new QueryClient({
  defaultOptions: {
    queries: {
      cacheTime: 1000 * 60 * 60 * 24, // 24 hours
    },
  },
})

// we need a default mutation function so that paused mutations can resume after a page reload
queryClient.setMutationDefaults(['todos'], {
  mutationFn: ({ id, data }) => {
    return api.updateTodo(id, data)
  },
})

export default function App() {
  return (
    <PersistQueryClientProvider
      client={queryClient}
      persistOptions={{ persister }}
      onSuccess={() => {
        // resume mutations after initial restore from localStorage was successful
        queryClient.resumePausedMutations()
      }}
    >
      <RestOfTheApp />
    </PersistQueryClientProvider>
  )
}
```
*[Offline Example](https://tanstack.com/query/v4/docs/examples/react/offline)*

## [Query Invalidation](https://tanstack.com/query/v4/docs/guides/query-invalidation)

Waiting for queries to become stale before they are fetched again doesn't always work, especially when you know for a fact that a query's data is out of date because of something the user has done. For this purpose `QueryClient` has an `invalidateQueries` method that lets you intelligently mark queries as stale and potentially refetch them too.
```tsx
// Invalidate every query in the cache
queryClient.invalidateQueries()
// Invalidate every query with a key that starts with `todos`
queryClient.invalidateQueries(['todos'])
```

>**Note: React Query comes with the tools to avoid the manual labor that comes with maintaining normalized caches and instead prescribes targeted invalidation, background-refetching and ultimately atomic updates**

When a query is invalidated with `invalidateQueries`, two things happen:
1. It is marked as stale. This stale state overrides any `staleTime` configurations being used in `useQuery` or related hooks
2. If the query is currently being rendered via `useQuery` or related hooks, it will also be refetched in the background

### Query Matching with `invalidateQueries`

When using APIs like `invalidateQueries` and `removeQueries` (and others that support partial query matching), you can match multiple queries by their prefix, or get really specif and match an exact query. For information on the types of filters you can use, look at [Query Filters](https://tanstack.com/query/v4/docs/guides/filters#query-filters)
Example, `todos` prefix to invalidate any queries that start with `todos` in their query key:
```tsx
import { useQuery, useQueryClient } from '@tanstack/react-query'

// Get QueryClient from the context
const queryClient = useQueryClient()

queryClient.invalidateQueries(['todos'])

// Both queries below will be invalidated
const todoListQuery = useQuery(['todos'], fetchTodoList)
const todoListQuery = useQuery(['todos', { page: 1 }], fetchTodoList)
```

You can  invalidate queries with specific variables by passing a more specific query key to the `invalidateQueries` method:
```tsx
queryClient.invalidateQueries(['todos', { type: 'done' }])

// The query below will be invalidated
const todoListQuery = useQuery(['todos', { type: 'done' }], fetchTodoList)

// However, the following query below will NOT be invalidated
const todoListQuery = useQuery(['todos'], fetchTodoList)
```

In the event of needing more granularity, a predicate function can be passed to the `invalidateQueries` method. This function will receive each `Query` instance from the query cache and allow you to return a `true` or `false` for whether you want to invalidate the quert:

```tsx
queryClient.invalidateQueries({
  predicate: query =>
    query.queryKey[0] === 'todos' && query.queryKey[1]?.version >= 10,
})

// The query below will be invalidated
const todoListQuery = useQuery(['todos', { version: 20 }], fetchTodoList)

// The query below will be invalidated
const todoListQuery = useQuery(['todos', { version: 10 }], fetchTodoList)

// However, the following query below will NOT be invalidated
const todoListQuery = useQuery(['todos', { version: 5 }], fetchTodoList)
```




