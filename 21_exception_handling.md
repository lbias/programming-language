# 什么是异常处理 Exception

异常处理 (Exception Handling) 是高阶编程语言的一种用于处理异常状况的流程机制，在 Ruby、JavaScript、Python、Java、Swift 等语言中都有这个功能。

哪些算是异常状况呢？是指不在程序运行中预期会发生的事情，例如语法错误、发送 HTTP API 时网络不通、写入档案时硬盘已满、数字除以零等等。

当发生异常时，程序会中断跳开，跳到处理异常的代码。如果没有处理这段异常的代码，整个程序就会终止(也就是软件crash、app 闪退、网站看到500错误画面)

在开发 Rails 时，每次你看到的红色错误画面，就是发生了一个异常错误(Exception)。

![](https://s3-ap-northeast-1.amazonaws.com/ontrackapp-production/f5fhohrHRmeUopMZzlr6_1.png)

## Ruby 语法说明: raise

让我们实际看看异常处理的语法：

```
puts "Start"
raise "Errorrrr"
puts "Never execute"
```

这段代码中，用 `raise` 会丢出一个异常(Exception)，执行的结果是：

```
Start
Called
exception.rb:4:in `<main>': Errorrrr (RuntimeError)
```

注意到第三行的 `puts "Never execute"` 是没有被执行的，执行到 `raise` 时程序就终止了。

>`raise 'An error has occured.'` 等同于 `raise RuntimeError.new('An error has occured.')`，`RuntimeError` 是一个 Ruby 内建的默认异常对象，用来储存关于这个异常的信息。
>Rails 还有内建其他不同的异常对象，详见 [Ruby API](https://ruby-doc.org/core-2.2.0/Exception.html)。

为什么你好像很少用到 `raise` 这个功能呢？这是因为丢出 `raise` 的大多是 Ruby 本身或是我们使用的库和框架之中，例如：

- 当你对一个对象调用一个不存在的方法时，Ruby 会丢出 NoMethodError 异常
- 在 Rails 中当 URL 找不到任何路由规则可以符合时，会丢出 Routing Error 异常

## Ruby 语法说明: rescue

接下来让我们加上处理异常的代码：

```
puts "Start"

begin
  puts "Called"
  raise "Errorrrr"
  puts "Never execute"
rescue => e
  puts 'I am rescued.'
  puts e.message
end

puts "Done"
```

从 `begin` 到 `end` 包住整个可能会丢出异常的代码，然后用 `rescue => e` 可以捕捉到异常。执行的结果是：

```
Start
Called
I am rescued.
Errorrrr
Done
```

`puts "Never execute"` 这一行依然不会执行到，程式从 `raise` 后就跳去 `rescue` 的部分，然后再继续往后正常执行。

在 `rescue` 我们会写如何去处理(拯救)异常，而 `rescue => e` 的 `e` 是个异常对象，会储存关于这个异常的信息。

常见的 `rescue` 可能是显示错误讯息，告诉用户这个操作失败了，然后程序回复正常继续执行。

## Ruby 语法: ensure

`ensure` 部分则是不管有没有发生异常，都一定会执行到。例如：

```
begin
    # do something
    raise 'An error has occured.'
rescue => e
    puts 'I am rescued.'
ensure
    puts 'This code gets executed always.'
end
```

执行结果

```
I am rescued.
This code gets executed always.
```

## Ruby 语法: 顶层异常捕获

上述的 `begin...rescue` 语法，如果放在 def 方法定义中，则可以用以下的简略写法：

```
def 某个方法名称
  # do something

  raise 'An error has occured.'
rescue => e
  puts 'I am rescued.'
ensure
  puts 'This code gets executed always.'
end
```

也就是可以省略掉原本异常处理的 `begin` 跟结尾的 `end`。
