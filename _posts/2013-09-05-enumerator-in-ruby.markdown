---
layout: post
title:  "Enumerator in Ruby"
date:   2013-09-05 09:30:12
categories: ruby
---

Enumerator is related to iterators. The significant difference between the two is that Enumerator is an object and iterator is a method. Basically, Enumerator is a simple enumerable object with each method. It uses Enumerable module to define such methods like: take, drop, map, select etc. directly on top of its each method. The most interesting thing is how each method of Enumerator is defined. Enumerator doesn’t have built-in each method. Every time you create Enumerator, you have to specify this method. After you’ve did it, Enumerator knows how to use other enumerable methods (map, reduce etc.)

You can define each method algorithm in one of two ways:

* call Enumerator.new with code block, which contains the each logic
* attach enumerator to other object

## Enumerator.new with code block

Lets’ see how we can create Enumerator calling new method on it. This is a simple example:

{% highlight ruby %}
e = Enumerator.new do |y|
  y << 'a'
  y << 'b'
  y << 'c'
  y << 'd'
end	
{% endhighlight %}

y is an instance of Enumerator::Yielder, which is automatically passed to the block. In the block we teach yielder what should happens when an each is called. Let’s try to call some enumerable methods on our enumerator in irb console:

{% highlight ruby %}
e.to_a
#=> ["a", "b", "c", "d"] 
e.take(2)
#=> ["a", "b"] 
e.map(&:upcase)
#=> ["A", "B", "C", "D"]	
{% endhighlight %}

As you can see each method works as expected. You can provide to Enumerator block any logic you like. For example, if you want to work on the whole alphabet, you can pass something like this:

{% highlight ruby %}
e = Enumerator.new do |y|
  ('a'..'z').each { |letter| y << letter }
end	
{% endhighlight %}

I even found so crazy and useless example:

{% highlight ruby %}
a = [1,2,3,4,5,6]

e = Enumerator.new do |y|

  sum = 0

  until a.empty?
    sum += a.pop
    y << sum
  end

end

e.take(3) #=> [6, 11, 15] 
a #=> [1, 2, 3] 
e.map { |n| n*2 } #=> [6, 10, 12] 
a #=> []		
{% endhighlight %}

## Attach enumerator to other object

The second method to work with enumerators is more common. What you have to do here, is hooking up the enumerator to another object iterator (the most often to the each method but it can be any method that yields values). This will give the enumerator the basic knowledge what to do when each logic is called.
To use this approach we call enum_for on the object from which we want to borrow iteration logic. As a first argument, we pass the name of the method onto which the enumerator will attach its each method. Let’s see the example:

{% highlight ruby %}
a = [1,2,3,4]

e = a.enum_for(:select)
e.each { |n| n > 2 }	
{% endhighlight %}

When we run this code in irb console, we get [3,4]. Here we use select method as a foundation of enumerator each method. After defined that, we can pass to each method block the condition we normally use for select method and it works :)

We can also pass additional arguments to enum_for method. Example:

{% highlight ruby %}
a = [1,2,3,4,5]

e = a.enum_for(:inject, 'Countdown:')
e.each { |string, n| string + " #{n}..." }

#=> "Countdown: 5... 4... 3... 2... 1..."	
{% endhighlight %}

## Enumerator uses

### Protecting objects with enumerators

One situation when enumerators can be useful is when you’d like to protect object from changes. Let’s say that you have an method that takes an array as an argument:

{% highlight ruby %}
def some_method(arr)
  #some code here
end	
{% endhighlight %}

Then the method can change the passed object:

{% highlight ruby %}
def some_method(arr)
  arr.pop
end

array = [1,2,3,4]
some_method(array)
puts array # [1,2,3]	
{% endhighlight %}

What can you do if you’d like to protect array argument form being changed? Yes! You can use Enumerator for this:

{% highlight ruby %}
array = [1,2,3,4]
some_method(Enumerator.new(array))
puts array # undefined method `pop' for #<Enumerator: [1, 2, 3, 4]:each> (NoMethodError)	
{% endhighlight %}

### Enumerator can remember state

Enumerator is an object, so it remembers its state. It allows us to do something along these lines:

{% highlight ruby %}
arr = %w{Paris London Rome}
e = arr.to_enum
puts e.next # Paris
puts e.next # London
puts e.next # Rome
puts e.rewind # returns to the beginning
puts e.next # Paris	
{% endhighlight %}

### Add enumerability to object

Enumerator can add enumerability to objects that don’t have it but they possess an iterator method. Let’s say we have a class:

{% highlight ruby %}
module MyModule
  class Car

    BRAND = %w{ BMW Ford Toyota Ferrari }

    def wiki
      BRAND.each { |brand| yield(brand) }
    end

  end
end

cars = MyModule::Car.new
puts cars.map(&:upcase) # it won't work: undefined method `map' for #<MyModule::Car:0x007fc819886628>	
{% endhighlight %}

Our class doesn’t mixin Enumerable module, so it can use such method as map. So what can we do? The easiest solution is mixin Enumerable module, of course. But we can also use Enumerator:

{% highlight ruby %}
e = cars.to_enum(:wiki)
puts e.map(&:upcase)	
{% endhighlight %}

### map_with_index – here you are!

Enumerable module has a very handy method – each_with_index:

{% highlight ruby %}
brands = %w{ BMW Ford Toyota Ferrari }
brands.each_with_index do |brand, index|

  puts "Brand #{index+1}: #{brand}"

end	
{% endhighlight %}

It will be good to have similar method, that passes index as an argument but with ability of map method. There is no map_with_index method but Enumerator class has with_index method which we can use instead:

{% highlight ruby %}
brands.map.with_index { |brand, index| [brand, index] }
# [["BMW", 0], ["Ford", 1], ["Toyota", 2], ["Ferrari", 3]]	
{% endhighlight %}

How it works? If we call an enumerable method without block, it returns Enumerable object. When we get one, we can use it to call with_index method.

As you can see, there are a lot of situations, when Enumerator can be really useful. For further reading about Enumerator and Enumerable module, I recommend article “Building Enumerable & Enumerator in Ruby”.


	
	

	
	
	






	