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
  

