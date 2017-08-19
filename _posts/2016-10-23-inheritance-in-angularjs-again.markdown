---
layout: post
title:  "Inheritance in AngularJS, again"
date:   2016-10-23
category: coding
backgrounds:
    - https://blog.jdonado.com/assets/images/jsbg.jpg
thumb: https://blog.jdonado.com/assets/images/inheritance-again.jpg
tags: angularjs inheritance ecmascript5
---

### Rethinking about inheritance in AngularJS 1.5

In [a previous post]({% link _posts/2016-08-06-inheritance-and-observer-pattern-in-angular-js.markdown %}) I showed an example about how could inheritance be implemented in an AngularJS project, using Javascript (ECMAScript 5).

In that example I used it to implement the [observer pattern](https://en.wikipedia.org/wiki/Observer_pattern) in different services without having to define the same functions again and again.

I think it is a common problem when dealing with the (now old) ECMAScript 5, as long as you want to avoid code repetition and structure your code in a similar fashion as you would with a pure object oriented language, such as Java or C#.

And yes, you can sort of implement classical inheritance in Javascript. *But should you?*

After thinking about it and trying out many experiments, I decided it's not really worth it, since **it complicates your code and makes it more difficult to read and maintain**. But of course, **repeating the code is no solution either**. So maybe we can find an easier solution.

I think we shouldn't fight against the language we're using: it's very tiring and at the end of the day it's going to make our code worse.

Trying to do something the language was not designed for is somehow fighting against it. Instead of that, a better solution would be using the native characteristics of that language to fulfill our goals.

Javascript provides many alternatives to classical inheritance if you're willing to embrace prototypal inheritance. [Eric Eliott has many very interesting articles about it](https://medium.com/javascript-scene/common-misconceptions-about-inheritance-in-javascript-d5d9bab29b0a).

Here I come with an alternative solution that worked for me with AngularJS services. The idea is a service with a toolset of constructors, implementing the code of the objects we may want to use again and again. Something like a [factory](https://en.wikipedia.org/wiki/Factory_method_pattern).

{% highlight javascript %}

'use strict';
angular.module('exampleApp', [])
  .service('factoryService', [function () {
    return {
      // Observer is going to be our constructor
      Observer: function () {
        var observer = {},
          observerCallbacks = [];

        observer.registerObserverCallback = function (callback) {
          if (typeof callback !== "function") {
            return;
          }
          observerCallbacks.push(callback);
        };

        observer.unregisterObserverCallback = function (callback) {
          var found = -1;
          observerCallbacks.forEach(function (element, idx) {
            if ((element + '') === (callback + '')) {
              found = idx;
            }
          });
          if (found !== -1) {
            this.observerCallbacks.splice(found, 1);
          }
        };

        observer.notifyObservers = function () {
          angular.forEach(this.oberverCallbacks, function (callback) {
            callback();
          });
        };

        return observer;
      } // end: Observer constructor
    };
  }]);

{% endhighlight %}

Depending on how many constructors you want to define, you can have different factory services.

Now, if you want to create a new service that implements the functions defined in `Observer`, you can do something like this:

{% highlight javascript %}

angular.module('exampleApp', [])
  .service('NewService', ['factoryService', function (factoryService) {
    // New object using the constructor defined in the factory
    var observable = new factoryService.Observer();
    return {
      observable: observable,
      otherStuff: ['service', 'specific', 'things']
    };
  }]);   

{% endhighlight %}

This way, you will have a property in the `NewService` that's called `observable`, on which you can call the functions of an `Observer` object that only exists in `NewService`.

You have not repeated the code of the functions defined in `Observable` and the code is easier to understand and maintain.

What do you think about this approach? Would you prefer [the other one]({% link _posts/2016-08-06-inheritance-and-observer-pattern-in-angular-js.markdown %})? Do you see any potential problems when doing something like this?

Let me know!
