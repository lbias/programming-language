# 异常处理策略

现在你了解异常处理的语法了，我们回过头来思考一下，当程序发生错误时会如何处理? 程序发生错误其实是蛮常见的事情，但是并不是每种错误都会用异常处理功能来解决。

## 回传错误码 vs. 抛出异常

事实上有两种处理方式：第一种是回传某个代表错误的值(例如 nil, true/false 或错误数字码)，第二种才是抛出异常 Exception。

例如从 Hash 或 Array 中取出一个值，如果该 key 不存在，其实也可以算是一种异常：

```
h = { :foo => "123" }
h[:bar] # 回传 nil
h.fetch(:bar) # 丢出 KeyError 异常
```

其中 `:bar` 是一个不存在的 key，而 `h[:bar]` 默认回传 `nil` 代表了此值不存在。但 Ruby 也提供另一个 Hash#fetch 用法，如果该 key 不存在，会丢出错误异常 KeyError。同一件事情，Ruby 提供了两种 API 方法，但有不同的异常处理策略。

在看 Ruby API 或是 gem 的 API 文档时，你可以注意一下它们是如何处理异常情况的。例如在 [rest-client](https://github.com/rest-client/rest-client) gem 中(在 Web API 教程中，我们用这个 gem 来抓取第三方的信息)，如果抓取失败，就会抛出 [RestClient::ExceptionWithResponse 异常](https://github.com/rest-client/rest-client#exceptions-see-httpwwww3orgprotocolsrfc2616rfc2616-sec10html)。

## 何时用抛出异常？

什么时候会用错误码？什么时候会用抛出异常的方式呢？这得取决于这个错误是否在预期之内。抛出异常的意思像是: 这个异常这里不知道怎么处理，只好把烫手山芋抛出去，看哪一层有办法处理。如果最后都没有人可以处理，那程序只好 crash 了!

以 Rails 来说，存储的 API 有两种，一个是 `save` 一个是 `save!`。差别是当 validation 验证失败时的处理方式不同。以下是大家比较熟习的版本：

```
def create
  @user = User.new params[:user]
  if @user.save
    redirect_to user_path(@user)
  else
    flash[:notice] = 'Unable to create user'
    render :action => :new
  end
end
```

这是改用异常处理的版本：

```
def create
  @user = User.new params[:user]
  @user.save!
  redirect_to user_path(@user)
rescue ActiveRecord::RecordNotSaved
  flash[:notice] = 'Unable to create user'
  render :action => :new
end
```

在这两个版本中，前者还是比较好的。这是因为用户输入不正确其实是很常见的事情，处理用户输入失败，重新显示表单也算是正常流程处理的一部分，所以用 `if ... else ...` 的写法还比较清楚。改用异常处理的版本，代码并不会比较清楚。

异常处理的初衷，是希望处理异常的代码不要干扰正常代码的可读性，会发生异常的可能性很低，因此将处理异常的代码放到最后面去。

甚至，不处理异常也是很常见的事情，例如在百宝箱「自订列表顺序」中，其中有个 `reorder` action 是这样的：

```
def reorder
  @event = Event.find_by_friendly_id!(params[:id])
  @event.row_order_position = params[:position]
  @event.save!
end
```

在这个动作中，用了 `save!` 而不是 `save`。这是因为我们没有预期会发生 validation 失败，我们也没有打算要写 validation 失败了要怎么处理。在这种情况下，我们就会改用 `save!` 而不是 `save`。这样如果真的万一出事了，程序就会中断，用户会看到 500 错误画面，这个错误会被记录在 log 之中让我们程序员去检查这个错误。如果你用 `save` 的话，如果失败只会回传 `false` 然后就继续执行下去，用户只会觉得很奇怪为什么没有存成功，但是却没有任何错误记录。
