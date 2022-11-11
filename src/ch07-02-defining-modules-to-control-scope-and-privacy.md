## 定义模块以控制作用域和私有性

在本节中, 我们将讨论模块以及模块系统中其余的部分, 也就是让您可以对项目进行命名的
*路径* (paths); 将路径引入作用域的 `use` 关键字; 以及使项目公开可用的 `pub` 关键字.
我们也会探讨 `as` 关键字, 外部包, 以及 glob 运算符.

首先, 我们将以几条您在未来组织代码时可以遵循的规则开始, 便于参考. 接着,
我们将对每条规则进行详细解释.

### 模块有关内容速查

这里我们提供了一些关于模块, 路径, `use` 关键词, `pub` 关键词在编译器中的工作原理,
以及大多数开发者组织他们代码方式的快速参考. 在本章中, 我们将通过实例来认识每条规则,
但这里是您重温模块工作原理的好去处.

- **从 crate root 开始**: 在编译某个 crate 时, 编译器首先会查看其 crate root
  文件 (对于库 crate 一般是 *src/lib.rs*, 二进制 crate 则是 *src/main.rs*)
  中的内容, 从中寻找要编译的代码.
- **模块的声明**: 您可以在 crate root 文件中声明新的模块; 比如您可以用 `mod
  garden;` 语句声明一个 "garden" 模块. 至于模块代码, 编译器则会在下面这些地方寻找:
  - 行间 (inline): 紧随 `mod garden` 后面, 将分号替换为花括号后, 括号之间的代码
  - 在文件 *src/garden.rs* 中
  - 在文件 *src/garden/mod.rs* 中
- **子模块的声明**: 您可以在除 crate root 之外的任何文件中声明子模块. 比如,
  您可能会在 *src/garden.rs* 中声明 `mod vegetables;`.
  至于子模块的代码, 编译器将会在上级模块名所指明的目录下的这些地方寻找:
  - 行间 (inline): 紧随 `mod vegetables` 其后, 在花括号中间 (而不是分号)
  - 在文件 *src/garden/vegetables.rs* 中
  - 在文件 *src/garden/vegetables/mod.rs* 中
- **模块中代码的路径**: 一旦某模块位于您 crate 中, 只要满足私有性规则, 
  您便可以使用路径在同一 crate 中的其它任何地方引用该模块中的代码. 例如, 一个位于
  garden vegetables 模块中的 `Asparagus` 类型可以经由
  `crate::garden::vegetables::Asparagus` 被找到.
- **是 private 还是 public**: 某模块内部的代码, 默认对其上级模块不可见 (private).
  要将一个模块变为公开 (public), 需要使用 `pub mod` 声明, 而不是 `mod`.
  要使某公开模块内部的项也为公开状态, 则要在它们的声明前使用 `pub` 关键字.
- **use 关键字**: 在同一作用域内, 使用 `use` 关键字将创建到某一项目的捷径,
  以减少冗长路径的重复出现. 在任何能够引用 `crate::garden::vegetables::Asparagus`
  的作用域中, 您可以使用 `use crate::garden::vegetables::Asparagus;` 创建一个捷径,
  之后仅需写出 `Asparagus`, 便可以使用该类型.

下面我们创建一个名为 `backyard` 的二进制 crate, 说明这些规则. 该 crate 的目录, 
同样名为 `backyard`, 包含如下文件和目录:

```text
backyard
├── Cargo.lock
├── Cargo.toml
└── src
    ├── garden
    │   └── vegetables.rs
    ├── garden.rs
    └── main.rs
```

在本例中, crate root 文件为 *src/main.rs*, 它里面包含如下内容:

<span class="filename">文件名: src/main.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/main.rs}}
```

其中 `pub mod garden;` 一行告诉编译器需要包含进 *src/garden.rs* 中发现的代码,
也就是:

<span class="filename">文件名: src/garden.rs</span>

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden.rs}}
```

这里, `pub mod vegetables;` 意味着 *src/garden/vegetables.rs*
中的代码也被包含了进来. 这些代码是:

```rust,noplayground,ignore
{{#rustdoc_include ../listings/ch07-managing-growing-projects/quick-reference-example/src/garden/vegetables.rs}}
```

接下来让我们深入了解这些规则的细节, 并且用实际行动说明它们.

### 将相关联的代码集合在模块中

*模块* 让我们可以用一种易于阅读和复用的方式组织 crate 内部的代码.
模块允许我们控制各项目的 *私有性* (privacy), 因为模块内部的代码默认均为私有.
私有项目为不可供外部使用的内部实现细节. 我们可以选择将其中一些模块和项目为变为公开,
这样就将他们暴露给了外部代码, 从而可被外部代码使用和依赖.

为了举例, 让我们一起编写一个能够提供常见餐厅功能的库 crate. 我们将只定义函数的签名,
而将具体的函数体留空, 以便专注于代码的组织, 而非餐厅功能的具体实现.

在餐饮行业中, 餐厅的一些部分被称为 *前台*, 而其余部分被称为 *后台*.
前台是顾客们所处的地方; 接待员引客人入座, 服务员下单和收款, 以及酒保制作饮品,
都位于这里. 后台则是主厨和厨师们下厨, 洗碗工做清洁, 以及经理们进行管理工作的地方.

要将我们 crate 的结构设计成这种样子, 我们可以将这些功能函数安排进嵌套的模块里.
首先运行 `cargo new restaurant --lib` 命令新建一个名为 `restaurant` 的库;
然后将代码清单 7-1 中的内容输入进 *src/lib.rs*, 来定义一些模块和函数签名.
前台部分的内容如下:

<span class="filename">文件名: src/lib.rs</span>

```rust,noplayground
{{#rustdoc_include ../listings/ch07-managing-growing-projects/listing-07-01/src/lib.rs}}
```

<span class="caption">代码清单 7-1: 一个名为 `front_of_house` 的模块,
其中包含其它一些模块, 函数则包含在这些模块中</span>

我们用 `mod` 关键字和跟在它后面的模块名 (本例中为 `front_of_house`) 定义了一个模块.
然后将模块的主体写在了花括号之间. 我们可以在模块中放置其它模块, 至于本例中, 我们放进了
`hosting` 和 `serving` 这两个模块. 模块中也能存放其他项目的声明, 比如结构体, 枚举,
常量, 特征, 以及 — 如代码清单 7-1 中所示 — 函数.

通过使用模块, 我们将相互具有关联的定义集合在一起, 并通过命名来说明它们之间是何种联系.
使用这些代码的程序员不用阅读所有定义, 而是基于分组情况, 就能对代码进行定位,
这使他们查找相关定义的过程变得轻松. 在向代码中添加新功能时, 程序员也得以知晓,
该将代码放在何处才能保持程序的整洁有序.

早些时候我们讲过, *src/main.rs* 和 *src/lib.rs* 被叫做 crate root.
它们之所以叫这个名字, 是因为两个文件中各自的内容, 都会形成一个名为 `crate` 的模块,
作为对应 crate 的模块结构的根 (root), 该模块结构也被称作 *模块树* (module tree).

代码清单 7-2 展示了代码清单 7-1 中代码结构对应的模块树.

```text
crate
 └── front_of_house
     ├── hosting
     │   ├── add_to_waitlist
     │   └── seat_at_table
     └── serving
         ├── take_order
         ├── serve_order
         └── take_payment
```

<span class="caption">代码清单 7-2: 代码清单 7-1 中代码所对应的模块树. </span>

这棵树展示了各模块是如何嵌套在彼此之间的; 举个例子, `hosting` 就是嵌套在
`front_of_house` 之中. 该树还说明了有些模块彼此之间是 *同级模块* (siblings),
意味着它们被定义在了同一个模块中; `hosting` 和 `serving` 就为定义在
`front_of_house` 中的同级模块. 如果模块 A 被包含在模块 B 中, 我们则称 A 为模块 B
的 *次级模块* (child), 而模块 B 则为模块 A 的 *上级模块* (parent). 需要注意, 
整棵模块树都位于一个名为 `crate` 的隐含模块下.

说到模块树, 您可能会想起计算机文件系统中的目录树; 这是个很恰当的类比!
正如您通过文件系统中的目录管理文件一样, 您也应使用模块来组织代码.
而且正如我们需要定位目录中的文件一样, 我们也需要一种方式来找到这些模块.
