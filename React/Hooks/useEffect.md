# useEffect

```js
useEffect(() => {
  async function fetchData() {
    // You can await here
    const response = await MyAPI.getData(someId);
    // ...
  }
  fetchData();
}, [someId]); // Or [] if effect doesn't need props or state
```

# 链接

- https://medium.com/trabe/react-useeffect-hook-44d8aa7cccd0
