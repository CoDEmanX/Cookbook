# Adding Background workers to my Foxx App

## Problem

I have an existing Foxx app (for example from the [first recipe about Foxx](https://docs.arangodb.com/cookbook/FoxxFirstSteps.html)) and I want to send out an email every time an item is created.

## Solution

Go to [SendGrid](https://sendgrid.com) and sign up for a free account there. You will need your credentials (your username and password) to configure the Foxx Job for SendGrid. First, install the job by going to the admin interface of ArangoDB and clicking on 'Applications' and then 'Add Application'. Now click on the 'Store' button and search for the `mailer-sendgrid` app from Alan Plum. Click the 'Install' button next to it.

Mount it to `/sendgrid` and provide your username and password from SendGrid. Now press configure, and the app will be installed and configured. Now, let's go to your app.

In your controller, first add a variable for a new queue with `queue = Foxx.queues.create('todo-mailer', 1)`. This will create a new queue with the name `todo-mailer` and give you access to it via the `queue` variable.

Now we want to push a job onto our queue every time somebody adds an item to the todo list (the route that posts to `/`). Here is the according code (don't forget to adjust the `to` field to be your email address):

```js
controller.post('/', function (req, res) {
  var todo = req.params('todo');
  todos.save(todo);
  res.json(todo.forClient());

  queue.push('mailer.sendgrid', {
    from: 'todos@example.com',
    to: 'YOUR ADDRESS',
    subject: 'New Todo Added',
    html: 'New Todo added: ' + todo.get('title')
  });
}).bodyParam('todo', 'The Todo you want to create', Todo);
```

And that's it! To try it out go to your app in the Applications tab and create a new todo item with the interactive documentation. You will receive an email with the title of the todo shortly after that. As we are using the queue, the user of your API doesn't need to wait until the email is sent, but gets an immediate answer.

Author: [Lucas Dohmen](https://github.com/moonglum)

Tags: #foxx