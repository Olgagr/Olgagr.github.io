---
layout: post
title:  "Method’s arguments in Ruby"
date:   2013-04-23 09:30:12
categories: ruby
---

In Ruby every method you define can take list of the arguments. You define them as a comma-separated list after the method name:

{% highlight ruby %}
def my_method(a,b,c)
  #method code here
end
{% endhighlight %}

You can define required and optional arguments for the method. If you call the method with wrong number of the required arguments, Ruby will complain that there is an error:

{% highlight ruby %}
def my_method(a,b,c)
  #method code here
end
 
my_method 1 
#=> ArgumentError: wrong number of arguments (1 for 3)
{% endhighlight %}

## Any number of arguments

Sometimes you don’t want to define the exact number of the arguments. Ruby allows you to define such methods. To do this, you put an asterisk (*: a star) in front of the name of the single argument:

{% highlight ruby %}
def my_method(*args)
  #method code
end

my_method 1
my_method 1,2
{% endhighlight %}

The *args notation means that the method can take zero, one or more arguments. After calling the method, the arguments are converted into the array. So you can get them traversing the array:

{% highlight ruby %}
def my_method(*args)
  puts args[0]
end

my_method 1,2 
#=> puts 1
{% endhighlight %}

## Default value fot an argument

You can also define default values for method’s arguments:

{% highlight ruby %}
def my_method(a,b=2)
  puts a+b
end

my_method 1 # puts 3, as b has default value
my_method 1,1 # puts 2, as b has now value 1	
{% endhighlight %}

In these example, in line 5 we don’t give second argument, so b equals 2. In line 6 we overwrite the default value and b equals 1.

## Let’s play with arguments

Let’s play a little bit with arguments to understand how Ruby works. What would happend if we call the method like this:

{% highlight ruby %}
my_method(a,*b,c)
  puts a, b, c
end

my_method 1,2,3,4	
{% endhighlight %}

Ruby tries to give the value all the arguments. The argument with asterisk gets the lowest priority. So first of all, the a and c arguments get values. If there are more arguments to allocate, they will be put into b arguments (it’s an array, as you remember). So calling the method this way, we gets:

{% highlight ruby %}
1
[2,3]
4	
{% endhighlight %}

But if we call the method with any 2 arguments:

{% highlight ruby %}
my_method 1,2	
{% endhighlight %}

we get:

{% highlight ruby %}
1
[]
2	
{% endhighlight %}

In this case, Ruby sets value for the a and c arguments. Nothing leave for b argument, so we get empty array.

Let’s do our example even more complicated and add default value for an argument.

{% highlight ruby %}
def my_method(a, b=2, *c, d)
  puts a,b,c,d
end	
{% endhighlight %}

So right now we have two mandatory arguments (a and d), one argument with default value (b) and optional argument (c). So what will happen if we call out method like this:

{% highlight ruby %}
my_method 1,3,2,4,5	
{% endhighlight %}

As we already know, the mandatory arguments are given the values in the first place. After that Ruby give arguments value from left to right. So first the b argument gets value and then c. So we’ll get:

{% highlight ruby %}
1
3
[2,4]
5	
{% endhighlight %}

If we call the method with only two arguments, we get:

{% highlight ruby %}
my_method 1,3

# results in
1
2
[]
3	
{% endhighlight %}

## Order of the arguments makes difference!

You must be aware of the order of the arguments in a metod definition. Let’s see one more example:

{% highlight ruby %}
def my_method a, *b, c=1
  #method code
end	
{% endhighlight %}

This will give us the syntax error. Let’s think about it: first a argument gets value, then Ruby gives value for the rest of argument from left to right. So there is no way that c gets value because all velues are put into b array. So always remember to put asterisk argument AFTER arguments with default values.

	
	
	

	
	
	
	








	
	




