# UIKit框架

UIKitk框架提供一系列的Class(类)来建立和管理iPhone OS应用程序的用户界面( UI )接口、应用程序对象、事件控制、绘图模型、窗口、视图和用于控制触摸屏等的接口。

## [UIResponder](http://ju.outofmemory.cn/entry/119644)

他的子类主要有 `UIApplication` , `UIView` , `UIViewController`

UIResponder 这个类定义了很多用来响应事件处理的接口

事件通常分为两种 : 触摸(touch)事件 和 运动(motion)事件, IOS9.0之后还有 按压(press)事件

```
触摸(touch)事件:
touchesBegan:withEvent:
touchesMoved:withEvent:
touchesEnded:withEvent:
touchesCancelled:withEvent:
```

用户手指接触屏幕或者离开屏幕会产生一个 `UIEvent` 对象, 包含 `UITouch`对象

```
运动(motion)事件
motionBegan:withEvent:
motionEnded:withEvent:
motionCancelled:withEvent:
```

`canPerformAction:withSender:` 方法可以启用或者禁止用户界面事件

`NSUndoManager` 可以很方便的完成撤销操作。NSUndoManager会记录下修改、撤销操作的消息

`remoteControlReceivedWithEvent:` 远程控制事件

```
按压(press)事件
pressesBegan:withEvent:
pressesCancelled:withEvent:
pressesEnded:withEvent:

```

### Responder Chain

responder chain是由一系列responder对象连接起来的，他从第一个responder对象开始一直到application对象结束。如果第一个responder不能够处理该事件则该事件会被发送到下一个在该responder chain中的responder来处理。

创建时间:

响应链是在构建视图层次结构时生成的。

确定hit-test视图:

当用户触发某一事件(触摸事件或运动事件)后，UIKit会创建一个事件对象(UIEvent)，该对象包含一些处理事件所需要的信息。然后事件对象被放到一个事件队列中。这些事件按照先进先出的顺序来处理。当处理事件时，程序的UIApplication对象会从队列头部取出一个事件对象，将其分发出去。通常首先是将事件分发给程序的主window对象，对于触摸事件来讲，window对象会首先尝试将事件分发给触摸事件发生的那个视图上。这一视图通常被称为hit-test视图，而查找这一视图的过程就叫做hit-testing。

系统使用hit-testing来找到触摸下的视图，它检测一个触摸事件是否发生在相应视图对象的边界之内(即视图的frame属性，这也是为什么子视图如果在父视图的frame之外时，是无法响应事件的)。如果在，则会递归检测其所有的子视图。包含触摸点的视图层次架构中最底层的视图就是hit-test视图。在检测出hit-test视图后，系统就将事件发送给这个视图来进行处理。

hit-test视图可以最先去处理触摸事件，如果hit-test视图不能处理事件，则事件会沿着响应链往上传递，直到找到能处理它的视图。

事件传递:

最有机会处理事件的对象是hit-test视图或第一响应者。如果这两者都不能处理事件，UIKit就会将事件传递到响应链中的下一个响应者。每一个响应者确定其是否要处理事件或者是通过nextResponder方法将其传递给下一个响应者。这一过程一直持续到找到能处理事件的响应者对象或者最终没有找到响应者。

当自己定义的一个类想让他成为first responder时需要做两件事：

1.重写 canBecomeFirstResponder 方法让他返回YES

2.接受 becomeFirstResponder 消息，如果必要的话可让对象给自己发送该消息。

在这里有一个地方需要注意，当把一个对象变为first responder是要确保这个对象的图形界面已经建立起来，也就是说要在viewDidAppear中调用becomeFirstResponder方法。如果在veiwWillAppear方法中调用becomeFirstResponder将会得到一个NO。

输入视图有两种，一个是inputView，另一个是inputAccessoryView

禁用命令
```
- (id)targetForAction:(SEL)action withSender:(id)sender
{
    UIMenuController *menuController = [UIMenuController sharedMenuController];
    if (action == @selector(selectAll:) || action == @selector(paste:) ||action == @selector(copy:) || action == @selector(cut:)) {
        if (menuController) {
            [UIMenuController sharedMenuController].menuVisible = NO;
        }
        return nil;
    }
    return [super targetForAction:action withSender:sender];
}
```

## UIView

UIWindow自身是没有可见内容的, 所有至少有一个UIView来承载展示的内容.UIView用来管理app的视图内容,每个view都是管理着一个矩形的区域.响应绘制和多点触控事件,管理subviews.

```
绘制的方式:
1.Core Graphics
2.OpenGL ES
3.UIKit
```

CALayer就是图层，图层的功能自然就有渲染图片， 播放动画的功能。每当创建一个UIView的时候，系统会自动的创建一个CALayer，但是这个CALayer对象你不能改变，只能修改某些属性。所以通过修改CALayer，不仅可以修饰UIView的外观，还可以给UIView添加各种动画。CALayer属于CoreAnimation框架中的类

一般情况下, superview不会裁剪subview的可视区域, 可以用 `clipsToBounds` 来设置.

```
几何相关的属性:
frame - 定义位置和尺寸
bounds - view内部的尺寸
center - 改变view的位置但是不改变大小
```

```
旋转中心:
anchor,锚点, 默认是0.5 0.5
```

```
UIViewContentMode:
ScaleToFill - 拉伸铺满填充(等价于bgsize:100%)
ScaleAspectFit - 等比缩放至有一边能充满区域(等价于bgsize:contain)
ScaleAspectFill - 等比缩放至填满区域(等价于bgsize:cover)
Redraw - 改变`bounds`的时候调用`setNeedsDisplay`
```

在一个UIView对象中有以下的动画化属性：

```
frame - 你可以使用这个来动画的改变视图的尺寸和位置
bounds - 使用这个可以动画的改变视图的尺寸
center － 使用这个可以动画的改变视图的位置
transform － 使用这个可以翻转或者放缩视图
alpha － 使用这个可以改变视图的透明度
backgroundColor － 使用这个可以改变视图的背景颜色
contentStretch - 使用这个可以改变视图内容如何拉伸
```

常用的方法:

```
addSubview(_ view: UIView)
添加一个子视图到接收者并让它在最上面显示出来。 
bringSubviewToFront(_ view: UIView)
把指定的子视图移动到顶层
sendSubviewToBack(_ view: UIView)
把指定的子视图移动到最底层
removeFromSuperview()
把视图从父视图中移除
insertSubview(_ view: UIView, atIndex index: Int)
在指定的视图层级索引位置插入一个视图
insertSubview(_ view: UIView, aboveSubview siblingSubview: UIView)
在另外一个视图上面插入一个视图
insertSubview(_ view: UIView, belowSubview siblingSubview: UIView)
在另外一个视图下面插入一个视图
exchangeSubviewAtIndex(_ index1: Int, withSubviewAtIndex index2: Int)
交换两个视图层级索引的位置所对应的视图

convertPoint(_ point: CGPoint, toView view: UIView?) -> CGPoint
转换一个点从接收者坐标系到给定的视图坐标系 
convertPoint(_ point: CGPoint, fromView view: UIView?) -> CGPoint
把一个点从一个坐标系转换到接收者的坐标系 
convertRect(_ rect: CGRect, toView view: UIView?) -> CGRect
转换接收者坐标系中的矩形到其他视图
convertRect(_ rect: CGRect, fromView view: UIView?) -> CGRect
转换一个矩形从其他视图坐标系到接收者坐标系。

didAddSubview(_ subview: UIView)
告诉视图当子视图已经添加
isDescendantOfView(_ view: UIView) -> Bool
返回一个布尔值指出接收者是否是给定视图的子视图或者指向那个视图
```

可以单独设置手势的单独识别和同时识别

疑问:实现头像圆角:

```

```

## UILabel

继承 UIView

UILabel 实现了一个只读的文本view, 可以用于单行或者多行文本.

简单用法:

```
//创建UILabel
        let label:UILabel = UILabel(frame: CGRectMake(50.0, 20.0, 200.0, 50.0))
        //设置字体相关属性
        label.text="label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1" //设置文本内容
        label.font = UIFont(name:"Zapfino", size:20) //设置字体属性, 包括字体的name和size
        label.textColor=UIColor.blueColor() //设置字体颜色
        label.textAlignment=NSTextAlignment.Right //设置字体对齐方式 , 一共有5种 Left,Center,Right,Justified,Natural
        label.shadowColor=UIColor.grayColor() //设置字体阴影颜色
        label.shadowOffset=CGSizeMake(-5,5) //设置字体阴影的偏移值
        label.enabled = true //设置文本内容是否可以改变, 默认为true
        //设置背景颜色
        label.backgroundColor=UIColor.blackColor()
        //设置文本高亮
        label.highlighted = true
        //设置文本高亮颜色
        label.highlightedTextColor = UIColor.greenColor()
        //设置文字截断处理
        label.allowsDefaultTighteningForTruncation=false //设置在截断之前是否要收紧字体间距, 默认是false
        label.lineBreakMode=NSLineBreakMode.ByTruncatingTail  //隐藏尾部并显示省略号
        label.lineBreakMode=NSLineBreakMode.ByTruncatingMiddle  //隐藏中间部分并显示省略号
        label.lineBreakMode=NSLineBreakMode.ByTruncatingHead  //隐藏头部并显示省略号
        label.lineBreakMode=NSLineBreakMode.ByClipping  //截去多余部分也不显示省略号
        label.lineBreakMode=NSLineBreakMode.ByWordWrapping  //单词截断
        label.lineBreakMode=NSLineBreakMode.ByCharWrapping  //字符截断
        label.adjustsFontSizeToFitWidth=true //当文字超出标签宽度时，自动调整文字大小，使其不被截断
        label.minimumScaleFactor = CGFloat(0.5) //设置字体最小的缩放比例, 默认值为0
        //设置文字缩放基线,只有在单行文本的时候才有用, 也就是numberOfLines = 1
        label.baselineAdjustment = UIBaselineAdjustment.AlignBaselines //基于基线对齐
        label.baselineAdjustment = UIBaselineAdjustment.AlignCenters //基于盒子高度垂直居中
        label.baselineAdjustment = UIBaselineAdjustment.None //基于盒子左上角,默认值
        //设置多行显示
        label.numberOfLines=2  //显示两行文字（默认只显示一行，设为0表示没有行数限制）
        label.preferredMaxLayoutWidth = CGFloat(200.0) //换行宽度
        //富文本设置
        let attributeString = NSMutableAttributedString(string:"welcome to hangge.com")
        //从文本0开始6个字符字体HelveticaNeue-Bold,16号
        attributeString.addAttribute(NSFontAttributeName, value: UIFont(name: "HelveticaNeue-Bold", size: 16)!, range: NSMakeRange(0,6))
        //设置字体颜色
        attributeString.addAttribute(NSForegroundColorAttributeName, value: UIColor.blueColor(), range: NSMakeRange(0, 3))
        //设置文字背景颜色
        attributeString.addAttribute(NSBackgroundColorAttributeName, value: UIColor.greenColor(), range: NSMakeRange(3,3))
        label.attributedText = attributeString
        //设置是否允许跟用户交互, 如果不允许就在事件队列中移除
        label.userInteractionEnabled = true
        
        //设置边框
        label.layer.borderWidth = 2
        label.layer.borderColor = UIColor.redColor().CGColor
        //设置圆角
        label.layer.cornerRadius = 20
        //切掉超出layer的的区域
        label.clipsToBounds = true
        
        //让label拥有点击事件
        label.multipleTouchEnabled = true
        let tap:UITapGestureRecognizer = UITapGestureRecognizer(target: self, action: "click:")
        label.addGestureRecognizer(tap)
        
        //添加到父view中
        self.view.addSubview(label)
        
        //多行文字截断
        let label2:UILabel = UILabel(frame: CGRectMake(50.0, 120.0, 200.0, 50.0))
        label2.numberOfLines=2
        label2.text="label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1"
        self.view.addSubview(label2)
        
        //添加动画效果
        UIView.animateWithDuration(5, animations: { () -> Void in
            
            label2.alpha = 0.7
            label2.alpha = 0
            }) { (Bool) -> Void in
                label2.removeFromSuperview()
        }
        
        //高度自适应
//        let label3:UILabel = UILabel(frame: CGRectMake(50.0, 220.0, 200.0, 50.0))
        self.view.addSubview(label3)
        //先设置numberOfLines=0
        label3.numberOfLines=0
        //设置字符换行模式
        label.lineBreakMode=NSLineBreakMode.ByWordWrapping
        //设置文字
        label3.text="label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1label 1"
        label3.font = UIFont(name:"Zapfino", size:20)
        //计算实际大小
        label3.sizeToFit()
        //设置大小上限,重置大小
        label3.frame=CGRectMake(50.0, 220.0, 200.0, 250.0)
        
        //让Label显示html标签
        let label4:UILabel = UILabel(frame: CGRect(x: 50, y: 470, width: 300, height: 35))
        let html = "this is html <a href=\"http://www.baidu.com\">link</a>"
        var atext:NSAttributedString
        if let data = html.dataUsingEncoding(NSUTF8StringEncoding, allowLossyConversion: false) {
            do {
                atext = try NSAttributedString(data: data, options: [NSDocumentTypeDocumentAttribute: NSHTMLTextDocumentType], documentAttributes: nil)
                label4.attributedText = atext
            }
            catch _ {}
        }
        label4.multipleTouchEnabled = true
        self.view.addSubview(label4)
```

action:需要传入事件

```
func click(sender:AnyObject){
        var tap:UITapGestureRecognizer = sender as! UITapGestureRecognizer
        label3.text="label1被点击了"
        NSLog("this is click")
    }
```

默认的内容风格是 `UIViewContentModeRedraw`, 默认忽略用户事件,裁剪子视图, 要监听事件需要在初始化之后设置`userInteractionEnabled`为`true`, 要允许超出区域可视要设置`clipsToBounds`为`false`, 设置多行文本`numberOfLines`

有一些属性是不能通过属性检查器, 必须用代码的方式去设置.

```
Text Appearance(文本外观) - attributed可以单独设置每个字符的样式
numberOfLines(多行文本) - 会导致区域大小固定, 自适应应该设置0
adjustsFontSizeToFitWidth - false 不改变字体大小
adjustsLetterSpacingToFitWidth - 字体间距
minimumScaleFactor - 最小缩放比例
minimumFontSize - 最小字号

lineBreakMode(截断模式) - 设置了之后不要设置 adjustsFontSizeToFitWidth 和 adjustsLetterSpacingToFitWidth
UILineBreakModeWordWrap = 0,
以单词为单位换行，以单位为单位截断。
UILineBreakModeCharacterWrap,
以字符为单位换行，以字符为单位截断。
UILineBreakModeClip,
以单词为单位换行。以字符为单位截断。
UILineBreakModeHeadTruncation,
以单词为单位换行。如果是单行，则开始部分有省略号。如果是多行，则中间有省略号，省略号后面有4个字符。
UILineBreakModeTailTruncation,
以单词为单位换行。无论是单行还是多行，都是末尾有省略号。
UILineBreakModeMiddleTruncation,
以单词为单位换行。无论是单行还是多行，都是中间有省略号，省略号后面只有2个字符。
```

富文本可以配置的项:

```
// NSFontAttributeName 设置字体属性，默认值：字体：Helvetica(Neue) 字号：12 
// NSForegroundColorAttributeNam 设置字体颜色，取值为 UIColor对象，默认值为黑色 
// NSBackgroundColorAttributeName 设置字体所在区域背景颜色，取值为 UIColor对象，默认值为nil, 透明色
// NSLigatureAttributeName 设置连体属性，取值为NSNumber 对象(整数)，0 表示没有连体字符，1 表示使用默认的连体字符 
// NSKernAttributeName 设定字符间距，取值为 NSNumber 对象（整数），正值间距加宽，负值间距变窄 
// NSStrikethroughStyleAttributeName 设置删除线，取值为 NSNumber 对象（整数） 
// NSStrikethroughColorAttributeName 设置删除线颜色，取值为 UIColor 对象，默认值为黑色 
// NSUnderlineStyleAttributeName 设置下划线，取值为 NSNumber 对象（整数），枚举常量 NSUnderlineStyle中的值，与删除线类似 
// NSUnderlineColorAttributeName 设置下划线颜色，取值为 UIColor 对象，默认值为黑色 
// NSStrokeWidthAttributeName 设置笔画宽度，取值为 NSNumber 对象（整数），负值填充效果，正值中空效果 
// NSStrokeColorAttributeName 填充部分颜色，不是字体颜色，取值为 UIColor 对象 
// NSShadowAttributeName 设置阴影属性，取值为 NSShadow 对象 
// NSTextEffectAttributeName 设置文本特殊效果，取值为 NSString 对象，目前只有图版印刷效果可用： 
// NSBaselineOffsetAttributeName 设置基线偏移值，取值为 NSNumber （float）,正值上偏，负值下偏 
// NSObliquenessAttributeName 设置字形倾斜度，取值为 NSNumber （float）,正值右倾，负值左倾 
// NSExpansionAttributeName 设置文本横向拉伸属性，取值为 NSNumber （float）,正值横向拉伸文本，负值横向压缩文本 
// NSWritingDirectionAttributeName 设置文字书写方向，从左向右书写或者从右向左书写 
// NSVerticalGlyphFormAttributeName 设置文字排版方向，取值为 NSNumber 对象(整数)，0 表示横排文本，1 表示竖排文本 
// NSLinkAttributeName 设置链接属性，点击后调用浏览器打开指定URL地址 
// NSAttachmentAttributeName 设置文本附件,取值为NSTextAttachment对象,常用于文字图片混排 
// NSParagraphStyleAttributeName 设置文本段落排版格式，取值为 NSParagraphStyle 对象　
```

## UITextView

UIResponder , UIView , UIScrollView(和我猜想的一样, 能滚动的东西一定继承了UIScrollView , 应该梳理出基本功能的几个类, 其他具体类的实现都是继承这些基础类的)

用来实现一个多行的并且可以滚动的文本区域.还可以被编辑.ios6开始支持富文本(attributedText).

当你点击一个可以编辑的UITextView时, 这个UITextView会成为第一响应者并调起一个关联的键盘.隐藏键盘需要发送`resignFirstResponder`通知给到当前响应的UITextView并结束编辑状态.

如果要自定义键盘的外观和行为, 需要实现`UITextInputTraits`协议, 设置如(ASCII, Numbers, URL, Email, and others) 样式, 或者是否需要大小写等等.

当唤起键盘的时候会发送通知事件:

```
UIKeyboardWillShowNotification
UIKeyboardDidShowNotification
UIKeyboardWillHideNotification
UIKeyboardDidHideNotification
```

只有监听这些事件才可以获取键盘相关的信息, 如大小, 可以用来作为改变视图的计算依据.

与UILabelView的不同:

```
属性不同:
editable, 是否允许编辑,默认为true
allowsEditingTextAttributes, 是否允许用户编辑文本样式, 默认false
dataDetectorTypes: UIDataDetectorTypes, 用来声明文本中一些特殊字符的处理方式(如数字, 链接, 邮箱等等)

textview.dataDetectorTypes = UIDataDetectorTypes.None //都不加链接
textview.dataDetectorTypes = UIDataDetectorTypes.PhoneNumber //只有电话加链接
textview.dataDetectorTypes = UIDataDetectorTypes.Link //只有网址加链接
textview.dataDetectorTypes = UIDataDetectorTypes.All //电话和网址都加

typingAttributes: [String : AnyObject], 用来设置用户新输入的富文本样式
linkTextAttributes: [String : AnyObject]!, 用来设置链接的外观, 默认是蓝色字体和下划线
textContainerInset: UIEdgeInsets, 用来设置显示内容的padding
用法:textView.textContainerInset = UIEdgeInsetsMake(0, 10, 0, 10);
效果是右侧的滚动条距离内容10像素
selectedRange: NSRange, 设置点击编辑的时候光标位置
clearsOnInsertion, 设置是否
selectable, 设置是否允许选择
inputView: UIView?, 默认为nil,即系统标准键盘
inputAccessoryView: UIView?, 默认为nil, 在键盘视图上方设置自定义的视图,例如实现微信聊天输入法的工具条

常用方法:
scrollRangeToVisible(_ range: NSRange), 设置要滚动到展示的文本,可以用来设置输入时跳到最后一行
becomeFirstResponder, 设置成为第一响应者, 就跟默认focus一样.
resignFirstResponder, 隐藏键盘

事件通知:
UITextViewTextDidBeginEditingNotification
UITextViewTextDidChangeNotification
UITextViewTextDidEndEditingNotification

关于换行:
从XML或者json中读取出来的"\n"，系统认为是字符串，会默认转换为"\\n"，所以当显示的时候就是字符串了，要想显示换行，需要自己手动将"\\n"转换为"\n"，这样才能换行.
```

简单用法:

```
class ViewController: UIViewController, UITextViewDelegate{
    
    private var mycontext = 0
    let t2 = UITextView()
    let placeholderLabel = UILabel()
    let textField = UITextField()
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
        let text = UITextView(frame: CGRectMake(50.0, 20.0, 200.0, 200.0), textContainer: nil)
        text.text = "http://a.b.c,这是一\n个UITextView, 后面 +8602980000000 还有很多很多很多很多很多很多很多很多很多很多很多很多很多很多很多很多很多很多"
        text.textColor = UIColor.whiteColor()
        text.textAlignment = NSTextAlignment.Center
        text.font = UIFont(name:"Heiti TC", size:20)
        text.frame = CGRectMake(50.0, 20.0, 200.0, 200.0)
        text.backgroundColor = UIColor.grayColor()
        // 设置输入的时候是否清楚之前的内容
        text.clearsOnInsertion = true
        // 设置是否允许选择
        text.selectable = true
        // 设置成为第一响应者
        text.becomeFirstResponder()
        // 设置选中的区域
        text.selectedRange = NSRange(location: 0,length: 0)
        // 设置滚动到需要展示文本的地方
        text.scrollRangeToVisible(NSRange(location: text.text.characters.count ,length: 1))
        // 设置滚动不要每次都重头开始
        text.layoutManager.allowsNonContiguousLayout = false
        
        // 设置链接的处理方式
        // 需要设置不允许编辑
        text.editable = false
        text.dataDetectorTypes = UIDataDetectorTypes.None //都不加链接
        text.dataDetectorTypes = UIDataDetectorTypes.PhoneNumber //只有电话加链接
        text.dataDetectorTypes = UIDataDetectorTypes.Link //只有网址加链接
        text.dataDetectorTypes = UIDataDetectorTypes.CalendarEvent //只有日历
        text.dataDetectorTypes = UIDataDetectorTypes.All //电话和网址都加
        
        // 设置选择菜单
        let mail = UIMenuItem(title: "邮件", action: "onMail")
        let weixin = UIMenuItem(title: "微信", action: "onWeiXin")
        let menu = UIMenuController()
        menu.menuItems = [mail,weixin]
        
        // 字符串替换换行符
        var aText = "asdada\nasdasdads"
        aText = aText.stringByReplacingOccurrencesOfString("\\n", withString: "\n")
        
        // 注册事件通知
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "onUITextViewTextDidBeginEditing:", name: UITextViewTextDidBeginEditingNotification, object: nil);
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "onUITextViewTextDidChange:", name: UITextViewTextDidChangeNotification, object: nil);
        NSNotificationCenter.defaultCenter().addObserver(self, selector: "onUITextViewTextDidEndEditing:", name: UITextViewTextDidEndEditingNotification, object: nil);
        
        // 实现垂直居中的文本显示
        t2.text = "这是一个demo"
        t2.text = ""
        t2.textColor = UIColor.blackColor()
        t2.textAlignment = NSTextAlignment.Center
        t2.font = UIFont(name:"Heiti TC", size:20)
        t2.frame = CGRectMake(50.0, 220.0, 200.0, 100.0)
        t2.backgroundColor = UIColor.purpleColor()
        // 实现 UITextViewDelegate 协议
        t2.delegate = self
        // KVO实现监听内容大小的变化
        t2.addObserver(self, forKeyPath: "contentSize", options: .New, context: &mycontext)
        
        // 实现placeholder
        placeholderLabel.frame = t2.frame
        placeholderLabel.text = "这是一个placeholderLabel"
        placeholderLabel.textAlignment = NSTextAlignment.Center
        placeholderLabel.lineBreakMode = NSLineBreakMode.ByCharWrapping
        placeholderLabel.alpha = 0.0

        self.view.addSubview(text);
        self.view.addSubview(t2);
        self.view.addSubview(placeholderLabel);
        
        // let textField = UITextField()
        textField.frame = CGRectMake(50.0, 350.0, 200.0, 50.0)
        // 设置文本框的类型
        // UITextBorderStyle.None - 无边框
        // UITextBorderStyle.Line - 直线边框
        // UITextBorderStyle.RoundedRect - 圆角矩形边框
        // UITextBorderStyle.Bezel - 边线+阴影
        textField.borderStyle = UITextBorderStyle.RoundedRect
        // 设置placeholder
        textField.placeholder = "请输入用户名"
        //当文字超出文本框宽度时，自动调整文字大小
        textField.adjustsFontSizeToFitWidth = true
        //最小可缩小的字号
        textField.minimumFontSize = 14
        /** 水平对齐 **/
        textField.textAlignment = .Right //水平右对齐
        textField.textAlignment = .Center //水平居中对齐
        textField.textAlignment = .Left //水平左对齐
        /** 垂直对齐 **/
        textField.contentVerticalAlignment = .Top  //垂直向上对齐
        textField.contentVerticalAlignment = .Center  //垂直居中对齐
        textField.contentVerticalAlignment = .Bottom  //垂直向下对齐
        // 背景图片设置,先要去除边框样式
        // textField.borderStyle = .None
        // textField.background=UIImage(named:"background1")
        // 设置清除按钮的模式
        textField.clearButtonMode=UITextFieldViewMode.WhileEditing  //编辑时出现清除按钮
        textField.clearButtonMode=UITextFieldViewMode.UnlessEditing  //编辑时不出现，编辑后才出现清除按钮
        textField.clearButtonMode=UITextFieldViewMode.Always  //一直显示清除按钮
        // 设置文本框关联的键盘类型
        // Default - 系统默认的虚拟键盘
        // ASCII Capable - 显示英文字母的虚拟键盘
        // Numbers and Punctuation - 显示数字和标点的虚拟键盘
        // URL - 显示便于输入数字的虚拟键盘
        // Number Pad - 显示便于输入数字的虚拟键盘
        // Phone Pad - 显示便于拨号呼叫的虚拟键盘
        // Name Phone Pad - 显示便于聊天拨号的虚拟键盘
        // Email Address - 显示便于输入Email的虚拟键盘
        // Decimal Pad - 显示用于输入数字和小数点的虚拟键盘
        // Twitter - 显示方便些Twitter的虚拟键盘
        // Web Search - 显示便于在网页上书写的虚拟键盘
        textField.keyboardType = UIKeyboardType.NumberPad
        // 设置键盘return键的样式
        textField.returnKeyType = UIReturnKeyType.Done //表示完成输入
        textField.returnKeyType = UIReturnKeyType.Go //表示完成输入，同时会跳到另一页
        textField.returnKeyType = UIReturnKeyType.Search //表示搜索
        textField.returnKeyType = UIReturnKeyType.Join //表示注册用户或添加数据
        textField.returnKeyType = UIReturnKeyType.Next //表示继续下一步
        textField.returnKeyType = UIReturnKeyType.Send //表示发送
        // 键盘return键的响应 , 需要实现 UITextFieldDelegate
        self.view.addSubview(textField)
        
        let button = UIButton(frame:CGRectMake(10, 450, 100, 30))
        button.setTitle("普通状态", forState:UIControlState.Normal) //普通状态下的文字
        button.setTitle("触摸状态", forState:UIControlState.Highlighted) //触摸状态下的文字
        button.setTitle("禁用状态", forState:UIControlState.Disabled) //禁用状态下的文字
        
        button.setTitleColor(UIColor.whiteColor(),forState: .Normal) //普通状态下文字的颜色
        button.setTitleColor(UIColor.greenColor(),forState: .Highlighted) //触摸状态下文字的颜色
        button.setTitleColor(UIColor.grayColor(),forState: .Disabled) //禁用状态下文字的颜色
        
        button.setTitleShadowColor(UIColor.greenColor(),forState:.Normal) //普通状态下文字阴影的颜色
        button.setTitleShadowColor(UIColor.yellowColor(),forState:.Highlighted) //普通状态下文字阴影的颜色
        button.setTitleShadowColor(UIColor.grayColor(),forState:.Disabled) //普通状态下文字阴影的颜色
        
        button.backgroundColor=UIColor.blackColor()
        
        button.addTarget(self,action:Selector("tapped"),forControlEvents:.TouchUpInside)
        self.view.addSubview(button)
        
        // 监听键盘改变通知
        NSNotificationCenter.defaultCenter().addObserver(self,
            selector: "keyboardWillChange:",
            name: UIKeyboardWillChangeFrameNotification, object: nil)

    }
    
    func tapped(){
        print("tapped")
        var sb = UIStoryboard(name: "Main", bundle: nil)
        let anotherView = sb.instantiateViewControllerWithIdentifier("tableViewController") as! tableViewController
        self.presentViewController(anotherView, animated: true, completion: nil)
    }
    
    override func viewDidLayoutSubviews(){
        if(t2.text.characters.count <= 0){
            placeholderLabel.alpha = 1.0
        }
    }
    
    // 键盘改变
    func keyboardWillChange(notification: NSNotification) {
        if let userInfo = notification.userInfo,
            value = userInfo[UIKeyboardFrameEndUserInfoKey] as? NSValue,
            duration = userInfo[UIKeyboardAnimationDurationUserInfoKey] as? Double,
            curve = userInfo[UIKeyboardAnimationCurveUserInfoKey] as? UInt {
                let frame = value.CGRectValue()
                print(frame)
                let intersection = CGRectIntersection(frame, self.view.frame)
                print(intersection)
                textField.frame = CGRectMake(50.0, frame.origin.y - 50.0, 200.0, 50.0)
                //self.view.setNeedsLayout()
                //改变下约束
//                self.bottomConstraint.constant = CGRectGetHeight(intersection)
                UIView.animateWithDuration(duration, delay: 0.0,options: UIViewAnimationOptions(rawValue: curve), animations: {
                    _ in
                    self.view.layoutIfNeeded()
                }, completion: nil)
        }
    }
    
    // 实现了UITextViewDelegate协议的时候, 事件处理block
    func textViewDidBeginEditing(textView: UITextView){
        print(111)
    }
    func textViewDidChange(textView: UITextView){
        if(t2.text.characters.count <= 0){
            placeholderLabel.alpha = 1.0
        }else{
            placeholderLabel.alpha = 0.0
        }
    }
    
    // 重写 观察者 处理block
    override func observeValueForKeyPath(keyPath: String?, ofObject object: AnyObject?, change: [String : AnyObject]?, context: UnsafeMutablePointer<Void>){
        print(context)
        if((object?.isEqual(t2)) != nil){
            print(1)
        }
        if(context == &mycontext){
            print("Changed to:\(change![NSKeyValueChangeNewKey]!)")
            let tempView = object
            var topCorrect = ((tempView?.bounds.size.height)! - (tempView?.contentSize.height)! * (tempView?.zoomScale)!) / 2.0
            topCorrect = ( topCorrect < 0.0 ? 0.0 : topCorrect )
            tempView?.setContentOffset(CGPoint(x: 0, y: -topCorrect), animated: true)
        }
    }
    
    override func touchesBegan(touches: Set<UITouch>, withEvent event: UIEvent?) {
        for touch: AnyObject in touches {
            let t:UITouch = touch as! UITouch
            // 当在屏幕上连续拍动两下时，背景恢复为白色
            if(t.tapCount == 2)
            {
                self.view.backgroundColor = UIColor.whiteColor()
            }
            // 当在屏幕上单击时，屏幕变为红色, 在屏幕点击的时候,收起输入法键盘
            else if(t.tapCount == 1)
            {
                self.view.backgroundColor = UIColor.redColor()
                textField.resignFirstResponder()
            }
            print("event begin!")
        }
    }

    override func didReceiveMemoryWarning() {
        super.didReceiveMemoryWarning()
        // Dispose of any resources that can be recreated.
    }
    
    deinit {
        NSNotificationCenter.defaultCenter().removeObserver(self);
        t2.removeObserver(self,forKeyPath:"contentSize")
    }
    
    func onMail(){
        print("mail")
    }
    
    func onWeiXin(){
        print("weixin")
    }
    
    func onUITextViewTextDidBeginEditing(sender:AnyObject){
        print("began")
    }

    func onUITextViewTextDidChange(sender:AnyObject){
        print("change")
    }

    func onUITextViewTextDidEndEditing(sender:AnyObject){
        print("end")
    }

}
```

UITextView 不支持 placeholder , 可以借助label实现, 类似在IE实现一样

## UITextField

文本输入框（UITextField）是一个带一个事件按钮的可编辑文本输入框.可以用于像搜索, 聊天等场景.

与UITextView的不同:

```
属性不同:
placeholder, 直接提供placeholder功能
borderStyle, 边框样式属性
adjustsFontSizeToFitWidth, 是否缩放字体适应宽度
minimumFontSize: CGFloat , 最小缩放比例
contentVerticalAlignment, 垂直对齐方式
background: UIImage? , 背景图片设置,先要去除边框样式
clearButtonMode: UITextFieldViewMode , 清除按钮的模式
keyboardType: UIKeyboardType , 键盘的类型
returnKeyType: UIReturnKeyType , 返回按钮的类型
```

简单用法:

```
        let textField = UITextField()
        textField.frame = CGRectMake(50.0, 350.0, 200.0, 50.0)
        // 设置文本框的类型
        // UITextBorderStyle.None - 无边框
        // UITextBorderStyle.Line - 直线边框
        // UITextBorderStyle.RoundedRect - 圆角矩形边框
        // UITextBorderStyle.Bezel - 边线+阴影
        textField.borderStyle = UITextBorderStyle.RoundedRect
        // 设置placeholder
        textField.placeholder = "请输入用户名"
        //当文字超出文本框宽度时，自动调整文字大小
        textField.adjustsFontSizeToFitWidth = true
        //最小可缩小的字号
        textField.minimumFontSize = 14
        /** 水平对齐 **/
        textField.textAlignment = .Right //水平右对齐
        textField.textAlignment = .Center //水平居中对齐
        textField.textAlignment = .Left //水平左对齐
        /** 垂直对齐 **/
        textField.contentVerticalAlignment = .Top  //垂直向上对齐
        textField.contentVerticalAlignment = .Center  //垂直居中对齐
        textField.contentVerticalAlignment = .Bottom  //垂直向下对齐
        // 背景图片设置,先要去除边框样式
        // textField.borderStyle = .None
        // textField.background=UIImage(named:"background1")
        // 设置清除按钮的模式
        textField.clearButtonMode=UITextFieldViewMode.WhileEditing  //编辑时出现清除按钮
        textField.clearButtonMode=UITextFieldViewMode.UnlessEditing  //编辑时不出现，编辑后才出现清除按钮
        textField.clearButtonMode=UITextFieldViewMode.Always  //一直显示清除按钮
        // 设置文本框关联的键盘类型
        // Default - 系统默认的虚拟键盘
        // ASCII Capable - 显示英文字母的虚拟键盘
        // Numbers and Punctuation - 显示数字和标点的虚拟键盘
        // URL - 显示便于输入数字的虚拟键盘
        // Number Pad - 显示便于输入数字的虚拟键盘
        // Phone Pad - 显示便于拨号呼叫的虚拟键盘
        // Name Phone Pad - 显示便于聊天拨号的虚拟键盘
        // Email Address - 显示便于输入Email的虚拟键盘
        // Decimal Pad - 显示用于输入数字和小数点的虚拟键盘
        // Twitter - 显示方便些Twitter的虚拟键盘
        // Web Search - 显示便于在网页上书写的虚拟键盘
        textField.keyboardType = UIKeyboardType.NumberPad
        // 设置键盘return键的样式
        textField.returnKeyType = UIReturnKeyType.Done //表示完成输入
        textField.returnKeyType = UIReturnKeyType.Go //表示完成输入，同时会跳到另一页
        textField.returnKeyType = UIReturnKeyType.Search //表示搜索
        textField.returnKeyType = UIReturnKeyType.Join //表示注册用户或添加数据
        textField.returnKeyType = UIReturnKeyType.Next //表示继续下一步
        textField.returnKeyType = UIReturnKeyType.Send //表示发送
        // 键盘return键的响应 , 需要实现 UITextFieldDelegate
        self.view.addSubview(textField)
```

## UITableView

继承 UIScrollView , 并且只允许垂直方向的滚动

用来展示列表信息, 每个列表项是 UITableViewCell, 每个都独占一行, 并且有右边附带视图, 可以进入编辑模式, 让用户可以插入,删除,重新排列.

有两种样式 `UITableViewStylePlain` 和 `UITableViewStyleGrouped` ,在创建是一个UITableView实例的时候必须要声明其中一种.声明之后不允许修改.

UITableViewStylePlain 模式, 可以设置右边快速导航侧边栏(例如A-Z).头部和底部是在内容的上方.

UITableViewStyleGrouped 模式, 会设置一个UITableViewCell的默认背景颜色和字体颜色.可以局部设置背景. 这个模式不能建立索引.

只有在实例化的或者重新分配数据源的时候会默认执行reloadData方法.这个方法会清除视图当前的状态,包括选中的内容.如果显示的调用reloadData, 对layoutSubviews的直接或者间接调用都不会触发重新加载.

UITableView 有两个delegate , `dataSource` 和 `delegate`

dataSource是UITableViewDataSource类型，主要为UITableView提供显示用的数据(UITableViewCell)，指定UITableViewCell支持的编辑操作类型(insert，delete和reordering)，并根据用户的操作进行相应的数据更新操作，如果数据没有更具操作进行正确的更新，可能会导致显示异常，甚至crush。

delegate是UITableViewDelegate类型，主要提供一些可选的方法，用来控制tableView的选择、指定section的头和尾的显示以及协助完成cell的删除和排序等功能。

UITableView声明了一个NSIndexPath的类别，主要用来标识当前cell的在tableView中的位置，该类别有section和row两个属性，前者标识当前cell处于第几个section中，后者代表在该section中的第几行。

UITableView只能有一列数据(cell)，且只支持纵向滑动，当创建好的tablView第一次显示的时候，我们需要调用其reloadData方法，强制刷新一次，从而使tableView的数据更新到最新状态。

## UITableViewController

UITableViewController是系统提供的一个便利类，主要是为了方便我们使用UITableView，该类生成的时候就将自身设置成了其包含的tableView的dataSource和delegate，并创建了很多代理函数的框架，为我们大大的节省了时间，我们可以通过其tableView属性获取该controller内部维护的tableView对象。默认情况下使用UITableViewController创建的tableView是充满全屏的，如果需要用到tableView是不充满全屏的话，我们应该使用UIViewController自己创建和维护tableView。

UITableViewController提供一个初始化函数initWithStyle:，根据需要我们可以创建Plain或者Grouped类型的tableView，当我们使用其从UIViewController继承来的init初始化函数的时候，默认将会我们创建一个Plain类型的tableView。 

UITableViewController默认的会在viewWillAppear的时候，清空所有选中cell，我们可以通过设置self.clearsSelectionOnViewWillAppear = NO，来禁用该功能，并在viewDidAppear中调用UIScrollView的flashScrollIndicators方法让滚动条闪动一次，从而提示用户该控件是可以滑动的。
　　
创建cells

```
func registerNib(_ nib: UINib?, forCellReuseIdentifier identifier: String)
func registerClass(_ cellClass: AnyClass?, forCellReuseIdentifier identifier: String)
传入一个nib或者一个实现了cell的class, 来告诉table view应该怎样去创建一个cell, 并传入一个不为`nil`或者空字符串的值作为标识,添加到重用队列中.
使用一个相同的标识的时候, 会覆盖旧的标识.
设置第一个参数为`nil`来注销重用队列中的重用cell



``` 

## UITableViewCell

UITableView中显示的每一个单元都是一个UITableViewCell对象，看文档的话我们会发现其初始化函数initWithStyle:reuseIdentifier:比较特别，跟我们平时看到的UIView的初始化函数不同。这个主要是为了效率考虑，因为在tableView快速滑动的滑动的过程中，频繁的alloc对象是比较费时的，于是引入了cell的重用机制，这个也是我们在dataSource中要重点注意的地方，用好重用机制会让我们的tableView滑动起来更加流畅。

我们可以通过cell的selectionStyle属性指定cell选中时的显示风格，以及通过accessoryType来指定cell右边的显示的内容，或者直接指定accessoryView来定制右边显示的view。 

系统提供的UITableView也包含了四种风格的布局，分别是:

```
UITableViewCellStyle:
Default - 黑色左对齐的文本,可选的图片
Value1 - 左边是黑色左对齐的文本, 右边是蓝色右对齐小字号文本(设置列表)
Value2 - 左边是蓝色右对齐的文本, 右边是黑色左对齐小字号文本(联系人列表)
Subtitle - 带有子标题的
```

自定义cell:

```
1、直接向cell的contentView上面添加subView
这是比较简单的一种的，根据布局需要我们可以在不同的位置添加subView。但是此处需要注意：所有添加的subView都最好设置为不透明的，因为如果subView是半透明的话，view图层的叠加将会花费一定的时间，这会严重影响到效率。同时如果每个cell上面添加的subView个数过多的话(通常超过3，4个)，效率也会受到比较大的影响。

2、从UITableViewCell派生一个类
通过从UITableViewCell中派生一个类，可以更深度的定制一个cell，可以指定cell在进入edit模式的时候如何相应等等。最简单的实现方式就是将所有要绘制的内容放到一个定制的subView中，并且重载该subView的drawRect方法直接把要显示的内容绘制出来(这样可以避免subView过多导致的性能瓶颈)，最后再将该subView添加到cell派生类中的contentView中即可。但是这样定制的cell需要注意在数据改变的时候，通过手动调用该subView的setNeedDisplay方法来刷新界面
```

为了使UITableVeiew进入edit模式以后，如果该cell支持reordering的话，reordering控件就会临时的把accessaryView覆盖掉。为了显示reordering控件，我们必须将cell的showsReorderControl属性设置成YES，同时实现dataSource中的tableView:moveRowAtIndexPath:toIndexPath:方法。我们还可以同时通过实现dataSource中的 tableView:canMoveRowAtIndexPath:返回NO，来禁用某一些cell的reordering功能。

```
当我们在tableView中点击一个cell的时候，将会调用tableView的delegate中的tableView:didSelectRowAtIndexPath:方法。

　　关于tableView的cell的选中，苹果官方有以下几个建议：

 　　1、不要使用selection来表明cell的选择状态，而应该使用accessaryView中的checkMark或者自定义accessaryView来显示选中状态。 

 　　2、当选中一个cell的时候，你应该取消前一个cell的选中。 

 　　3、如果cell选中的时候，进入下一级viewCOntroller，你应该在该级菜单从navigationStack上弹出的时候，取消该cell的选中。

　　这块儿再提一点，当一个cell的accessaryType为UITableViewCellAccessoryDisclosureIndicator的时候，点击该accessary区域通常会将消息继续向下传递，即跟点击cell的其他区域一样，将会掉delegate的tableView:didSelectRowAtIndexPath:方法，当时如果accessaryView为 UITableViewCellAccessoryDetailDisclosureButton的时候，点击accessaryView将会调用delegate的 tableView:accessoryButtonTappedForRowWithIndexPath:方法。
```

# UIButton

继承 UIControl , 用于实现拦截用户点击屏幕按钮的事件.

不需要`delegate`, UIControl定义了行为.

用户点击的时候发送`UIControlEventTouchUpInside`事件,

```
UIButtonType:
Custom - 定制按钮，前面不带图标，默认文字颜色为白色，无触摸时的高亮效果
System - 前面不带图标，默认文字颜色为蓝色，有触摸时的高亮效果
DetailDisclosure - 前面带“!”图标按钮，默认文字颜色为蓝色，有触摸时的高亮效果
infoLight - 为感叹号“!”圆形按钮
InfoDark - 为感叹号“!”圆形按钮
ContactAdd - 前面带“+”图标按钮，默认文字颜色为蓝色，有触摸时的高亮效果

常用的触摸事件类型：
TouchDown：单点触摸按下事件，点触屏幕
TouchDownRepeat：多点触摸按下事件，点触计数大于1，按下第2、3或第4根手指的时候
TouchDragInside：触摸在控件内拖动时
TouchDragOutside：触摸在控件外拖动时
TouchDragEnter：触摸从控件之外拖动到内部时
TouchDragExit：触摸从控件内部拖动到外部时
TouchUpInside：在控件之内触摸并抬起事件
TouchUpOutside：在控件之外触摸抬起事件
TouchCancel：触摸取消事件，即一次触摸因为放上太多手指而被取消，或者电话打断
```

```
初始化
UIColor 是一个类
CGColor 是一个结构体
C++可以调用CG相关的
如果需要特定类型的button需要在初始化的时候指定,因为在后面不能直接改buttonType
```

## UIImage

UIImage对象是不可以改变的, 所以只能在初始化的时候设置它的属性.在内存不足的情况下, 会清除UIImage对象内部的data 数据, 而不是清除对象本身.如果在绘制一个没有data数据的UIImage对象的话, 会重新读取data数据, 这额外的操作可能会带来一些性能的损失.

不要创建尺寸超过 `1024 x 1024` 的图片,这种图片会消耗大量的内容,特别在使用合并图的时候,如果你是做基于代码的操作,例如位图操作的时候就不受限制.

缓存机制可能不会返回相同的UIImage,要用`isEqual:`去比较对象是否相等

`UIImagePNGRepresentation` 和 `UIImageJPEGRepresentation` 用来输出NSData

`UIImagePickerController`用来打开摄像头 `UIImageWriteToSavedPhotosAlbum`用来保存到相册

## UIImageView

提供一个展示图片列表的容器, 可以设置动画的细节

## UIImageAsset

## UITabBar

UITabBar是一个视图控制器, 里面的每一个按钮都是一个UITabBarItem的实例,通过`setItems:animated:`方法可以添加一个UITabBarItem到UITabBar里面,用`selectedItem`来控制当前选中的item

如果一个UITabBar是在UITabBarController的管理下面的, 不要直接修改UITabBarController的实例来编辑tabbar, 可以通过API方法去修改,

## UITabBarItem



## UITabBarController

不要直接修改UITabBarController的实例来编辑tabbar

## tintColor

在iOS 7后，UIView新增加了一个tintColor属性，这个属性定义了一个非默认的着色颜色值，其值的设置会影响到以视图为根视图的整个视图层次结构。它主要是应用到诸如app图标、导航栏、按钮等一些控件上，以获取一些有意思的视觉效果。

默认情况下，一个视图的tintColor是为nil的，这意味着视图将使用父视图的tint color值。当我们指定了一个视图的tintColor后，这个色值会自动传播到视图层次结构(以当前视图为根视图)中所有的子视图上。如果系统在视图层次结构中没有找到一个非默认的tintColor值，则会使用系统定义的颜色值(蓝色，RGB值为[0,0.478431,1]，我们可以在IB中看到这个颜色)。因此，这个值总是会返回一个颜色值，即我们没有指定它。

与tintColor属性相关的还有个tintAdjustmentMode属性，它是一个枚举值，定义了tint color的调整模式。其声明如下：

```
var tintAdjustmentMode: UIViewTintAdjustmentMode
```

枚举UIViewTintAdjustmentMode的定义如下：

```
enum UIViewTintAdjustmentMode : Int {
    case Automatic          // 视图的着色调整模式与父视图一致
    case Normal             // 视图的tintColor属性返回完全未修改的视图着色颜色
    case Dimmed             // 视图的tintColor属性返回一个去饱和度的、变暗的视图着色颜色
}
```

因此，当tintAdjustmentMode属性设置为Dimmed时，tintColor的颜色值会自动变暗。而如果我们在视图层次结构中没有找到默认值，则该值默认是Normal。

与tintColor相关的还有一个tintColorDidChange方法，其声明如下：

```
func tintColorDidChange()
```

这个方法会在视图的tintColor或tintAdjustmentMode属性改变时自动调用。另外，如果当前视图的父视图的tintColor或tintAdjustmentMode属性改变时，也会调用这个方法。我们可以在这个方法中根据需要去刷新我们的视图。

```
override func viewDidLoad() {
    super.viewDidLoad()
 
    print("\(self.view.tintAdjustmentMode.rawValue)")         // 输出：1
    print("\(self.view.tintColor)")                           // 输出：UIDeviceRGBColorSpace 0 0.478431 1 1
 
    self.view.tintAdjustmentMode = .Normal
    self.dimTintSwitch?.on = false
 
    // 加载图片
    var shinobiHead = UIImage(named: "shinobihead")
    // 设置渲染模式
    shinobiHead = shinobiHead?.imageWithRenderingMode(.AlwaysTemplate)
 
    self.tintedImageView?.image = shinobiHead
    self.tintedImageView?.contentMode = .ScaleAspectFit
}
```

有些复杂控件，可以有多个tint color，不同的tint color控件不同的部分。如上面提到的UIProgressView，又如navigation bars, tab bars, toolbars, search bars, scope bars等，这些控件的背景着色颜色可以使用barTintColor属性来处理。

## familyNames

```
["Copperplate", "Heiti SC", "Iowan Old Style", "Kohinoor Telugu", "Thonburi", "Heiti TC", "Courier New", "Gill Sans", "Apple SD Gothic Neo", "Marker Felt", "Avenir Next Condensed", "Tamil Sangam MN", "Helvetica Neue", "Gurmukhi MN", "Times New Roman", "Georgia", "Apple Color Emoji", "Arial Rounded MT Bold", "Kailasa", "Kohinoor Devanagari", "Kohinoor Bangla", "Chalkboard SE", "Sinhala Sangam MN", "PingFang TC", "Gujarati Sangam MN", "Damascus", "Noteworthy", "Geeza Pro", "Avenir", "Academy Engraved LET", "Mishafi", "Futura", "Farah", "Kannada Sangam MN", "Arial Hebrew", "Arial", "Party LET", "Chalkduster", "Hoefler Text", "Optima", "Palatino", "Lao Sangam MN", "Malayalam Sangam MN", "Al Nile", "Bradley Hand", "PingFang HK", "Trebuchet MS", "Helvetica", "Courier", "Cochin", "Hiragino Mincho ProN", "Devanagari Sangam MN", "Oriya Sangam MN", "Snell Roundhand", "Zapf Dingbats", "Bodoni 72", "Verdana", "American Typewriter", "Avenir Next", "Baskerville", "Khmer Sangam MN", "Didot", "Savoye LET", "Bodoni Ornaments", "Symbol", "Menlo", "Bodoni 72 Smallcaps", "Papyrus", "Hiragino Sans", "PingFang SC", "Euphemia UCAS", "Telugu Sangam MN", "Bangla Sangam MN", "Zapfino", "Bodoni 72 Oldstyle"]
```


