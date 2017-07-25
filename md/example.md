What is AngularJs?

- Structural framework for dynamic web apps 
- Teach the brower new syntax 
- Not a library for DOM manipulation, such as jQuery



Overview

```html
<div ng-app="myApp">
  <div ng-controller="exampleCtrl" ng-init="amount=100;">
    <input ng-model="amount"></input>
    <p>{{ amount | amount }}</p>
    <p>{{ rmb | amount }}</p>
    <my-amount></my-amount>
  </div>
</div> 
```

```js
angular.module('myApp', ['otherModule'])
.controller('exampleCtrl', [
    '$scope', 
    'currencyConverter', 
    function ($scope, converter) {
        $scope.rmb = converter.convert($scope.amount);
    }
])
.factory('currencyConverter', function () {
    return {
        convert: function(amount) { 
            return amount * 6.09;
        }
    }
})
.directive('myAmount', function() {
    return {
        template: '<div>my amount: {{ amount }}</div>'
    };
})
```



How AngularJs initializes?


- AngularJs looks for the ngApp directive which designates your application root, once the ngApp directive is found then:


![concepts-startup](../dist/img/concepts-startup.png)

- Load the module associated with the directive
- Create the application injector
- Compile the DOM with **ngApp** directive as the root of the compilation. This allows making only a portion of the DOM as an AngularJs application



Core Concepts

- Module
- Data Binding
- Controller
- Expressions
- Scope
- Filter
- Service
- Directives


<h3>Module</h3>

- A container including controllers, services, filters, directives
- Aeparate the application into parts for code reuse
- As dependence to other module

```html
<div ng-app="myApp">
</div>
```

```js
angular.module('myApp', []).
config(function(injectables) { // provider-injector
  // You can only inject Providers (not instances)
}).
run(function(injectables) { // instance-injector
  // You can only inject instances (not Providers)
});
```


<h3>Data Binding</h3>

- Data-binding in AngularJS apps is the automatic synchronization of data between the model and view

```html
<div ng-init="name='hello'">
  <input ng-model="name"></input>
  <div>{{ name }}</div>
  <div ng-click="changeName()"></div>
</div>
```

```js
function changeName($scope) {
  $scope.name = 'hello world';
}
```


<h3>Controller</h3>

- Business logic behind views, once controller instantiated, a new scope will be created which assign to **$scope** object as an injectable parameter to the controller's constructor function

```html
<div ng-controller="myCtrl">
</div>
```

```js
myApp.controller('myCtrl', ['$scope', function($scope) {
  $scope.name = 'xxxx';
  $scope.login = function() { window.alert('login!')};
}])
```


use controllers to:

- Set up the initial state of $scope
- Add behavior to the $scope object


do not use controllers to:

- Manipulate DOM 
- Filter output
- Share code or state across controllers 


<h3>Expressions</h3>

- Expressions are JavaScript-like code snippets that are mainly placed in interpolation bindings or directive attributes

```html
<div ng-click="functionExpression()"> 
  <span title="{{ attrBinding }}">{{ textBinding }}</span>
  <span>{{ a+b }}</span>
  <span>{{ user.name }}</span>
</div>
```


AngularJS Expressions vs. JavaScript Expressions

- Evaluated against a **scope** object
- No Control Flow Statements
- No Function Declarations
- No Object Creation With New Operator


<h3>Scope</h3>

- An object that refers to the application model

```html
<div ng-controller="MyController">
    <input type="text" ng-model="username">
    <button ng-click='sayHello()'>greet</button>
  <hr>
  {{greeting}}
</div>
```

```js
myApp.controller('MyController', ['$scope', function($scope) {
  $scope.username = 'World';

  $scope.sayHello = function() {
    $scope.greeting = 'Hello ' + $scope.username + '!';
  };
}]);
```


- Be created in hierarchical structure like the DOM structure 

```html
<div class="show-scope-demo">
  <div ng-controller="GreetController">
    Hello {{name}}!
  </div>
  <div ng-controller="ListController">
    <ol>
      <li ng-repeat="name in names">{{name}} from {{department}}</li>
    </ol>
  </div>
</div>
```

```js
myApp.controller('GreetController', ['$scope', '$rootScope', function($scope, $rootScope){
  $scope.name = 'World';
  $rootScope.department = 'AngularJS';
}])
.controller('ListController', ['$scope', function($scope) {
  $scope.names = ['Igor', 'Misko', 'Vojta'];
}]);
```


![concepts-scope](../dist/img/concepts-scope.png)


- Watch expressions and propagate events

```html
<div ng-controller="EventController">
  <!-- to parent scope -->
  <button ng-click="$emit('MyEvent')"></button>
  <!-- to child scope -->
  <button ng-click="$broadcast('MyEvent')"></button>
</div>
```

```js
myApp.controller('EventController', ['$scope', function($scope) {
  $scope.count = 0;
  $scope.$on('MyEvent', function() {
    // got event
  });
}]);
```


<h3>Filter</h3>

- Formats the value of an expression for display to the user, they can be used in view templates, controllers or services

```html
<div>{{ hello | upper }}</div>
```

```js
myApp.filter('upper', function(input) {
    return input.toUpperCase();
})
```


<h3>Service</h3>

 - Reusable business logic independent of views, can use services to organize and share code across app


- Lazily instantiated 
 - AngularJs only instantiates a service when an application component depends on it
- Singletons 
 - Each component dependent on a service gets a reference to the single instance generated by the service factory


```html
<div ng-controller="myCtrl">
amount: {{ amount }}
</div>
```
```js
myApp.factory('currencyConverter', function () {
    return {
        convert: function(amount) { 
            return amount * 6.09;
        }
    }
})
myApp.controller('myCtrl', ['currencyConverter', function(currencyConverter) {
  $scope.amount = currencyConverter(100);
}])
```


<h3>Directives</h3>

- Extend HTML with custom attributes and elements
- The only place where an application should access the DOM
- Build-in directives, like **ng-app**, **ng-model**

```html
<my-name name="{{myname}}"></my-name>
```

```js
myApp.directive('myAmount', function() {
    return {
        template: '<div>my name: {{ name }}</div>'
        restrict: 'E'
        scope: {
            name: '@'
        }
    };
})
```



Advanced Concepts

- Dependency Injection
- Injector


Dependency Injection

- DI is a software design pattern that deals with how components get their dependencies


- In the below example, **SomeClass** is not concerned with creating or locating the greeter dependency, it is simply handed the greeter when it is instantiated

```js
function SomeClass(greeter) {
  this.greeter = greeter;
}

SomeClass.prototype.doSomething = function(name) {
  this.greeter.greet(name);
}
```

- This is desirable, but it puts the responsibility of getting hold of the dependency on the code that constructs SomeClass


Injector

- The injector is a **service locator** that is responsible for construction and lookup of dependencies

```js
// Provide the wiring information in a module
var myModule = angular.module('myModule', []);
myModule.factory('greeter', ['$window', function($window) {
  return {
    greet: function(text) {
      $window.alert(text);
    }
  };
}]);

// Create a new injector that can provide components defined in our myModule module
var injector = angular.injector(['myModule']);
var greeter = injector.get('greeter');
```


```html
<div ng-controller="MyController">
  <button ng-click="sayHello()">Hello</button>
</div>
```

```js
function MyController($scope, greeter) {
  $scope.sayHello = function() {
    greeter.greet('Hello World');
  };
}

// When AngularJs compiles the HTML, it processes the ng-controller directive by asking the injector to create an instance of the controller and its dependencies
injector.instantiate(MyController);
```

- Finally, the application code simply declares the dependencies it needs, without ever knowing about the injector. This setup does not break the [Law of Demeter](https://en.wikipedia.org/wiki/Law_of_Demeter)



Depth in Data Binding


Browser's Event Loop

- The browser's event-loop waits for an event to arrive. An event is a user interaction, timer event, or network event (response from a server)
- The event's callback gets executed. This enters the JavaScript context. The callback can modify the DOM structure
- Once the callback executes, the browser leaves the JavaScript context and re-renders the view based on DOM changes


AngularJs Event Processing loop

![concepts-runtime](../dist/img/concepts-runtime.png)


- Enter the AngularJS execution context by calling scope.$apply(stimulusFn)
- AngularJS executes the stimulusFn(), which typically modifies application state
- AngularJS enters the $digest loop. The $digest loop keeps iterating until the model stabilizes, which means $watch list does not detect any changes and $evalAsync queue is empty


- The $evalAsync queue is used to schedule work which needs to occur outside of current stack frame, like $http request
- The $watch list is a set of expressions which may have changed since last iteration. If a change is detected then the $watch function is called which typically updates the DOM with the new value
- Once the AngularJS $digest loop finishes, the execution leaves the AngularJS context. The browser re-rendering the DOM to reflect any changes




AngularJs's sweet spot


- A higher level of abstraction to the developer. Like any abstraction, it comes at a cost of flexibility


- In other words, not every app is a good fit for AngularJs. AngularJs was built with the CRUD application in mind


- Such as Games and GUI with intensive and tricky DOM manipulation. In these cases it may be better to use a library with a lower level of abstraction, such as jQuery



[simple demo](http://xiaodongli.me/)
