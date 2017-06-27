# 特性三: 多态(polymorphism)

多态的意思是可以把很多不一样的东西，当作同一种东西来处理。例如箱子有很多种，打开的实作方式各有不同(有的有锁、有的没锁)，但是这些箱子都有提供「打开」这个接口可以操作。下命令的人只需要知道呼叫这个指令即可。

先来示范一个不是面向对象的设计：

```
box1 = { :name => "Box1", :type => "locked" }
box2 = { :name => "Box2", :type => "unlocked"}
box3 = { :name => "Box3", :type => "seal" }

def open_box(box)
  if box[:type] == "locked"
    puts "Open locked"
  elsif box[:type] == "unlocked"
    puts "Open unlocked"
  elsif box[:type] == "seal"
    puts "Open Seal"
  end
end

arr = [box1, box2, box3]
arr.each do |x|
  open_box(x)
end
```

上述的代码中，有一个很厉害的 `open_box` 方法，在里面会判断不同的箱子调用不同的输出。这样设计的缺点是不好扩充，维护性低。因为无论是新增不同的箱子，或是修改某一个箱子打开的行为，都得修改同一个方法，复杂度全部集中在 `open_box` 之中。

让我们改用面向对象来写：

```
class Box
  attr_accessor :name

  def initialize(name)
    @name = name
  end

  def open
    puts "Open default box"
  end
end

class LockedBox < Box
  def open
    puts "Open locked"
  end
end

class UnlockedBox < Box
  def open
    puts "Open Unlocked"
  end
end

class SealBox < Box
  def open
    puts "Open seal"
  end
end

box1 = LockedBox.new("Box1")
box2 = UnlockedBox.new("Box2")
box3 = SealBox.new("Box3")

arr = [box1, box2, box3]
arr.each do |x|
  x.open()
end
```

代码看起来好像变多了，但是扩充性和维护性比较好。因为如果要新增不同的箱子，只需要新增类即可，不需要改到本来的代码。没有多型的话，单一函数就会充满根据数据类型的判断的 if-else，变得难以扩充。这种多型的特型让我们不需要担心确切的数据类型，只要接口一致(都有 open 方法)就可以操作

## 鸭子型别 Duck Typing

在动态语言中，不同对象只要方法的接口一样，就可以有多态的特型，这又叫做 [Duck Typing](https://zh.wikipedia.org/wiki/鸭子类型)：当看到一只鸟走起来像鸭子、游泳起来像鸭子、叫起来也像鸭子，那么这只鸟就可以被称为鸭子。

> 名词释疑：方法的「接口(Interface)」指的是方法的名称和参数，方法的「实作(Implement)」指的是方法内实际要做的代码。上述的 LockedBox、UnlockedBox 和 SealBox 都有一样的 open 接口，但是子类别中定义了不同的实作。

刚刚的 Box 例子是透过继承来达成多态，因为所有继承自 Box 的子类，都一定有 open 方法，自然是多态的。
