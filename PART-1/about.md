# 关于Swift

## 初见

Swift 是基于 Cocoa 和 Cocoa Touch 框架

使用自动引用计数(Automatic Reference Counting, ARC)来简化内存管理

### 值

使用 let 来声明常量,使用 var 来声明变量。

不用明确地声明类型,声明的同时赋值的话,编译器 会自动推断类型

如果初始值没有提供足够的信息(或者没有初始值),那你需要在变量后面声明类型,用冒号分割。

值永远不会被隐式转换为其他类型。如果你需要把一个值转换成其他类型,请显式转换。

有一种更简单的把值转换成字符串的方法:把值写到括号中,并且在括号之前写一个反斜杠。

let appleSummary = "I have \(apples) apples."

使用方括号 [] 来创建数组和字典

疑问:

```
初始化语法含义
[String]()
[String: Float]()
!!!这里使用的是泛型!!!
还有[] 和 [:] 意义
```

### 控制流

使用 if 和 switch 来进行条件操作,使用 for-in 、 for 、 while 和 repeat-while 来进行循环。包裹条件和循环 变量括号可以省略,但是语句体的大括号是必须的。

在 if 语句中,条件必须是一个布尔表达式——这意味着像 if score { ... } 这样的代码将报错,而不会隐形地 与 0 做对比。

你可以一起使用 if 和 let 来处理值缺失的情况。这些值可由可选值来代表。一个可选的值是一个具体的值或者 是 nil 以表示值缺失。在类型后面加一个问号来标记这个变量的值是可选的。

另一种处理可选值的方法是通过使用 ?? 操作符来提供一个默认值。如果可选值缺失的话,可以使用默认值来代 替。

思考:

```
接触过java中, 不能直接判断一个变量是否存在值,但是设置为final之后就可以了,跟let的意思是否一样?
Optional外链
https://mp.weixin.qq.com/s?__biz=MjM5NTIyNTUyMQ==&mid=450497602&idx=1&sn=ffd27742a47fd27c154864d07b886ed1&scene=1&srcid=0222K52OTn9AJiNOgnaim4Mi&pass_ticket=xsNZo7i3zhYRNhRNbBlR8YRs6Ii5K6y1wrilFjnwk%2BIskRZayVVUd5Q0vr1zX%2Bu4#rd
```

switch 支持任意类型的数据以及各种比较操作——不仅仅是整数以及测试相等

疑问:

如果没有default语句, 是不是会在没有匹配带子句的情况下无法退出switch语句

使用 for-in 来遍历字典,需要两个变量来表示每个键值对。字典是一个无序的集合,所以他们的键和值以 任意顺序迭代结束。

使用 while 来重复运行一段代码直到不满足条件。循环条件也可以在结尾,保证能至少循环一次。

可以在循环中使用 ..< 来表示范围,也可以使用传统的写法,两者是等价的

使用 ..< 创建的范围不包含上界,如果想包含的话需要使用 ...

### 函数和闭包

使用 func 来声明一个函数,使用名字和参数来调用函数。使用 -> 来指定函数返回值的类型。

函数可以带有可变个数的参数,这些参数在函数内表现为数组的形式

函数可以嵌套

函数是第一等类型,这意味着函数可以作为另一个函数的返回值

函数也可以当做参数传入另一个函数

函数实际上是一种特殊的闭包:它是一段能之后被调取的代码。闭包中的代码能访问闭包所建作用域中能得到的变 量和函数,即使闭包是在一个不同的作用域被执行的 - 你已经在嵌套函数例子中所看到。你可以使用 {} 来创建 一个匿名闭包。使用 in 将参数和返回值类型声明与闭包函数体进行分离。

如果一个闭包的类型已知,比如作为一个回调函数,你可以忽略参数的类型和返回值。单个语句闭包会把它语句的值当做结果返回

你可以通过参数位置而不是参数名字来引用参数——这个方法在非常短的闭包中非常有用。当一个闭包作为最后
一个参数传给一个函数的时候,它可以直接跟在括号后面。当一个闭包是传给函数的唯一参数,你可以完全忽略
括号。

思考:

和js闭包的区别

### 对象和类

使用 class 和类名来创建一个类。类中属性的声明和常量、变量声明一样,唯一的区别就是它们的上下文是 类。同样,方法和函数声明也一样。

注意 self 被用来区别实例变量。当你创建实例的时候,像传入函数参数一样给类传入构造器的参数。每个属性都 需要赋值——无论是通过声明还是通过构造器(就像 name )。

使用 init 来创建一个构造器,如果需要在删除对象之前进行一些清理工作,使用 deinit 创建一个析构函数。

子类的定义方法是在它们的类名后面加上父类的名字,用冒号分割。创建类的时候并不需要一个标准的根类,所
以你可以忽略父类。

子类如果要重写父类的方法的话,需要用 override 标记

属性可以有 getter 和 setter 

如果你不需要计算属性,但是仍然需要在设置一个新值之前或者之后运行代码,使用 willSet 和 didSet 。

### 枚举和结构体

使用 enum 来创建一个枚举。就像类和其他所有命名类型一样,枚举可以包含方法。

疑问:

枚举的用途

结构体是传值,类是传引用

### 协议和扩展

使用 protocol 来声明一个协议。类、枚举和结构体都可以实现协议。

mutating 关键字用来标记一个会修改结构体的方法

使用 extension 来为现有的类型添加功能,比如新的方法和计算属性。你可以使用扩展在别处修改定义,甚至是 从外部库或者框架引入的一个类型,使得这个类型遵循某个协议。

### 泛型

在尖括号里写一个名字来创建一个泛型函数或者类型。

NSData 通用的数据交换协议

