# Building-custom-react-hook

React Project 

Author : Smriti Pradhan Date : 18 - 02 - 2023
In this Project we will understand how to build custom hooks


1. What and Why?
2. Building a Custom Hook
3. Custom Hook Rules and Practices 
4. Realistic Examples

--Custom hooks are in the end , just regular functions just as the built in hooks but are hooks which contain stateful logic . Outsource stateful logic into reusable functions.Unlike regular functions, custom hooks can use other React hooks, including other custom hooks.

Just Regular functions + use React hooks .

### Creating a Custom Hook Function

Here is an example application (dummy just to understand the concept of Custom Hooks).
Folder - 01-starting-project

The application has only two counters. One counting forward and another backwards.

Backward Counter
```
const [counter, setCounter] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCounter((prevCounter) => prevCounter - 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);
```  

Forward Counter
```
const [counter, setCounter] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCounter((prevCounter) => prevCounter + 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);
```  

Both of them have similar functionality.In real application we have components that might do related tasks.And there is code duplication in forward and backward Counter.When we find duplicate codes we need to refactor and build a function. Here we will be using custom hook to resolve the issue.

The problem we face here is the code which we want to reuse has some react hooks for example - useState() and useEffect().As we know we cannot use React hooks inside any random function , we need to use it inside React Component function or Custom Hooks.

So we will be building a Custom Hook.

#### How we will build a Custom Hook ?
Folder - 02-building-a-custom-hook

The function which we create has to start with "use" , that is a rule we need to follow. The use in the name signals React that it will be a custom hook and it gives React gurantee that we will use this hooks following all the rules .

use-counter.js
```
import React from "react";
import { useState , useEffect } from "react";

const useCounter = () => {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setCounter((prevCounter) => prevCounter + 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);
};

export default useCounter;

```

We used the same code as the forwardCounter Component. Now how we will use this useCounter Hook in other component?
We are going to use the custom hook the same way we use React hooks.
So, now if we call a custom hook in one of our component it registers a state or an effect as we do it will be tied to the component . The state created in the custom hook will be tied to the component where it is used.If we use custom hook in multiple components , every component will receive its own state. Just because we use custom hook does not mean we share state or effect between the components.Instead for every component the custom hook is executed again and it receives the its state. Just the logic is shared.

We will return the counter state here and in the forward counter component we can use it there.

ForwardCounter.js

```
 const counter = useCounter();

```

Now the counter works for both forward and backward counter. The backward counter using the custom hook is still pending . 

### Configuring Custom for both Forward and Backward Counter
