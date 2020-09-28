---
title: OC对象，isa和superclass
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:48:57
password:
summary:
tags:
categories:
---



instance对象实例对象实例对象

class对象类对象类对象

meta-class对象元类对象元类对象

## 一.instance对象

> **概念：** instance对象就是通过类alloc出来的对象，每次调用alloc 都会产生新的instance对象



```
NSObject *object1 = [[NSObject alloc] init];
NSObject *object2 = [[NSObject alloc] init];
复制代码
```

> object1、object2是NSObject的instance对象实例对象实例对象

> 它们是不同的两个对象，分别占用两块不同的内存

> **instance对象**在内存中存储的信息包括:
>
> - isa指针
>
> - 其他成员变量

### 对象的isa指针指向哪里？



![截屏2020-06-02 上午11.04.17](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.04.17.png)

**截屏2020-06-02 上午11.04.17**



> instance的isa指针指向class。
> 当调用对象方法时，通过instance的isa指针找到class，最后找到对象方法的实现进行调用。

> class的isa指针指向meta-class。
> 当调用类方法时，通过class的isa指针找到meta-class，最后找到类方法的实现进行调用。

## 二.class对象



```
Class objcClass1 = [object1 class];
Class objcClass2 = [object2 class];
Class objcClass3 = object_getClass(object1);
Class objcClass4 = object_getClass(object2);
Class objcClass5 = [NSObject class];
复制代码
```

> objcClass1~objcClass5都是NSObject的class对象类对象类对象

> 它们都是同一个对象，每个类在内存中有且只有一个class对象 -> NSObject

> **class对象**在内存中存储的信息包括:
>
> - isa指针（class的isa指针指向meta-class。
>   当调用类方法时，通过class的isa指针找到meta-class，最后找到类方法的实现进行调用。）
> - superclass指针
> - 类的属性@𝑝𝑟𝑜𝑝𝑒𝑟𝑡𝑦@property、类的对象方法信息𝑖𝑛𝑠𝑡𝑎𝑛𝑐𝑒𝑚𝑒𝑡ℎ𝑜𝑑instancemethod
> - 类的协议信息𝑝𝑟𝑜𝑡𝑜𝑐𝑜𝑙protocol、类的成员变量信息𝑖𝑣𝑎𝑟ivar
>
> #### class对象的superclass指针



![截屏2020-06-02 上午11.06.38](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.06.38.png)

**截屏2020-06-02 上午11.06.38**



当Student的instance对象要调用Person的对象方法时，会先通过isa找到Student的class，然后通过superclass找到Person的class，最后找到对象方法的实现进行调用。

## 三.meta-class对象



```
// 将类对象作为参数传入，获得元类对象
Class objcMetaClass = object_getClass([NSObject class]);
复制代码
```

> objcMetaClass是NSObject的meta-class元类对象元类对象

> 每个类在内存中有且只有一个meta-class对象

> **meta-class**对象和class对象的内存结构是一样的，但是用途不一样，在内存中存储的信息主要包括：
>
> - isa指针
> - superclass指针
> - 类的类方法信息𝑐𝑙𝑎𝑠𝑠𝑚𝑒𝑡ℎ𝑜𝑑classmethod

#### meta-class对象的superclass指针



![截屏2020-06-02 上午11.09.34](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.09.34.png)

**截屏2020-06-02 上午11.09.34**



> #### **注意：** 一下代码获取的objectClass是class对象，并不是meta-class对象



```
Class objectClass = [[NSObject class] class];
复制代码
```

> **补充：** 查看Class是否为meta-class



```
Class objcClass5 = [NSObject class];
BOOL result1 = class_isMetaClass(objcClass5); // NO
BOOL result2 = class_isMetaClass(object_getClass(objcClass5)); // YES
```

## 四.object_getClass内部实现



```
Class objc_getClass(const char *aClassName)
复制代码
```

> 传入字符串类名

> 返回对应的类对象



```
Class object_getClass(id obj)
复制代码
```

> 传入obj可能是instance对象、class对象、meta-class对象

> 返回值：
>
> - 如果是instance对象，返回class对象
> - 如果是class对象，返回meta-class对象
> - 如果是meta-class对象，返回NSObject基类基类的meta-class对象



```
- (Class)class，+(Class)class
复制代码
```

> 返回值就是类对象

## isa细节

从64bit开始，isa需要进行一次位运算，才能计算出真实地址



![截屏2020-06-02 上午11.15.38](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.15.38.png)

**截屏2020-06-02 上午11.15.38**





![截屏2020-06-02 上午11.16.20](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.16.20.png)

**截屏2020-06-02 上午11.16.20**



# isa、superclass总结



![截屏2020-06-02 上午11.29.44](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8A%E5%8D%8811.29.44.png)

**截屏2020-06-02 上午11.29.44**



实例对象𝑖𝑛𝑠𝑡𝑎𝑛𝑐𝑒instance的isa指向class

类对象𝑐𝑙𝑎𝑠𝑠class的isa指向meta-class

#### 元类对象𝑚𝑒𝑡𝑎−𝑐𝑙𝑎𝑠𝑠meta−class的isa指向基类的meta-class

类对象𝑐𝑙𝑎𝑠𝑠class的superclass指向父类的class

- 如果没有父类，superclass指针为nil

元类对象𝑚𝑒𝑡𝑎−𝑐𝑙𝑎𝑠𝑠meta−class的superclass指向父类的meta-class

- 基类的meta-class的superclass指向基类的class

instance调用对象方法的轨迹

- isa找到class，方法不存在，就通过superclass找父类

class调用类方法的轨迹

- isa找到meta-class，方法不存在，就通过superclass找父类

## class和meta-class的结构



```
struct objc_class {
    Class _Nonnull isa  OBJC_ISA_AVAILABILITY;

#if !__OBJC2__
    Class _Nullable super_class                              OBJC2_UNAVAILABLE;
    const char * _Nonnull name                               OBJC2_UNAVAILABLE;
    long version                                             OBJC2_UNAVAILABLE;
    long info                                                OBJC2_UNAVAILABLE;
    long instance_size                                       OBJC2_UNAVAILABLE;
    struct objc_ivar_list * _Nullable ivars                  OBJC2_UNAVAILABLE;
    struct objc_method_list * _Nullable * _Nullable methodLists              BJC2_UNAVAILABLE;
    struct objc_cache * _Nonnull cache                       OBJC2_UNAVAILABLE;
    struct objc_protocol_list * _Nullable protocols          OBJC2_UNAVAILABLE;
#endif

} OBJC2_UNAVAILABLE;
/* Use `Class` instead of `struct objc_class *` */
复制代码
```

> 在runtime.h头文件中我们可以看到一个条件编译如果不是objc2的版本下，那么条件范围内的代码将不会被执行，所以在OC2.0中，这段代码已经过时了。而且通过OBJC2_UNAVAILABLE也可以看出，struct objc_class这个结构体在OC2.0中已经不可用了。

#### 4.1 窥探struct objc_class的结构

> 类和元类在内存中的结构，就是struct objc_class这样一个结构体，这个结构体在objc-runtime-new.h头文件中可以找到最新版本的定义。如下图结构体struct objc_class中部分代码所示：



```
struct objc_class : objc_object {
    Class isa;
    Class superclass;
    cache_t cache;             // formerly cache pointer and vtable-方法缓存
    class_data_bits_t bits;    // class_rw_t * plus custom rr/alloc flags-用于获取具体的类信息
    ...
    ...
    ...
}
复制代码
```

> **注意：** 冒号语法是继承的意思，结构体objc_class继承自objc_object，这种语法是C++的语法。



```
class_rw_t *data() const {
    return bits.data();
}
复制代码
class_rw_t* data() const {
    return (class_rw_t *)(bits & FAST_DATA_MASK);
}
```

**图为老版本代码结构，可以参考新版本源码**

![截屏2020-06-02 下午4.15.02](https://wizhiai.github.io/2020/05/24/dui-xiang-lei-isa-he-superclass/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8B%E5%8D%884.15.02.png)

**截屏2020-06-02 下午4.15.02**



> struct objc_class结构体中包含上面这样一段代码，返回值是class_rw_t，是一个可读写的表单。

> 进入class_rw_t头文件中我们可以看到一段public的代码，返回值是class_rw_ext_t类型。如下图所示

class_rw_ext_t *ext **const** { **return** get_ro_or_rwe.dyn_cast>; }

进入class_rw_ext_t头文件，我们可以看到方法列表、属性列表、协议列表的相关定义。如下图所示



```
struct class_rw_ext_t {
    const class_ro_t *ro;
    method_array_t methods;
    property_array_t properties;
    protocol_array_t protocols;
    char *demangledName;
    uint32_t version;
};
```

> 进入到class_rw_ext_t结构体中class_ro_t类型的头文件中，我们可以看到instanceSize、ivars的定义。下图为头文件中的部分代码。



```
struct class_ro_t {
    uint32_t flags;
    uint32_t instanceStart;
    uint32_t instanceSize;
#ifdef __LP64__
    uint32_t reserved;
#endif

    const uint8_t * ivarLayout;

    const char * name;
    method_list_t * baseMethodList;
    protocol_list_t * baseProtocols;
    const ivar_list_t * ivars;
    ...
    ...
    ...
```



![截屏2020-06-02 下午4.17.27](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/%E6%88%AA%E5%B1%8F2020-06-02%20%E4%B8%8B%E5%8D%884.17.27.png)

**截屏2020-06-02 下午4.17.27**



**总结：** OC的类信息存放在哪里？

- 对象方法、属性、成员变量、协议信息，存放在class对象中。
- 类方法，存放在meta-class对象中。
- 成员变量的具体值，存放在instance对象。

# 拓展

## class_ro_t 和 class_rw_t 的区别



```cpp
struct class_rw_t {
    uint32_t flags;
    uint32_t version;

    const class_ro_t *ro;

    method_array_t methods;
    property_array_t properties;
    protocol_array_t protocols;

    Class firstSubclass;
    Class nextSiblingClass;
};

struct class_ro_t {
    uint32_t flags;
    uint32_t instanceStart;
    uint32_t instanceSize;
    uint32_t reserved;

    const uint8_t * ivarLayout;

    const char * name;
    method_list_t * baseMethodList;
    protocol_list_t * baseProtocols;
    const ivar_list_t * ivars;

    const uint8_t * weakIvarLayout;
    property_list_t *baseProperties;
};
```

Cpp

可以看出，class_rw_t结构体内有一个指向class_ro_t结构体的指针。

每个类都对应有一个class_ro_t结构体和一个class_rw_t结构体。在编译期间，class_ro_t结构体就已经确定，objc_class中的bits的data部分存放着该结构体的地址。在runtime运行之后，具体说来是在运行runtime的realizeClass 方法时，会生成class_rw_t结构体，该结构体包含了class_ro_t，并且更新data部分，换成class_rw_t结构体的地址。

用两张图来说明这个过程：

类的realizeClass运行之前：



![1286243-2c06dbdc008c5687](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/1286243-2c06dbdc008c5687.png)

**1286243-2c06dbdc008c5687**



类的realizeClass运行之后：



![1286243-1b121fd2ea224477](OC%E5%AF%B9%E8%B1%A1%EF%BC%8Cisa%E5%92%8Csuperclass.assets/1286243-1b121fd2ea224477.png)

**1286243-1b121fd2ea224477**



细看两个结构体的成员变量会发现很多相同的地方，他们都存放着当前类的属性、实例变量、方法、协议等等。区别在于：class_ro_t存放的是编译期间就确定的；而class_rw_t是在runtime时才确定，它会先将class_ro_t的内容拷贝过去，然后再将当前类的分类的这些属性、方法等拷贝到其中。所以可以说class_rw_t是class_ro_t的超集，当然实际访问类的方法、属性等也都是访问的class_rw_t中的内容

参考：
[Objective-C runtime - 属性与方法](https://links.jianshu.com/go?to=http%3A%2F%2Fvanney9.com%2F2017%2F06%2F05%2Fobjective-c-runtime-property-method%2F)

## Ivar 详解

#### 1.Ivar

##### 1.1Ivar 的类型



```
typedef objc_ivar * Ivar;
```



```cpp
  struct objc_ivar {
     char *ivar_name;
     char *ivar_type;
     int ivar_offset;
  #ifdef __LP64__
     int space;
  #endif
  } 
```

Cpp

`Ivar`是`objc_ivar`的指针，包含变量名，变量类型等成员。

##### 1.2为类添加 Ivar

运行时规定,只能在`objc_allocateClassPair`与`objc_registerClassPair`两个函数之间为类添加变量

如下所示:



```kotlin
  //额外空间     未知,通常设置为 0
  Class clazz = objc_allocateClassPair(父类class,类名,额外空间);
  //以NSString*为例
  //变量size sizeof(NSString)
  //对齐     指针类型的为log2(sizeof(NSString*))
  //类型     @encode(NSString*)
  BOOL flag = class_addIvar(clazz,变量名,变量size,对齐,类型);
  objc_registerClassPair(clazz);
```

Kotlin

##### 1.3 Ivar的相关操作



```objectivec
  //获取Ivar的名称
  const char *ivar_getName(Ivar v);
  //获取Ivar的类型编码,
  const char *ivar_getTypeEncoding(Ivar v)
  //通过变量名称获取类中的实例成员变量
  Ivar class_getInstanceVariable(Class cls, const char *name)
  //通过变量名称获取类中的类成员变量
  Ivar class_getClassVariable(Class cls, const char *name)
  //获取指定类的Ivar列表及Ivar个数
  Ivar *class_copyIvarList(Class cls, unsigned int *outCount)
  //获取实例对象中Ivar的值
  id object_getIvar(id obj, Ivar ivar) 
  //设置实例对象中Ivar的值
  void object_setIvar(id obj, Ivar ivar, id value)
```

Objectivec

##### 1.4 Ivar的使用



```objectivec
  //在运行时创建继承自NSObject的People类
  Class People = objc_allocateClassPair([NSObject class], "People", 0);
  //添加_name成员变量
  BOOL flag1 = class_addIvar(People, "_name", sizeof(NSString*), log2(sizeof(NSString*)), @encode(NSString*));
  if (flag1) {
      NSLog(@"NSString*类型  _name变量添加成功");
  }
  //添加_age成员变量
  BOOL flag2 = class_addIvar(People, "_age", sizeof(int), sizeof(int), @encode(int));
  if (flag2) {
      NSLog(@"int类型 _age变量添加成功");
  }
  //完成People类的创建
  objc_registerClassPair(People);
  unsigned int varCount;
  //拷贝People类中的成员变量列表
  Ivar * varList = class_copyIvarList(People, &varCount);
  for (int i = 0; i<varCount; i++) {
      NSLog(@"%s",ivar_getName(varList[i]));
  }
  //释放varList
  free(varList);
  //创建People对象p1
  id p1 = [[People alloc]init];
  //从类中获取成员变量Ivar
  Ivar nameIvar = class_getInstanceVariable(People, "_name");
  Ivar ageIvar = class_getInstanceVariable(People, "_age");
  //为p1的成员变量赋值
  object_setIvar(p1, nameIvar, @"张三");
  object_setIvar(p1, ageIvar, @33);
  //获取p1成员变量的值
  NSLog(@"%@",object_getIvar(p1, nameIvar));
  NSLog(@"%@",object_getIvar(p1, ageIvar));
```

Objectivec

#### 2. Property

##### 2.1 objc_property_t 与 objc_property_attribute_t类型



```
typedef struct objc_property *objc_property_t;
```



```cpp
  //特性
  typedef struct {
      const char *name;           //特性名称
      const char *value;          //特性的值
  } objc_property_attribute_t;
```

Cpp

**特性相关编码**
属性的特性字符串 以 T@encode𝑡𝑦𝑝𝑒type 开头, 以 V实例变量名称 结尾,中间以特性编码填充,通过`property_getAttributes`即可查看

特性编码 具体含义
R readonly
C copy
& retain
N nonatomic
G𝑛𝑎𝑚𝑒name getter=𝑛𝑎𝑚𝑒name
S𝑛𝑎𝑚𝑒name setter=𝑛𝑎𝑚𝑒name
D @dynamic
W weak
P 用于垃圾回收机制

##### 2.2 为类添加Property



```cpp
  BOOL class_addProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)
```

Cpp

##### 2.3 Property的相关操作



```cpp
  //替换类中的属性
  void class_replaceProperty(Class cls, const char *name, const objc_property_attribute_t *attributes, unsigned int attributeCount)
  //获取类中的属性
  objc_property_t class_getProperty(Class cls, const char *name)
  //拷贝类中的属性列表
  objc_property_t *class_copyPropertyList(Class cls, unsigned int *outCount)
  //获取属性名称
  const char *property_getName(objc_property_t property)
  //获取属性的特性
  const char *property_getAttributes(objc_property_t property) 
  //拷贝属性的特性列表
  objc_property_attribute_t *property_copyAttributeList(objc_property_t property, unsigned int *outCount)
  //拷贝属性的特性的值
  char *property_copyAttributeValue(objc_property_t property, const char *attributeName)
```

Cpp

##### 2.4 Property的使用



```cpp
  Class People = objc_allocateClassPair([NSObject class], "People", 0);
  objc_registerClassPair(People);
  //T@
  objc_property_attribute_t attribute1;
  attribute1.name = "T";
  attribute1.value=@encode(NSString*);
  //Noatomic
  objc_property_attribute_t attribute2 = {"N",""};//value无意义时通常设置为空
  //Copy
  objc_property_attribute_t attribute3 = {"C",""};
  //V_属性名
  objc_property_attribute_t attribute4 = {"V","_name"};
  //特性数组
  objc_property_attribute_t attributes[] ={attribute1,attribute2,attribute3,attribute4};
  //向People类中添加名为name的属性,属性的4个特性包含在attributes中
  class_addProperty(People, "name", attributes, 4);
  //获取类中的属性列表
  unsigned int propertyCount;
  objc_property_t * properties = class_copyPropertyList(People, &propertyCount);
  for (int i = 0; i<propertyCount; i++) {
      NSLog(@"属性的名称为 : %s",property_getName(properties[i]));
      NSLog(@"属性的特性字符串为: %s",property_getAttributes(properties[i]));
  }
  //释放属性列表数组
  free(properties);
```


