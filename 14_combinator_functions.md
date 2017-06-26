# Combinator functions

另一个常见的应用是 Combinator functions，指的是处理容器的三个组合招数，让我们一一道来：

## 映射 map

映射的意思就是将容器里面的元素，一对一变换成另一个新的容器。

例如我们想将以下的 `arr` 元素每一个都加一，首先示范一下传统的写法：

```
arr = [1,2,3,4,5,6,7,8,9,10]

result = []
arr.each do |i|
  result.push(i+1)
end

puts result
# 输出 [2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

改用 `map` 方法，这个方法接受一个匿名函式来做转换：

```
arr = [1,2,3,4,5,6,7,8,9,10]

result = arr.map { |i| i+1 }

puts result
# 输出 [2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
```

## 过滤 filter

过滤就是将容器里的元素，根据某些条件过滤建立另一个容器。

例如过滤出所有偶数，首先示范一下传统的写法：

```
arr = [1,2,3,4,5,6,7,8,9,10]
result = []
arr.each do |i|
  if (i % 2 == 0)
    result.push(i);
  end
end

puts result
# 输出 [2, 4, 6, 8, 10]
```

改用 `select` 方法，这个方法接受一个匿名函式来决定条件：

```
arr = [1,2,3,4,5,6,7,8,9,10]

result = arr.select{ |i| i % 2 == 0 }  # 这个匿名函式要回传 true 或 false


puts result
```

## 归纳 reduce

归纳就是将一个容器里的元素，归纳成一个值：

例如加总好了，首先示范一下传统的写法：

```
arr = [1,2,3,4,5,6,7,8,9,10]

result = 0;
arr.each do |i|
  result = result + i
end

puts result
# 输出 55
```

改用 `reduce` 方法：

```
arr = [1,2,3,4,5,6,7,8,9,10]
result = arr.reduce(0) { |sum, i| sum + i }

puts result
```

`result` 比较难理解一点：`reduce` 的第一个参数 `0` 是初始值，然后这个匿名函式依序走访容器，两个参数 `sum` 跟 `i`，前者 `sum` 是前一次循环的回传结果。`i` 是这次走访的元素。

找最大值其实也可以用 `reduce` 方法：

```
arr = [9, 2, 10, 6, 2, 4, 5, 6, 0, 4]

max = arr.reduce do |max, i|
  if max > i
    max
  else
    i
  end
end

puts max
# 输出 10
```

每次循环，匿名函式回传的值就是下一次的 `max` 参数。

如果 `reduce` 没给第一个参数，那容器的第一个元素会是初始值。

## 综合应用

假如我们想从以下的数据找出小于 1000 的最大的金额，要怎么找呢？

先示范传统写法：

```
tickets = [
  { name: "Ticket A", amount: 100 },
  { name: "Ticket B", amount: 1123 },
  { name: "Ticket C", amount: 670 },
  { name: "Ticket D", amount: 50 },
  { name: "Ticket E", amount: 990 },
]

result = tickets[0][:amount]   # 一定要先在 code block 外面初始这个变量，如果放在里面，出来就被清掉了

tickets.each do |ticket|
  if (ticket[:amount] < 1000 && ticket[:amount] > result )
    result = ticket[:amount];
  end
end

puts result
# 输出 990
```

改成刚刚学到的写法：

```
 tickets = [
  { name: "Ticket A", amount: 100 },
  { name: "Ticket B", amount: 1123 },
  { name: "Ticket C", amount: 670 },
  { name: "Ticket D", amount: 50 },
  { name: "Ticket E", amount: 990 },
]

result = tickets.map{ |t| t[:amount] }.select{ |a| a < 1000 }.reduce{ |x,y|
(x > y)? x : y }

puts result
# 输出 990
```

一行就可以写完，有没有觉得很厉害呢。
