# Technologies

* [NodeJS](https://nodejs.org/en/)
* [Nodemon](https://nodemon.io)
* [ExpressJS](http://expressjs.com)
* [MongoDB](https://www.mongodb.com)
* [Postman](https://www.postman.com)


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

`npm install express`

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
See [commit 8ab1447](https://github.com/friso-the-coding-guy/url-shortener/commit/8ab1447596fbc431d451ec6d9d0dd964ebf2c169)


```gitignore
# Database
db 
```

`mongod -dbpath db/data`

## Setting up MongoDB
`npm install mongodb`

```json
{
  "...",
  "dependencies": {
    "...",
    "mongodb": "^3.6.7"
  },
}
```

```javascript
const MongoClient = require('mongodb').MongoClient;
const ObjectId = require('mongodb').ObjectId;
const mongoUri = 'mongodb://localhost:27017';
const urlCollection = 'url-shortener';

const client = new MongoClient(mongoUri, {
    useNewUrlParser: true,
    useUnifiedTopology: true,

})
```


## More Express Functionality

```javascript
app.use(express.json());

app.post('/', async (request, response) => {
    const body = request.body;
    //insert into database
});

app.get('/:id', async (request, response) => {
    const id = request.params.id;
    //get from database
});
```

```json
{
  '...',
  ops: [ { url: 'https://example.com/', _id: 60ac0c12e9e13a05087b3e41 } ],
}
```

## Inserting Into MongoDB
```javascript
try {
  //connect to db
  await client.connect();
  const db = client.db();
  const urls = db.collection(urlCollection);

  // insert the data
  const result = await urls.insertOne(body)

  // return the saved object
  response.send({
    id: result.ops
  });
} catch (error) {
  // do something with the error
} finally {
  client.close();
}
```

## Find In MongoDB

```javascript
try{
  //connect to db
  await client.connect();
  const db = client.db();
  const urls = db.collection(urlCollection);

  //find saved 
  const result = await urls.findOne({ _id: ObjectId(id)});

  //return saved
  response.send(result);
} catch (error) {
  console.error(error);
  response.send(error);
} finally {
  client.close();
}
```

## Current Issue
Need to restart node after every command, client doesn't seem to close properly

SOLVED in [commit e7bb71c](https://github.com/friso-the-coding-guy/url-shortener/commit/e7bb71ce4606b78b763eec86c1c73c03041ca1f3) by using callback functions instead of awaiting asynchronous calls.

```javascript
app.post('/', (request, response) => {
    const body = request.body;
    console.log(body);

    mongoClient.connect(async (error, client) => {
        if (error) {
            console.error(error);
            response.status(400).send(error);
        }

        const db = client.db(URLDATABASE);
        const urls = db.collection(URLCOLLECTION);

        // put in the data
        urls.insertOne(body, (error, result) => {
            if (error) {
                console.error(error);
                response.status(400).send(error);
            }

            response.send({
                id: result.ops
            });
        });
    });
});

app.get('/:id', (request, response) => {
    const id = request.params.id;

    mongoClient.connect((error, client) => {
        if (error) {
            console.error(error);
            response.status(400).send(error);
        }

        const db = client.db(URLDATABASE);
        const urls = db.collection(URLCOLLECTION);

        //find saved 
        urls.findOne({ _id: OBJECTID(id)}, (error, result) => {
            if (error) {
                console.error(error);
                response.status(400).send(error);
            }

            response.send(result);
        });
    });
});
```

## MongoDB Warnings
```powershell
(node:657) [MONGODB DRIVER] Warning: the options [mode] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [tags] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [hedge] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [preference] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [isValid] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [slaveOk] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [equals] is not supported
(node:657) [MONGODB DRIVER] Warning: the options [toJSON] is not supported
```
