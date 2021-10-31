---
layout: post
title:  "Inheritance and Observer Pattern in AngularJS 1.4"
date:   2016-06-12
category: coding
backgrounds:
    - https://blog.jdonado.com/assets/images/jsbg.jpg
thumb: https://blog.jdonado.com/assets/images/observer-angular.jpg
tags: angularjs design-patterns observer inheritance 
---

In AngularJS, when designing services, it is well common to return an instance of the **$http** service. The code that requests it (usually within a controller), must process then the promise and do whatever it is to be done with the data that come from server.

Like in this example.

{% highlight javascript %}
'use strict';
angular.module('exampleApp', [])
  .service('ItemsService', ['$http', function ($http) {
    this.getItems = function () {
      return $http.get('www.myapi-url.com/items');
    };
  }])
  .controller('ExampleController', ['ItemsService', function (ItemsService) {
    var self = this;

    this.itemsList = []; // A property of the controller stores an array with items, initially empty.

    ItemsService.getItems().then(
      function (data) {
        self.itemsList = data.data;
      },
      function (error) {
        console.log("There has been an error" + error);
      }
    );
  }]);            
{% endhighlight %}            

        
But, sometimes you just don't want to handle the server information in the controller, because you might need to do it in many other places in your app, and you don't want to repeat the same code in your controllers. After all, it seems reasonable that any processing related to your items (filters, date parsing or whatever you need to do) can be encapsulated in that ItemsService, so that you can reuse it easily.

Specially if it is something that must be done over and over, because the API you're working with doesn't exactly give you back what you need or in the format that you need.

On the other hand, you might want to reduce the number of http requests your app performs, and storing these data as a property of the service might be convenient.

## Solution: the Observer Pattern

This is why I thought it could be a good idea to implement the [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern) in angular services that retrieve information from server, store it and manipulate it in some way.

But if you want to implement this pattern in more than one service, it is better to define it just once and create a new service that inherits that pattern whenever necessary.

Since inheritance is something that can be a bit tricky when using Javascript, and even more if you want to combine it with Angular, I thought it might be useful to other people if I shared my solution.

I've created [a git repository](https://github.com/mrdonado/notes-app) with a complete working example. Next, I'll describe briefly how it works.

### The Observable Service (Parent)

First, a service is declared with the functions that implement the observable pattern. But instead of returning a JSON, as usual in an AngularJS service, we return a function:

{% highlight javascript %}
angular.module('notesApp')
  .factory('observableService', function () {
    // A constructor is defined in order to create further services that extend this functionality
    return function () {
      this.observerCallbacks = [];
      //register an observer
      this.registerObserverCallback = function (callback) {
        if (typeof callback !== "function") {
          return;
        }
        this.observerCallbacks.push(callback);
      };
      //unregister an observer
      this.unregisterObserverCallback = function (callback) {
        var found = -1;
        this.observerCallbacks.forEach(function (element, idx) {
          if ((element + '') === (callback + '')) {
            found = idx;
          }
        });
        if (found !== -1) {
          this.observerCallbacks.splice(found, 1);
        }
      };
      this.notifyObservers = function () {
        angular.forEach(this.observerCallbacks, function (callback) {
          callback();
        });
      }
    };
  });
{% endhighlight %}
    
### Child Observable Service

Then, in order to inherit these functions, you can simply do the following ([extract from the file configuration-service.js](https://github.com/mrdonado/notes-app/blob/master/app/scripts/services/configuration-service.js)).

{% highlight javascript %}    
angular.module('notesApp')
  .factory('configurationService', function ($http, observableService) {
    var configService = {};
    // The config service inherits observable service
    configService = new observableService();

    configService.someNewProperty = "Initial Value";

    return configService;

  }
{% endhighlight %}
    
### Inheritable Service that Inherits the observableService

If you need to create a service that inherits from the observableService, that is also inheritable by other services, you should return a function, instead of a JSON object. This is what we do in [basic-resource.js](https://github.com/mrdonado/notes-app/blob/master/app/scripts/services/basic-resource.js).

{% highlight javascript %}
angular.module('notesApp')
  .factory('basicResource', ['observableService', '$http', 'configurationService',
    function (observableService, $http, configurationService) {

      var basicResource;

      // some private variables and functions can be added here
      var privateVar = "privateValue";

      var privateFunction = function (){
        return "privateFunction";
      };

      // The function you return works as the constructor of your class
      basicResource = function () {
        // In your new constructor, you apply the constructor of the class you want to inherit
        observableService.apply(this, arguments);
        var self = this;

        // here you define the properties of the service
        this.allElements = [];

        this.otherPublicProperty = "Initial Value";

      };

      // We inherit from observableService now
      basicResource.prototype = new observableService();

      return basicResource;

    }]);
{% endhighlight %}
 
### Controller that Observes Services that Inherit from observableService

Now that you have observable services, you can simply define the callbacks in your controllers, and these will be updated whenever the service retrieves new information.

{% highlight javascript %}
 angular.module('notesApp').controller('MainController',
  ['$scope', 'configurationService', 'notesService',
    function ($scope, configurationService, notesService) {
      'use strict';

      var self = this;

      // Callback to be run when the configuration is ready
      var configurationReady = function () {
        notesService.loadElements();
      };

      // Callbacks to update the scope when new elements are available
      var updateNotes = function () {
        self.notes = notesService.allElements;
      };

      // Register observer callbacks
      notesService.registerObserverCallback(updateNotes);
      configurationService.registerObserverCallback(configurationReady);

      if (configurationService.isConfigurationReady()) {
        configurationReady();
      } else {
        // The observer has been registered: when ready, loadElements will be automatically called.
        configurationService.loadConfiguration();
      }

    }]);
{% endhighlight %}

That was it. If you want to explore the complete repository, or even experiment with it and modify it, feel free to fork me on github: [https://github.com/mrdonado/notes-app](https://github.com/mrdonado/notes-app).
