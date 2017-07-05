# 元编程: define_method

元编程(Meta-programming)的意思是让程序帮我们写程序，这听起来非常神奇，但这就是 Rails 很多 API 用法之所以这么 magic 背后的秘密武器。撰写元编程的能力是库和框架作者的必备技巧。

这一章我们将简单介绍一些元编程的技巧，目的是让各位学员能够稍微理解 Rails 背后的原理。如果想要进一步了解如何使用元编程技巧，推荐 [Ruby元编程](https://book.douban.com/subject/26575429/) 一书。

## 动态定义方法 define_method

`define_method` 是个 Ruby 的类方法，可以动态定义对象方法，例如：

```
class Dragon
  define_method(:foo) { puts "bar" }

  ['a','b','c','d','e','f'].each do |x|
    define_method(x) { puts x }
  end
end

dragon = Dragon.new
dragon.foo # 输出 "bar"
dragon.a # 输出 "a"
dragon.f # 输出 "f"
```

其中 `define_method(:foo) { puts "bar" }` 等同于

```
def foo
  puts "bar"
end
```

也许你会好奇 `define_method` 跟 `def` 同样都可以定义方法，差别在哪里呢? 差别在 define_method 用匿名函式来定义方法，所以有 Closure(闭包) 特性。我们需要这个特性，才可以有弹性地根据参数去自定义出不同的方法实作。例如：

```
class A
  def self.define_my_method(x)
    define_method("output_#{x}") do
      puts "This is #{x}"
    end
  end
end

class B < A
  define_my_method :foo # 定义 output_foo 方法
end

class C < A
  define_my_method :bar # 定义 output_bar 方法
end

B.new.output_foo # 输出 This is foo
C.new.output_bar # 输出 This is bar
```

如果你能理解上述的代码，那么你就能理解在 Rails 很多这样的宣告背后，都是用 `define_method` 做出来的：

```
class Firm < ActiveRecord::Base
  has_many   :clients
  has_one    :account
  belongs_to :user
end

# has_many 是 AciveRecord 的类方法(class method)

# 其内容是动态定义出 Firm 的一堆对象方法(instance methods)

firm = Firm.find(1)
firm.clients
firm.account
firm.build_account
firm.user
```

其中 `clients`、`account`、`build_account`、`user` 等方法，都是透过 `has_many :clients`、`has_one :account`、`belongs_to :user` 所定义出来的。
