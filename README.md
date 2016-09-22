# KnowledgeTips
#####记录平时的小东西,整合一下吧

###1--最新适用的手机号正则表达 
 
```
/**
 *  验证是否是手机号
 *
 *  @return YES是 NO不是
 */
- (BOOL)isPhoneNum {
    NSString *MOBILE = @"^(0|86|17951)?(13[0-9]|15[012356789]|17[0678]|18[0-9]|14[57])[0-9]{8}$";
    NSPredicate *regMobile = [NSPredicate predicateWithFormat:@"SELF MATCHES %@", MOBILE];
    return [regMobile evaluateWithObject:self];
}

```

###2--判断文字是否为空

```
+ (BOOL)isBlankString:(NSString *)string{
    if (string == nil) {
        return YES;
    }
    if (string == NULL) {
        return YES;
    }
    if ([string isKindOfClass:[NSNull class]]) {
        return YES;
    }
    if ([[string stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length]==0) {
        return YES;
    }
    return NO;
}

```

###3--应用内拨打电话

```
//第一种打电话的方式
- (IBAction)firstCall:(id)sender {
NSString *phone = @"18749627117";
if (phone != nil) {
[[UIApplication sharedApplication] openURL:[NSURL URLWithString:[NSString stringWithFormat:@"tel:%@",phone]]];//使用这个方法，打电话结束后会进入联系人列表
//[[UIApplication sharedApplication] openURL:[NSURL URLWithString:[NSString stringWithFormat:@"telprompt://%@",phone]]];//使用这种方法在拨打电话前会先弹出框，询问是否打电话，然后打完电话之后回到主程序中，有人说这种方法可能不合法，无法通过审核
}
}
//第二种打电话的方式
- (IBAction)secondCall:(id)sender {
NSString *str = [NSString stringWithFormat:@"tel:%@",@"18749627117"];
UIWebView *callWebView = [[UIWebView alloc]init];
[callWebView loadRequest:[NSURLRequest requestWithURL:[NSURL URLWithString:str]]];
[self.view addSubview:callWebView];//也可以不加到页面上
}

```

###4--获取当前屏幕的VC

```
//获取当前屏幕显示的ViewController
+ (UIViewController *)getCurrentVC
{
    UIViewController *result = nil;
    UIWindow * window = [[UIApplication sharedApplication] keyWindow];
    if (window.windowLevel != UIWindowLevelNormal)
    {
        NSArray *windows = [[UIApplication sharedApplication] windows];
        for(UIWindow * tempWindow in windows)
        {
            if (tempWindow.windowLevel == UIWindowLevelNormal)
            {
                window = tempWindow;
                break;
            }
        }
    }
    UIView *frontView = [[window subviews] objectAtIndex:0];
    id nextResponder = [frontView nextResponder];
    if ([nextResponder isKindOfClass:[UITabBarController class]]) {
        UITabBarController *tempTabBarVC = (UITabBarController *)nextResponder;
        UIViewController *navVC = tempTabBarVC.selectedViewController;
        if ([navVC isKindOfClass:[UINavigationController class]]) {
            UINavigationController *tempNav = (UINavigationController *)navVC;
            result = tempNav.topViewController;
        }else {
            result = navVC;
        }
    }else if ([nextResponder isKindOfClass:[UINavigationController class]]){
        UINavigationController *tempNav = (UINavigationController *)nextResponder;
        result = tempNav.topViewController;
    }else if ([nextResponder isKindOfClass:[UIViewController class]]){
        result = nextResponder;
    }else if ([nextResponder isKindOfClass:[UIWindow class]]){
        UIWindow *resPonder = (UIWindow *)nextResponder;
        //这里需要确定rootViewController属于哪个类
        UITabBarController *tabBarVC = (UITabBarController *)resPonder.rootViewController;
        UINavigationController *vc = tabBarVC.selectedViewController;
        result = vc.visibleViewController;
    }else {
        result = window.rootViewController;
    }
    return result;
}

```

###5--保存图片到系统相册
* 出自<http://blog.csdn.net/hengshujiyi/article/details/22879495>
 
应用中有时我们会有保存图片的需求，如利用UIImagePickerController用IOS设备内置的相机拍照，或是有时我们在应用程序中利用UIKit的 UIGraphicsBeginImageContext，UIGraphicsEndImageContext，UIGraphicsGetImageFromCurrentImageContext方法创建一张图像需要进行保存。 IOS的UIKit Framework提供了UIImageWriteToSavedPhotosAlbum方法对图像进行保存，该方法会将image保存至用户的相册中，描述如下：

```
void UIImageWriteToSavedPhotosAlbum (
   UIImage  *image,
   id       completionTarget,
   SEL      completionSelector,
   void     *contextInfo
  );
   
```

参数说明： 

* image 带保存的图片UImage对象 
* completionTarget 图像保存至相册后调用completionTarget指定的selector（可选） 
* completionSelector completionTarget的方法对应的选择器，相当于回调方法，需满足以下格式 

```
- (void) image: (UIImage *) image didFinishSavingWithError: (NSError *) error contextInfo: (void *) contextInfo;

```

当我们需要异步获得图像保存结果的消息时，我们需要指定completionTarget对象以及其completionSelector对应的选择器。示例如下

```
- (IBAction)saveImageToAlbum:(id)sender {
    UIImageWriteToSavedPhotosAlbum(self.imageView.image, self, @selector(imageSavedToPhotosAlbum:didFinishSavingWithError:contextInfo:), nil);
}

//实现imageSavedToPhotosAlbum:didFinishSavingWithError:contextInfo:方法

- (void)imageSavedToPhotosAlbum:(UIImage *)image didFinishSavingWithError:(NSError *)error contextInfo:(void *)contextInfo
{
    NSString *message = @"呵呵";
    if (!error) {
        message = @"成功保存到相册";
    }else
    {
        message = [error description];
    }
    NSLog(@"message is %@",message);
}
```

###6--关于collectionView与tableView的cell的复用问题
collectionView对于cell的不用试通过`initWithFrame`方法，  
而tableView是通过`- (instancetype)initWithStyle:(UITableViewCellStyle)style reuseIdentifier:(NSString *)reuseIdentifier;`  
对于headerFooterView，collectionView一样是通过`initWithFrame`  
而tableView是通过`- (instancetype)initWithReuseIdentifier:(NSString *)reuseIdentifier;`

###7--Unable to simultaneously satisfy constraints
<http://www.cnblogs.com/zhouyi-ios/p/5240003.html>  

表示无法同时满足所有约束。

一、出现的场景

当我创建一个width为100的视图，我添加距左约束300，居右约束300后，运行后视图不显示，而且控制台有输出。

原因：屏幕的宽度为640时，而上面100+300＋300已经超出屏幕的宽度，这是width被压缩，所以导致了这个问题。。。

 

二、基本概念

1、intrinsicContentSize：固有大小。即视图在不受约束影响时，本来的尺寸。
2、Content Hugging Priority：内容压缩优先级。即阻止view返回的实际尺寸比intrinsicContentSize大的优先级。默认为251，如果某约束优先级低于251，则不拉伸视图，动态增加低优先级的约束值。
3、Content Compression Resistance Priority：内容抗压缩优先级。默认为750，如果某约束优先级低于750，则不压缩视图，动态减少低优先级的约束值。
 
在autoLayout中，默认添加的约束的优先级为1000，如果约束设置不妥当，则会压缩/拉伸视图，那么如果设置了Content Hugging Priority/Content Compression Resistance Priority，则解决了约束设置和intrinsicContentSize冲突的问题

###8--数据库报错SQLiteLog (1) no such Column: 

今天在进入sqlite数据库查询的时候出现了这个问题，SQLiteLog (1) no such Column: B4B147BC522828731F1A016BFA72C073，很明显问题是数据库中没有这一列，但是检查过之后数据库中明显存在  
sql语句是  
	
```
	//查询满足两个条件的数据
	NSString *sql = [NSString stringWithFormat:@"SELECT * FROM t_downloading WHERE user_id = %ld and videoName = %@;",(long)kUserID,videoDetail.fileName];

```

“B4B147BC522828731F1A016BFA72C073”是我报错时候传入的videoDetail.fileName，它并不是我数据库中的列名字段，查询一段时间后发现我的videoDetail.fileName是TEXT类型，并不是通常所用的Integer类型，sql语句应该改为  

```
//注意\的使用，不然会报错
NSString *sql = [NSString stringWithFormat:@"SELECT * FROM t_downloading WHERE user_id = %ld and videoName = \"%@\";",(long)kUserID,videoDetail.fileName];
```
这样才能获取数据库表中的数据，否则则出现类似没有列名,列名是参数的错误，

###9--修改backBarButtonItem的标题:  
有时候我们需要修改backBarButtonItem的标题，我们可能会这样做：  

```
self.navigationItem.backBarButtonItem.title = @"返回";

```
但是并不能返回，因为可以跟中backBarButtonItem，他没有值，于是我们附一个新值给它：
  
```
UIBarButtonItem *backItem = [[UIBarButtonItem alloc] init];
backItem.title = @"返回";
self.navigationItem.backBarButtonItem = backItem;

```
然而还不成功，这是因为你改的是下一级页面的标题。比如有A和B界面，从A切换到B，那么你要修改B上的返回按钮标题就要在A里加入你上面的代码，因为back按钮是属于上一个页面的。

###10--NSAttributedString的属性:

* NSFontAttributeName; //字体，value是UIFont对象  
* NSParagraphStyleAttributeName;//绘图的风格（居中，换行模式，间距等诸多风格），value是  
* NSParagraphStyle对象  
* NSForegroundColorAttributeName;// 文字颜色，value是UIFont对象  
* NSBackgroundColorAttributeName;// 背景色，value是UIFont  
* NSLigatureAttributeName; //  字符连体，value是NSNumber  
* NSKernAttributeName; // 字符间隔  
* NSStrikethroughStyleAttributeName;//删除线，value是NSNumber  
* NSUnderlineStyleAttributeName;//下划线，value是NSNumber  
* NSStrokeColorAttributeName; //描绘边颜色，value是UIColor  
* NSStrokeWidthAttributeName; //描边宽度，value是NSNumber  
* NSShadowAttributeName; //阴影，value是NSShadow对象  
* NSTextEffectAttributeName; //文字效果，value是NSString  
* NSAttachmentAttributeName;//附属，value是NSTextAttachment 对象  
* NSLinkAttributeName;//链接，value是NSURL or NSString  
* NSBaselineOffsetAttributeName;//基础偏移量，value是NSNumber对象  
* NSUnderlineColorAttributeName;//下划线颜色，value是UIColor对象  
* NSStrikethroughColorAttributeName;//删除线颜色，value是UIColor  
* NSObliquenessAttributeName; //字体倾斜  
* NSExpansionAttributeName; //字体扁平化  
* NSVerticalGlyphFormAttributeName;//垂直或者水平，value是 NSNumber，0表示水平，1垂直  

###11--删除某个cell的分割线
在一些时候，在tableView上我们需要系统分割线，但在某个cell又不想显示分割选，cell中又没有这个方法，只有调整他的缩进，其实分割线还是在cell的View上的，我们打印cell的subViews的class，可以得到一个`_UITableViewCellSeparatorView` 的view，这就是我们想要找到的分割线，设置removeFromSuperview就可以了，设置hidden是不可行的。

```
[cell.subviews enumerateObjectsUsingBlock:^(__kindof UIView * _Nonnull obj, NSUInteger idx, BOOL * _Nonnull stop) {
                    if ([obj isKindOfClass:NSClassFromString(@"_UITableViewCellSeparatorView")]) {
                        [obj removeFromSuperview];
                    }
                }];

```
###12--快速pod update/install

pod update --verbose --no-repo-update

###13--在- (UIViewController *)popViewControllerAnimated:(BOOL)animated中调用popToRootViewControllerAnimated会导致navigationBar显示异常
  
  具体原因待研究
  
###14--关于屏幕旋转问题

最近遇到了屏幕旋转的坑，记录一下以思悔过。  
对于莫名其妙的问题，一定要细心细心细心！！  
对于iOS7以后，对于屏幕旋转的支持：  

1. 在plist.info里进行支持
2.  在VC里边配置  

对于现在APP的主流框架TabBar+NavigationVC的模式，我们需要设置如下：  

在`baseTabBarController`中：  

```
//是否支持旋转
- (BOOL)shouldAutorotate {
    return [self.selectedViewController shouldAutorotate];
}
//设置转屏支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return [self.selectedViewController supportedInterfaceOrientations];
}

```  

在`BaseNavgationVC`中：  

```
//是否支持旋转
- (BOOL)shouldAutorotate {
    return [self.visibleViewController shouldAutorotate];
}
//设置转屏支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations
{
    return [self.visibleViewController supportedInterfaceOrientations];
}

```

对于我们的项目，一般我们都会有一个`BaseViewController`来控制一些统一设置的方法，所以我在我的BaseViewController中设置了：

```
- (BOOL)shouldAutorotate {
    return NO;
}

- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return UIInterfaceOrientationMaskAllButUpsideDown;
}

```

一切的逻辑都正常，测试也正常，由于在BaseViewController中设置了shouldAutorotate返回值是NO,只有在子类重写shouldAutorotate为YES的时候才会旋转，但是。。没有但是的话也就没了这个记录。。
当我们: 

```
[self presentViewController:[[ZXBaseNavigation alloc] initWithRootViewController:loginVC] animated:YES completion:nil];
```
模态出来一个视图的时候，如果在摩太值钱旋转了设备。。就会导致模态出来的这个loginVC发生旋转（loginVC也是BaseViewController的子类），而且此时在旋转设备也没反应，说明shouldAutorotate起了作用。这是什么原因造成呢？

###懒！懒！懒！

我们仔细看我`BaseViewController`中`supportedInterfaceOrientations`的方法  

由于有几个页面需要屏幕旋转我就吧页面支持了`UIInterfaceOrientationMaskAllButUpsideDown`屏幕方向   

心中想着只要在需要旋转的视图修改`shouldAutorotate`为YES就可以了，就不用多写一个方法`supportedInterfaceOrientations`了

问题就出在这里，因为我是在BaseViewController中写的`supportedInterfaceOrientations`为`UIInterfaceOrientationMaskAllButUpsideDown`，就导致了所有的BaseViewController子类都支持三个屏幕方向，在我们模态视图的时候，APP的window的rootViewController发生了变化，他就会去调用
shouldAutorotate和supportedInterfaceOrientations，由于我们的视图是支持三个方向的，所以此时视图会根据设备的方向去loadView，由于shouldAutorotate为NO，所以他不会旋转。

所以我们应该在BaseViewController中这样写：

```
- (BOOL)shouldAutorotate {
    return NO;
}

- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return UIInterfaceOrientationMaskPortrait;
}

```
让其只支持竖屏，然后在需要支持其他方向的VC去重写这个两个方法完成屏幕的旋转。  
~~###ps:如果你有自己的UIAlertController，他的shouldAutorotate与supportedInterfaceOrientations一定要与BaseViewController相同！~~

今天又发现一个问题，如果项目里边使用了UIAlertController，在preserent的时候会产生crash的情况，后台抛出异常  

	*** Terminating app due to uncaught exception 'NSInternalInconsistencyException', reason: 'UIAlertController:supportedInterfaceOrientations was invoked recursively!'
	
是因为UIAlertController现在是作为一个单独的控制器模态出来的，我们的BaseNavgationVC中选择的写的`supportedInterfaceOrientations`为可见的控制器，当preserent出UIAlertController的时候，系统会去UIAlertController中寻找这个方法，然而没有找到就会产生崩溃，最好的做法就是在BaseNavgationVC中进行判断 

``` 

	- (UIInterfaceOrientationMask)supportedInterfaceOrientations
{
    if ([self.visibleViewController isKindOfClass:[UIAlertController class]]) {
        return UIInterfaceOrientationMaskAll;
    }
    return [self.visibleViewController supportedInterfaceOrientations];
}

```


所以最终写法是：

###TabBarController中

```
//是否支持旋转
- (BOOL)shouldAutorotate {
    return [self.selectedViewController shouldAutorotate];
}
//设置转屏支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return [self.selectedViewController supportedInterfaceOrientations];
}

```
###BaseNavigationController中

```
//是否支持旋转
- (BOOL)shouldAutorotate {
    return [self.visibleViewController shouldAutorotate];
}
//设置转屏支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations
{
	//这里去除UIAlertController的情况
    if ([self.visibleViewController isKindOfClass:[UIAlertController class]]) {
        return UIInterfaceOrientationMaskAll;
    }
    return [self.visibleViewController supportedInterfaceOrientations];
}

```
###BaseViewController中

```
//是否支持旋转
- (BOOL)shouldAutorotate {
    return NO;
}
//设置转屏支持的方向
- (UIInterfaceOrientationMask)supportedInterfaceOrientations {
    return UIInterfaceOrientationMaskPortrait;
}

```

###15--EXC_BAD_ACCESS EXC_I386_GPFLT问题
有可能是在你构建一个字符串的时候，例如

```
	NSString *pathName = [NSString stringWithFormat:@"Documents/VideoTemp/%@.mp4",task.zx_videoModel.fileName];
	
```
当这个task.zx_videoModel已经被dealloc的时候，就会出现EXC_BAD_ACCESS EXC_I386_GPFLT，可以检查一下代码在运行到这里的时候对象是否已经被销毁



  








