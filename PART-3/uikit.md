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
UIViewContentMode:
ScaleToFill - 拉伸铺满填充(等价于bgsize:100%)
ScaleAspectFit - 等比缩放至有一边能充满区域(等价于bgsize:contain)
ScaleAspectFill - 等比缩放至填满区域(等价于bgsize:cover)
Redraw - 改变`bounds`的时候调用`setNeedsDisplay`
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

## UIButton

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




