## 用路径引用模块树中的项

和在文件系统中进行定位相同, 我们也是使用一个路径, 来告诉 Rust 
该在模块树中的何处找到某个特定项. 比如, 要调用某个函数, 我们需要知道它的路径.

路径可以有两种形式:

* *绝对路径* (*absolute path*) 以 crate root 作为起点, 是一个全路径;
  对于来自外部 crate 的代码, 绝对路径则以这个 crate 的名称开始, 而对于当前 crate
  中的代码, 它就以字面上的 `crate` 这个词开始.
* *相对路径* (*relative path*) 则以当前模块作为起点, 以 `self`, `super`,
  或者当前模块中的标识符开头.
  
不管是绝对路径还是相对路径, 后边都会跟有一个或多个用双冒号 (`::`) 隔开的标识符.

让我们回到清单 7-1, 假设我们想要调用其中的这个 `add_to_waitlist` 函数.
这就等同于在问: `add_to_waitlist` 函数的路径是什么?
我们对清单 7-1 做了一些简化, 将其中一些模块和函数移去后, 放在了清单 7-3 中.

我们接下来会展示两种从一个定义在 crate root 处的 `eat_at_restaurant` 新函数,
调用 `add_to_waitlist` 函数的方式. 这些路径是正确的, 但是该例子还存在一个问题,
使得它还不能按现在这样编译. 我们将对原因稍作解释.

这个 `eat_at_restaurant` 函数是我们库 crate 公开 API 的一部分, 因为我们使用 `pub`
关键词对其进行标注. 在 ["使用 `pub` 关键字暴露路径"][pub]<!-- ignore --> 一节中,
我们将讨论有关 `pub` 的更多细节.

<span class="filename">文件名: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-03/src/lib.rs}}
```

<span class="caption">清单 7-3: 分别使用绝对和相对路径调用 `add_to_waitlist`
函数</span>

当我们在 `eat_at_restaurant` 中第一次调用 `add_to_waitlist` 函数时, 
使用了一个绝对路径. `add_to_waitlist` 函数被定义在和 `eat_at_restaurant` 相同的
crate 中, 意味着我们可以使用 `crate` 关键字来开始这个绝对路径.
然后我们逐个引入各个模块, 直到我们抵达 `add_to_waitlist`.
您可以想象一个具有相同结构的文件系统: 我们需要指明
`/front_of_house/hosting/add_to_waitlist` 路径以便执行 `add_to_waitlist` 程序;
用 `crate` 这个名称从 crate root 起始, 就如同我们在 shell 中使用 `/`
从文件系统的根起始一样.

我们第二次在 `eat_at_restaurant` 中调用 `add_to_waitlist` 函数时,
使用了相对路径. 这个路径以 `front_of_house` 开始, 即这个在模块树中与
`eat_at_restaurant` 同级的模块的名字. 若是在刚刚文件系统的例子中, 这里等价的路径应为
`front_of_house/hosting/add_to_waitlist`. 以模块名作为起始,
意味着这个路径是相对的.

至于是使用相对路径还是绝对路径, 需要基于您项目的情况做决定,
同时也取决于您是更可能会将某项的代码与使用它的代码放在一起, 还是将其与它们分开存放.
举个例子, 如果我们将 `front_of_house` 模块和 `eat_at_restaurant` 函数移至一个叫做
`customer_experience` 的模块中, 我们则需要更新到 `add_to_waitlist` 的绝对路径,
但是相对路径仍然会有效. 然而, 如果我们单独将 `eat_at_restaurant` 函数移至另一个叫做
`dining` 的模块中, 调用 `add_to_waitlist` 函数所使用的绝对路径无需改变,
但相对路径却需要更新. 一般, 我们倾向于指定绝对路径,
因为我们往往希望将代码定义和对它们的调用分别放在属于各自不同的地方.

让我们尝试编译清单 7-3, 并查看它为何还不能编译! 得到的错误展示在清单 7-4 中.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-03/output.txt}}
```

<span class="caption">清单 7-4: 编译器构建清单 7-3 中的代码时产生的错误</span>

错误消息说 `hosting` 模块是私有的. 换句话说, 虽然我们有着到 `hosting` 模块和
`add_to_waitlist` 函数的正确路径, 但 Rust 不允许我们使用它们,
因为它不能访问私有部分. 在 Rust 中, 所有项 (函数, 方法, 结构, 枚举, 模块以及常量)
默认情况下均对上级模块为私有. 如果您想将函数或结构之类的项设为私有, 请将其放入模块中.

上级模块中的项不能使用次级模块中的私有项,
但次级模块中的项可以使用其上级所有模块中的项.
这是因为次级模块将它们的实现细节包装并隐藏了起来, 但却清楚它们被定义处的上下文.
若继续使用我们的比喻, 把私有性规则想象成餐厅的后台办公室:
那里发生的事情对餐厅顾客来说是私有不可见的,
但办公室经理却能看到并操控他们所经营的餐厅中的任何事情,

Rust 选择如此设计其模块系统功能, 以使隐藏内部实现细节成为一种默认. 这样您就知道,
哪些内部代码可以被您更改, 而不会影响外层的代码.
但是, Rust 确实也为您提供了将内层次级模块代码公开给外层上级模块的选择,
用 pub 关键字将项公开即可.

### 使用 `pub` 关键字暴露路径

让我们回到清单 7-4 中展示的错误, 它告诉我们 `hosting` 模块是私有的.
我们希望其上级模块中的 `eat_at_restaurant` 函数能够拥有对子模块中
`add_to_waitlist` 函数的访问权限, 于是我们使用 `pub` 关键字标记 `hosting` 模块,
如清单 7-5 中所示.

<span class="filename">文件名: src/lib.rs</span>

```rust,ignore,does_not_compile
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-05/src/lib.rs}}
```

<span class="caption">清单 7-5: 将 `hosting` 模块声明为 `pub` 以在
`eat_at_restaurant` 中使用它</span>

不幸的是, 清单 7-5 中的代码仍然会出错, 如清单 7-6 所示.

```console
{{#include ../listings/ch07-managing-growing-projects/listing-07-05/output.txt}}
```

<span class="caption">清单 7-6: 编译器构建清单 7-5 中的代码时产生的错误</span>

发生了什么呢? 将 `pub` 关键字添加到 `mod hosting` 之前会使该模块变为公开. 如此改变之后,
如果我们能够访问 `front_of_house`, 也就能访问 `hosting`. 不过 `hosting` 中的
*内容* 依然是私有的; 公开某个模块, 并不意味着公开其中的内容. 模块上的 `pub` 关键字,
仅允许其上级模块引用它本身, 但无法访问它内部的代码. 介于模块仅仅是容器,
如果只把模块变为公开, 并不能让我们做什么; 我们需要更进一步,
根据需要, 将模块内部的一个或多个项也变为公开.

清单 7-6 中的错误称 `add_to_waitlist` 是私有的. 实际上, 私有性规则适用于结构,
枚举, 函数, 方法以及模块.

如清单 7-7 所示, 让我们在 `add_to_waitlist` 函数定义前添加 `pub` 关键字,
也将其变为公开.

<span class="filename">文件名: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-07/src/lib.rs}}
```

<span class="caption">清单 7-7: 在 `mod hosting` 和 `fn add_to_waitlist`
前加上 `pub` 关键字, 使我们得以从 `eat_at_restaurant` 中调用该函数</span>

现在代码将能够编译了! 让我们再看看 `add_to_waitlist` 中的绝对路径和相对路径,
以明白为什么在我们添加 `pub` 关键字之后, 私有性规则就允许使用它们了.

在绝对路径中, 我们以 `crate` 开始, 也就是我们 crate 模块树的根部.
首先 `front_of_house` 模块定义于 crate root 下. 尽管 `front_of_house` 并未公开,
但因为 `eat_at_restaurant` 函数和 `front_of_house` 被定义在了同一个模块中
(也就是说 `eat_at_restaurant` 和 `front_of_house` 互为同级模块), 我们仍可以在
`eat_at_restaurant` 中引用到 `front_of_house`. 下一站则是标记了 `pub` 的 `hosting`
模块. 我们能够访问 `hosting` 上级的这个模块, 自然也可以访问 `hosting`. 最后, 
`add_to_waitlist` 函数被 `pub` 标记, 且我们能够访问它的上级模块,
因此这次函数调用可以成功.

在相对路径中, 除第一步以外, 其逻辑和绝对路径相同: 该路径从 `front_of_house` 出发,
而不是 crate root. `front_of_house` 模块和 `eat_at_restaurant`
函数被定义在相同的模块内部, 因此从 `eat_at_restaurant`
所在模块中出发的相对路径可以工作. 之后, 由于 `hosting` 和 `add_to_waitlist` 均被
`pub` 标记, 于是路径的剩余部分可以工作, 该次函数调用是有效的!

如果您计划分享您的库 crate 以便其它项目可以使用您的代码, 那么您的公开 API
就是您和您 crate 的用户之间的合约, 决定着他们可以怎样与您的代码交互.
在对您的公开 API 作出更改时, 有很多需要考虑的地方, 可以让人们在依赖您的 crate
时更为轻松. 这些考量超出了本书的范畴; 如果您对这个话题感兴趣, 请参见
[Rust API 指南][api-guidelines].

> #### 同时含有库和二进制二者的包的最佳实践
>
> 之前我们提到, 一个包 (package) 中可以同时含有 *src/main.rs* 作为二进制
> crate 的 root 以及 *src/lib.rs* 作为库 crate 的 root, 且两个 crate 默认使用包
> 的名字. 通常，类似这种同时包含库和二进制 crate 的包, 它的二进制 crate 中的代码,
> 往往刚好能够启动一个可执行文件, 然后调用其位于库 crate 中的代码.
> 这样能使其它项目尽可能多地受益于该包所提供的功能, 因为库 crate 的代码才可以被共享.
>
> 模块树应当被定义在 *src/lib.rs* 中. 然后, 在二进制 crate
> 中便可以通过以包名开始的路径使用任何公开的项.
> 二进制 crate 变成了一个使用该库 crate 的用户, 正如一个完全外部的 crate
> 使用该库 crate 时那样: 只能使用公开的 API. 这样可以帮助您设计一个良好的 API;
> 您不仅仅是作者, 同时也是用户!
>
> 在 [第十二章][ch12]<!-- ignore --> 中, 我们将会通过一个同时含有二进制 crate
> 和库 crate 的命令行程序, 来演示这种组织代码的做法.

### 使用 `super` 关键字开始相对路径

我们可以通过在路径的开头使用 `super` 关键字, 来构造开始于上级模块的相对路径,
而非开始于当前模块或 crate root. 这就像是在文件系统中以 `..` 语法开头表示路径.
使用 `super` 允许我们引用我们知道位于上级模块中的项;
当该模块与上级模块密切相关, 而上级模块未来可能会被移动到模块树的其它地方时, 
这样将会使重新排列模块树轻松许多.

考虑清单 7-8 中的代码, 其建模了一个厨师修复错误订单并亲自将餐品送出至顾客的场景.
定义在 `back_of_house` 模块中的 `fix_incorrect_order` 函数,
使用 `super` 关键字开始到 `deliver_order` 的路径,
调用定义在上级模块中的 `deliver_order` 函数.

<span class="filename">文件名: src/lib.rs</span>

```rust,noplayground,test_harness
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-08/src/lib.rs}}
```

<span class="caption">清单 7-8: 使用以 `super`
开始的相对路径调用函数</span>

该 `fix_incorrect_order` 函数位于 `back_of_house` 模块中, 因此我们能使用
`super` 来到 `back_of_house` 的上级模块, 在本例中也就是 `crate`, 这个 crate 的根.
从那里, 我们寻找 `deliver_order`, 并且能够找到它. 成功! 我们认为 `back_of_house`
模块和 `deliver_order` 函数很可能会保持这样的相对位置关系不变,
并在我们有朝一日决定重新组织该 crate 的模块树时一起被移动. 因此, 我们使用了 `super`
关键字, 以便我们在将来移动这部分代码到其它模块时, 可以少改几处代码.

### 将结构和枚举设为公开

我们也可以使用 `pub` 关键字将结构体和枚举指定为公开的, 但是将 `pub`
与结构体和枚举一起使用, 还有一些额外的细节需要注意.
如果我们在结构定义之前加上 `pub` 关键字, 我们是将该结构设为公开的了,
但结构的各个字段仍然是私有的. 我们可以根据具体情况选择是否将某个字段设为公开或非公开.
在清单 7-9 中, 我们定义了一个公开的 `back_of_house::Breakfast` 结构,
它的 `toast` 字段是公开的, 而 `seasonal_fruit` 字段是私有的.
这建模了一种餐厅的情景, 其中顾客可以选择随餐提供的面包类型, 但随餐搭配哪种水果,
则由厨师根据季节和库存来决定. 可供选择的水果变化得很快, 因此客户不得选择水果,
甚至无法看到他们将会得到哪种水果.

<span class="filename">文件名: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-09/src/lib.rs}}
```

<span class="caption">清单 7-9: 一个具有一些公开字段和一些私有字段的结构</span>

由于位于 `back_of_house::Breakfast` 结构中的 `toast` 字段是公开的,
在 `eat_at_restaurant` 中我们可以使用点表示法读写该 `toast` 字段.
需要注意, 我们不能在 `eat_at_restaurant` 中使用 `seasonal_fruit` 字段,
因为 `seasonal_fruit` 是私有的. 尝试取消对尝试修改 `seasonal_fruit`
字段的值一行代码的注释, 来看看会得到什么样的错误!

同时也请注意, 由于 `back_of_house::Breakfast` 有私有字段,
该结构需要提供一个用于构造 `Breakfast` 实例的公开的关联函数
(我们已经将其命名为 `summer`, 如上所示). 如果 `Breakfast` 不具备这样的函数,
我们将不能够在 `eat_at_restaurant` 中创建 `Breakfast` 的实例,
因为我们不能在 `eat_at_restaurant` 中给私有字段 `seasonal_fruit` 设定值.


相反, 如果我们将一个枚举设为公开, 那么它的所有变体都将变为公开的.
我们只需要在 `enum` 关键字之前添加上 `pub`, 如清单 7-10 所示.

<span class="filename">文件名: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-10/src/lib.rs}}
```

<span class="caption">清单 7-10: 将一枚举设为公开, 将使其所有变体变为公开</span>

由于我们将 `Appetizer` 枚举设为了公开, 我们可以在 `eat_at_restaurant` 中使用
`Soup` and `Salad` 变体.

对于枚举来说, 除非它的变体均为公开, 否则它将不是很有用; 每次都必须用 `pub`
标注所有枚举变体将会很麻烦, 所以默认情况下枚举的变体均为公开的.
结构则通常在其字段不公开的情况下有用,
因此结构体字段遵循 "默认皆为私有" 的一般规则, 除非使用 `pub` 标注.

还有一个牵涉到 `pub` 的场景我们没有涉及, 也就是我们模块系统的最后一个功能:
`use` 关键字. 我们首先将探讨单独的 `use`,
之后我们将展示如何将 `pub` 和 `use` 结合起来.

[pub]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#使用-pub-关键字暴露路径
[api-guidelines]: https://rust-lang.github.io/api-guidelines/
[ch12]: ch12-00-an-io-project.html
