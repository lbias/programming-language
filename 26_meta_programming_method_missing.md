# 元编程: method_missing

Ruby 在调用方法找不到时，会改掉用这个 `method_missing` 这个方法。例如以下的代码中，任何 `go_to_XXXX` 的方法调用，都可以输出 `go to XXXX` 字符串。

```
car = Car.new

car.go_to_taipei
# go to taipei

car.go_to_shanghai
# go to shanghai

car.go_to_japan
# go to japan
```

但是我们不可能定义出所有的 `go_to_XXX` 方法啊，这背后的秘诀就是 `method_missing` 方法：

```
class Car
  def go(place)
    puts "go to #{place}"
  end

  def method_missing(name, *args)
    if name.to_s =~ /^go_to_(.*)/
      go($1)
    else
      super
    end
  end
end

car = Car.new

car.go_to_taipei
# go to taipei

car.blah # NoMethodError: undefined method `blah`
```

>super 当你在类别中复写一个方法时，透过 super 可以调用到上一层被你复写的方法。

当调用 `go_to_XXXX` 时，因为我们并没有特别定义这个方法，所以就会改成调用 `method_missing` 方法，在这个方法中我们再检查是不是 `go_to_` 开头，如果是的话就调用 `go` 方法，不然就改调用 `super` 回到原本的行为，也就是抛出 NoMethodError 异常。

如果你能理解这个方法，那个在 Rails 中就有几个功能是这样做出来的：

例如 ActiveRecord 的 `find_by_XXX("YYY")` 功能，会变成 `where( :XXX => "YYY" ).first`

又例如在 Web API 教程中，用到 Jbuilder 样板来输出 JSON：

`app/views/api/v1/trains/show.json.jbuilder`
```
json.number @train.number
json.available_seats @train.available_seats
json.created_at @train.created_at
```

其中的 `number`、`available_seats` 和 `created_at` 方法其实都进到 `method_missing` 了。
