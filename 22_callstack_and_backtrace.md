# 什么是 callstack 和 backtrace

在一个复杂的软件中，方法内会调用其他方法，然后方法又在调用其他方法，例如以下范例，c 方法调用 b 方法、b 方法内又调用 a 方法：

```
def a
  puts "a"
  raise "A error"
end

def b
  puts "b"
  a()
end

def c
  puts "c"
  b()
end

begin
  c()
rescue => e
  puts e.message
  puts e.backtrace
ensuce
  puts "finally"
end
```

这种一层一层的关系，又叫做 `callstack`。

其中 a 方法中丢出了异常，但是在 a 方法内并没有 rescue，这个异常会一层一层往外抛出，直到某一层有 rescue 有本事捕捉这个例外。如果一直到到最外层都没人能处理异常，那么程序才会中断。

在上述的 `rescue` 中，我们用 `e.backtrace` 可以列出调用的 `callstack` 关系，`backtrace` 的意思就是回朔当初的调用关系。

在 Rails 中如果发生异常，根据 development 模式或 production 模式，最外层有不同的异常处理策略：在开发时最外层的 `rescue` 会显示错误的 backtrace，这样可以帮助我们了解发生错误的来龙去脉：

![](https://s3-ap-northeast-1.amazonaws.com/ontrackapp-production/9Bgdkhk0QKa8tLVSNlhA_2.png)

这里区分了 Application Trace、Framework Trace 和 Full Trace。默认显示 Application Trace 也就是我们写的代码 backtrace，而 Framework Trace 则是发生在 Rails 框架内的 backtrace。

如果是 production 模式，默认的最外层 `rescue` 其实是显示 500 错误画面(也就是 public/500.html)。

![](https://s3-ap-northeast-1.amazonaws.com/ontrackapp-production/4J89ANRWQ3qa5Qp24vqT_3.png)
