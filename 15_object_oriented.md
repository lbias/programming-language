# 什么是面向对象
所谓的对象(Object)就是指一个带有状态和方法的容器，例如以下是 JavaScript 中的自定义对象：

```
 var my_object = {
  name: "ihower",
  move: function(){
    console.log( this.name + " is moving" )
  }
}

my_object.move()
```

`my_object` 有两个属性，一个是 `name` 一个是 `move`，而 `move` 其实是一个匿名函式。这个对象有自己的状态(`name` 是 ihower)，有自己的方法(move)。我们把相关的数据和方法，一起包进物件里面。

`my_object.move()` 可以看成是朝对象 `my_object` 发送一个 `move` 讯息。接收者 `my_object` 接到一个 `move` 讯息。

向面对象(Object-Oriented)程序设计指的就是以对象为基础的编程方式，整个软件就是一群对象之间的互动。

## 为什么要用面向对象？

在编程语言第一集中，算法和数据结构强调的是正确性和高效率。但是在面向对象要追求的是软件的扩充性、维护性、修改弹性、可读性、可测性，是一种将代码适当安排组织的一种设计方式。

Ruby 是一个非常面向对象思维的编程语言，在 Ruby 中其实每一个数据其实都是对象。

## Class-based 基于类的面向对象设计

主流编程语言中，只有 JavaScript 可以像刚刚一样直接创造一个自定义对象，其他编程语言包括 Ruby，都是要先定义类(Class-based)才能创造对象。所谓的类(Class)就是去定义了某一种类型的对象所拥有的属性和方法。类别是抽象的事物，而不是其所描述的特定对象。以下的 Ruby 代码先定义了 `Person` 类，然后再用 `new` 方法创造出两个对象 `p1` 和 `p2`：

```
class Person
  attr_accessor :name

  def move
    puts "Hello, #{@name}"
  end
end

p1 = Person.new
p1.name = "ihower"
p1.move # 输出 Hello, ihower


p2 = Person.new
p2.name = "John"
p2.move # 输出 Hello, John
```

你可以想像类(Class)就是对象(Object)的模版，这些对象都有 `name` 属性和一个 `move` 方法。

> 名词释疑：除了 Object (对象)的讲法，这些由类产生出来的 Object，又叫做 Instance (实例)
