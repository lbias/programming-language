# 编译型语言和解释型语言

高级语言又可以分为两种：编译型语言(静态语言/Static)和解释型语言(动态语言/Dynamic)，这两种语言的优缺点，一直以来都是开发者社群最爱论战的话题。

编译型语言包括：C 语言、C++、Java 语言等等，这种语言要求一定要先把全部代码编译变成机器码(native code)，也就是可执行的档案。软件散布和分享的时候，是拿最后的执行档。在这类语言中，使用变量必须事先宣告类型，例如这个变量一开始宣告是 int，那就一定只能是 int，不能换成存字符串。

解释型语言包括: Ruby、Python、JavaScript、PHP 等等，这种语言不需要先编译，而是透过一种叫做解释器(interpreter)的软件，逐行编译然后直接执行。软件散布和分享的时候，是拿源代码。在这类语言中，使用变量不需要事先宣告类型，一开始存整数，后来换成存字符串也可以。

编译型语言每次修改代码，都必须重新把程序编译好，如果程序任一行有错，就无法编译。但是因为都先编译好了，所以执行的速度比较快，而且执行档很小。刚刚的 C 语言范例，编译后只有8K的大小。

解释型语言不需要先编译，修改起来比较方便，写代码也快一些，但是任何错误都要等到真正执行之后，才会知道。因为要等到执行时才编译，所以执行的速度较慢。一个只有一行的 Ruby 代码程序，如果要再另一台电脑跑起来，那台电脑也必须把 Ruby 解释器安装起来，而且跑起来至少需要 5MB 的内存，即使只是输出一行 Hello World。

所以为何有不同的程式语言呢？一方面是大家对于效率的需求不一样，有的希望是执行快，有的希望开发快。一般来说 「机器语言 -> 组合语言 -> C 语言 -> Java 语言 -> 动态语言」越往右边跑起来效能较差，但开发起来效率比较好。

另一方面也是编程语言的设计哲学不同，有些喜欢功能多、程式码比较有表现力，例如 Scala，但语言本身会比较复杂。有些喜欢功能少，比较精实但打比较多字，例如 Go 语言。

## 为什么 Web 应用，使用解释型语言有更好的的优势?

着名的”人月神话”一书作者Fred Brooks曾说：「一个程式设计师一天能产生的程式码行数是差不多的，无论什么程式语言」。因此一个具有表达能力的高阶程式语言，就会比低阶的程式语言能完成更多功能。相较于静态程式语言，使用更高阶的动态脚本语言可以帮助我们：

1. 用更少程式码做更多事情，大大增加生产力
1. 更快因应客户开发需求，敏捷开发

不过，动态语言也不是没有缺点：

1. 执行效能是绝对比不上静态语言的
1. 没有编译期可以检查型别错误

但是，我们知道现在的电脑越来越快、越来越便宜、上网越来越容易、内存越来越多、硬盘越来越大。另外，行动装置也越来越多，需要搭配的网络服务需求也增加了。这些趋势告诉我们有更多的软件的需求，另一方面由于硬件效能的增强，人力开发成本比起软件的执行期的效能，也越来越重要。同样一个程式，用动态语言执行的效能已经可以达到实用(例如每秒可以处理50~500个的HTTP请求，也可以透过增加服务器来扩展架构)，也许用静态语言后的执行速度可以再快一倍，但是却需要十倍以上的时间来开发，这件事情是不是值得呢？

在硬件资源有限的行动装置及嵌入式系统上，仍是静态语言的天下，这一点需要更多时间才有动态语言的生存空间。

没有编译期可以检查型别错误的问题，也随着单元测试和TDD(Test-driven development)测试驱动开发等敏捷最佳实务而逐渐降低重要性。而大部分的Bug会出自于商业逻辑错误，而不是型别错误上。
