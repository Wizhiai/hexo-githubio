---
title: KVC里的valueforkeypath
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:51:06
password:
summary:
tags:
categories:
---

**一、KVC 的用法和实践**

**用法**

KVC（Key-value coding）键值编码，顾名思义。额，简单来说，是可以通过对象属性名称（Key）直接给属性值（value）编码（coding）`“编码”可以理解为“赋值”`。这样可以免去我们调用getter和setter方法，从而简化我们的代码，也可以用来修改系统控件内部属性，KVC是KVO、Core Data、CocoaBindings的技术基础，他们都是利用了OC的动态性

KVC用法

- setValue:forKey:为对象的属性赋值为对象的属性赋值
- setValue: forKeyPath:（为对象的属性赋值（包含了setValue:forKey:的功能，并且还可以对对象内的类的属性进行赋值））
- valueForKey:（根据key取值）
- valueForKeyPath:根据𝑘𝑒𝑦𝑃𝑎𝑡ℎ取值根据keyPath取值
- setValuesForKeysWithDictionary:（对模型进行一次性赋值）

为什么可以用NSNumber来接收int、float的数据类型？

因为：使用valueForKey:时，KVC会自动将标量值𝑖𝑛𝑡、𝑓𝑙𝑜𝑎𝑡、𝑠𝑡𝑟𝑢𝑐𝑡等int、float、struct等翻入NSNumber或NSValue中包装成一个对象，然后返回。因此，KVC有自动包装功能。 

例如：生成一个这样子的对象Person
person.h



```
@class Car;
@interface Person : NSObject
@property (nonatomic,copy) NSString *name;
@property (nonatomic,strong)Car *car;
@end
```

Car.h



```
@interface Car : NSObject
@property (nonatomic,strong) NSNumber *price;
@end
```

在ViewController.m中调用

ViewController.m



```
- (void)viewDidLoad {
      [super viewDidLoad];
      Person *person=[[Person alloc]init];
      [person setValue:@"lxh" forKey:@"name"];
      float price=100.0;
      Car *car=[[Car alloc]init];
      person.car=car;
      [person setValue:[NSNumber numberWithFloat:price] forKeyPath:@"car.price"];
      NSLog(@"%@",person.name);

      NSLog(@"%f",car.price.floatValue);
}
```

注意点：

1. 在Person中我仅仅只是声明了@class Car,而没有引用#import “Car.h”,然后在ViewController.m中便可以对其进行： [person setValue:[NSNumber numberWithFloat:price] forKeyPath:@”car.price”];这样子的赋值。所以说明KVC会去自动查找Car类进行赋值
2. \- 𝑣𝑜𝑖𝑑voidsetValue:𝑛𝑢𝑙𝑙𝑎𝑏𝑙𝑒𝑖𝑑nullableidvalue forKeyPath:(NSString *)keyPath;你会发现*==**value的值必须是id,也就是说不能传基本数据类型，必须是指针类型的变量\***==。
3. 

**key和keyPath的区别**

keyPath方法是集成了key的所有功能，也就是说对一个对象的一般属性进行赋值、取值，两个方法是通用的，都可以实现。但是==对对象中的对象进的属性行赋值，只有keyPath能够实现==。

==**setValuesForKeysWithDictionary==:的巧妙使用（字典转模型）**



```
-(instancetype)initWithDict:(NSDictionary *)dict{
         if (self = [super init]) {
               [self setValuesForKeysWithDictionary:dict]; 
          } 
         return self;
}
```

**注意点：**

- 字典转模型的时候,字典中的某一个key一定要在模型中有对应的属性
- 如果一个模型中包含了另外的模型对象,是不能直接转化成功的。
- 通过kvc转化模型中的模型,也是不能直接转化成功的
- ==底层还是调用了setValue: forKey:==

使用例子

#### **（1）修改系统控件内部属性**（runtime + KVC）

例如，界面设计图是这样的



![738923-20160824125215105-2100905206](KVC%E9%87%8C%E7%9A%84valueforkeypath.assets/738923-20160824125215105-2100905206.png)

**738923-20160824125215105-2100905206**



怎么感觉有点不同，这`UIPageControl`怎么跟我平常用的不一样？平常不都是这样的？？如下图



![738923-20160824125258323-135140765](KVC%E9%87%8C%E7%9A%84valueforkeypath.assets/738923-20160824125258323-135140765.png)

**738923-20160824125258323-135140765**



首先想到的肯定是，查看`UIPageControl`的头文件，如下



![复制代码](KVC%E9%87%8C%E7%9A%84valueforkeypath.assets/copycode-20200602185223472.gif)**复制代码**





```
NS_CLASS_AVAILABLE_IOS(2_0) @interface UIPageControl : UIControl 

@property(nonatomic) NSInteger numberOfPages;          // default is 0
@property(nonatomic) NSInteger currentPage;            // default is 0. value pinned to 0..numberOfPages-1

@property(nonatomic) BOOL hidesForSinglePage;          // hide the the indicator if there is only one page. default is NO

@property(nonatomic) BOOL defersCurrentPageDisplay;    // if set, clicking to a new page won't update the currently displayed page until -updateCurrentPageDisplay is called. default is NO
- (void)updateCurrentPageDisplay;                      // update page display to match the currentPage. ignored if defersCurrentPageDisplay is NO. setting the page value directly will update immediately

- (CGSize)sizeForNumberOfPages:(NSInteger)pageCount;   // returns minimum size required to display dots for given page count. can be used to size control if page count could change

@property(nullable, nonatomic,strong) UIColor *pageIndicatorTintColor NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;
@property(nullable, nonatomic,strong) UIColor *currentPageIndicatorTintColor NS_AVAILABLE_IOS(6_0) UI_APPEARANCE_SELECTOR;

@end
```



![复制代码](KVC%E9%87%8C%E7%9A%84valueforkeypath.assets/copycode.gif)**复制代码**



不够用啊兄弟。能不能给我个可以赋值`UIImage`对象的属性？看来正常途径使用系统的控件是设不了了！如何解呢 ？

第一种方式：自定义UIPageControl 第二种方式：通过runtime遍历出`UIPageControl`所有属性（包括私有成员属性，runtime确实很强大）

直接用第二种吧 第一种有兴趣的可以自己试试！

使用runtime遍历`UIPageControl`结果如下打印：



![复制代码](https://common.cnblogs.com/images/copycode.gif)**复制代码**





```
2016-03-23 01:09:26.161 TenMinDemo[6224:507269] UIPageControl -> _lastUserInterfaceIdiom = q
2016-03-23 01:09:26.161 TenMinDemo[6224:507269] UIPageControl -> _indicators = @"NSMutableArray"
2016-03-23 01:09:26.161 TenMinDemo[6224:507269] UIPageControl -> _currentPage = q
2016-03-23 01:09:26.161 TenMinDemo[6224:507269] UIPageControl -> _displayedPage = q
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _pageControlFlags = {?="hideForSinglePage"b1"defersCurrentPageDisplay"b1}
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _currentPageImage = @"UIImage" // 当前选中图片
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _pageImage = @"UIImage" // 默认图片
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _currentPageImages = @"NSMutableArray"
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _pageImages = @"NSMutableArray"
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _backgroundVisualEffectView = @"UIVisualEffectView"
2016-03-23 01:09:26.162 TenMinDemo[6224:507269] UIPageControl -> _currentPageIndicatorTintColor = @"UIColor"
2016-03-23 01:09:26.163 TenMinDemo[6224:507269] UIPageControl -> _pageIndicatorTintColor = @"UIColor"
2016-03-23 01:09:26.163 TenMinDemo[6224:507269] UIPageControl -> _legibilitySettings = @"_UILegibilitySettings"
2016-03-23 01:09:26.163 TenMinDemo[6224:507269] UIPageControl -> _numberOfPages = q
```



![复制代码](https://common.cnblogs.com/images/copycode.gif)**复制代码**



结果非常满意，果然找到我想要的图片设置属性

然后通过KVC设置自定义图片，实现了效果，代码如下



```
UIPageControl *pageControl = [[UIPageControl alloc] init]; 
 [pageControl setValue:[UIImage imageNamed:@"home_slipt_nor"] forKeyPath:@"_pageImage"];
 [pageControl setValue:[UIImage imageNamed:@"home_slipt_pre"] forKeyPath:@"_currentPageImage"];
```

**二、底层原理的分析**

**KVC的赋值原理**

setValue:forKey:赋值原理如下：

- 去模型中查找有没有对应的setter方法：例如：setIcon方法，有就直接调用这个setter方法给模型这个属性赋值[self setIcon:dic[@”icon”]];

- 如果找不到setter方法，接着就会去寻找有没有icon属性，如果有，就直接访问模型中的icon属性，进行赋值，icon=dict[@”icon”];

- 如果找不到icon属性，接着又会去寻找_icon属性，如果有，直接进行赋值_icon=dict[@”icon”];

- 如果都找不到就会报错:[<Flag 0X7fb74bc7a2c0> setValue:forUndefinedKey:]

- 如果对某个类，不允许使用KVC，可以通过设置 accessInstanceVariablesDirectly 控制。

  

  ```
  // 在该类的内部，重写此方法，外部使用KVC时，禁用没有写set get 方法的属性值。
  // 注意：对于 @property 定义的属性可以 KVC+   
  -(BOOL)accessInstanceVariablesDirectly{ 
    return NO;
  }
  ```

- 赋值检查

  

  ![复制代码](KVC%E9%87%8C%E7%9A%84valueforkeypath.assets/copycode-20200602185328625.gif)**复制代码**

  

  

  ```
  // 在类的内部，进行检查，不符合要求 返回NO ，提供外部参考。
  - (BOOL)validateValue:(inout id _Nullable __autoreleasing *)ioValue forKey:(NSString *)inKey error:(out NSError * _Nullable __autoreleasing *)outError{
     if ([inKey isEqualToString:@"colors"] && [*ioValue isKindOfClass:[NSArray class]]) { 
              return YES; 
        } else { 
              return NO; 
        }
  }
  //用法：
  // 外部 使用时，先判断是否符合要求，再使用KVC。 
  NSError *error; 
  NSString *apoint = @"name"; 
  if ([aPerson validateValue:&apoint forKey:@"_colors" error:&error]) { 
      NSLog(@"可以赋值 apoint"); 
      [aPerson setValue:apoint forKey:@"_colors"]; 
  } else { 
      NSLog(@"不可以赋值 apoint");  
      NSLog(@"%@",error.debugDescription); 
  }
  ```

  

  ![复制代码](https://common.cnblogs.com/images/copycode.gif)**复制代码**

  

KVC内部的实现

比如说如下的一行KVC的代码：

[site setValue:@”sitename” forKey:@”name”];

就会被编译器处理成：

SEL sel = sel_get_uid “𝑠𝑒𝑡𝑉𝑎𝑙𝑢𝑒:𝑓𝑜𝑟𝐾𝑒𝑦:”“setValue:forKey:”;

IMP method = objc_msg_lookup 𝑠𝑖𝑡𝑒−>𝑖𝑠𝑎,𝑠𝑒𝑙site−>isa,sel;

method𝑠𝑖𝑡𝑒,𝑠𝑒𝑙,@”𝑠𝑖𝑡𝑒𝑛𝑎𝑚𝑒”,@”𝑛𝑎𝑚𝑒”site,sel,@”sitename”,@”name”;

这下KVC内部的实现就很清楚的清楚了：一个对象在调用setValue的时候，（1）首先根据方法名找到运行方法的时候所需要的环境参数。（2）他会从自己isa指针结合环境参数，找到具体的方法实现的接口。（3）再直接查找得来的具体的方法实现。

# 扩展

## 使用 Runtime 遍历控件的所有的属性

```
unsignedintcount =0;
// 拷贝出 UITextField 所有的成员变量列表
Ivar *ivars = class_copyIvarList([UITextField class], &count);
for (int i = 0; i < count; i++) {
// 取出成员变量
// Ivar ivar = *(ivars + i);
Ivar ivar = ivars[i];
// 打印成员变量名字
LSLog(@"%s %s", ivar_getName(ivar), ivar_getTypeEncoding(ivar));
}
// 释放
free(ivars);
```

## 遍历类中的属性名

有一个ZHHAssertNewNew类,类中有两个属性

```
@property (nonatomic, strong) NSString* name;
@property (nonatomic, assign) int age;
1,导入#import 
2,实现方法
void test() {
u_int count = 0;
Ivar* ivars = class_copyIvarList(objc_getClass("ZHHAssertNewNew"), &count);
```

`` 

`for (int i=0; i

 `Ivar ivar = ivars[i];`

 `//name`

 `const char* name = ivar_getName(ivar);`

 `NSLog(@"name->%s",name);`

`` 

 `//type`

 `const char* type = ivar_getTypeEncoding(ivar);`

 `NSLog(@"type->%s",type);`

`` 

 `//得到实际的类型`

 `Class c = realTypeWithType(type);`

 `NSLog(@"class->%@",c);`

`` 

 `/* log打印结果`

 `name->_age`

 `type->i`

 `class->(null)`

`` 

 `name->_name_`

 `type->@"NSString"`

 `class->NSString`

 `*/`

 `/*`

 `关于type`

 `如果返回i代表int类型`

 `如果返回@"NSString"代表NSString`

 `...同理`

 `*/`

`` 

```
}
```

　　`//释放:与copy对应`

```
free(ivars);
}
```

`` 

`` 

```
//通过返回类型返回实际类型
Class realTypeWithType(const char* type) {
NSString* code = [NSString stringWithUTF8String:type];
```

`` 

```
if ([code hasPrefix:@"@"] && (code.length > 3)) {
```

 `//去掉前面的@"和后面的"`

 `code = [code substringFromIndex:2];`

 `code = [code substringToIndex:code.length-1];`

 `return NSClassFromString(code);`

```
}
```

`` 

`` 

```
//基本数据类型没做处理
return nil;
}
```

## 用runtime遍历用户属性的方法与传统方法的比较

**初始化方法**



```objectivec
#import "User.h"
#import <objc/message.h>

@implementation User

- (id) initWithDicionary:(NSDictionary*)dic {
    self = [super init];
    if (self) {
        //更新用户信息:
        [self updateUserInfo:dic];
    }
    return self;
}
```

Objectivec

**更新用户信息**



```objectivec
- (void)updateUserInfo:(NSDictionary *)dic {
    id userId =  [[dic objectForKey:@"data"] objectForKey:@"user_id"];
    if ([userId isKindOfClass:[NSNull class]]) self.user_id = @"0000";
    self.user_id = userId;

    NSString *nickName = [[dic objectForKey:@"data"] objectForKey:@"nickname"];
    if ([nickName isKindOfClass:[NSNull class]]) self.nickname = @"";
    self.nickname = nickName;

    id mobile = [[dic objectForKey:@"data"] objectForKey:@"mobile"];
    if ([mobile isKindOfClass:[NSNull class]]) self.moblie = @"0000";
    self.moblie = mobile;

    id lng = [[dic objectForKey:@"data"] objectForKey:@"lng"];
    if ([lng isKindOfClass:[NSNull class]]) self.lng = @"00.00";
    self.lng = lng;

    id lat = [[dic objectForKey:@"data"] objectForKey:@"lat"];
    if ([lat isKindOfClass:[NSNull class]]) self.lat = @"00.00";
    self.lat = lat;

    NSString *avater = [[dic objectForKey:@"data"] objectForKey:@"avatar"];
    if ([avater isKindOfClass:[NSNull class]]) self.avatar = @"";
    self.avatar = avater;

    NSString *token = [dic objectForKey:@"token"];
    if ([token isKindOfClass:[NSNull class]]) self.token = @"";
    self.token = token;

    NSString *describe = [[dic objectForKey:@"data"] objectForKey:@"describe"];
    if ([describe isKindOfClass:[NSNull class]]) self.describe = @"";
    self.describe = describe;

    NSString *signature = [[dic objectForKey:@"data"] objectForKey:@"signature"];
    if ([signature isKindOfClass:[NSNull class]]) self.signature = @"";
    self.signature = signature;

    NSString *gender = [[dic objectForKey:@"data"] objectForKey:@"gender"];
    if ([gender isKindOfClass:[NSNull class]])  self.gender = @"";
    self.gender = gender;

    NSString *school = [[dic objectForKey:@"data"] objectForKey:@"school"];
    if ([school isKindOfClass:[NSNull class]]) self.school = @"";
    self.school = school;

    NSString *department = [[dic objectForKey:@"data"] objectForKey:@"department"];
    if ([department isKindOfClass:[NSNull class]]) self.department = @"";
    self.department = department;

    NSString *start_time = [[dic objectForKey:@"data"] objectForKey:@"start_time"];
    if ([start_time isKindOfClass:[NSNull class]]) self.start_time = @"";
    self.start_time = start_time;

    NSString *education_id = [[dic objectForKey:@"data"] objectForKey:@"education_id"];
    if ([education_id isKindOfClass:[NSNull class]])  self.education_id = @"";
    self.education_id = education_id;
}
```

Objectivec

**runtime遍历属性**



```objectivec
/*
 对象归档解档改为运行时进行:

 */
#pragma mark - 对象归档
- (void)encodeWithCoder:(NSCoder *)aCoder {
    unsigned int count = 0 ;
    Ivar *ivars = class_copyIvarList([User class], &count) ;
    for (int i = 0; i < count; i++) {
        Ivar ivar = ivars[i] ;
        const char *propertyName = ivar_getName(ivar) ;
        NSString *key = [[NSString alloc] initWithUTF8String:propertyName] ;
        [aCoder encodeObject:[self valueForKey:key] forKey:key] ;
    }
}

#pragma mark - 对象解档
- (instancetype)initWithCoder:(NSCoder *)aDecoder {
    self = [super init] ;
    if (self) {
        unsigned int count = 0 ;
        Ivar *ivars = class_copyIvarList([User class], &count) ;
        for (int i = 0; i < count; i++) {
            Ivar ivar = ivars[i] ;
            const char *propertyName = ivar_getName(ivar) ;
            NSString *key = [[NSString alloc] initWithUTF8String:propertyName] ;
            id value = [aDecoder decodeObjectForKey:key] ;
            [self setValue:value forKey:key] ;
        }
    }
    return self ;
}
```

Objectivec

**原始方法一个一个去写**



```objectivec
// 归档(NSCoding 协议方法):
- (void)encodeWithCoder:(NSCoder *)aCoder {
    [aCoder encodeObject:self.user_id forKey:@"user_id"];
    [aCoder encodeObject:self.nickname forKey:@"nickname"];
    [aCoder encodeObject:self.moblie forKey:@"moblie"];
    [aCoder encodeObject:self.lng forKey:@"lng"];
    [aCoder encodeObject:self.lat forKey:@"lat"];
    [aCoder encodeObject:self.token forKey:@"token"];
    [aCoder encodeObject:self.avatar forKey:@"avatar"];
    [aCoder encodeObject:self.describe forKey:@"describe"];
    [aCoder encodeObject:self.signature forKey:@"signature"];
    [aCoder encodeObject:self.gender forKey:@"gender"];
    [aCoder encodeObject:self.school forKey:@"school"];
    [aCoder encodeObject:self.department forKey:@"department"];
    [aCoder encodeObject:self.start_time forKey:@"start_time"];
    [aCoder encodeObject:self.education_id forKey:@"education_id"];
}


// 反归档(NSCoding 协议方法):
- (nullable instancetype)initWithCoder:(NSCoder *)aDecoder {
    if (self = [super init]) {
        self.user_id = [aDecoder decodeObjectForKey:@"user_id"];
        self.nickname = [aDecoder decodeObjectForKey:@"nickname"];
        self.moblie = [aDecoder decodeObjectForKey:@"moblie"];
        self.lng = [aDecoder decodeObjectForKey:@"lng"];
        self.lat = [aDecoder decodeObjectForKey:@"lat"];
        self.token = [aDecoder decodeObjectForKey:@"token"];
        self.avatar = [aDecoder decodeObjectForKey:@"avatar"];
        self.describe = [aDecoder decodeObjectForKey:@"describe"];
        self.signature = [aDecoder decodeObjectForKey:@"signature"];
        self.gender = [aDecoder decodeObjectForKey:@"gender"];
        self.school = [aDecoder decodeObjectForKey:@"school"];
        self.department = [aDecoder decodeObjectForKey:@"department"];
        self.start_time = [aDecoder decodeObjectForKey:@"start_time"];
        self.education_id = [aDecoder decodeObjectForKey:@"education_id"];

    }
    return self;
}
```

Objectivec

**重写description方法打印**



```objectivec
- (NSString *)description {
    return [NSString stringWithFormat:@"%@, %@, %@, %@, %@, %@, %@", _user_id, _nickname, _avatar, _moblie, _token, _lat, _lng];
}

@end
```


来源: LJ的博客
作者: 胡
链接: https://bolg.91youzhi.com/2020/06/02/kvc-li-de-valueforkeypath/
本文章著作权归作者所有，任何形式的转载都请注明出处。