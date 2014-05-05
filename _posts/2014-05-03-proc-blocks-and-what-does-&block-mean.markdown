---
layout: post
title:  "Proc, blocks and what does &block mean ?"
date:   2014-05-03 09:30:12
categories: ruby
---

When you start write in Ruby, sooner or later (rather sooner), you will meet with the concept of Proc and blocks. Especially the last one is something really popular in Ruby, so it's good to know what is this and why is it so useful. I think that there are many sources that can explain the concept of blocks, therefore I'm not going to write another article about basics of the blocks. For me the more interesting is the differences between Proc and blocks and how is it possible that you can write some methods like this:

{% highlight ruby %}
def my_method(&block)
  block.call   
end	
{% endhighlight %}

What happens when you execute such methods and why is this possible to give it as an argument block or Proc interchangeably? These are question that I will try to answer today.

## Proc class

Let's do quick reminder: Proc is a class. To create and instance of that class, we call method *new* and as an argument we give block:

{% highlight ruby %}
my_proc = Proc.new { puts "I'm Proc object" }	
{% endhighlight %}

Exactly the same effect you get when you use *proc* method, since the method returns the instance of Proc object: 

{% highlight ruby %}
my_proc = proc	{ puts "I'm Proc object" }
{% endhighlight %}


Now, when we have this instance of the Proc object we can send it the *call* message:

{% highlight ruby %}
my_proc.call #=> "I'm Proc object"	
{% endhighlight %}

This caused that the block was invoked. So you can imagine the Proc object as a box in which you can preserve block of code and exetute it later.

## The differences between blocks and Proc'a

So we already know that Proc is a class and we can create an instance of it. Block on the other hand is not an **object**. We can't create any instance of it and we are not able to send any message to it. Moreover, we can't send it as an argument to a method, since methods can only take objects as arguments. Wait a minute. So how is it possible that we can call such method ?

{% highlight ruby %}
['a','b','c'].each { |letter| letter.upcase }	 	
{% endhighlight %}

Interesting, isn't it ? Let's investigate how the signature of the method looks like.

{% highlight ruby %}
def each(&block)
end	
{% endhighlight %}

Here we are our misterious operator &. It looks like this additional sign before argument causes that this is possible to send block as an argument, but how exactly does this work ?

## &block

Let's start with an example of method:

{% highlight ruby %}
def simple(&block)
  block.call
end	

simple { puts "I'm just a block" }
{% endhighlight %}

What really happens here is that Ruby in background triggers *to_proc* method on the block. As a results we get Proc instance. So inside method we do not operate on block any more, but we have Proc instance for our disposal. That's why we can send *call* message to *block* argument.

But this is not the whole story yet. In real world, our simple method can also take Proc instance as an argument as well.

{% highlight ruby %}
my_proc = Proc.new { puts "I'm a proc" }	
simple(&my_proc)
{% endhighlight %}

In this case we use *&* operator in method calling and it's job is quite different here. *simple* method is waiting for a block, not a Proc instance. So in this case *&* operator does two jobs:

* tells *simple* method that proc instance is serving as a block
* calls *to_proc* method on *my_proc*. In case of Proc instance, this method just returns the instance itself.
	

	
	


	
	