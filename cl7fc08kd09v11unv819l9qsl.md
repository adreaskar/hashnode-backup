---
title: "How to use routers with Express and split your API logic 🗃️"
seoTitle: "How to use routers with Express and split your API logic"
seoDescription: "In this tutorial we see how we can split our Express API logic by the use of routers. This is a very useful technique for a large number of endpoints."
datePublished: Mon Aug 29 2022 22:28:01 GMT+0000 (Coordinated Universal Time)
cuid: cl7fc08kd09v11unv819l9qsl
slug: how-to-use-routers-with-express-and-split-your-api-logic
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1662712607512/UZQrMRh9a.png
tags: javascript, web-development, nodejs, apis, expressjs-cilb5apda0066e053g7td7q24

---

Is your `app.js` API file becoming a big pile of code nonsense? Do you have trouble distinguishing one endpoint from another? Well then, this tutorial is definitely for you!

In this article, we will take a look at how you can split your endpoints logic into different files by utilizing the  `express.Router` class.

As always, here is the [official documentation](https://expressjs.com/en/guide/routing.html) link for a more detailed look at Express routing.

# The basic setup

Let’s suppose that our API looks like this:

```javascript
const express = require('express');
const app = express();

app.get("/", (req, res) => {
  res.send('hello world');
})

app.post("/", (req,res) => {
  // User login code
  // …
})
```
This is the most basic setup, and it’s quite ok if you don’t plan on using too many endpoints. 

But let’s suppose we want to **add another path** to our API. Our code would look like this:

```javascript
const express = require('express');
const app = express();

// Home route - - - - - - -
app.get("/", (req, res) => {
  res.send('hello world');
})

app.post("/", (req,res) => {
  // User login code
  // …
})

// Register route - - - - - - - - - -
app.get("/register", (req, res) => {
  res.send('Register an account');
})

app.post("/register", (req,res) => {
  const user = req.body.username;
  const password = req.body.password; // Always encrypt passwords
})
```
Now it starts to get a bit difficult to read, hence the comments to section out the different routes.

# A better technique

Luckily, we can create **chainable route handlers** for each path, and reduce some of the repetitive code. 

We can do that by using the `.route()` built-in method. Here’s how that works:

```javascript
const express = require('express');
const app = express();

// Home route - - -
app.route("/")
  .get((req, res) => {
    res.send('hello world');
  })
  .post((req, res) => {
    // User login code
  })

// Register route - - -
app.route("/register")
  .get((req, res) => {
    res.send('Register an account');
  })
  .post((req, res) => {
    const user = req.body.username;
    const password = req.body.password; // Always encrypt passwords
  })
```
Yea, that looks much better. But still, it’s not practical for a large number of endpoints, since all of our code is on the same file. You can imagine how messy that can get if we just keep adding more.

# The routers solution

Let’s take a step back and look at our project’s directory tree. 

At this point it’s very simple, just our `app.js` file:

```bash
/project-root-folder
  app.js
```
But in order to start using routers, it is best practice if we make a separate directory to store them. So let’s make one:

 ```bash
/project-root-folder
  app.js
  /routers
```
Great, now we can start creating our router files. Let’s make one for our `"/"` endpoint named **homeRoute.js**:

```bash
/project-root-folder
  app.js
  /routers
    homeRoute.js
```

```javascript
const express = require('express');
const router = express.Router();

router.route("/")
  .get((req,res) => {
    res.send('hello world');
  })
  .post((req,res) => {
    // User login code
  })

module.exports = router;
```
Okay, so what is going on here? 

A router file is a JavaScript **module**, a piece of code that can be imported when needed. More on modules [here](https://nodejs.org/api/modules.html). For it to work, you need to **require express** and use the `module.exports` command at the end of the file.

Right, so how on earth do we use that in our `app.js` file? Here’s how:

```javascript
const express = require('express');
const app = express();

// Import the homeRoute.js file
const homeRoute = require('./routes/homeRoute');

// Use it as an endpoint
app.use(homeRoute);
```
Now that’s what I’m talking about!

Let’s implement the `"/register"` route in the same fashion and call it a day:

 ```bash
/project-root-folder
  app.js
  /routers
    homeRoute.js
    registerRoute.js
```

```javascript
const express = require('express');
const router = express.Router();

router.route("/register")
  .get((req,res) => {
    res.send('Register an account');
  })
  .post((req,res) => {
    const user = req.body.username;
    const password = req.body.password; // Always encrypt passwords
  })

module.exports = router;
```
And in `app.js`:

```javascript
const express = require('express');
const app = express();

const homeRoute = require('./routes/homeRoute');
const registerRoute = require('./routes/registerRoute');

app.use(homeRoute);
app.use(registerRoute);
```

# Conclusions

And that was it! 🎉 

I hope this helped you organize your Express API as it grows bigger with more endpoints. In a future blog post, we will explore the concept of **controllers**, a more advanced technique for splitting your JavaScript logic.

Till next time! ✌️
