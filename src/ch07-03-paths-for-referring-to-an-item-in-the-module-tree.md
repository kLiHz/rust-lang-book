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
若继续使用我们的比喻, 把隐私规则想象成餐厅的后台办公室:
那里发生的事情对餐厅顾客来说是私有不可见的,
但办公室经理却能看到并操控他们所经营的餐厅中的任何事情,

Rust 以这种形式提供它的模块系统功能, 将内部实现细节的隐藏变得默认. 这样, 您就知道,
在不破坏外部代码的情况下, 哪些部分的内部代码是可以更改的.
但是, Rust 确实也为您提供了将内层次级模块代码公开给外层上级模块的选择,
用 pub 关键字将项公开即可.

### 使用 `pub` 关键字暴露路径

让我们回到清单 7-4 中展示的错误, 它告诉我们 hosting 模块是私有的.
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

发生了什么呢? 添加 `pub` 关键字到 `mod hosting` 前会使该模块变为公开. 如此改变之后,
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


[pub]: ch07-03-paths-for-referring-to-an-item-in-the-module-tree.html#使用-pub-关键字暴露路径
[api-guidelines]: https://rust-lang.github.io/api-guidelines/
[ch12]: ch12-00-an-io-project.html
