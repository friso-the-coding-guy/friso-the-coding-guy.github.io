# Technologies

* [NodeJS]()
* [ExpressJS]()
* [MongoDB]()


# Setup
See [commit 8e2815b](https://github.com/friso-the-coding-guy/url-shortener/commit/8e2815bdb2447ed37886b70d14a64f30c58e09d2)

`npm init`

```json
{
   "name": "url-shortener",
   "version": "1.0.0",
   "description": "",
   "main": "server.js",
   "scripts": {
     "start": "node server.js",
     "test": "test"
   },
   "repository": {
     "type": "git",
     "url": "git+https://github.com/friso-the-coding-guy/url-shortener.git"
   },
   "author": "Friso the Coding Guy",
   "bugs": {
     "url": "https://github.com/friso-the-coding-guy/url-shortener/issues"
   },
   "homepage": "https://github.com/friso-the-coding-guy/url-shortener#readme"
}
```

# Adding Express
See [commit 54a8a29](https://github.com/friso-the-coding-guy/url-shortener/commit/54a8a296a9a987cb5a6d6dcb5ab49657a41069e8).

```javascript
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (request, response) => {
    response.send({
        message: "Hello World"
    })
});

app.listen(port, () => {
    console.log(`Listening on port ${port}`)
});
```

# Adding MongoDB

`mongod -dbpath db/data`