# 基础数据类型

## NSString

一个NSString对象可以存储一段Unicode字符。在cocoa中，所有和字符、字符串相关的处理都是使用NSString来完成。

用于对字符串进行各种处理（创建、截取、判断比较、转化数据类型、拼接、替换、添加、追加、读取、写入、删去、...等等)

NSString创建赋值后不能动态修改长度和内容，除非给重新赋值。而NSMutableString类似与链表的，在创建赋值后可以进行修改长度，插入，删除等操作。

NSString和SwiftString的区别和使用场景

```
1.NSString是引用类型。SwiftString是值类型。
2.SwiftString字符串之间的拼接比NSString方便
3.SwiftString 可以实现字符串遍历
4.SwiftString计算字符串长度的方法与NSString不同
5.SwiftString比较字符串相等的方式与NSString不同
6.NSString可以同基本数据类型见转化
7.SwiftString可以通过isEmpty属性来判断该字符串是否为空
8.SwiftString独有的字符串插入字符功能
```

## NSNumber

NSNumber可以将基本数据类型包装起来，形成一个对象，这样就可以给其发送消息，装入NSArray中等等。

NSNumber继承NSObject ，可以使用比较 compare： isEqual等

NSNumber是NSValue的子类

封装char, short int, int, long int, long long int, float, double , BOOL.

## NSValue

NSValue是用来存储一个C或者Objective－C数据的简单容器, 可以保存任意类型的数据.

NSValue主要用来封装自定义的数据结构，可以是系统框架提供的CGRect/CGPoint/CGSize等数据结构，也可以是自己定义的struct。

NSValue类的目标就是允许以上数据类型的数据结构能够被添加到集合里，例如那些需要其元素是对象的数据结构，如NSArray或者NSSet 的实例。需要注意的是NSValue对象一直是不可枚举的。

## NSDate

NSDate对象用来表示一个具体的时间点, 是相对另外一个时刻的时间点. NSDate存储的是GMT时间

```
可以快速获取的时间点有5个:
now    （当前时间点）
相对于1 January 2001, GMT的时间点
相对于1970的时间点
distantFuture   （不可达到的未来的某个时间点）
distantPast     （不可达到的过去的某个时间点
```

*iOS中用NSDate表示的时间只能在distantPast和distantFuture之间！*


## NSData

使用文件时，需要频繁地将数据读入一个临时存储区，它通常成为缓冲区。

NSData类提供了一种简单的方式，它用来设置缓冲区、将文件的内容读入缓冲区，或将缓冲区的内容写到一个文件。

对于32位应用程序，NSDATA缓存区最多可以存储2GB的数据。

我们既可定义不变缓冲区（NSData类），也可定义可变的缓冲区（NSMutableData类）。

NSData主要是提供一块原始数据的封装，方便数据的封装与流动，比较常见的是NSString／NSImage数据的封装与传递。在应用中，最常用于访问存储在文件中或者网络资源中的数据。

NSData不管传递的内容到底是什么，仅仅是传递一块内存 ,仅需内存的起始地址和长度

## NSNull

在集合中不能存放nil值，因为在NSArray和NSDictionary中nil有特殊的含义。但是在有些时候，确实需要用到这样的空值，所以Cocoa提供了NSNull

不能向NSNull发送消息, 那会导致程序奔溃

## NSCharacterSet

NSCharacterSet其实是许多字符或者数字或者符号的组合

NSCharacterSet ，以及它的可变版本NSMutableCharacterSet，用面向对象的方式来表示一组Unicode字符。它经常与NSString及NSScanner组合起来使用，在不同的字符上做过滤、删除或者分割操作。例如去掉空格, 挤压空格等操作.


## NSScanner

NSScanner 是个用以解析任意或半结构化的字符串的数据的类。当你为一个字符串创建一个扫描器时，你可以指定忽略哪些字符，这样可以避免那些字符以各种各样的方式被包含到解析出来的结果中。

## NSRange

NSRange表示范围作用的结构体，其中location是一个以0为开始的index，length是表示对象的长度。他们都是NSUInteger类型.

## NSDictionary

用于保存键值对数据的字典

## NSArray

数组只能存储对象, 是一种集合类型, 是有序的集合, 可以做排序的操作, 内置二分查找

## NSSet

跟数组类似, 也是一种集合类型, 是无序的集合, 提供集合的运算如交集,并集等, 查找元素基于哈希算法

## NSOrderSet

NSOrderSet是NSArray和NSSet的一个好处结合体, 如对象查找，对象唯一性，和快速随机访问, 但是性能会消耗较大,NSOrderedSet比NSSet和NSArray占用更多的内存，因为它需要一起维护哈希值和索引。

## NSError

NSERror对象封装了更加丰富和更具扩展性的错误信息而不是简单的错误码或错误字符串

NSError允许你附加任意的字典对象，并提供一个方法来返回一个具有可读性的错误描述。

NSError不是一个抽象类，可以直接使用，允许用户创建具体的子类来提供更好的错误描述信息

NSError对象中，主要有三个私有变量

```
错误域（NSInteger）： _domain
错误标示（NSString *）：_code
错误详细信息（NSDictionary *）：_userInfo
通常用_domain和_code一起标识一个错误信息
```

如何使用:

```
1.声明NSError变量。一定要加"?"，不加或者加"!"都不行。因为使用了optional，所以要用var而不用let。
2.使用的时候在变量前加上"&"。

```


