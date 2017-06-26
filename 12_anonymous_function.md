# 匿名函式

我们学过在编程语言中数据有不同类型，例如字符串、数字等。这一节要教大家一个非常重要的概念，那就是函式也是一种数据类型。函式也是一种数据类型。函式也是一种数据类型。很重要所以说三遍。

> 又叫做 [First-class function 头等函数](https://zh.wikipedia.org/wiki/头等函数)

以下的 Ruby 程式码中，变量 `x` 就是一个函式变量。它的值 `->{ puts "Hello World"}` 叫做匿名函式(或叫做 anonymous function, code block 或 lambda )。匿名函式要透过 `.call` 才会实际调用。

```
x = -> { puts "Hello World" }
x.call # 输出 Hello World
```

> -> 等同于 lambda 等同于 Proc.new，这三种写法都可以。有小小的差异但目前先不管没关系。

这里 Ruby 的语法看起来有点奇怪，如果你有学过一点 JavaScript，以下是等同的 JavaScript 代码：

```
x = function(){ console.log("Hello World") }
x() # 输出 Hello World
```

其中 `x` 是一个函式变量，透过 `x()` 才会调用。在 JavaScript 中调用一个函式一定要加上括号，所以用 `x()` 表示触发调用。但是在 Ruby 里面，调用函式是可以省略括号的，因为没办法区分 `x` 跟 `x()` 的情况下，Ruby 需要用 `.call` 方法才会调用匿名函式。

## 把函式变量当作参数

既然函式也是一种数据类型，我们就可以将这个函式变量当作参数来传递，例如

```
foo = -> { puts "foo" }

def bar(x)
  puts "bar"
  x.call
end

bar(foo)

# 输出

# bar

# foo
```

我们将 `foo` 变量当作参数，传到 `bar` 里面去，然后再调用它。注意到第一行宣告 `foo` 函式的时候，我们并没有真的调用它，直到 `x.call` 时才调用它。因此是先输出 bar，然后才输出 foo。

这段代码等同于以下的 JavaScript 代码：

```
foo = function() { console.log("foo") }

function bar(x) {
  console.log("bar")
  x()
}

bar(foo)
```

## 直接内嵌的写法

也可以直接用匿名函式放到参数里面：

```
def bar(x)
  puts "bar"
  x.call
end

bar( ->{ puts "zoo" } )

# 输出

# bar

# zoo
```

等同于

```
function bar(x) {
  console.log("bar")
  x()
}

bar(function(){
  console.log("zoo")
})
```

有写过 JQuery 的同学们应该很熟悉这种形式。不过 Ruby 的写法似乎不太常见这样写，这是因为通常会简化成这样：

```
def bar
  puts "bar"
  yield
end

bar do
  puts "foo"
end

# 或是 bar { puts "foo" }
```

看起来很熟悉了吧。如果参数列最后一个参数是匿名函式，那么就会用这种简化的写法：传入的匿名函式用 `{ ...}` 或 `do .... end` 表示，然后在函式里面用 `yield` 这个关键字来实际调用它。

> 通常单行的程式会用 `{ ... }` 的写法，多行则会用 `do ... end` 的写法。这只是 Coding Style 惯例而已，作用是一样的。

如果匿名函式接受参数的话，语法是这样：

```
def bar
  puts "bar"
  yield("zoo")
end

bar do |x|
  puts x
  puts "foo"
end

# 输出

# bar

# zoo

# foo
```

其中 `yield("zoo")` 会将 "zoo" 带入匿名函式，也就是函式 `bar` 的 `x` 参数

另外，还有一种混合的写法长这样：

```
def bar(&block)
  puts "bar"
  block.call
end

bar do
  puts "foo"
end

# 或是 bar { puts "foo" }
```

用 `&block` 可以具体化传入的 `{....}` 或 `do ... end` 参数。不过除非你还会继续在 bar 里面调用另一个方法代入这个匿名函式，否则一般不需要这样写。
