# javascript-unit-testing


I am learning to write more of my JavaScript code without the help of any library or framework. I like to have a good unit test coverage for any code I write because it gives me confidence when I want to refactor my code or change it for any other reason. In this tutorial, I will demonstrate how I write unit tests without any framework.

### We will start by creating two files:

  - ```test.js``` contains the test functions ```assert``` and ```it``` we need for testing. It also contains all the unit tests.
  - ```test.html``` is the test runner.

### Test Functions

**Let’s first create a ```test.js``` file and write the ```it``` function as shown below.**

```
(function(){
  'use strict';
  
  /**
   * test function
   * @param {string} desc
   * @param {function} fn
   */
  function it(desc, fn) {
    try {
      fn();
      console.log('\x1b[32m%s\x1b[0m', '\u2714 ' + desc);
    } catch (error) {
      console.log('\n');
      console.log('\x1b[31m%s\x1b[0m', '\u2718 ' + desc);
      console.error(error);
    }
  }
})();
```


The ```it``` function takes two parameters. 
  - First parameter ```desc``` is the description of the test case and second parameter ```fn``` is the test function. 
  - ```fn``` is encapsulated in a ```try/catch``` block. 
  - If ```fn``` executes successfully then we show the success message in the console and if the test fails then we show the fail message in the console.


**Next, we implement the ```assert``` function in ```test.js```.**

```
 function assert(isTrue) {
    if (!isTrue) {
      throw new Error();
    }
  }
  ```


```assert``` function takes one parameter ```isTrue`` which is a condition such as
```x === y```. 
  - ```assert``` checks the condition and throws an error if the condition is ```false```.

That’s it. That’s our entire testing framework.

### Test Runner

```test.html``` is our test runner which includes the ```test.js``` file.

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Test Runner</title>
</head>
<body>
  <div id="selector"></div>
  
  <script src="test.js"></script>
</body>
</html>
```


**Simple Test Cases to Test the Framework**

Let us make sure our test framework is working as intended by writing a couple of simple tests.

  - **Failing Test**

    Write the following test case after the ```assert``` function in ```test.js```

```
it('should fail', function() {
  assert(1 !== 1);
});
```

Open the test runner file ```test.html``` in a browser (preferably Chrome). 
  - Open the browser console. 
  - You should see a failure message in the console like below

![image](https://user-images.githubusercontent.com/47826697/167706119-92e9ce98-a7fe-4e5d-bc07-23b6c4306b0f.png)


**Sample Test Fail Message**

Since ```1 !== 1``` is an invalid condition, the test fails. 
  - You get an idea of where the failure occurred by looking at the error stack. 
  - In this case, I would check line 26 in my ```test.js``` to start debugging the issue.

**Passing Test**

Now let us write a small passing test in test.js.

```
it('should pass', function() {
  assert(1 === 1);
});
```
Refresh ```test.html``` in the browser and inspect the console. 
  - You should see a success message in the console like below.

![image](https://user-images.githubusercontent.com/47826697/167706363-eef8e949-8a4a-4029-8293-c2e93fb9e790.png)


Great! Our test framework seems to be working fine. Now, let us use our test framework for some real world scenarios.

## Create Application Code File

Typically, you will be writing your application code in a separate JavaScript file such as ```app.js```. 
  - Let us create ```app.js``` file.

```
                           /* app.js */
(function(){
  'use strict';
  //Create a global variable and expose it to the world
  var $myapp = {};
  self.$myapp = $myapp;
})();
```
We created a global variable ```$myapp``` and exposed it to the world so we can call this global variable from ```test.js``` file. Always use a unique name for your global variable so it does not clash with other global variables.

Now let us include ```app.js``` file in ```test.html``` like below

```
                           /* test.html */
...
<body>
  ...
  <script src="app.js"></script>
  <script src="test.js"></script>
</body>
```
Make sure ```app.js``` is included before ```test.js```.

## Test a Method in Application Code

Let us write a date validation method in our ```app.js``` file.

```
(function () {
  'use strict';

  // Create a global variable and expose it to the world
  var $myapp = {};
  self.$myapp = $myapp;

  $myapp.isValidDate = function (dateString) {
    var dateRegex = /(0[1-9]|1[012])[- \/.](0[1-9]|[12][0-9]|3[01])[- \/.](19|20)\d\d/gi;
    return dateRegex.test(dateString);
  };
})();
```

```isValidDate``` determines if a given date string is valid and return a ```true``` or ```false```.

Now, let us write a test case for this method in our ```test.js```.

```
//test.js
...
it('should validate a date string', function () {
  // Valid Date
  assert($myapp.isValidDate('02/02/2020'));
  // Invalid Date
  assert(!$myapp.isValidDate('01/32/2020'));
});
...
```
In the above test case, first ```assert``` checks for a valid date string and the second ```assert``` checks for an invalid date string. 
  - Open or refresh ```test.html``` test runner file and check the console. You should see a message like below.

![image](https://user-images.githubusercontent.com/47826697/167706955-3bf6ce55-bb6f-4956-ad94-8afb652da528.png)


## Test DOM

Let’s say we are writing a simple todo list application. 
  - We have a form with an input field to enter a todo item and a submit button. 
  - We listen to the submit event in our ```app.js``` file and the event handler updates the todo list in the DOM.

Here is the html code.

```
<body>
  <form id="aform">
    <input type="text" name="todo-input">
    <button>Submit</button>
  </form>
  <ul id="todo-list"></ul>

  <script src="app.js"></script>
</body>
```

Following is the JavaScript code in ```app.js``` which updates the todo list.

```
document.addEventListener('submit', function (event) {
    event.preventDefault();
    var elements = document.querySelector('#aform').elements;
    var todoItem = elements['todo-input'].value;

    var todoList = document.querySelector('#todo-list');
    if (todoItem) {
      todoList.innerHTML += '<li>' + todoItem + '</li>';
    }
  });
  ```

We want to test the JavaScript code and make sure it adds a todo item to the ```#todo-list``` unordered list element in the DOM as expected.

# Test Case for DOM

Following is the test case to check the event handler code to make sure it updates the DOM when a new todo item is entered.

```
it('should add a todo item to the list', function () {
    var selector = document.querySelector('#selector');
    selector.innerHTML = '<form id="aform"><input type="text" name="todo-input"><button>Submit</button></form><ul id="todo-list"></ul>';
    var form = document.querySelector('#aform');
    form.elements['todo-input'].value = 'task 1';

    var ev = document.createEvent('HTMLEvents');
    ev.initEvent('submit', true, true);
    form.dispatchEvent(ev);

    assert(selector.innerHTML.toLowerCase().includes('<li>task 1</li>'));
    
    //cleanup
    selector.innerHTML = '';
  });
  ```


In our ```test.html``` we created a placeholder element ```<div id="selector"></div>``` to insert test code in our test runner’s DOM.

In the test case, ```var selector = document.querySelector('#selector')``` gets a reference to our placeholder element. 
  - Then we append the selector with the form with an ```id aform```. 
  - We have to make sure all the element names and ids are as expected by the event handler in ```app.js```.

Following line sets the value of the input field

```
form.elements['todo-input'].value = 'task 1'
```

Then we create and initialize a ```‘submit’``` HTML event, and have our form dispatch the event.

```
form.dispatchEvent(ev)
```

This will trigger the event handler in ```app.js``` file listening to the ```‘submit’``` event. 
  - The event handler will add ```‘task 1’``` to the ```todo-input``` HTML list.

Following line of code asserts to check if a task was added to the HTML list as expected.

```
assert(selector.innerHTML.toLowerCase().includes('<li>task 1</li>'));
```

Run the test runner in a browser and check the browser console. 
  - You should see the following success message if everything went as expected.

![image](https://user-images.githubusercontent.com/47826697/167707686-e182e14c-d955-47c6-a288-6d78fb275ee5.png)


The final line in the test case ```selector.innerHTML = ''``` clears up the placeholder element.

## Testing when Asynchronous Function is Involved

Let’s say we are making a REST API call to get a user’s first name and last name, which then gets rendered to the DOM. 
  - We want to test if the DOM gets successfully rendered without actually making the API call.

Following is the code that we would like to test.

```
$myapp.get = function (url, callback) {
    var requestListener = function () {
      if (httpRequest.readyState === XMLHttpRequest.DONE) {
        if (httpRequest.status === 200) {
          var response = JSON.parse(httpRequest.response);
          callback(response);
        } else {
          alert('There was a problem with the request');
        }
      }
    };

    var httpRequest = new XMLHttpRequest();
    httpRequest.addEventListener('load', requestListener);
    httpRequest.open('GET', url);
    httpRequest.send();
  };

  $myapp.getUser = function (id) {
    $myapp.get('/api/users?id=' + id, function (user) {
      var domElement = document.querySelector('#user-detail');
      if (user) {
        domElement.innerHTML = user.fname + ' ' + user.lname;
      }
    });
  };
  ```


```$myapp.get``` method encapsulates an ```XMLHttpRequest``` GET request. It takes a string url parameter and calls the callback function with response.

```$myapp.getUser``` is the method we want to test. It calls ```$myapp.get``` method and then the callback function renders the DOM with the user’s first name and last name.

## Stub the Asynchronous Code

As shown above, we stub the asynchronous ```$myapp.get``` method and return a fake user object.

We call ```$myapp.getUser(1)``` method. This should successfully execute the stubbed ```$myapp.get``` method and render the user’s first and last name as defined in the stub.

The following code asserts to see if our test DOM was updated with proper first and last name.

```
assert(selector.innerHTML.includes('Amit Gupta'))
```

Finally we clear out the placeholder DOM object by resetting its ```innerHTML```.

## Summary
This tutorial showed how you can unit test your front end JavaScript code without using any framework. 

  - How to create ```it``` and ```assert``` test functions
  - How to unit test application functions
  - How to unit test the DOM
  - How to unit test when asynchronous calls are involved

This is a simple and practical way to unit test front end JavaScript code in the browser.
