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
