---
layout: post
title:  "Self in Ruby"
date:   2013-11-16 09:30:12
categories: ruby
---

‘self’ in Ruby is really important conception, one of this: if you understand it, your Ruby-journey will be much more fun and aware. self in Ruby program means current object or default object. When your program is running, at every point of it there could be only one object that are accessible via self. If you want to know what self is in particular part of the program, you must know the context you’re in. For example, self is different inside class definition and inside instance method. Let’s write very basic example and see, what self means in the different context:

{% highlight ruby %}
puts "We are in top level context: #{self}"

class Test
  puts "We are in class context: #{self}"

  def self.some_class_method
    puts "We are in class method: #{self}"
  end

  def some_instance_method
    puts "We are in instance method: #{self}"
  end
end

Test.some_class_method

t = Test.new
t.some_instance_method	
{% endhighlight %}

## Top level context

When you run the above example, you can see that outside of any class or method self is main object. What is main? Outside class or method Ruby give us default self object. Main is a special term that the default self object uses internally to refer to itself. Internally means that you can’t refer to main directly. So if you try to do something like this:

{% highlight ruby %}
puts "#{main}"	
{% endhighlight %}

you’ll get an error as Ruby will treat main as an regular variable and try to find it.

## Class and module context

When your program is inside class or module definition, the self meaning changes (see second and third puts invocation in the above example). In this context self means class or module object. Let’s see another example to understand it right:

{% highlight ruby %}
class OutsideClass

  puts "self means: #{self}"

  module InnerModule
    puts "self means: #{self}"
  end

  puts "self means: #{self}"

end	
{% endhighlight %}

When you run the program, you get:

{% highlight ruby %}
self means: OutsideClass
self means: OutsideClass::InnerModule
self means: OutsideClass	
{% endhighlight %}

As we can see inside class, self is class object (in our example this is OutsideClass) but when program reaches module the self switch into InnerModule object.

## Class method context

Inside class method:

{% highlight ruby %}
class SomeClass

  def self.some_method
    puts "self means: #{self}"
  end

end

SomeClass.some_method	

#=>self means: SomeClass
{% endhighlight %}

self is also class object (in our example SomeClass object).

## Instance method context

Let’s see how situation looks like when program runs inside instance method. To check this we have to send message to the method (call the method).

{% highlight ruby %}
class SomeClass
  def some_method
    puts "self means: #{self}"
  end
end	

#=> self means #<C:0x007fbaf410b678>
{% endhighlight %}

This a little strange entry means that inside instance method self is the instance of the object. In the example above self is the instance of the SomeClass class.

## Singleton method context

The last example is about singleton method defined directly on the object. Let’s see how we can create such method:

{% highlight ruby %}
some_obj = Object.new

def some_obj.some_method
  puts "self means: #{self}"
end

some_obj.some_method	
{% endhighlight %}

As you can see for singleton method self means also the instance of the object. However, this time this is the instance of the object on which the method was defined.

## Know better your instance variable

Knowing what self means in different place in a program can help you to better understand the instance variables. The rule is simple here: every variable in the Ruby program belongs to the object which is the current object (self) at that point of the program. Let’s see that example:

{% highlight ruby %}
class SomeClass

  def some_method
    @var = 'I belongs to object instance'
    puts @var
  end

  @var = 'I belongs to class object'

end

c = SomeClass.new
c.some_method	
{% endhighlight %}

The example above prints out:

{% highlight ruby %}
I belongs to object instance	
{% endhighlight %}

So inside the method, instance variable belongs to SomeClass instance object. Instance variable inside the instance method and instance variable in the class context are entirely different variables. So they can live in our program independently.
	

	
	









	
	

	