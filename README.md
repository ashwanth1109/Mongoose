# MONGOOSE & MONGO DB

This repo is meant to be a resource for me (and hopefully others) for developing in Mongoose.
As I was learning the stack, I found it difficult to understand the stack and I wanted to create notes to refer to while working with Mongoose & Mongo DB. For beginners, I strongly recommend reading the book 'Mongoose for Application Development' by Simon Holmes [Packt Publishing]. That was one of the few books I managed to find that went into great detail about using Mongoose. Since it was released in 2013, Im hoping to update some of the outdated information from the book and publish them here based on the Mongoose API documentation. If any information is inaccurate or you would like to contribute some content, feel free to raise an issue and we can integrate content using pull requests.

### The Technology Stack - Node.js, npm, MongoDB, and Express

For a long time, JS was mainly perceived as a browser-based scripting language.

However on May 27, 2009, a developer by the name of Ryan Dahl released the very first version of Node.js.
Node.js was a new server side system that enabled the usage of JavaScript on the server side.

Most web developers saw this as an interesting development since they didn't have to learn a new language from scratch for the back-end and they could also share some code between the front-end and the back-end. For example, form validation is usally done on browser and server and with Node JS, you could use the same code for both sides.

### Blocking vs Non-Blocking Code

Unlike some of the popular server side technology such as PHP, ASP.NET, Ruby & Java, which all follow a multi-threaded approach where each client request results in the instantiation of a new thread or process, Node.js follows a single-threaded event loop model architecture to simultaneously handle multiple clients.

If you would like to know about the event loop in detail, check out this really cool youtube clip by Philip Roberts titled 'What the heck is the event loop anyway?' - published by JSConf.

In traditional stacks, on getting the request, we first take the data, write it to the database, send a confirmation message and wait for the next request. This works fine in multi-threaded stacks, but in a single threaded stack this can block the code.

This problem is solved in Node.js by using callbacks to run the tasks for you.

Consider the following hello world example,

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
