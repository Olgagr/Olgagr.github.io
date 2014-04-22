---
layout: post
title:  "How to get maximum or minimum value from an array in JavaScript ?"
date:   2014-02-02 09:30:12
categories: javascript
---

JavaScript is great but sometimes it’s really annoying because it’s lack simple, built-in functions. Let’s say we have an array of the integers:

{% highlight javascript %}
var arr = [2, 10, 30, 7, 4];	
{% endhighlight %}

and we want to get the highest value from the array. We can of course iterate the array and check each value:

{% highlight javascript %}
var len = arr.length,
max = 0; 

for(var i=0; i<len; i++){ 
  if(arr[i] > max) {
    max = arr[i]
  } 
};	
{% endhighlight %}

but it’s so much code!

So to do it in more subtle way, let’s do it in only one line of code:

{% highlight javascript %}
Math.max.apply(Math, arr)	
{% endhighlight %}

In JavaScript we have built-in object Math, which has a method max. This method does what it should be: count the highest value for the arguments list. The problem is that we can’t give it as an argument the array. So this will be working:

{% highlight javascript %}
Math.max(2, 10, 30, 7, 4) // 30	
{% endhighlight %}

but this won’t:

{% highlight javascript %}
Math.max([2, 10, 30, 7, 4]) // NaN
{% endhighlight %}

So to get it around we can use apply method. Every function in JavaScript has this method built-in. It allows as to invoke function in given context with array of arguments. It’s exactly what we need here. We give as a context Math object but we can also do something like this and our example will still works:

{% highlight javascript %}
Math.max.apply(null, arr)	
{% endhighlight %}
	


	




	