layout: Objective-C-Grammar

title: iOS Objective-C语法的一些理解(一)

date: 2020-7-13 00:38:02

tags: OC-Grammar

---

[TOC]

### 1. 对象的isa指针指向哪里？

- `instance`对象的`isa``指向class`对象
- `class`对象的`isa`指向`meta-class`(元类)对象
- `meta-class`对象的isa指向基类的`meta-class`对象



<!-- more -->

### 2. iOS用什么方式实现对一个对象的KVO/KVO的本质是什么

- 利用`Runtime`的`API`动态生成一个子类，并且让`instance`对象的`isa`指向这个全新的子类
- 当修改`instance`对象的属性时，会调用`Foundation`的`_NSSetXXXValueAndNotify`函数

- 内部会触发监听器（Oberser）的监听方法( observeValueForKeyPath:ofObject:change:context:）

> _NSSetXXXValueAndNotify内部做了什么?
>
> 1. willChangeValueForKey:
> 2. 父类原来的setter
> 3. didChangeValueForKey:



### 3. 如何手动触发KVO

- 手动调用willChangeValueForKey:和didChangeValueForKey:

### 4. Category的实现原理

- Category编译之后的底层结构是`struct` `category_t`，里面存储着分类的对象方法、类方法、属性、协议信息
- 在程序运行的时候，`runtime`会将`Category`的数据，合并到类信息中（类对象、元类对象中）

### 5. Category和Class Extension的区别是什么？

- `Class Extension`在编译的时候，它的数据就已经包含在类信息中
- `Category`是在运行时，才会将数据合并到类信息中

### 6. Category中有load方法吗？load方法是什么时候调用的？load 方法能继承吗？

- 有`load`方法
- `load`方法在`runtime`加载类、分类的时候调用, 也就是说是在`main`函数之前调用
- `load`方法可以继承，但是一般情况下不会主动去调用`load`方法，都是让系统自动调用

### 7. load、initialize方法的区别什么？它们在category中的调用的顺序？以及出现继承时他们之间的调用过程？

- 调用方式, load是根据函数地址直接调用, initalize是通过obje_msgSend调用
- 调用时刻, load是运行时调用, runtime加载类, 分类的时候, 只调用一次; initalize是类第一次接收到消息的时候调用, 每个类的initalize只会调用一次, 父类的initalize可能被调用多次
- 调用顺序, load: 先调用类的load, 先编译的类优先调用, `父类优先于(>)子类优先于(>)分类` initalize : 父类>子类

### 8. ISA指针



![屏幕快照 2020-07-13 00.17.00](https://tva1.sinaimg.cn/large/007S8ZIlly1ggonakm4ajj318e0giq51.jpg)

#### 8.1 instance的isa指向class

- 当调用对象方法时，通过instance的isa找到class，最后找到对象方法的实现进行调用

#### 8.2 class的isa指向meta-class

- 当调用类方法时，通过class的isa找到meta-class，最后找到类方法的实现进行调用



### 9. class对象的superclass指针

![屏幕快照 2020-07-13 00.21.01](https://tva1.sinaimg.cn/large/007S8ZIlly1ggoneka2hfj31aw0jcae9.jpg)

当Student的instance对象要调用Person的对象方法时，会先通过isa找到Student的class，然后通过superclass找到Person的class，最后找到对象方法的实现进行调用



### 10.meta-class对象的superclass指针

![图片](https://tva1.sinaimg.cn/large/007S8ZIlly1ggonh3gh3xj31ai0hqdil.jpg)

当Student的class要调用Person的类方法时，会先通过isa找到Student的meta-class，然后通过superclass找到Person的meta-class，最后找到类方法的实现进行调用

### 11. isa、superclass总结概括

- instance的isa指向class
- class的isa指向meta-class
- meta-class的isa指向基类的meta-class
- class的superclass指向父类的class, 如果没有父类，superclass指针为nil
- meta-class的superclass指向父类的meta-class, 基类的meta-class的superclass指向基类的class
- instance调用对象方法的轨迹, isa找到class，方法不存在，就通过superclass找父类
- class调用类方法的轨迹, isa找meta-class，方法不存在，就通过superclass找父类

![屏幕快照 2020-07-13 00.26.13](https://tva1.sinaimg.cn/large/007S8ZIlly1ggonkne2auj30mk0nctc0.jpg)

### 12. 类, 元类对象的本质

class、meta-class对象的本质结构都是struct objc_class

![屏幕快照 2020-07-13 00.30.14](https://tva1.sinaimg.cn/large/007S8ZIlly1ggono6htylj31ca0mwk4w.jpg)

### 13. KVC

KVC: KVC的全称是Key-Value Coding，俗称“键值编码”，可以通过一个key来访问某个属性

常见的API有: 

```objective-c
- (void)setValue:(id)value forKeyPath:(NSString *)keyPath;
- (void)setValue:(id)value forKey:(NSString *)key;
- (id)valueForKeyPath:(NSString *)keyPath;
- (id)valueForKey:(NSString *)key; 

```

#### 13.1 setValue:forKey:的原理

![屏幕快照 2020-07-13 00.34.27](https://tva1.sinaimg.cn/large/007S8ZIlly1ggonsj3ndfj31c20js0w5.jpg)

accessInstanceVariablesDirectly方法的默认返回值是YES

#### 13.2 valueForKey:的原理

![屏幕快照 2020-07-13 00.36.09](https://tva1.sinaimg.cn/large/007S8ZIlly1ggonucmmkoj31cg0jwwhv.jpg)

[https://opensource.apple.com/tarballs/objc4/](https://opensource.apple.com/tarballs/objc4/)

待续~