# Building-custom-react-hook

React Project 

Author : Smriti Pradhan Date : 18 - 02 - 2023
In this Project we will understand how to build custom hooks


1. What and Why?
2. Building a Custom Hook
3. Custom Hook Rules and Practices 
4. Realistic Examples (Building a Custom HTTP hook).

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
folder - 03-configuring-custom-hook
We will pass an arguement to the useCounter to configure/resusable it based on Forward and Backward Component.

```
import React from "react";
import { useState, useEffect } from "react";

const useCounter = (forward = true) => {
  const [counter, setCounter] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      if (forward) setCounter((prevCounter) => prevCounter + 1);
      else setCounter((prevCounter) => prevCounter - 1);
    }, 1000);

    return () => clearInterval(interval);
  }, []);

  return counter;
};

export default useCounter;

```
BackwardCounter.js
``` 
useCounter(false);

```

### Onwards to a more Realistic Example
Folder - 04-onwards-to-a-more-realistic-example

In this application we are going to send http requests. The application is like a todo in which we will add a task and then fetch the task by sending http request to the backend.Here we have the GET and POST method to fetch the data . We can use a Custom hook for both sending and getting the data as they share the same logic with some configuration . 

Steps to run this project -  
1. npm i
2. Create a Firebase account and create a Realtime Database 
3. Replace the url here with the url provided to you.
 <your url>/tasks.json 

 ### Building a Custom HTTP Hook 

Created a hook useHttp inside the hooks folder which is inside src . This hook should not be just dealing with fetching data and fetching tasks specifically because the hook would not be very reusable , it would be limited to one operation. Instead this hook should be able to send any kind of requests to any kind of URL and do any kind of data transformation but it should manage the same state loading and error and execute the same steps in the same order.

We need some parameters to configure this hook


use-http.js 
```
const response = await fetch(requestConfig.url, {
        method: requestConfig.method,
        headers: requestConfig.headers,
        body: JSON.stringify(requestConfig.body),
      });
```
We receive requestConfig and applyData function as parameters and configure the custom hook accordingly.

### Using Custom Hook 

folder - 05-building-a-custom-http-hook
```
const response = await fetch(requestConfig.url, {
        method: requestConfig.method ? requestConfig.method:'GET',
        headers: requestConfig.headers ? requestConfig.headers:{},
        body: requestConfig.body ? JSON.stringify(requestConfig.body):null,
      });
```      

Making the hook more flexible so that the component does not send any dummy data to make it work.

App.js

```
 const transformData = (taskObj) => {
    const loadedTasks = [];

    for (const taskKey in taskObj) {
      loadedTasks.push({ id: taskKey, text: taskObj[taskKey].text });
    }
    setTasks(loadedTasks);
  };

  const { isLoading, error, sendRequest: fetchTasks } = useHttp();
  //useHttp expects two things - a requestConfig and applyData function

  useEffect(() => {
    fetchTasks(
      {
        url: "https://react-post-call-default-rtdb.firebaseio.com/tasks.json",
      },
      transformData
    );
  }, []); //Need to add dependency later 

```

destructuring  { isLoading, error, sendRequest: fetchTasks } = useHttp();
and call fetchTasks.
Note - Do not forget to change the url based on your firebase account

### Adjusting the Custom Hook Logic

In the useEffect we need to add the dependency.

```
 useEffect(() => {
    fetchTasks(
      {
        url: "https://react-post-call-default-rtdb.firebaseio.com/tasks.json",
      },
      transformData
    );
  }, [fetchTasks]);
```

  In here the component is attached to the custom hook. When the function will be called and when the states are set the component where we use the custom hook will be rerendered because when we use a custom hook,which uses the state and we use the hook in that component, that component implicitely use that state setup in the custom hook. 

  When the component is re-evaluated  and that custom hook is called again. We are indeed recreating the send request function and therefore useEffect will run again.

  Behind the scenes functions are objects in Javascript . And everytime a function is recreated even it contains the same logic,its a brand new object in the memory and therefore useEffect will treat it as a new value,even if it is technically the same function and it would be re executed .

  So, to avoid this , we could wrap sendRequest inside useCallback hook.

```
  const sendRequest = useCallback(async (requestConfig, applyData) => {
    setIsLoading(true);
    setError(null);
    try {
      const response = await fetch(requestConfig.url, {
        method: requestConfig.method ? requestConfig.method:'GET',
        headers: requestConfig.headers ? requestConfig.headers:{},
        body: requestConfig.body ? JSON.stringify(requestConfig.body):null,
      });

      if (!response.ok) {
        throw new Error("Request failed!");
      }

      const data = await response.json();
      applyData(data);
     
    } catch (err) {
      console.log(err.message);
      setError(err.message || "Something went wrong!");
    }
    setIsLoading(false);
  },[])
  ```

  ```
   useEffect(() => {

    const transformData = (taskObj) => {
      const loadedTasks = [];
  
      for (const taskKey in taskObj) {
        loadedTasks.push({ id: taskKey, text: taskObj[taskKey].text });
      }
      setTasks(loadedTasks);
    };

    fetchTasks(
      {
        url: "https://react-post-call-default-rtdb.firebaseio.com/tasks.json",
      },
      transformData
    );
  }, [fetchTasks]);
```
