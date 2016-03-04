# 其他类(线程管理)

## ARC

Automatic Reference Counting 自动引用计数

ARC 能够管理 Objective-C 对象的内存，却不能管理 CF( core foundation ,c语言接口) 对象，CF 对象依然需要我们手动管理内存。

在 CF 和 ObjC 之间 bridge 对象的时候，问题就出现了，编译器不知道该如何处理这个同时有 ObjC 指针和 CFTypeRef 指向的对象。

这时候，我们需要使用__bridge, __bridge_retained, __bridge_transfer 修饰符来告诉编译器该如何去做。

## Toll-free bridging

简称为TFB, 是一种允许某些ObjC类与其对应的CoreFoundation类之间可以互换使用的机制。

## NSArray

```
//: 空数组 - 疑问: 为什么 var s=[] 控制台输出 (
var s=[]
var s1=[AnyObject]()
//: 普通数组
var s2=[1,"2"]
//: 指定泛型数组
var s3:[Int]=[1,2]
//: 深度克隆一个数组
var s4=NSArray(array: s3,copyItems: true)
//: 创建一个重复项的数组
var s5=[Int](count:3,repeatedValue: 2)
//: 通过打开一个文件的方式创建数组, 把文件内容作为数组的内容, 格式不对的话就会nil
var s6=NSArray(contentsOfFile:"/Users/Shared/data.txt")
//: 通过打开一个URL地址的方式, 解析内容来创建数组
var s7=NSArray(contentsOfURL: NSURL(fileURLWithPath: "http://www.baidu.com"))

//: 访问数组属性 - 疑问: firstObject 和 lastObject 是废弃了吗?
var s8=[s2,s3]
s8.count
s8.first
s8.last

//: 访问元素
s5[2]
//: 给元素赋值
s5[2]=3
//: 添加元素
s5.append(1)
//: 插入元素
s5.insert(1,atIndex: 2)
//: 删除元素 - 返回的是被删除的元素, 可以用range类型
s5.removeAtIndex(0)
//: 判断 比较方法
s8.isEmpty
//: 遍历数组
for item in s5 {
    print(item)
}
//: 数组排序
s5.sort()
//: 倒序输出
s5.reverse()
map()
filter()
flatMap()
reduce()
emmuter()
多线程导致的线程安全
原子锁
objc.sync_end()
objc.sync_exit()
```

## NSDictionary

```
//: 空字典 - 疑问: 为什么控制台输出是 {
var d=[:]
//: 创建指定泛型的字典
var d1=[String : AnyObject]()
//: 通过字面量来创建
var d2=["TYO": "Tokyo", "DUB": "Dublin"]

//: 访问字典属性 - 返回集合
d2.keys
d2.values

//: 访问元素
d2["TYO"]
//: 给元素赋值 - updateValue 有键值就更新,没有就创建, 返回修改前的值, 用于检测是不是更新了值
d2["TYO"]="123"
d2.updateValue("222", forKey: "TYO")
d2.updateValue("222", forKey: "TYO1")
//: 删除元素 - removeValueForKey 有值就删除并返回值, 没有返回nil
d2["TYO"]=nil
d2.removeValueForKey("TYO1")
for (key, value) in d2 {
    print("\(key): \(value)")
}
```

## NSJSONSerialization

* IOS5.0 >>

用于在JSON数据类型和基础对象类型之间的转换

一个可以被转化成JSON的对象需要具备以下条件:

```
1.顶级对象需要是数组NSArray或者字典NSDictionary
2.所有对象的实例需要是NSString, NSNumber, NSArray, NSDictionary, or NSNull.
3.所有字典键值的实例必须是NSString
4.数值不能是 NaN 或者 infinity 无穷
注意：尽量使用NSJSONSerialization.isValidJSONObject先判断能否转换成功。
```

options选项的含义:

```
MutableContainers: 指定返回可变的数组或字典类型
MutableLeaves: 指定 JSON 对象中的字符串被创建为 NSMutableString 的实例。
AllowFragments: 允许JSON字符串最外层既不是NSArray也不是NSDictionary，但必须是有效的JSON Fragment。例如使用这个选项可以解析 @“123” 这样的字符串。
```

用法:

```
//: 首先判断是否可以转换
if (!NSJSONSerialization.isValidJSONObject(d2)) {
    print("is not a valid json object")
//    return
}
//: 创建一个JSON对象 方法返回的是一个data类型
var j1:NSData! = try? NSJSONSerialization.dataWithJSONObject(d2, options: [])
//: 把NSData转成JSON对象 - AllowFragments：允许JSON字符串最外层既不是NSArray也不是NSDictionary，但必须是有效的JSON Fragment。例如使用这个选项可以解析 @“123” 这样的字符串。
var json1:AnyObject! = try? NSJSONSerialization.JSONObjectWithData(j1, options: NSJSONReadingOptions.AllowFragments)
```

## NSKeyedArchiver 和 NSKeyedUnarchiver

面向对象的程序在运行的时候会创建一个复杂的对象图，经常要以二进制的方法序列化这个对象图，这个过程叫做Archiving. 二进制流可以通过网络或写入文件中

NSKeyedArchiver 和 NSKeyedUnarchiver 提供了很方便的API把对象读取/写入磁盘。

可以写入本地文件系统或者使用NSUserDefaults(每个应用程序都有自己的user preferences, 存储和检索遵循NSCoding协议的对象或者是C类型数据)

```
NScoding 是一个协议，主要有下面两个方法
-(id)initWithCoder:(NSCoder *)coder;//从coder中读取数据，保存到相应的变量中，即反序列化数据
-(void)encodeWithCoder:(NSCoder *)coder;// 读取实例变量，并把这些数据写到coder中去。序列化数据
NSCoder 是一个抽象类，抽象类不能被实例话，只能提供一些想让子类继承的方法。
NSKeyedUnarchiver   从二进制流读取对象。
NSKeyedArchiver       把对象写到二进制流中去。
```

问题:

怎样把一个自定义的类, 把成员属性保存到本地, 并存取

```
要实现对数据模型的归档，需要我们实现NScoding协议，
NScoding协议需要实现两个方法：
func encodeWithCoder(aCoder: NSCoder)
以keyValue形式对基本数据类型Encoding
init(coder aDecoder: NSCoder)
以keyValue形式对基本数据类型Decoding

// 数据模型(自定义类)

class JKContactModel : NSObject,NSCoding {
    
    var name:NSString!
    var phone:NSString!
    
    func encodeWithCoder(aCoder: NSCoder){
        aCoder.encodeObject(self.name, forKey: "name")
        aCoder.encodeObject(self.phone, forKey: "phone")
    }
    
    required init(coder aDecoder: NSCoder) {
        super.init()
        self.name = aDecoder.decodeObjectForKey("name") as? NSString
        self.phone = aDecoder.decodeObjectForKey("phone") as? NSString
    }
    
    override init() {
        
    }
    
}

// 给一个 extension 添加运行时存储属性, 通过创建getter 和 setter
// 创建一个Uint 类型用于保存 运行时属性的地址
// 要在实例化之前进行扩展
var uint1 : UInt = 0
extension JKContactModel{
    var astr:String? {
        set{
            objc_setAssociatedObject(self, &uint1, newValue, objc_AssociationPolicy.OBJC_ASSOCIATION_RETAIN_NONATOMIC)
        }
        get{
            return objc_getAssociatedObject(self, &uint1) as? String
        }
    }
    
    func abc(){
        
    }
}

let ct = JKContactModel()
ct.name = "tt"
ct.phone = "13800138000"

let ac = NSKeyedArchiver.archiveRootObject(ct, toFile: "a.txt")
let ac2 = NSKeyedUnarchiver.unarchiveObjectWithFile("a.txt")
ac2?.phone
ac2?.name

```

实现NSCoding的类不要干掉, 就算不用了

## NSXMLParser

解析XML文件需要实现NSXMLParserDelegate协议, 是线程安全的

```
let xmlfile = NSBundle.mainBundle().pathForResource("current_news_list", ofType: "xml");
let xmlParse = NSXMLParser(contentsOfURL: NSURL(fileURLWithPath: xmlfile!))
xmlParse?.delegate = self
xmlParse?.parse()

//解析XML文档开始前
func parserDidStartDocument(parser: NSXMLParser) {}

//解析XML文档结束后
func parserDidEndDocument(parser: NSXMLParser) {}

//开始解析每个XML每个元素之前,即解析开始标签元素,如开始标签<news>
func parser(parser: NSXMLParser, didStartElement elementName: String, namespaceURI: String?, qualifiedName qName: String?, attributes attributeDict: [String : String]) {}

//解析每个XML元素之后，即解析结束标签元素,如闭合标签</news>
func parser(parser: NSXMLParser, didEndElement elementName: String, namespaceURI: String?, qualifiedName qName: String?) {}

//解析XML的内容
func parser(parser: NSXMLParser, foundCharacters string: String) {}
```

```
var xmlString="<newslist><news><id>68481</id></news></newslist>"
let xmlParse=NSXMLParser(data: xmlString.dataUsingEncoding(NSUTF8StringEncoding)!)
xmlParse.parse()
```

```
class testParse : NSObject, NSXMLParserDelegate {
    
    //解析XML文档开始前
    func parserDidStartDocument(parser: NSXMLParser) {
//        print(11)
    }
    
    //解析XML文档结束后
    func parserDidEndDocument(parser: NSXMLParser) {
//        print(22)
    }
    
    //开始解析每个XML每个元素之前,即解析开始标签元素,如开始标签<news>
    func parser(parser: NSXMLParser, didStartElement elementName: String, namespaceURI: String?, qualifiedName qName: String?, attributes attributeDict: [String : String]) {}
    
    //解析每个XML元素之后，即解析结束标签元素,如闭合标签</news>
    func parser(parser: NSXMLParser, didEndElement elementName: String, namespaceURI: String?, qualifiedName qName: String?) {}
    
    //解析XML的内容
    func parser(parser: NSXMLParser, foundCharacters string: String) {}
    
}

var test=testParse()
var xmlString="<newslist><news><id>68481</id></news></newslist>"

let xmlParse=NSXMLParser(data: xmlString.dataUsingEncoding(NSUTF8StringEncoding)!)

xmlParse.delegate=test
xmlParse.parse()
注意: 如果实例一个类的对象没有声明变量来保存, 会导致野指针的情况出现
```

## NSLock

主要是用于多线程中锁住关键代码不被其他线程调用, 可以执行关键的代码

如果一个线程调用了lock方法, 其他线程就必须等待这个线程调用unlock方法之后才可以调用

## NSPipe

管道通常用在两个线程间通信或进程间通信.管道是单向通信通道.

通过管道传递的数据进行缓冲处理;缓冲区的大小是由底层操作系统决定的。NSPipe 是一个抽象的类

## NSPort

也是通信通道, 实现分布式通讯.

## NSUUID

创建 UUID 字符串，这些字符串来唯一地标识类型、 接口和其他项目。是 128 位的值.随机生成

## NSCache

NSCache是系统提供的一种类似于集合（NSMutableDictionary）的缓存，它与集合的不同如下：

1. NSCache具有自动删除的功能，以减少系统占用的内存；

2. NSCache是线程安全的，不需要加线程锁；

3. 键对象不会像 NSMutableDictionary 中那样被复制。（键不需要实现 NSCopying 协议）。

## NSNotification

一个通知对象包含 name , object , 和一个optional dictionary

name - 是一个通知的标识符
object - 是通知发送者, 通常是发送通知的这个对象
dictionary - 储存其他相关的对象, 也可以没有

NSNotification 是一个不可改变的对象, 没有实例变量,实现子类的时候不要调用`[super init]`. NSNotification不能直接实例化, 他的init方法会导致程序奔溃

可以通过`notificationWithName:object:`和`notificationWithName:object:userInfo:`方法来创建一个通知对象, 但是通常情况下是不需要自己去创建的, NSNotificationCenter提供了`postNotificationName:object:`和`postNotificationName:object:userInfo:`让你直接广播通知而不用先创建.

NSNotification 采用的是指针比较的方式, 不能进行跨进程的通讯.

额外的数据信息是需要通知发送者和观察者协商.

## NSNotificationCenter

NSNotificationCenter简单来说就是通知中心,提供了一种机制可以在程序内进行通知的广播.本质是一个通知调度表.

一个对象可以通过 `addObserver:selector:name:object:`和 `addObserverForName:object:queue:usingBlock:` 方法来在通知中心注册一个用于接收 NSNotification 对象.

每一个程序都有一个自己的通知中心，即NSNotificationCenter对象。该对象采用单例设计模式，采用defaultCenter方法就可以获得唯一的NSNotificationCenter对象。

KVO - 实现多对多
Delegate - 实现一对一(安全, weak 不存在实例的时候是nil,做API,当实现一对多的时候设置delegates,数组中是strong,一般不建议)
Notification - 实现一对多


