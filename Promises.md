## Promises

Let us upgrade ourselves with a better tool for getting html from a link, the `fetch` function.

```js
const htmlPromise = fetch("https://overreacted.io/")
console.log(htmlPromise)
```

Notice that we did not pass a callback to the `fetch` function but also it did not return us a response. Instead a promise is returned. The log shows us that the promise has two properties `[[PromiseState]]` and `[[PromiseResult]]`. The `[[PromiseState]]` can be in three states `<pending>`, `<fullfilled>` and `<rejected>`. When the `[[PromiseState]]` is `<pending>`, `[[PromiseResult]]` will be `undefined`. When `[[PromiseState]]` is `<rejected>`
 then the `[[PromiseResult]]` is an `Error`. This leaves us the last `[[PromiseState]]` which is `<fullfilled>`, this state returns in `[[PromiseResult]]` the value that we actually want.

|`[[PromiseState]]`|`[[PromiseResult]]`|
|------------------|-------------------|
|   `<pending>`    |   `undefined`     |
|   `<rejected>`   |   `Error`         |
|   `<fullfilled>` |   value           |

So instead of giving us the value directly, we got a context wrapping the value. We can get a rejected promise if our request gets an error.

```js
const maybeHtml = fetch("https://nodejs.org/en/")
console.log(maybeHtml)
```

This returns a promise which gets rejected and gives us an `Error` as it gets blocked (*\*\*cough\*\** *\*\*cough\*\** CORS). Thus we recieved an `Error` instead of our value. At a time, promise can return either an `Error` or our value. This sounds familiar! We did this in `readFile`'s callback. Still what we have is just a promise, we instead need our value. A promise has a few method inherited to access the values and errors occured. This first one we are going to look at is `then` method.

```js
htmlPromise.then((response) => console.log(response))
```

The `then` method will fire the `console.log` whenever the promise gets fullfilled. Also we get back to our old ways of splitting the program flow using callbacks. The difference is that, we provide the callback to the promise instead of the function itself, as if we have dettached the mechanism of passing the callback out of the function and into the callback. We can also handle error if we pass another argument to this method

```js
maybeHtml.then((response) => console.log(response), (error) => console.error(error))
```

If you run these lines in a console, you'll notice that it has returned a value instead of our usual `undefined`. It is another promise!!!. Although this particular promise has the value `undefined`. Let us try to get some value out of this another promise.

```js
const anotherPromise = htmlPromise.then((response) => {
  console.log(response)
  return 0
})
console.log(anotherPromise)
```

Now this promise has the value `0`. Interestingly the return value of this callback is wrapped into a promise and returned from the `then` method. Let us return something useful instead of `0`.

```js
const statusPromise = htmlPromise.then((response) => {
  return response.status
})
console.log(statusPromise)
```

Now what if we need to get the `response.status` as a value? We can use the `then` method on `statusPromise`.

```js
statusPromise.then((status) => console.log(status))
```

Since the `then` method returns a promise, we can immediately call `then` method on the returned promise thus chain them together just like array methods.

```js
htmlPromise.then((response) => response.status).then((status) => console.log(status))
```

We still need to get the HTML out of this response. We can use the `text` method on `response` to get the response body as text.

```js
htmlPromise.then((response) => {
  console.log(response.text())
})
```

If you run this in a console you'll notice something. This return a promise too!!!. Well by now we know what to do with a promise, we use the `then` method.

```js
htmlPromise.then((response) => {
  response.text().then((text) => {
    console.log(text)
  })
})
```

But this get us back to the same problem we faced with callbacks, the program flow is being split at ever function, in this case ever promise. If we return the promise, then it would be wrapped into another promise which would still require two nested `then` method.

```js
htmlPromise.then((response) => response.text())
```

Wait! This actually returned the `text` method's promise, and not a promise of a promise. Somehow JavaScript knows if the callback returns a promise. We can use this to our advantage!!! We will chain the `then` methods to get out html.

> Note: You cannot nest Promises, an attempt at such a thing would lead to a Promise with a base value. Try this out in the console
> Promise.resolve(Promise.resolve(0))


```js
htmlPromise
  .then((response) => reponse.text())
  .then((text) => console.log(text))
```

This give us a distinct advantage over callbacks, here the promise along with its `then` method chain splits the program into just two program flows. Thus we can attempt to complete our task to generate a list of unique links.
We would have to rewrite out `generateLinks` function using our new `fetch` function.

```js
function generateLinks(url) {
  return (
    fetch(url)
      .then((response) => response.text())
      .then((html) => {
        const links = []
        const regex = /<a\b.*?href="(?<link>.*?)".*?>/g
        let match = regex.exec(html)
        while (match !== null) {
          links.push(match.groups.link)
          match = regex.exec(html)
        }
        return links
      })
  )
}
```

Let us concat all the second level links into a single array

```js
generateLinks("https://overreacted.io/")
  .then((links) => {
    return links
      .filter((l) => l.startsWith('/'))
      .map((l) => generateLinks(l))
  })
```

We did manage to get all the links but they are wrapped in promises. How are we going to get all the links out of these promises? The `generateLinks`' then chain returned a promise as expected which has an array as its value.

```js
Promise { <fulfilled>: Array(34) } 
```

Now this array contains all the promises we get from the `generateLinks` we called over each link.

```js
[
  Promise { <fulfilled>: Array(38) },
  Promise { <fulfilled>: Array(30) },
  Promise { <fulfilled>: Array(25) },
    ...
  Promise { <fulfilled>: Array(0) }
]
```

We need to wait for all these promises to fulfill. Then we would need to create a new array with just the array of link extracted out from each promise. Luckily JavaScript provieds a function for this excat use case named `Promise.all`.

```js
generateLinks("https://overreacted.io/")
  .then((links) => {
    return links
      .filter((l) => l.startsWith('/'))
      .map((l) => generateLinks(l))
  })
  .then((arrayOfPromises) => Promise.all(arrayOfPromises))
  .then((arrayOfLinks) => console.log(arrayOfLinks))
```

Note that `Promise.all` returns a promise which gets fulfilled only when the all the promises in the array get fulfilled. This returned promise has the value of an array of links. We can now flatten this out and just get the unique links

```js
generateLinks("https://overreacted.io/")
  .then((links) =>
    links
      .filter((l) => l.startsWith('/'))
      .map((l) => generateLinks(l))
  )
  .then((arrayOfPromises) => Promise.all(arrayOfPromises))
  .then((arrayOfLinks) => 
    arrayOfLinks
      .flat()
      .filter((link) => link.startsWith('/'))
      .reduce((acc, link) => 
        acc.includes(link) ? acc : [...acc, link]
      )
  )
  .then((uniqueLinks) => console.log(uniqueLinks))
```

If you noticed we missed out on the first level links. We could include it in the `arrayOfPromieses` if we could some how wrap it into a promise. JavaScript provide a function for this too named `Promise.resolve`. This returns a promise which always resolves to the provided value, in this case, the first level links. We could then add this to the `arrayOfPromises`.

```js
generateLinks("https://overreacted.io/")
  .then((links) => [
    Promise.resolve(links),
    ...links
      .filter((l) => l.startsWith('/'))
      .map((l) => generateLinks(l))
  ])
  .then((arrayOfPromises) => Promise.all(arrayOfPromises))
  .then((arrayOfLinks) => 
    arrayOfLinks
      .flat()
      .filter((link) => link.startsWith('/'))
      .reduce((acc, link) => 
        acc.includes(link) ? acc : [...acc, link]
      )
  )
  .then((uniqueLinks) => console.log(uniqueLinks))
```

`Promise` has a another useful method named `catch`. It tries to catch any error occuring along the `then` method chain and executes the callback.

```js
generateLinks("https://overreacted.io/")
  .then((links) => [
    Promise.resolve(links),
    ...links
      .filter((l) => l.startsWith('/'))
      .map((l) => generateLinks(l))
  ])
  .then((arrayOfPromises) => Promise.all(arrayOfPromises))
  .then((arrayOfLinks) => 
    arrayOfLinks
      .flat()
      .filter((link) => link.startsWith('/'))
      .reduce((acc, link) => 
        acc.includes(link) ? acc : [...acc, link]
      )
  )
  .then((uniqueLinks) => console.log(uniqueLinks))
  .catch((error) => console.log(error))
```

The `Promise.resolve` function is actually a convinence function, we can get this same behaviour using the `Promise` constructor.

```js
const alwaysZero = Promise.resolve(0)
const alwaysOne = new Promise((resolve) => resolve(1))
```
The `Promise.reject` functions returns a promise which always rejects and returns the rejected value. 

```js
const neverZero = Promise.reject(Error('Nothing'))
const neverOne = new Promise((resolve, reject) => reject(Error('Nothing')))
```

Let us get back to our callback example with `readFile`. We convert the `readFile` function into a function which returns a promise

```js
function readFilePromise(file) {
  return new Promise((resolve) => {
    readFile(file, (error, data) => {
      if (data) resolve(data)
    })
  })
}

readFilePromise('./Promises.md').then((data) => console.log(data.toString('utf-8')))
```

We could also handle the error by rejecting the promise on error like this

```js
const { readFile } = require('fs')

function readFilePromise(file) {
  return new Promise((resolve, reject) => {
    readFile(file, (error, data) => {
      if (data) {
        resolve(data)
      } else {
        reject(error)
      }
    })
  })
}

readFilePromise('./Promises.md')
  .then(
    (data) => console.log(data.toString('utf-8')),
    (error) => console.error(error)
  )
```

Similarly we could convert the `writeFile` function into a promise one.

```js
const { writeFile } = require('fs');

function writeFilePromise(file) {
  return new Promise((resolve, reject) => {
    writeFile(file, (error) => {
      if (error) {
        reject(error)
      } else {
        resolve()
      }
    })
  })
}

writeFilePromise('./Promises.md', 'Insert text here')
  .then(
    () => console.log('Done!!!'),
    (error) => console.error(error)
  )
```

Finally we could rewrite our callback example of rewriting `JavaScript` into `JAVASCRIPT`.

```js
readFilePromise('./Promises.md')
  .then((data) => {
    const fileContent = data.toString('utf-8').replace('JavaScript', 'JAVASCRIPT')
    return writeFilePromise('./Promises.md', fileContent)
  })
  .then(() => console.log('Done!!!'))
```
