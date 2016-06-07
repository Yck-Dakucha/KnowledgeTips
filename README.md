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
    }
    else if ([nextResponder isKindOfClass:[UINavigationController class]]){
        UINavigationController *tempNav = (UINavigationController *)nextResponder;
        result = tempNav.topViewController;
    }else if ([nextResponder isKindOfClass:[UIViewController class]]){
        result = nextResponder;
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