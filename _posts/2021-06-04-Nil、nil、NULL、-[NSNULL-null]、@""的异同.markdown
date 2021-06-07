```
NSString*str1 = nil;

NSString*str2 = Nil;

NSString*str3 = NULL;

NSNull*str4 = [NSNull null];

NSString *str5 = @"";


NSLog(@" \r str1:%p\r str2:%p\r str3:%p\r str4:%p\r str5:%p\r", nil, NULL, Nil, [NSNullnull], @"");
NSLog(@"\r str1:%@\r str2:%@\r str3:%@\r str4:%@\r str5:%@\r", str1, str2, str3, str4, str5);

```

### 打印结果：
2013-05-09 16:42:00.124 Targets[674:c07]  
 str1:0x0
 str2:0x0
 str3:0x0
 str4:0x1dc2678
 str5:0x46f4

2013-05-09 16:42:04.717 Targets[674:c07] 
 str1:(null)
 str2:(null)
 str3:(null)
 str4:<null>
 str5:

---

### 2.描述
```
Printing description of str1:
<nil>
Printing description of str2:
<nil>
Printing description of str3:
<nil>
Printing description of str4:
<null>
Printing description of str5:
<object returned empty description>
```

2种方式打印出来的描述不太一样， 直接打印nil，Nil等变量是指向的这些常量的地址，而如果赋值给字符串实际上打印的是字符串的地址

可以看到 `nil，Nil， NULL`， 本质上是相同的 都指向0X0 地址
而 `[NSNULL null]` 和 @“” 都是在常量存储区的，占用着固定地址。

![111](http://upload-images.jianshu.io/upload_images/299790-0bee9539b6d4595c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![11](http://upload-images.jianshu.io/upload_images/299790-d6c2af055a2c334c.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 


