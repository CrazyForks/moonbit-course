# 现代编程思想：月兔开发与面向值编程

大家好，欢迎来到由IDEA研究院基础软件中心为大家带来的现代编程思想公开课。今天的主题是月兔开发与月兔中的表达式。我们将会讲解如何准备月兔的开发环境，然后通过建立计算模型来逐步理解用月兔编写的程序。

## 月兔MoonBit

首先，让我们回顾一下上周提到过的内容。我们这门课程使用的编程语言月兔是一门现代的、静态类型的多范式编程语言，支持函数式编程、命令式编程等多种编程范式。它的语法较为容易上手。大家可以在月兔编程语言的[官网](https://moonbitlang.cn)看到更多的信息。

## 开发环境

月兔的开发工具主要由两部分构成：Visual Studio Code插件与命令行工具。

其中，Visual Studio Code插件依托VS Code这一被广泛使用的编辑器提供代码的开发环境，提供包括语言服务器及包级别构建等功能。语言服务器提供了诸如语法高亮、变量引用与定义的跳转、开发时的自动补全、程序的运行与调试等功能。包则是在月兔中，由多个文件构成的代码组织结构。在一个包中，可以在不同的文件之间定义相互的引用等，方便将功能分门别类定义在不同文件中，满足小型项目的开发需求。

命令行工具则提供项目级别的开发支持。一个项目通常由多个包组成，定义了包之间的相互依赖，同时一个项目也可能引用其他项目，来借用其他人实现的功能等。命令行工具提供了诸如项目级别的构建、测试、依赖管理等功能。

关于项目和包的组织结构与定义等，我们将会在之后的课程中详细介绍。在课程中，我们将会限制于包级别的开发。

VSCode插件可在浏览器的VSCode中使用，因此我们支持的开发环境分三类：浏览器环境（基于VSCode Web）、云原生开发环境（如[腾讯云Coding](https://coding.net)、[Gitpod.io](https://gitpod.io)、[Github.dev](https://github.dev)等）、本地开发环境。

### 浏览器环境

访问[try.moonbitlang.cn](https://try.moonbitlang.cn)即可使用。在试用环境中，提供了诸多代码样例，方便大家熟悉月兔语法。在试用环境中，大家可以创建新文件并运行，也可以参考包的案例。试用环境还提供单个文件的分享功能，方便大家分享代码以及提交反馈。需要注意的是，当前的浏览器环境不会自动保存修改，因此如果窗口被刷新，所有的修改都会丢失。请大家使用“另存为”功能对文件进行保存。

### 云原生开发环境

云原生开发环境即是基于远程服务器的开发环境。不同于传统的服务器按月收费，云原生开发环境根据实际用量进行收费，可以按需使用，就像一朵云，可大可小。

云原生开发环境需要依赖特定的云服务厂商。各个厂商的产品之间略有出入，但大体流程相同：在代码仓库（新建或克隆）启动云原生开发环境后，在扩展中搜索并安装"MoonBit Language"插件。此时，基于云厂商提供的VS Code软件，可以像浏览器环境一样开发。对于作业环境，到这一步即足够。

如果需要更进一步开发，则需要安装[命令行工具](https://www.moonbitlang.cn/download/)，或克隆[云原生开发模板](https://github.com/peter-jerry-ye/moonbit-template)。开发模板实现为大家准备了二进制文件，并在扩展面板中提供了安装建议。后续的二进制工具使用请参考[月兔构建系统教程](https://www.moonbitlang.cn/docs/build-system-tutorial/)

### 本地开发环境

本地开发需首先安装[VSCode](https://code.visualstudio.com/)，或兼容VSCode插件的其他开发软件，如VSCode的开源构建[VS Codium](https://mirrors.cernet.edu.cn/list/VSCodium)。国内的小伙伴可以使用后者，有各个大学提供的镜像站方便下载。之后步骤与准备云原生开发环境相同：需安装插件进行简单开发，再安装命令行工具进行大型工程开发。

## 月兔中的表达式

在进行了月兔的开发准备后，让我们尝试理解月兔的代码。下面将为大家展示一个典型的月兔程序，亦是上一节课的案例代码。

```moonbit
//顶层函数定义
fn num_water_bottles(num_bottles: Int, num_exchange: Int) -> Int {
  // 本地函数定义
  fn consume(num_bottles, num_drunk) {
    // 条件表达式
    if num_bottles >= num_exchange {
      // 数据绑定
      let num_bottles = num_bottles - num_exchange + 1
      let num_drunk = num_drunk + num_exchange
      // 函数运算
      consume(num_bottles, num_drunk)
    } else {
      num_bottles + num_drunk
    }
  }
  consume(num_bottles, 0)
}

// 程序测试
test {
  // 命令
  assert_eq(num_water_bottles(9, 3), 13)
  assert_eq(num_water_bottles(15, 4), 19)
}
```

我们在此定义了一个顶层函数以及程序的入口。顶层函数中，我们定义了一个本地函数并且调用该函数进行计算。本地函数的值为一个条件表达式，对应条件为真的表达式块中我们定义了数据绑定并调用函数作为表达式块的值，而在另一个分支中我们进行了简单的加法运算。在程序入口中，我们使用了两个断言命令来判断我们程序的正确性。我们在上一节课已经看到了，这个程序的运行没有任何的输出，但它具体是如何运行的呢？

为了能够正确地理解代码所定义的程序的行为，从而写出正确的程序或是读懂他人的程序，我们需要能够理解程序的计算过程。为此，我们需要建立一个计算模型。月兔程序可以通过以值为中心的编程，或者说基于表达式的编程来描述，即将程序看作是定义一个值的表达式，而程序的运行则是对这个表达式进行简化获得我们所定义的值的过程。相比较之下，命令式编程风格则是定义做什么，通常由一系列的命令组成，例如：`创建名为x的变量`、`令x为5`、`令y指向x`、……我们将会在后续课程中更深入了解。

### 类型、值与表达式

每一个**类型**对应的是一个**值**的集合，如`Int`对应部分整数的集合；`Double`对应了部分实数的集合；`String`对应了字符串的集合等，我们稍后将详细了解月兔的基础数据类型。而每一个**表达式**由基于值的**运算**构成，并且可以简化为一个值（或已经是一个值）。在定义表达式的时候，可以通过使用括号来嵌套表达式。以下是一些例子。

|类型|值|运算|表达式|
|-----|------|----------|-----------|
|`Int`|`-1` `0` `1` `2`|`+` `-` `*` `/`|`5` `(3 + y * x)`|
|`Double`|`0.12` `3.1415`|`+` `-` `*` `/`|`3.0 * (4.0 * a)`|
|`String`|`"hello"` `"Moonbit"`|`+`|`"Hello, " + "MoonBit"`|
|`Bool`|`true` `false`|`&&` `\|\|` `not()`|`not(b1) \|\| b2`|


### 静态与动态

“静态”指在程序运行**之前**的事物，而“动态”指在程序运行**之时**的事物，动静描述的便是程序是否在运行状态。

月兔拥有静态类型系统。这意味着，在程序运行之前，编译器即会检查程序的类型是否定良好，以最大程度减少在运行时出现对数据进行错误类型的运算从而导致程序中断或给出错误的结果，如对一个逻辑值进行加减乘除。

月兔中每一个**标识符**（即标识某个实体的符号）都关联着唯一一个类型，一般用冒号进行关联，如：
- `x: Int`
- `a: Double`
- `s: String`

月兔中每一个**表达式**都有唯一的一个类型，这个类型由组成它的子表达式决定：
![类型推导示范 height:200px](well-typed-expression.drawio.png)
如图中的表达式，`a`的类型为`Double`，可以与同为`Double`的0.2进行加法。之后，我们通过`to_int`函数将其转换为`Int`，此时我们可以与`x`进行整数加法，获得一个整数作为表达式的值。因此，表达式的类型为`Int`。

月兔使用**类型推导**来确认程序是否正确使用类型，而开发工具也会在开发时实时提示检查到的类型错误：
![类型错误示例 height:200px](not-well-typed-expression.drawio.png)
如图中的表达式，`s`的类型为`String`，因此我们期待它与另一个字符串相加，即连接两个字符串。但此时，右侧类型为`Int`，不能相加。因此，编辑器以红色下划线提示我们它的错误。

### 月兔的基本数据类型

为了进行基于表达式的编程，我们需要了解月兔中有哪些值。我们在这里先介绍最简单的月兔的基本数据类型中的一部分：逻辑值、整数、浮点数、字符与字符串、多元组。其他的数据类型将在之后的课程中一一介绍。我们在这部分的介绍中不涉及数据的底层表现，如二进制、二进制补码等，感兴趣的同学可以在推荐阅读中进行拓展学习。

#### 逻辑值（布尔值）

首先我们要介绍的数据类型为逻辑值，亦称为布尔值，纪念发明布尔代数的数学家乔治·布尔而得名。它只有两种值：真和假，`true`和`false`。

常见的逻辑值的运算有：或、与、非。

非，即是对当前逻辑值取反，非真即为假，非假即为真。

与，当两者皆真才为真。例如，真与假，其结果为假。真与真，其结果为真。假与假，其结果为假。

或，当两者皆假才为假。例如，真或假，其结果为真。真或真，其结果为真。假或假，其结果为假。

我们这里给出月兔中定义或、与、非的代码样例：
  - 非：`not(true) == false`
  - 与：`true && false == false`
  - 或：`true || false == true`

代码样例中的两个等号表示比较，右侧则是我们对左侧定义的表达式的简化值的预期。换句话说，这些代码本身即是类型为逻辑值的表达式，并且我们期待它们的值均为真。

在这里，我们给出一个小练习：如何用或、与、非定义异或？异或，当一者为真才为真。

#### 整数类型

接下来我们介绍的数据类型为整数。整数大家都很熟悉，然而在计算机中，中央处理器一次能计算的数据大小是有限的。为了保证性能，基础数据所代表的数据范围也是有限的。在月兔中，基础类型中的整数分为两个类型，分别有不同的值域：
- 整型 `Int`：从$-2^{31}$到$2^{31}-1$（即从-2147483648到2147483647）
- 长整型 `Int64`：从$-2^{63}$到$2^{63}-1$

在月兔中，整数相除获得的依然是整数，其结果为整数除法中的商。若涉及负数，则按照正数进行运算后赋予正负号。例如，-4除以3结果为-1。当然，除以零依然不被允许，会在运行时产生错误中断程序。

既然整数有范围，那么对整数进行超出范围的运算后，获得的结果会**溢出**，即会变成范围中的某一整数而非期待的正确结果。举例来说，如果对最大的整型加一，则会变为最小的整型。

在月兔中，整数的四则运算只能由相同类型的整型进行，也就是整型只能与整型进行运算，长整型只能与长整型进行运算。如果想要将整型与长整型进行计算，需要将整型利用函数`to_int64`提升为长整形之后再运算，如`(100).to_int64()`。当直接定义长整型数字时，需在数字最后加上大写的L以示区别，如`2147483648L`，否则默认为整型。而将长整型转化为整型则使用`to_int`，如`100L.to_int()`。

需要注意的是，整数调用函数必须加括号。

在这里，我们给出一个小练习：如何计算两个正的`Int`的平均数（不考虑小数）而不溢出？

#### 浮点数类型

接下来我们介绍实数在计算机内部的表示。如之前所说，计算机中，基础数据代表的数据长度是有限的，因此我们无法表示一切的实数，而只能表示一部分的有限小数，并且通过$b \times 2^e$（尾数`b`与指数`e`均为整数）的方式进行近似表达。[例如](https://try.moonbitlang.cn/#02ce0b43)：`0.1 + 0.2 != 0.3`（感叹号与等号在月兔中代表不等号）。

在月兔中，浮点数有一个类型：`Double`。与整数相同，浮点数的四则运算只能是浮点数与浮点数进行并获得浮点数。若要对整数与浮点数进行运算，则须将整数提升为浮点数，如`(1).to_double()`，再进行运算。反之，也可通过舍弃小数点后的部分从浮点数近似为整数，如`(-1.2).to_int()`。

在这里，我们给出一个小练习：如何通过整数与浮点数之间的相互转换，比较`0.1 + 0.2`与`0.3`到小数点后一位使得结果为真？

#### 字符与字符串

我们在电脑上看到的一个个汉字、英文字母、数字等，都被归类为字符。而它们的序列则是字符串。在月兔中，字符类型为`Char`，而字符串类型为`String`。字符用英文单引号标识，如`'a'`；字符串用英文双引号标识，如`"Hello!"`。

这些文字，是如何在计算机中表达的呢？答案是，通过编码，将一个个数字映射为一个个字母，即一个字符对应一个数字。这种映射有多种可能的方式。较为通用的是ASCII，即美国信息交换标准代码，第一版在1963年发布。它定义了英文字符与常见符号到0～127的映射。举例来说，大写字母A到Z对应了从65到90的数字。之后，为了兼容所有国家的文字以及表情符号，有了Unicode，即统一码。在兼容ASCII的基础上进行了拓展。举例来说，“月”与“兔”在统一码中分别对应26376与20820。
统一码有不同的编码方式，如UTF-8、UTF-16等，感兴趣的同学可以搜索它们的区别。

月兔的字符编码采用了Unicode。基于编码，我们可以将整型转化为字符，如`Char::from_int(65)`应为`'A'`。

#### 多元组

在了解了一系列基础数据后，我们也会希望能够表达一些比较复杂的数据类型，例如日期需要由三个数字构成，而一个人的个人信息或许可以包含他/她的名字和年龄。为此，多元组允许我们将不同类型的数据结合在一起，用小括号包裹，用逗号分隔。如：
- `("Bob", 3): (String, Int)`
- `(2023, 10, 24): (Int, Int, Int)`

我们可以通过从0开始的下标访问数据
- `(2023, 10, 24).0 == 2023`
- `(2023, 10, 24).1 == 10`

#### 其他数据类型

月兔还有着其他的类型结构，如函数类型、单值类型、列表类型、自定义结构体等。我们将会在后续的课程中见到它们，并且学习如何自定义复杂的数据结构。

### 计算表达式的值

#### 简化 vs 运行

我们可以将月兔的表达式看作定义**值**的一种方式；同样地，我们可以将月兔的运行过程看作一系列的**计算**或者**简化**的求值步骤。相对而言，命令式编程则可以被看作执行一系列**行为**或者**命令**，其中每一个命令都会修改机器的状态，如：
- 创建指针`x`与`y`并分配内存，令`x`值为3，令`y`值为4
- 令`y`指向`x`
- 令`x`自增
- ……

![](./local-store.drawio.png)

我们将<表达式>简化为<值>记作：<表达式> $\Rightarrow$ <值>，如：
- `3` $\Rightarrow$ 3 （此时值即为本身）
- `3 + 4` $\Rightarrow$ 7
- `2 * (4 + 5)` $\Rightarrow$ 18
- `num_water_bottles(9, 3)` $\Rightarrow$ 13

我们将分解$\Rightarrow$简化的流程为**单步**计算，记作$\mapsto$，如：

&nbsp;     `(2 + 3) * (5 - 2)`  
$\mapsto$ `5 * (5 - 2)`          因为`2 + 3` $\mapsto$ 5  
$\mapsto$ `5 * 3`                        因为`5 - 2` $\mapsto$ 3  
$\mapsto$ `15`                               因为`5 * 3` $\mapsto$ 15

故 `(2 + 3) * (5 - 2)` $\Rightarrow$ `15`

#### 数值绑定

数值绑定的定义形式为：`let <标识符> : <类型> = <表达式>`。数值绑定将一个标识符与一个表达式绑定，作为计算的中间值来简化程序定义，避免出现超级复杂的表达式。数据绑定中的类型声明多数时候可以省略，月兔编译器会根据表达式类型进行推断，如：
- `let x = 10`
- `let y = "String"`

数值绑定后，出现的标识符均可被替换为绑定的表达式。若同一标识符被绑定多次，则以最近一次绑定为准，即最新的定义遮掩了之前的定义。如超出定义生效范围，则会恢复被遮掩的定义，因此并非进行了修改。

#### 表达式块与作用域

表达式块由大括号定义，形如：
```
{
  数值绑定
  数值绑定
  ……
  表达式
}
```

表达式块的类型即为最后的表达式的类型，表达式块的值即为最后表达式的值。

如果一个函数定义或数值绑定定义在所有表达式块外，则我们称之为顶层函数或顶层定义；如果一个函数定义或数值绑定定义在某个表达式内，则我们称之为本地函数或本地定义。顶层可以被称为全局，而本地可称为局部，这是因为它们生效的范围，又称作用域。

作用域指的是定义或数值绑定有效的范围，分为全局和局部。全局指的是整个文件范围内生效，而局部指的是在定义开始之后到当前表达式块结束生效。在顶层定义的，在整个文件范围内生效，因此又称全局；在本地定义的，在定义之后到表达式块结束前生效，因此又称局部。
例如：
![height:12cm](scope.drawio.png)

图中我们定义了两个顶层标识符`value`和`y`。`value`的值由表达式块定义，其中定义了`x``tmp`和`another_tmp`。而在`another_tmp`中我们又定义了一次`tmp`。注意箭头指向，定义了`another_tmp`的值的表达式（第7行）中的`tmp`是最近定义的第5行的`tmp`，它遮掩了第3行`tmp`的定义，而定义了`value`的值的表达式（第9行）中的`tmp`则是第3行的`tmp`，因为此时已经超出了第5行的`tmp`的作用域（第5-8行）。同时我们注意到，第9行的`y`的定义出现在第12行，也就是定义出现在了使用之后。这是被允许的，因为`y`在顶层被定义，作用域为全局。

#### 数值绑定下的表达式简化

分为以下几步：
- 简化数值绑定右侧的表达式
- 将出现的标识符**替换**为简化后的值
- 省略数值绑定部分
- 对剩余表达式进行化简

不妨分析上面看到的案例：
```rust
let value = {
  let x = 1
  let tmp = x * 2
  let another_tmp = {
    let tmp = x * 3

    tmp
  }
  tmp + another_tmp + y
}

let y: Int = 10
```

首先，我们可以替换所有`x`出现的地方为`1`并省略`x`的定义。`y`同理。

```rust
let value = {
  // 省略x
  let tmp = 1 * 2 // 替换x
  let another_tmp = {
    let tmp = 1 * 3 // 替换x

    tmp
  }
  tmp + another_tmp + 10 // 替换y
}

// 省略y
```

之后，我们可以简化两处tmp定义的右侧表达式，并对`another_tmp`中的标识符进行替换。

```rust
let value = {
  let tmp = 2 // 简化右侧表达式
  let another_tmp = {
    let tmp = 3 // 简化右侧表达式

    3 // 替换表达式
  }
  tmp + another_tmp + 10
}
```

然后，我们可以计算`another_tmp`的值。回忆一下，表达式块的值即是表达式块最后的表达式的值。

```rust
let value = {

  let tmp = 2
  let another_tmp = 3
  tmp + another_tmp + 10
}
```

到了这一步，我们可以替换剩下的标识符为它们所绑定的值。

```rust
let value = {

  let tmp = 2
  let another_tmp = 3
  2 + 3 + 10
}
```

然后我们便可以得出结论：

```rust
let value = 15
```

#### 条件表达式

除了对值进行运算以外，月兔中还有条件表达式，用于判断，根据条件给出不同的值。

条件表达式的定义形式为：`if 条件 表达式块｜条件为真 else 表达式块｜条件为假`。在月兔中，条件表达式亦是表达式，因此可以被用在其他的表达式内，如：
- `if 1 < 100 { 1 } else { 0 } * 10`
- `if x > y { "x" } else { "y" } + " is bigger"`
- `if 0.1 + 0.2 == 0.3 { "Great!" } else { "C'est la vie :-)" }`

条件表达式中，条件的类型需为逻辑值，而其余两个表达式块类型需相同，这个类型代表了整个表达式的类型，如：
![](if-else-then.drawio.png)
其中，条件是对两个浮点数进行相等的判定，类型为逻辑值；真假分支表达式块的值均为字符串，因此整个条件表达式类型为字符串。

条件表达式的简化取决于条件的简化结果为真或假。若为真，则条件表达式简化为第一个表达式块的值；若为假，则简化为第二个表达式块的值。例如：

&nbsp;     `if 1 < 100 { 1 } else { 0 } * 10`
$\mapsto$ `if true { 1 } else { 0 } * 10`
$\mapsto$ `1 * 10`
$\mapsto$ `10`

条件表达式必须有两个分支（否则如果条件为假，表达式的值该是什么呢？）

## 课程总结

我们今天学习了

- 如何配置月兔开发环境
  - 浏览器开发环境
  - 云原生开发环境
  - 本地开发环境
- 月兔基本数据类型
  - 逻辑值
  - 整数和浮点数
  - 字符和字符串
  - 多元组ß
- 如何以表达式和值来看待月兔程序，以简化求值来理解月兔程序的运行