# 面向对象设计

到目前为止我们只是基本了解定义类的语法，以及认识了面向对象的三个特性。但是面对一个复杂的软件好像还是不知道怎么去设计要有哪些类？

别担心，作为应用程式开发的初学者，我们绝大部分需要的类设计，都由应用程式框架，也就是 Rails 提供了，只要照着框架的规范写程式即可，一开始不太需要自己设计全新的类别。随着大型专案的需求，才会逐步需要这方面的知识进行更好的程式架构调整。

> 这种不依赖框架，自己定义的对象，又叫作 Plain-Old Ruby Object

面向对象设计又是一门学问，有兴趣的学员，可以朝以下参考资源搜寻：

- [面向对象设计实践指南: Ruby 语言描述](https://book.douban.com/subject/25795276/) 人民邮电
- [SOLID](https://en.wikipedia.org/wiki/SOLID_%28object-oriented_design%29) OO 设计原则
- [Design Patterns 设计模式](https://zh.wikipedia.org/wiki/设计模式)，最有名的即 GoF patterns。设计模式针对了特定的情境，提供设计解法(通常是如何设计你的类别)，并且「命名」这些模式让程序员可以方便沟通和当作命名的元素。另一方面也是提供一种可以临摹的设计范例。常见的设计模式包括 Factory, Adapter, Composite, Decorator, Iterator, Observer 等等
例如以下是一个 Strategy Pattern 范例：情境是我们想要设计一个通用的数据输出功能，并且允许我们随时可以抽换不同的输出方式，例如 XML 或 JSON。

```
class AwesomeFormatter

  attr_accessor :data, :formatter

  def initialize(data, formatter)
    self.data = data
    self.formatter = formatter
  end

  def output
    puts self.formatter.output( self.data )
  end

end

class XMLFormatter
  def output(data)
    "<data>#{data}</data>"
  end
end

class JSONFormatter
  def output(data)
    "{ data: #{data} }"
  end
end


formatter = AwesomeFormatter.new( "ihower", XMLFormatter.new )

formatter.output() # 输出 XML 格式


formatter.formatter = JSONFormatter.new   # 动态更换不同的输出策略


formatter.output() # 输出 JSON 格式
```

在建构 `AwesomeFormatter.new` 时，第二个参数就是不同的策略，其中 `formatter` 属性就代表不同的输出方式。

透过这个 Strategy Pattern，我们可以随时抽换不同的输出方式。
