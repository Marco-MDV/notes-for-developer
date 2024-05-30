# Back-end

### The arguments are:

- [Info](info)
- [Node](node)
- [MongoDB](mongodb)
- [Express](express)


## Info

When you talk about back-end frist think as compost, this's compost: `serevr` and `database`. (these two not are forced to be together)
in past the server and client travelled together but now thanks middleware we can split them.

the functionality of this component are:

- client: interarct with user and return a request/response to server
- server: manages the request and return a response to client
- database: return a respos to server when this is called



## Node

#### The arguments are:
- [Introduction Node.js](#Introduction-Node.js)

### Introduction Node.js
If ask you "what is node js"? The reason is more seample, Node is equal to JS (JavaScript) whit some different features.
But if you are looking manual defintion:

<img src="../assets/img/definitionNodeJS.png" width="500" />

When a developer start with Node say this's limited, if he's coming of web JS, but this is not correct. If you used JS and interacted with browser, you're using different commando as "Windows" (in web JS) in Node not are present, but you can use other command for do other as "readFileSync/writeFileSync".

When we talk about backend, we can usage more language as python, java or Node.js, lucky for us, we usage Node.js that are more sample.
in node are present different concept fundamental for a backend as: “Middleware”, “pagination”, “creation of APIs”, “validation content”, “data management” and others.

We now start with “Middleware”, what's this element? First we can talk about work flow, the basic flow is compost the request ('GET','POST','PUT','PATCH' & 'DELETE') , process the request with work that we want and finally back the results.
But only this work is more seample and not secure for our web app , we can improve 3 element for enhance our code:

- Middleware
- Validation content
- Data management


#### Middleware
If in the past we talk about work flow now this change in alternative model:

    - Old:
        ```
        1. request
            2. work request
                3. response
        ```

    - New:
        ```
        1. request
            2. middleware
                3. work request
                    4. response
        ```


Now that you know where it is allocated the middleware, it is essential that you know how to use it.
The middleware intercept the request and code inside at it and return the response, that can be an error or a response.
an example of middleware: 
1. Logging Middleware
2. Authentication Middleware
3. Authorization Middleware
4. Rate Limiting Middleware
5. Error Handling Middleware
6. Body Parsing Middleware
7. Cookie Parsing Middleware
8. Static File Serving Middleware

example of middleware:

```
    name.get('path/that/you/want', middleware, async (req, res, next)=>{
        try{
            constwork that you want
            res.json/send({result: data})
        }catch(err){
            next(err)
        }
    })
```

tip:
- "name" is name of route that we will use if using more than one route. but you can usage all route in a single file as "app.js"
- i set "get" but is onl example you can set "post", "put", "patch" and "delete"
- "path/that/you/want" is endpoi that you want

you can usage more than one same time, the syntax is: [name1, name2, name3]
example:

```
    name.get('path/that/you/want', [name1, name2, name3], async (req, res, next)=>{
        try{
            constwork that you want
            res.json/send({result: data})
        }catch(err){
            next(err)
        }
    })
```

Maybe some question that you have are:
How to set a middleware? How to import a middleware? Is essential to construct a middleware in different file?

strat to say that not essential that you creat a file only for middleware or a folder for middlewares but is a best practice for optimise the code.
but now look as you can creat a middleware:

```
    const error = (err, req, res, next) => {
        res.send({message: err , status: req.bod.status})
        next()
    }
    module.exports = error;
```
for import:

`const error = require('./middleware/error')` <- in file wher you want use middleware

are present two types of middleware:
- global middleware
- local middleware

that can different actions but you can split into two types:
- error
- request

1. error 
    is seample that know beacuse is only middleware that present "err" in parameter.
2. request
    is seample that know beacuse not have "err" in parameter, but can do other action.

you can set a global middleware and ona local middleware.
you can set "next(err)" in catch and the code skip the every part of it because stay search middleware eror. (because when you pass an error, only middleware error to be trigger)
a global for example can be the previous middleware ereor because you pass a error status and the message.
the local middleware is locate only for the single endpoint that you want.


i believe that's seample understand, if it were not so you can send me a message on <a href='https://www.linkedin.com/in/marco-de-vincentiis-98299a217'>Linkedin</a>



## MongoDB

#### The arguments are:
- [Introduction MongoDB](#Introduction-MongoDB)

### Introduction MongoDB
MongoDB is a NoSQL Data-Base this's different traditional form, what? why?

<img src="../assets/img/definitionMongoDB.png" width="500" />

when you want interact with MongoDB you can do 2 type action:
1. using the terminal present on MongoDB altlast
2. using a back-end language for helping you interact with MongoDB.

example when you interact with this:
- create a "Schema" that function for structure our DB for struction of him.
    ```
    const mongoose = require('mongoose'); //require mongoose for interarct with db
    cinst SchemaThatYouWant = new mongoose.Schema({
        name:{
            type:String,//type of element that we want to store
            required:true//set that this element is required
        },
        surname:{
            type:String,
            required:true
        },
        age:{
            type:Number,
            required:true
        },
        email:{
            type:String,
            required:true,
            unique:true//set that this element is unique in this collection
        }
    })

    module.exports = mongoose.model('NameThatYouWant', SchemaThatYouWant) //export schema struction
    ```
    p.s.
    MongoDB work with collection that are similar table but are different for structure.

- recall the schema in file for route (is a good practices), example you have "user" you inser this file in "./routes/user/user.js", inside of you insert the schema.
    example when you recall route file:
    ```
    const Express = require('express');//recall express
    const nameThatYouWantForRacallAfterRoute = express.Router();//recall route
    const SchemaThatYouWant = require('../models/NameThatYouWant');//recall schema

    nameThatYouWantForRacallAfterRoute.post('/create', async (req, res) => {
        const newUser = new SchemaThatYouWant({
            name: req.body.name,
            surname: req.body.surname,
            age: req.body.age,
            email: req.body.email
        }) //define object to send to DB, this object respect the structure of schema
        try {
            const savedUser = await newUser.save();
            res.json(savedUser);
        } catch (err) {
            res.json({ message: err });
        }
    })

    ```

- last you recall the file router (that you want) in app that is file that you start for lauch server.
```
    const express = require('express');//recall express
    const cors = require('cors');//recall cors
    const app = express()//recall express to asigning to app
    const PORT = portThatYouWant//start port for run servicess on server
    require('dotenv').config();//recall dotenv for use .env file for don't show password in terminal
    const {default: mongoose} = require('mongoose');//recall mongoose
    const app.use(cors())//recall cors

    const nameThatYouWantForRacallAfterRoute = './path/to/route/file' //recall route file
    mongoose.connect(process.env.DB_CONNECT) //connect to DB
    const db = mongoose.connection //start connection to DB
    db.on('error', (error) => console.log(error)) //check for error in connection to DB
    db.once('open', () => console.log('Connected to DB')) //check for connection to DB

    app.use(express.json()) //using for tell db that talk with Json
    app.use('/', nameThatYouWantForRacallAfterRoute) //recall route

    app.listen(PORT, () => console.log(`Server is running on port ${PORT}`)) // for check server is running

```


## Express

#### The arguments are:
- [Introduction Express](#Introduction-Express)

### Introduction Express
What is Express?
Express is framework that help costruct web app in Node.js, is more easy to learn.
If you are looking manual defintion:

<img src="../assets/img/definationExpress.png" width="500" />
  

