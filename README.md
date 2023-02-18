# Building-custom-react-hook

React Project 

Author : Smriti Pradhan Date : 18 - 02 - 2023
In this Project we will understand how to build custom hooks


1. What and Why?
2. Building a Custom Hook
3. Custom Hook Rules and Practices (Examples)

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

Both of them have similar functionality.In real application we have components that might do related tasks.And there is code duplication in forward anf backward Counter.When we find duplicate codes we need to refactor and build a function. Here we will be using custom hook to resolve the issue.

The problem we face here is the code which we want to reuse has some react hooks for example - useState() and useEffect()