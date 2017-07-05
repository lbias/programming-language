# 元编程: Monkey Patch

猴子补丁的意思是直接复写 Class 的定义去修改行为，在 Rails 中常用这招来扩增原本 Ruby 的行为，例如：

## try 方法

```
person = Person.find_by_email(params[:email]) # 如果找不到，会回传 nil

@person.try(:name) # 如果 @person 是 nil，透过 try 会输出 nil

@person.name # 如果 @person 是 nil，这样会抛出异常 NoMethodError
```

try 这个方法的原理是什么呢？看一下 Rails 的原代码就知道了：

```
class NilClass
  def try(*args)
    nil
  end
end
```

## blank? 方法

`blank?` 方法是 Rails 提供的一个方法，检查对象是否是 `nil` 或空字符串：

```
[1,2,3].blank? # false
"blah".blank? # false
"".blank? # true

class Demo
  def return_nil
  end
end

Demo.new.blank? # false
Demo.new.return_nil.blank? # true
```

blank? 这个方法的原理是什么呢？看一下 Rails 的原代码就知道了：

```
class Object    # 在 Ruby 中所有的类都会继承自 Object 这个类

  def blank?
    respond_to?(:empty?) ? empty? : !self
  end

  def present?
    !blank?
  end

end

class NilClass
  def blank?
    true
  end
end

class FalseClass
  def blank?
    true
  end
end

class TrueClass
  def blank?
    false
  end
end
```
