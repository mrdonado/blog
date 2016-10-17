---
layout: post
title:  "ng-show and ng-animate"
date:   2016-08-08 19:26:53 +0200
categories: angularjs 
---


If ng-show happens to be too slow without an obvious reason

It happened to me tonight that ng-show was taking too long to refresh. I know [the differences between ng-if and ng-show](http://stackoverflow.com/questions/19177732/what-is-the-difference-between-ng-if-and-ng-show-ng-hide) and how sometimes ng-show can decrease performance. But in this case it was a pretty straightforward example, and it shouldn't.

In the end it turned out to be the combination of ng-show and ng-animate. I wasn't using any animation, but there was a css transition running anyway, and making things look slower than they were.

If any of this ever happens to you, and you don't want to remove ng-animate from your project, simply add the .no-animate class to the element where ng-show or ng-hide is used, and update your css with the following lines.

        
{% highlight css %}
.ng-animate.no-animate {
  transition: 0s none;
  -webkit-transition: 0s none;
  animation: 0s none;
  -webkit-animation: 0s none;
}
{% endhighlight %}
        
    
As simple as that.
