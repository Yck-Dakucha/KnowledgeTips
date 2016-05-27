# KnowledgeTips
#####记录平时的小东西,整合一下吧

###(1)--最新适用的手机号正则表达 
 
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

###(2)--判断文字是否为空

```
- (BOOL)isBlankString {
    if (self == nil) {
        return YES;
    }
    if (self == NULL) {
        return YES;
    }
    if ([self isKindOfClass:[NSNull class]]) {
        return YES;
    }
    if ([[self stringByTrimmingCharactersInSet:[NSCharacterSet whitespaceAndNewlineCharacterSet]] length]==0) {
        return YES;
    }
    return NO;
}

```
