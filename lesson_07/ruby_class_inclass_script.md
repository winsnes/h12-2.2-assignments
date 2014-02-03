Tema 17 - Økt 07 - Ruby Class & Inheritance Workshop
====================================================

## Ruby Class

Build Car class step by step

- class structure
- initialize method
- set instance variables

```ruby
class Car

  def initialize(make, model)
    @produced_at = Time.now
    @make = make
    @model = model
    @running = false
  end

end
```

Add method to start car

```ruby
class Car

  def initialize(make, model)
    @produced_at = Time.now
    @make = make
    @model = model
    @running = false
  end

  def start
    @running = true
    puts "Wroooooom"
  end

  def is_running?
    @running
  end

end
```

Create some cars

```ruby
my_car = Car.new "Mercedes", "S-Class"
my_car.is_running?
my_car.start
my_car.is_running?
```

Attempt to get produced_at

```ruby
my_car.produced_at
```

Make produced_at attribute readable

```ruby
class Car

  attr_reader :produced_at

  def initialize(make, model)
    @produced_at = Time.now
    @make = make
    @model = model
    @running = false
  end

  def start
    @running = true
    puts "Wroooooom"
  end

  def is_running?
    @running
  end

end
```

Recreate the car and attempt to check produced_at again

```ruby
my_car = Car.new "Mercedez", "S-Class"
my_car.produced_at
```

Produced_at has no need to be editable
You might want to recolor the car
Add color attribute and make it editable

```ruby
class Car

  attr_accessor :color
  attr_reader :produced_at

  def initialize(make, model)
    @produced_at = Time.now
    @make = make
    @model = model
    @running = false
    @color = "black"
  end

  def start
    @running = true
    puts "Wroooooom"
  end

  def is_running?
    @running
  end

end
```

```ruby
my_car = Car.new "Mercedez", "S-Class"
my_car.color
my_car.color = "orange"
my_car.color
```

So far, this is just like what we did in Processing and JavaScript
Let's up the game a bit

Don't close this irb session!

## Ruby Inheritance

Start with car from previous code

```ruby
class Car

  attr_accessor :color
  attr_reader :produced_at

  def initialize(make, model)
    @produced_at = Time.now
    @make = make
    @model = model
    @running = false
    @color = "black"
  end

  def start
    @running = true
    puts "Wroooooom"
  end

  def is_running?
    @running
  end

end
```

There are many types of cars
We have now described a very generic one
Let's make an electric car
It shares many of the attributes of a regular car

Make ElectricCar class that inherits from Car

```ruby
class ElectricCar < Car

end
```

Demonstrate that an ElectricCar has the same properties as the Car

```ruby
my_el_car = ElectricCar.new "Tesla", "Model S"
my_el_car.is_running?
my_el_car.start
my_el_car.is_running?
my_el_car.produced_at
```

But it also has some special abilities
It can perhaps, among other things, have a method to be_admired

```ruby
class ElectricCar < Car

  def be_admired
    puts "Oh yes, I'm a sexy sexy #{@color} car!"
  end

end
```

```ruby
my_el_car = ElectricCar.new "Tesla", "Model S"
my_el_car.be_admired
```

Compare to an instance of the original Car

```ruby
my_old_car = Car.new "Peugeot", "306"
my_old_car.be_admired
```

Undefined method!
Why u no admire old car??

Let's add one more differentiator to the ElectricCar
Electric cars don't make a "Wrooooom" sound when they start
Add a start method to the ElectricCar

```ruby
class ElectricCar < Car

  def be_admired
    puts "Oh yes, I'm a sexy sexy #{@color} car!"
  end

  def start
    @running = true
  end

end
```

```ruby
my_el_car = ElectricCar.new "Tesla", "Model S"
my_el_car.is_running?
my_el_car.start
my_el_car.is_running?
```

We now overwrote the start method that was originally inherited from Car
Any ElectricCar instance will start without a sound, but any regular Car will still make the sound

When we have inheritance between many classes, we can always check who the parent class is by asking for the superclass

```ruby
ElectricCar.superclass
```

But who is then the parent class of Car?

```ruby
Car.superclass
```

Yep, it's simply an object
Can we go deeper?

```ruby
Object.superclass
```

It's a BasicObject!
Once more with feeling?

```ruby
BasicObject.superclass
```

Ah, oh well
