# 关于 Rails 的异常处理

在一节我们来回顾一下 Rails 是如何做异常处理的?

## find

ActiveRecord 的 `find` API 如果找不到数据，会丢 `ActiveRecord::RecordNotFound` 的例外。在 show action 我们经常用这个 API，因为如果真的找不到，程序就会中断。

```
def show
  @event = Event.find(params[:id])
end
```

>在 development 开发模式中，你会看到 ActiveRecord::RecordNotFound 异常。在 production 上线模式，会显示 404 页面(public/404.html)

Rails 还有提供 `find_by_字段名称` 和 `find_by_字段名称!` API，前者如果找不到数据，会回传 `nil`，后者则是抛出异常。

在百宝箱第一集的「自定义 Model 网址」中，我们新增了一个 `friendly_id` 字段，然后 show action 改成：

```
def show
  @event = Event.find_by_friendly_id!(params[:id])
end
```

当找不到数据时，就会抛出 `ActiveRecord::RecordNotFound` 异常程序中断。

为什么这里偏好用抛出异常的策略呢？如果我们改用 `Event.find_by_friendly_id(params[:id])` 的话，找不到数据时会回传 `nil`，那么 @event 变成 `nil` 程序继续执行，一直进到 `show.html.erb` 里面的某一行去显示 @event.name，然后就会碰到 `nil.name` 抛出异常是 `NoMethodError` 最后程序中断。这时候要找到真正异常的原因就会多花一点时间。

我们希望在异常发生的第一时间就中断程序(fail fast)，而不是让程序无声地继续执行下去，最后像一个未爆弹一样最后莫名其妙地炸掉。

## save 和 save!

这刚刚有提过，存储有分 save 和 save!，更新有分 udpate 和 udpate!。

- 有搭配 `if ... else` 情况做处理用 save。这表示你会处理储存失败流程
- 没有的话，用 save! 惊叹号版本。这表示你认为 99% 应该会存成功，懒得处理存不成功。如果出错会异常抛出

## rescue_from

[rescue_from](http://api.rubyonrails.org/classes/ActiveSupport/Rescuable/ClassMethods.html)
可以在 controller 中宣告 `rescue_from` 去救回特定的异常对象。例如：

```
class ApplicationController < ActionController::Base
  rescue_from User::NotAuthorized, with: :deny_access # self defined exception
  rescue_from ActiveRecord::RecordInvalid, with: :show_errors

  rescue_from 'MyAppError::Base' do |exception|
    render xml: exception, status: 500
  end

  private
    def deny_access
      ...
    end

    def show_errors(exception)
      exception.record.new_record? ? ...
    end
end
```

这功能不太常用。你会发生其实我们不太常去处理 `rescue` 的情况，这是因为我们对于消费级软件的鲁棒性//抗变换性(Robust)的要求没这么高。如果异常发生了，只要能提示用户并记录下来就不错了。在一些高鲁棒性/抗变换性(Robust)要求的软件中，例如医疗、金融企业等级软件，则会进行重试(retry)或是切换备用系统。

## 安装异常通知

在 production 上线环境中，如果用户操作碰到异常会看到 500 错误画面，并且在 log 中会纪录下来。我们希望能有一些机制能够主动通知我们程序员，好让我可以 trace error、fixed bug 甚至在发生错误没多久就可以通知苦主发生了什么事情。

最基本我们可以安装 [Exception Notifier](https://github.com/smartinez87/exception_notification)，这个套件会在发生例外时寄 email 通知。或是使用第三方服务，例如：

- [Rollbar](https://rollbar.com/) 推荐这套，有免费额度一个月记录 5000 个异常
- [Airbrake](https://airbrake.io/)

这些第三方服务可以在网站发生异常错误的时候自动将错误讯息收集起来，并且提供了还蛮不错的后台可以浏览，还可以统计及追踪异常处理的情况。免费的方案对于小网站就很够用，非常推荐使用。
