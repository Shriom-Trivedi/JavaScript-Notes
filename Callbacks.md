## Callbacks

Let us start with an example, `node.js` provides a function in its standard library named `readFile` which allows us to read all the bytes in a file. Let us use this function to read this file.

```js
const { readFile } = require('fs')

readFile('./Callbacks.md', (error, data) => {
  console.log(data.toString('utf-8'))
})
```

Running this code in a console would lead to two outputs, the first one is the return value of the `readFile` and the second one is our `console.log`. So, the file is not read immediately! This is because the file is read by JavaScript concurrently i.e. the program flow splits in two, one which executes the main program, other executing the function we passed to `readFile` which console logs the file content. Note that at a time, only one flow is being executed but JavaScript switches between these flows giving the illusion that both are being executed at the same time. Such a function is called a callback.

This can also result in error, say if we call the wrong file, which can also be logged.

```js
readFile('./Callbacks.txt', (error, data) => {
  if (error) console.log(error)
  if (data) console.log(data.toString('utf-8'))
})
```

At time only one of the two arguments will have their value, either `error` or `data`. We can write this again as

```js
readFile('./Callbacks.txt', (error, data) => {
  if (error) {
    console.log(error)
  } else {
    console.log(data.toString('utf-8'))
  }
})
```

Ok now what if want this data into main program? Let us make an attempt to get this data.

```js
let fileContent = null
readFile('./Callbacks.md', (error, data) => {
  fileContent = data
})
console.log(fileContent)
```

Running this code in the console line by line will log the contents of this file. Job done???. Well ...,  try to run this snippet in a script instead. You'll find the that is logs `null`. The callback hasn't been executed yet as JavaScript isn't done with reading the file. We can wait for a second and then log `fileContent`.

```js
let fileContent = null
readFile('./Callbacks.md', (error, data) => {
  fileContent = data
})
setTimeout(() => console.log(fileContent), 1000)
```

Surely this works but this brings the `console.log` into another callback (Unless the file is too big to be read in one second!!!). We are back to sqaure one. So seems like a callback splits the programs into two flows, only one containing the file content. Thus any code involving the file content must be written inside of this callback.

Let us replace all the instances of the word `JavaScript` into `JAVASCRIPT` and edit this file. We would be using another function from node.js named `writeFile`.

```js
const { readFile, writeFile } = require('fs')

readFile('./Callbacks.md', (error, data) => {
  const fileContent = data.replace('JavaScript', 'JAVASCRIPT')
  writeFile('./Callbacks.md', fileContent, (error) => {
    console.log(error ?? "Done!!")
  })
})
```

Now this makes for an interesting tree which branches out like this

```
      readFile
      /        \
     /          \
   main     writeFile
           /         \
          /           \
         /             \
   readFile cb    writeFile cb
```

So each function which requires a callback splits the program into two flows. Let us write another script to understand this. We'll write a function which given a link, reads the html in it and generates a list of all the links present in the web page. We are going to use the old XMLHttpRequest

```js
function get(url, callback) {
  const request = new XMLHttpRequest()
  request.onreadystatechange = () => { 
    if (request.readyState == 4 && request.status == 200)
      callback(request.responseText)
  }
  request.open("GET", url, true)
  request.send(null)
}

function generateLinks(url, callback) {
  get(url, (html) => {
    const links = []
    const regex = /<a\b.*?href="(?<link>.*?)".*?>/g
    let match = regex.exec(html)
    while (match !== null) {
      links.push(match.groups.link)
      match = regex.exec(html)
    }
    callback(links)
  })
}

generateLinks("https://overreacted.io/", (links) => console.log(links))
```

> Note: Because of CORS, your requests will be blocked, to make this snippet work, open this link in the browser and then open the console for this page. Paste this code in their and run :)

Let us now write a spider which would visit all these links and generate more link!!!

> Note: For our sanity (*\*\*cough\*\** *\*\*cough\*\** CORS), we would be only visiting links for the current website.

```js
generateLinks("https://overreacted.io/", (links) => {
  links
    .filter((l) => l.startsWith('/'))
    .forEach((l) => {
      generateLinks(l, (link) => {
        console.log(link)
      })
    })
})
```

We get several logs with links, but a lot of these links are repeated. We could try making a list of unique links.

```js
generateLinks("https://overreacted.io/", (links) => {
  const uniqueLinks = [];
  links
    .filter((l) => l.startsWith('/'))
    .forEach((l) => {
      if (!uniqueLinks.includes(l)) {
        uniqueLinks.push(l)
      }
      generateLinks(l, (links) => {
        links.forEach(link => {
          if (link.startsWith('/') && !uniqueLinks.includes(link)) {
            uniqueLinks.push(link)
          }
        })
      })
    })
  console.log(uniqueLinks)
})
```

If you noticed, our script only logged the first level links, the second level `generateLinks` callback weren't executed when the `uniqueLinks` was logged. Looks like we are at the limits of callbacks. We would need a better way than callbacks to get this done.
