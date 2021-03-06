# 内存管理

了解内存是编程非常重要的概念，因为如果你把内存用光了，操作系统就得去把硬盘模拟成内存使用，但是由于硬盘的速度跟内存差太多了，整台电脑的效能会急剧下滑，就会呈现当机的状态。一台电脑的内存是有限的，你的 MacBook 可能只有 4G 或 8G，租一台服务器，最重要的也是先看有多少内存空间可以使用。内存越多，可以同时执行的程序就越多。

打开 Mac 的 Activity Monitor，点击 Memory 可以观察各个程序使用内存的情况

![](https://ws1.sinaimg.cn/large/006tKfTcgy1fhgx8nk95hj30yl0on7bm.jpg)

前几节提到在 C 语言中，使用变量需要预先跟内存索要空间，这跟 Ruby 很不一样。在 Ruby 是全自动的内存管理，你学到现在好像都不需要关心到底内存是如何被使用的吧。但是在 C 语言中需要手动管理内存。在硬件资源有限或需要效能至上的软件中，手动管理内存有其必要，但是缺点就是降低了开发效率，开发者必须注意好内存管理，用的时候要先宣告，不用的时候要释放回操作系统。如果一只程序一直消耗内存，却从来不归还给操作系统，那这只程序就是不断不断地耗用，直到全部内存都被吃光，最后电脑就当机了(或是手机上的操作系统会强制关闭你的应用)。

以编程语言的发展历史来看，第二重要的可能就是 Java 语言了。Java 有许多重大的发明，其中面向对象我们下一个教程会教的重点，另外就是内部 Virtual Machine(VM)跨平台设计，以及和[垃圾回收 Garbage Collection (GC)](https://zh.wikipedia.org/zh-cn/垃圾回收_(計算機科學)) 了，我们先谈谈 GC。

GC 是编程语言的一种内部功能，作用是自动把再没有用到的变量，把内存释出回操作系统。例如你可以想像，编程语言在执行的时候，会定时停一下检查所有变量，看看哪些变量已经没有被使用，就释放回操作系统。

垃圾回收可以让程式员减轻许多负担，也减少程式员犯错的机会。在 Java, Ruby, JavaScript 都有 GC，一般来说不需要特别烦恼内存的使用。还是有可能碰到内存泄露(memory leak)问题，例如不断使用内存但没有释放的机会。

例如以下 Ruby 程式，是一个无穷循环不断增加资料到数组里面:

```
arr = []
i=0

while(true)
  arr[i] = "hahaha!"
  i = i+1
end
```

请继续观察 Mac 的 Activity Monitor，等等你会发现有一只 Ruby 程序，它的内存用量不断上升......... (请记得中断这个程序)。

编程语言内部的 GC 算法非常重要，非常影响编程语言的执行效能。GC 多久执行一次，每次执行 GC 要花多少时间(一跑 GC，你的程序就等于是暂停下来)，这些都会严重影响程序的进行。例如在一些 real-time 强即时性的软件中，就说是一个机器人走路的软件好了，是不能用 GC 的，需要手动管理内存。想像一下走路腿抬到一半，然后编程语言不定就自动跑了 GC ，暂停一下，那就跌倒了。

近十年 Ruby 1.9 到 Ruby 2.4 的版本，都着重在改进内部的 GC 算法，以来增加效能。
