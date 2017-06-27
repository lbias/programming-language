# Ruby 语法说明

让我们完整解说一下 Ruby 中类的语法：

```
 class Person

    def initialize(name)
        @name = name
    end

    def say(word)
        puts "#{word}, #{@name}"
    end

end

p1 = Person.new("ihower")
p2 = Person.new("ihover")

p1.say("Hello") # 输出 Hello, ihower

p2.say("Hello") # 输出 Hello, ihover
```

- 类一定是大写开头，也是一种常数
- `initialize` 是对象的建构方法，当调用 `new` 的时候的会把参数传进这里
- `@` 开头的变量，也就是范例中的 `@name` 叫做对象变量(instance variable)。这个是对象的内部状态。
- `def` 会定义对象的方法

```
class Person

    @@name = “ihower”

    def self.say
        puts @@name
    end

end

Person.say # 输出 ihower
```

- 两个 `@@` 开头的变量，也就是 `@@name` 叫做类变量(class variable)，这个是属于类的
- 用 `self.def` 开头定义的方法，也就是 `def self.say` 叫做类方法。用 `Person.say` 就会调用。

另外，对象变量(@开头)和类变量(@@开头)，都是封装在类内部，类外无法存取。都需透过定义方法才可以存取到。

例如：

```
class Person
    def initialize(name)
        @name = name
    end
end

p = Person.new('ihower')
p.name
# NoMethodError 会出错

p.name='peny'
# NoMethodError 会出错
```

需要定义存取的方法，一个读、一个写：

```
class Person

   def initialize(name)
    @name = name
   end

   def name
     @name
   end

   def name=(name)
     @name = name
   end

end

p = Person.new('ihower')
p.name
=> "ihower"
p.name = "peny"
=> "peny"
```

## attr_* 用法

由于定义存取方法很常见，Ruby 提供了 `attr_accessor`、`attr_reader`、`attr_writer` 等类方法帮我们定义：

```
class Person

  attr_accessor :name

  attr_reader: foo
  attr_writer: bar
end
```

其中 `attr_accessor :name` 等同于刚刚我们自定义的存取方法。`attr_reader` 只定义读、`attr_writer` 只定义写。

public/protected/private 方法

对象方法还有分 public(公开)、protected 和 private 不同，默认是 public。

```
class MyClass

    def public_method
      private_method # 调用 private 方法

      self.protected_method # 调用 protected_method 方法，self. 可加可不加

    end

    private # 以下定义的都是 private 方法


    def private_method
    end

    protected  # 以下定义的都是 protected 方法


    def protected_method
    end

end

m = MyClass.new
m.public_method

m.private_method
# NoMethodError: private method `private_method' called 会出错


m.protected_method
# NoMethodError: protected method `protected_method' called 会出错
```

你不能对对象 `m` 调用 privated_method 或 protected_method。这两个方法只能在内部调用。

> 那 private 和 protected 有什么差别? protected 允许调用同一类的对象。private 则严格限制在内部调用。

为什么面向对象语言会设计这个功能呢？这是因为我们希望管控有哪些 public 方法。这些 public 是公开的 API 会给程序员调用的，如果有任何修改都会影响到软件的其他地方要一起修改。但是 private 但 protected 方法就只会影响这个对象的内部而已。
