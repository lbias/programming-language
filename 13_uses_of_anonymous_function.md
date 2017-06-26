# 匿名函式的应用

匿名函式是一种非常重要的概念，这种可以将函数当作参数的技巧，有非常多的 API 设计使用到，特别如果你是库(Library)或框架(Framework)的作者的话，更会用到这个技巧。以下来看看几种不同的应用：

> 这种技巧又叫做 Higher-order function [高阶函数](https://zh.wikipedia.org/wiki/高阶函数)

## pre-and post-processing

pre-and post-processing 的意思是前置和后置处理，将共享的前置和后置处理代码抽取出来。以 Ruby 的开档写入为例，无论要写入什么，首先一定要打开档案，完成后一定要调用 `close` 方法：

```
f = File.open("myfile.txt", 'w')
f.write("Lorem ipsum dolor sit amet")
f.write("Lorem ipsum dolor sit amet")
f.close
```

我们可以改用传入 block 参数的写法，这样就会自动关档了：

```
File.open("myfile.txt", 'w') do |f|
  f.write("Lorem ipsum dolor sit amet")
  f.write("Lorem ipsum dolor sit amet")
end
```

作为练习，假如我们模仿写一个类似的方法，会长这样：
```
def my_open_file(filename, file_mode)
  f = File.open(filename, file_mode) # 前置处理

  yield(f)
  f.close  # 后置处理

end

my_open_file("myfile.txt", 'w') do |f|
  f.write("Lorem ipsum dolor sit amet")
  f.write("Lorem ipsum dolor sit amet")
end
```

这个 `my_open_file` 方法就是一个通用的开档方法，这样的好处是不用写 `f.close` 了，而且也不可能忘记。如果忘了写 `end` 的话会语法错误。另一个好处是透过代码缩进也更好阅读了解这些代码在一起的。

## callback function

[回调函数](https://zh.wikipedia.org/wiki/回调函数)的意思是先挖好坑，让调用这个 API 的人可以填要执行什么。

例如 Rails ActiveRecord 可以注册 [callback 方法](https://ihower.tw/rails/activerecord-lifecycle-cn.html#sec1)，在 `save` 前后做一些事情。我们在百宝箱 2-3 自订 Model 网址曾经用过 `before_validation :generate_friendly_id, :on => :create`，这会在 `save` 前去调用 `generate_friendly_id` 方法。

这个原理就是回调函式，假如我们自己来写一个练习看看，这个 `save` 会长这样：

```
class MyRecord

  def self.register_before_callback(&block)
    @@before_callback = block
  end

  def self.register_after_callback(&block)
    @@after_callback = block
  end

  def save
    @@before_callback.call  # 挖了一个坑

    puts "save into DB"
    @@after_callback.call   # 挖了一个坑

  end

end
```

上面的代码属于库(Library)的通用代码，你可以想像在 Rails 内部大概是这样的。

接下来是我们实际使用的情况：

```
MyRecord.register_before_callback do # 填坑

  puts "this is before callback"
end

MyRecord.register_after_callback do # 填坑

  puts "this is before callback"
end

record = MyRecord.new
record.save

# 输出

# this is before callback

# save into DB

# this is after callback
```

调用函式在 JavaScript 用的就更多了，例如绑事件：

```
$("div").click(function(){
  console.log("click!")
})
```

这个 `click` 的参数就是一个回调函式用法，当我们真正 click 时，才会调用这个匿名函式。

## currying function

既然函数也是一种数据，我们可以造一种函式让他也回传一个匿名函式：

```
def add(x)
   ->(y){ x + y }
end

add2 = add(2)
add3 = add(3)


puts add2.call(4) # 输出 6

puts add3.call(6) # 输出 9
```

这虽然不太实用，但是是个有趣的例子。

## Closure 闭包特性

匿名函式有一个很重要的特性，那就是 Closure (闭包)。这个意思是它会将外面的作用域(scope)一起包进来，函式内可以读取到函数外的变量，但是在匿名函式中新建立的变量，离开匿名函式后会清掉。

```
arr = [1,2,3]
outer = 1

arr.each do
  puts outer  # 可以读取到外面的 outer 变量

  inner = 4   # 新建立一个 inner 变量

end

inner  # 错误 NameError，找不到 inner 这个变量
```
