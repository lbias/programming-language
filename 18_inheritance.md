# 特性二: 继承(inheritance)

继承可以让父类的定义都复制到子类，在 Ruby 中用 `class Child < Parent` 符号表示：

```
class Vehicle

  def move
    puts "vehicle move"
  end

end

class Car < Vehicle

  def foo
    puts "car foo"
  end

end

class Bike < Vehicle

  # overwrite
  def move
    puts "special bike move"
  end

  def foo
    puts "bike foo"
  end

end

car = Car.new
bike = Bike.new

car.move()
bike.move()

car.foo()
bike.foo()
```

`Vehicle` 类定义了 `move`方法，而 `Car` 跟 `Bike` 都继承自 `Vehicle`，因此他们也都有 `move` 方法。但是在 `Bike` 中你也可以复写掉 `move` 方法。

在 Rails 中也很常见继承，打开任一个 Model 档案，都是继承自 `ApplicationRecord`。再打开会发现 `ApplicationRecord` 继承自 `ActiveRecord::Base`。后者是 Rails 框架的核心类，我们之所以可以调用 `.save`、.`find`、`.where` 等等方法就是在 `ActiveRecord::Base` 中定义的。。

打开任一个 Controller 档案，都是继承自 `ApplicationController`，然后 `ApplicationController` 又是继承自 `ActionController::Base`，我们之所以可以调用 `before_action`、`render`、`redirect_to` 等等方法就是在 `ActionController::Base` 中定义的。

透过继承，我们写的 Model 和 Controller 都是基于 Rails 已经写好的功能进行扩充而已。

## 多重继承

上述的写法只能单一继承，也就是只能有一个父类。如果有多个父类要继承，在 Ruby 中会用到 module：

```
module Ownership
  def show_owner
    puts "#{self.class} show_owner called"
  end
end

class Vehicle
  def move
    puts "move"
  end
end

# Car has two parents: Car and Ownership

class Car < Vehicle
  include Ownership
end

class House
  include Ownership
end

car = Car.new
house = House.new

car.show_owner()
house.show_owner()
```

其中 `module Ownership` 会用 `include` 的语法 mix-in(混入)到 `Car` 里面。

> 那 class 跟 module 有什么不一样？你不能实例化 module，也就是不能对 module 调用 `new` 方法来产生对象。

## module 命名空间

顺道一提 module 的另一个用途，就是拿来做命名空间，也就是让常数的命名长一点避免撞名：

```
module  A
  class B
  end
end
```

这个被 module A 包起来的 class B，如果要使用它要用 `A::B`。

如果 module A 已经定义过了，则可以这样写：

```
class A::C

end
```

假如在 module A 里面定义了一个跟最外层撞名的类，这时候如果要拿外层的类，需要加上 `::` 符号：

```
class Person
end

module A
  class Person
    def foo
      Person    # 这个会是指 A::Person
      ::Person  # 前面要加 :: 表示要拿最外层的 Person
    end
  end
end
```

最后，可以用 module 来定义模块方法：

```
module MyUtil

  def self.foobar
    puts "foobar"
  end

end

MyUtil.foobar
# 输出 foobar
```

Ruby 的 [Math API](https://ruby-doc.org/core-2.2.0/Math.html) 就是长这种形式。
