# 特性一: 封装(encapsulation)

面向对象的其中一个特色就是封装，调用者不需要关心内部结构，只需根据公开接口进行操作。程式只依赖物件的公开接口，而不依赖内部结构。这样内部的结构可以根据架构需求而修改，而不会影响到其他程式。

我们看一个没有封装的例子，分数相加：

```
 # 设计处理分数的相加，假设分子是 x、分母是 y


def add_rational_numerator(x1, y1, x2, y2)
    x1*y2 + x2*y1
end

def add_rational_denominator(x1, y1, x2, y2)
    y1*y2
end

# 2/3 + 3/4

x1 = 2
y1 = 3
x2 = 3
y2 = 4

answer_x = add_rational_numerator(x1, y1, x2, y2)
answer_y = add_rational_denominator(x1, y1, x2, y2)

puts "#{answer_x}/#{answer_y}"
```

分子、分母、算分母的方法、算分子的方法，都是分开的。

如果改用面向对象来写，首先定义分数的类：

```
class MyRational

  attr_accessor :x, :y

  def initialize(x, y)
    @x, @y = x, y
  end

  def add(target)
    MyRational.new(@x*target.y + @y*target.x, @y*target.y)
  end

end

# 2/3 + 3/4

a = MyRational.new(2,3)
b = MyRational.new(3,4)
c = a.add(b)

puts "#{c.x}/#{c.y}"
```

于是有关分数的数据和方法，都被一起封装到对象里面去了。这样就很清楚它们是相关的。
