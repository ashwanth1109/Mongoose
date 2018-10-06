# MONGOOSE & MONGO DB

This repo is meant to be a resource for me (and hopefully others) while developing in Mongoose.
As I was learning this stack, I found it difficult to understand the stack and I wanted to create some form of notes to refer to when working with Mongoose & Mongo DB.

I strongly recommend reading the book 'Mongoose for Application Development' by Simon Holmes [Packt Publishing]. This was one of the few books I found, that provides a very good introduction to Mongoose and this is the book has been one of the primary references for these notes (meant for non commercial purposes only; no copyright infringement intended). Since it was released in 2013, Im hoping to update some of the outdated information from the book and publish them here based on the Mongoose API documentation and my personal learnings from attending the General Assembly Web Development Immersive Bootcamp.

If any information is inaccurate or you would like to contribute some content, feel free to raise an issue and we can integrate content using pull requests.

### The Technology Stack - Node.js, npm, MongoDB, and Express

For a long time, JS was mainly perceived as a browser-based scripting language.

However on May 27, 2009, a developer by the name of Ryan Dahl released the very first version of Node.js.
Node.js was a new server side system that enabled the usage of JavaScript on the server side.

Most web developers saw this as an interesting development since they didn't have to learn a new language from scratch for the back-end and they could also share some code between the front-end and the back-end. For example, form validation is usally done on browser side and server side separately, but with Node JS, you could re-use the same code for both sides.

### Blocking vs Non-Blocking Code

Unlike some of the other popular server side technology such as PHP, ASP.NET, Ruby & Java, which all follow a multi-threaded approach (where each client request results in the instantiation of a new thread or process), Node.js follows a single-threaded event loop model architecture to simultaneously handle multiple clients.

If you would like to know about the event loop in detail, check out this really cool youtube clip by Philip Roberts titled 'What the heck is the event loop anyway?' - published by JSConf.

In traditional stacks, on getting the request, we first take the data, write it to the database, send a confirmation message and wait for the next request. This works fine in multi-threaded stacks, but in a single threaded stack this can block the code.

This problem is solved in Node.js by using callbacks to run the tasks for you.

Consider the following hello world express example,

```javascript
const express = require("express");
const app = express();

// http://localhost:3000/
app.get("/", (req, res) => {
  res.send("Hello World");
});

app.listen(3000, () => {
  console.log("Server listening on port 3000");
});
```

Here we are sending a callback function to express's get method function. The parameters req and res are being sent to the callback function even though they haven't been defined or created anywhere. This works because, express's get method function will create these objects before calling the callback function.

### MongoDB

MongoDB is one of the most wanted choices for database as shown by this study conducted by stackoverflow in 2018 -
https://api.getguru.com/files/view/51a998fb-8aaf-4df7-8108-0d61da49caf0

MongoDB is a fast and flexible NoSQL database that is document oriented. MongoDB stores documents as binary encoded JSON which returns a JSON object on running a query.
A collection of documents is called collection which is similar to a table in relational databases. MongoDB is extremely scalable, with many built-in capabilities for distributing across multiple servers, without compromising speed or data integrity.

MongoDB has a number of unique features such as atomic updates, indexed array keys and a powerful query framework that can significantly influence schema design. Check out the following link to read more about the differences between MongoDB and traditional table data -
https://www.mongodb.com/blog/post/thinking-documents-part-1?jmp=docs&_ga=2.202168721.1294830246.1530196908-30583944.1529350623

### Express

Express is a web application framework for Node. In a Node project, there is a lot of groundwork to be done to even create a basic web server and you have to serve static files in a non-blocking way, amongst a few other things to take care of.

Express lets you create a server in a much simpler way by taking care of all of this setup for you and is a great starting point for creating your application.

### Mongoose

Mongoose is an object modelling tool for Node.js and can be installed as an npm package to leverage the powerful functionality of Node.js. Using Mongoose, you can define your data structure in JSON inside your project using schemas and models.

Mongoose makes it easier to interact with your data by defining the schema for it and interacting with MongoDB in a consistent & predictable manner. It handles some layers of complexity involved with certain queries which can have multi-nested callbacks to interact with the MongoDB driver.
Unlike MongoDB which returns a JSON string on querying, Mongoose returns the data as a JSON object. It also has a plethora of helpler functions and methods that make things more convenient.

### Installations

Once you have Node and MongoDB installed, we want to install initialize a project with

```
npm init -y
```

To install dependencies (express and mongoose) -

```
npm i express mongoose
```

### [Add steps to setup basic express server]

To get started we need to require mongoose and then establish a database connection.

### Establishing a database connection

There are two ways to connect to databases -

1. mongoose.connect
2. mongoose.createConnection

#### mongoose.connect

To set up a default connection, we use the mongoose.connect method as follows -

```javascript
const mongoose = require("mongoose");
const mongoURI = "mongodb://localhost:27017/" + "databaseName";
mongoose.connect(mongoURI);
```

This opens a mongoose connection to the Mongo database that is running on the localhost. As long as you require Mongoose, we can use this connection anywhere in the app.

#### mongoose.createConnection

To connect to more than one database at the same time, we need to use the mongoose.createConnection method as follows -

```javascript
const mongoose = require("mongoose");
const mongoURI = "mongodb://localhost:27017/" + "databaseName";
const databaseConnection = mongoose.createConnection(mongoURI);
```

The mongoURI string can use multiple options and some common ones are -

1. setting different ports:
   "mongodb://localhost:27018/databaseName"

2. accessing the database as a specific user:
   "mongodb://username:password@localhost/databaseName"

#### Connection Options

Mongoose allows you to pass certain options as an optional second paramenter in the form of a JSON object. For example,

```javascript
const mongoURI = "mongodb://localhost:27017/databaseName";
const dbOptions = { user: "db_username", pass: "db_password" };
mongoose.connect(
  mongoURI,
  dbOptions
);
```

Options that you can use this way are - user, pass, db, server, repiset

#### Closing the Connection

Each mongoose connection has a close() method that takes an optional callback function.

If you are using the default connection you call it as follows:

```javascript
const db = mongoose.connection;
db.close(() => {
  console.log(`Mongoose default connection closed`);
});
```

To close a named connection:

```javascript
databaseConnection.close(() => {
  console.log(`Mongoose database connection closed`);
});
```

#### [REVIEW]

Closing when the node process ends:
As a rule of thumb, you should close any connections on process termination using the following snippet of code:

```javascript
process.on(`SIGINT`, () => {
  db.close(() => {
    console.log(`Mongoose disconnected due to app termination`);
    process.exit(0);
  });
});
```

#### Connection Events

The connection process in Mongoose inherits the Node 'EventEmitter' class - which means that we can run certain code on certain events occurring - e.g. connected, disconnected, error etc.

Error:

```javascript
db.on(`error`, () => {
  console.log(`Mongoose connection error: ${err}`);
});
```

Connected:

```javascript
db.on("connected", () => {
  console.log("Mongoose connected succesfully");
});
```

Disconnected:

```javascript
db.on("disconnected", () => {
  console.log("Mongoose disconnected succesfully");
});
```

Alternatively, we can run the following code snippet for the same purpose:

```javascript
db.on("error", console.error.bind(console, "connection error:"));
db.once("open", () => {
  console.log("Mongoose connection established succesfully");
});
```

### Full Setup Code

In models/db.js

```javascript
//------------------------------------------------------------------------------------
// CREATE A FILE CALLED db.js & store it in models
//------------------------------------------------------------------------------------

//------------------------------------------------------------------------------------
// Import in the mongoose module
//------------------------------------------------------------------------------------
const mongoose = require("mongoose");

//------------------------------------------------------------------------------------
// Build the connection string
//------------------------------------------------------------------------------------
const mongoURI = "mongodb://localhost/MongoosePM";

//------------------------------------------------------------------------------------
// Create the database connection
//------------------------------------------------------------------------------------
mongoose.connect(mongoURI);

//------------------------------------------------------------------------------------
// Store mongoose.connection in db for ease of use
//------------------------------------------------------------------------------------
const db = mongoose.connection;

//------------------------------------------------------------------------------------
// Set error state on db
//------------------------------------------------------------------------------------
db.on("error", () => {
  console.log(`Mongoose connection error: ${err}`);
});

//------------------------------------------------------------------------------------
// Set connected state on db
//------------------------------------------------------------------------------------
db.on("connected", () => {
  console.log(`Mongoose connected to ${mongoURI}`);
});

//------------------------------------------------------------------------------------
// Set disconnected state on db
//------------------------------------------------------------------------------------
db.on("disconnected", () => {
  console.log(`Mongoose disconnected`);
});

//------------------------------------------------------------------------------------
// Catch if node process is ending & close connection
//------------------------------------------------------------------------------------
process.on("SIGINT", () => {
  db.close(() => {
    console.log(`Mongoose disconnected through app termination`);
    process.exit(0);
  });
});
```

Next, we open the connecion on starting the application in app.js:

In app.js,

```javascript
const express = require("express");
const db = require(`./models/db`);
```

Now we have finished setup and can move on to defining our schemas and models.

### Schemas & Models

These are building blocks of Mongoose.
To put it simply, a schema is a way to describe the structure of your data by labeling the data and defining what its data type is.

To define your database structure you create schemas using Mongoose in the following manner -

```javascript
const mongoose = require(`mongoose`);
const Schema = mongoose.Schema;

const userSchema = new Schema(
  {
    name: { type: String, required: true, unique: true },
    email: { type: String, required: true },
    gender: String,
    mobile: Number,
    location: String,
    joinDate: { type: Date, default: Date.now },
    friends: { type: String, min: 0 }
  },
  { timestamps: true }
);
```

A schema is an object that defines the structure of any documents that will be stored in your MongoDB collection and it lets you define the data types and provides options for setting default values, validations etc. Nexdt we compile our schema into a model.

A model is a class with which we construct our documents.
We use models to access a named collection and query that collection. A model is created by combining a Schema, a Connection and a collection name and can be exported as follows -

```javascript
module.exports = mongoose.model("User", userSchema);
```

You can read more about this at -
https://www.mongodb.com/blog/post/the-mean-stack-mistakes-youre-probably-making

A model can be thought of as a compiled version of the schema and each instance of the model will map to one document in the database.

A document in MongoDB created from this schema would look like the following code snippet -

```javascript
{
  "__v": 0,
  "__id": ObjectId("12595135c000148e6d7e3e00"),
  "createdOn": ISODate("2018-09-23T10:32:14.543Z"),
  "name": "Ashwanth",
  "email": "example@email.com",
  "gender": "m",
  "mobile": "9999999999",
  "location": "Bangalore",
  "joinDate": ISODate("2018-09-23T10:32:14.543Z"),
  "friends": 0
}
```

MongoDB imposes a maximum document size of 16MB and to work around this limit, we have to use the MongoDB GridFS API.

To create instances of our model, we create them just as we do with regular classes:

```javascript
const user1 = new User({ name: "Ashwanth" });
console.log(user1.name); // 'Ashwanth'
```

Models can also have methods defined in them if we have explicitly added these functions to the methods property of a schema before compiling it with mongoose.model():

```javascript
userSchema.methods.generateGreeting = () => {
  const greeting = this.name
    ? `Hello, I am ${this.name}`
    : `Sorry, name not defined`;
  return greeting;
};

const User = mongoose.model("User", userSchema);
```

These functions that are added to the methods property of a schema gets compiled into the Model prototype and can be exposed on each document instance as follows:

```javascript
const user1 = new User({ name: "Ashwanth" });
console.log(user1.generateGreeting());
```

Although we have a user, we still haven't saved it to the database. To do that, we need to call the save method on the instance as follows:

```javascript
user1.save((err, user) => {
  if (err) {
    console.log(err);
  } else {
    console.log(user);
  }
});
```

#### Data Types in Schemas

There are 8 data types that you can set in the schema -

1. String
2. Number
3. Date
4. Boolean
5. Buffer
6. ObjectId
7. Mixed
8. Array

##### String

The String SchemaType stores a string value, UTF-8 encoded.

##### Number

The Number SchemaType stores a number value with restrictions, Mongoose does not natively support long and double datatypes, even though MongoDB does. We can extend Mongoose's functionality to support it using some plugins.

##### Date

The Date SchemaType holds a date and time obkect, which is returned from MongoDB as an ISO Date Object.

##### Boolean

The Boolean SchemaType holds one of two values - true or false

##### Buffer

The Buffer SchemaType stores binary information, e.g. images

##### ObjectId

The ObjectId SchemaType can be used to assign a custom id rather than using \_id and must be defined as follows -

```javascript
userSchema.add({
  userId: Schema.Types.ObjectId
});
```

##### Mixed

The Mixed SchemaType defines that the value entered in that field can be of any type. It can be set in one of two ways as follows:

```javascript
const userSchema = new Schema({
  mixedData: {}
});
```

```javascript
const userSchema = new Schema({
  mixedData: Schema.Types.Mixed
});
```

There is a gotcha when using mixed however. Any changes made to mixed data type cannot be automatically detected by Mongoose, so it wont know to save them. Thus, you have to manually declare that the data has changed using the markModified method as follows:

```javascript
user.mixedData = { someValue: "value has been changed" };
user.markModified("mixedData");
user.save();
```

##### Array

The Array datatype can be used in two ways. First, a simple array of values:

```javascript
const userSchema = new Schema({
  emailAddresses: [String]
});
```

The Array datatype can also be used to store a collection using nested schemas as follows:

```javascript
const emailSchema = new Schema({
  email: String,
  verified: Boolean
});
const userSchema = new Schema({
  name: String,
  emailAddresses: [emailSchema]
});
```

You can also define a mixed type in your array as follows:

```javascript
const emailSchema = new Schema({
  addresses: []
});
```

(or)

```javascript
const emailSchema = new Schema({
  addresses: Array
});
```

You must keep in mind that Mongoose does not automatically detect any changes made to the mixed data type.

##### Custom SchemaTypes

You can also define custom SchemaTypes using Mongoose plugins for data types such as long, double, RegEx, email etc.

#### Defining Schemas

1. Create a variable and assign a Mongoose schema object to this variable.

```javascript
const mongoose = require("mongoose");
const Schema = mongoose.Schema;
const userSchema = new Schema({});
```

2. Now we can add basic value types for the different fields.
   We can use fields like createdOn, modifiedOn and lastLogin to keep track of useful information

```javascript
const userSchema = Schema({
  name: String,
  email: String,
  createdOn: Date
  modifiedOn: Date,
  lastLogin: Date
});
```

3. We can also set a default value

```javascript
const userSchema = Schema({
  createdOn: Date
});
```

is short for

```javascript
const userSchema = Schema({
  createdOn: { type: Date }
});
```

We can add more properties to this object:

```javascript
const userSchema = Schema(
  {
    createdOn: { type: Date, default: Date.now }
  },
  { timestamps: true }
);
```

To use Date.now, we need to add a timestamp to the controller function. 'default' is a reserved work in Javascript, and although reserved words can be used as keys, it is a safer option to wrap them in quotes like follows:

```javascript
const userSchema = Schema(
  {
    createdOn: { type: Date, default: Date.now }
  },
  { timestamps: true }
);
```

4. To ensure that there are no duplicate entries, we can set unique as true as follows:

```javascript
const userSchema = Schema({
  email: { type: String, unique: true }
});
```

This ensures that MongoDB will check if email already exists in the database and throws an errow if it does.

Earlier, we had seen that a sample data entity in the document created from our user schema looks like the following:

```javascript
{
  "__v": 0,
  "__id": ObjectId("12595135c000148e6d7e3e00"),
  "createdOn": ISODate("2018-09-23T10:32:14.543Z"),
  "name": "Ashwanth",
  "email": "example@email.com",
  "gender": "m",
  "mobile": "9999999999",
  "location": "Bangalore",
  "joinDate": ISODate("2018-09-23T10:32:14.543Z"),
  "friends": 0
}
```

In this entity, we did not set \_\_v and \_\_id. \_\_id is a unique identifier that is assigned by Mongo and \_\_v is an internal versioning number set by Mongoose when a document is created. It doesn't increment when the document is changed, but instead keeps track of when arrays within documents have changed their indexed positions for some entries. The \_\_v gives you a way to check if the array has changed, when you're trying to access any element using its position or index in the array.

#### Creating Models

A model is a named compiled version of the schema and each instance of the model maps to a single document in our database. The model allows for operations such as creating, reading, saving and deleting to manipulate the data.

We can build a model from our user schema on a default connection, we do it as follows:

```javascript
mongoose.model("User", userSchema);
```

If we are creating a model based on a named Mongoose connection, we can do it as follows:

```javascript
databaseConnection.model("User", userSchema);
```

We can create multiple instances from our model as follows:

```javascript
const user1 = new User({ name: "Ashwanth" });
const user2 = new User({ name: "Niko" });
```

We can access properties of these instances in the same way as we do with any regular database.

```javascript
console.log(user1.name); // Ashwanth
user1.name = "Dorotheos";
console.log(user1.name); // Dorotheos
```

We can save each of these instances to the database as follows:

```javascript
user1.save(err => {
  if (err) {
    console.log(err);
  } else {
    console.log("User 1 is saved to database");
  }
});
```

##### Find one instance from model

To find one specific instance of the model, we can use the findOne query in Mongoose:

```javascript
User.findOne({ name: "Ashwanth" }, (err, user) => {
  if (err) {
    console.log(err);
  } else {
    console.log(user);
  }
});
```

##### Find all instances in model

To find all instances in model, we use the find query:

```javascript
User.find({}, (err, users) => {
  if (err) {
    console.log(err);
  } else {
    console.log(users);
  }
});
```

##### Model Name & MongoDB Collection

The name of the MongoDB collection will by default be a lowercase pluralized version of the model name.

```javascript
mongoose.model("User", userSchema);
```

This model will reference a collection called 'users' in MongoDB.

##### Overriding the collection name

You can choose not to go with the default collection name by specifying a different collection name either in the schema declaration or in the model command.

We can specify the collection name in the schema as follows:

```javascript
const userSchema = new Schema(
  {
    name: String,
    email: String
  },
  { collection: "collectionName" }
);
```

We can specify the collection name in the model as follows:

```javascript
mongoose.model("User", userSchema, "collectionName");
```

To completely define the schema and models, we need to ensure that the following steps are completed:

1. Require mongoose
2. Set the connection string for the MongoDB database
3. Define all the necessary schema
4. Build all the necessary models
5. Open the mongoose connection to the database

So far, we have merely begun to explore all the possibilities and functionality that models provide us.

#### CRUD operations - Introduction

To reiterate a document is a single instance of a model. So, in order to operate on a document, we need to work with the provided methods of the model. Some examples of these methods are - create, find, update, remove etc. These methods are usually applied to the model, however we also have instance methods that let us apply certain methods on individual instances of the model - e.g. instance.save method.

Mongoose also let you create your own model methods, thus providing you with the functionality necessary to create any kind of method.

In order to further explore the methods available to use, we will continue with the 'User' model compiled from the 'userSchema'.
