# 1. 了解OC的起源

- OC是在C语言基础上添加了面向对象特性的语言，是其超集
- 消息结构型语言（OC）和函数调用型语言（C++、Java）的关键区别：前者运行时所应该执行的代码由运行环境决定；后者由编译器决定
- OC重要工作都由“运行期组件”（包含使用OC的面向对象特性所需的全部数据结构和函数）完成而非编译器。举例来说，运行期组件中包含全部的内存管理，本质上是一种与开发者所编代码相连接的“动态库”，其代码能把开发者所编写的所有程序粘合起来。这样一来，只需要更新运行期组件就可以提升程序性能；而那些许多工作都在编译期完成的语言，若想获得类似的提升，则需要重新编译程序
- 所有的OC对象(可以粗略的理解为带 * 的变量)所占的内存总是分配在“堆”上，而绝不会分配在“栈”上；而不带 * 的变量可能会使用栈空间，这些变量保存的不是OC对象，比如CGRect。结构体开销比对象小（对象还要考虑内存的分配和释放），如果只需保存int、float等“非对象对类型”，优先使用结构体
- C语言中内存和指针的部分概念在OC中得到了传承，如“栈中存放指针，指向堆内的实例”，要注意OC中的指针是用来指示对象的。但OC存在新的内存管理结构：引用计数

# 2. 在类的头文件中尽量少引用其他头文件

- 在.h中使用@class(向前声明)尽量延后引入（#import）头文件的时机，可减少编译时间
- @class也可以解决两个类相互引用导致无法编译的问题，使用#import而非#include虽然不会导致死循环，但是却意味着两个类中有一个无法被正确编译
- 无法使用类的前置声明（如遵循协议），使用“class-continuation 分类”

# 3. 多用字面量语法

- 使用字面量语法来创建字符串、数值、数组、字典，做到简明扼要。好处：代码易写易读易理解

  字面数值、表达式

  ```objective-c
  //字面数值
  NSNumber *intNumber = @1;
  NSNumber *intNumber = @2.5f;
  NSNumber *intNumber = @3.1415926；
  NSNumber *intNumber = @YES；
  NSNumber *intNumber = @'a';
  //字面表达式
  int x = 5;
  float y = 6.32f;
  NSNumber *expressionNumber = @(x * y);
  ```

  字面量数组

  ```objective-c
  //创建
  NSArray *animals = @[@"cat",@"dog",@"mouse",@"badger"];
  //访问
  NSString *dog = animals[1];
  ```

  字面量字典

  ```objective-c
  //创建
  NSDictionary *personData = @{@"firstName" : @"Matt" , @"lastName" : @"Galloway" , @"age" : @28};
  //访问
  NSString *lastName = personData[@"lastName"];
  ```

  可变数组与字典

  ```objective-c
  //修改元素内容
  mutableArray[1] = @"dog";
  mutableDictionary[@"lastName"] = @"Galloway";
  ```

- 字面量语法创建的字符串、数组、字典对象都是不可变的，想要可变版本需要复制

  ```objective-c
  NSMutableArray *mutable = [@[@1, @2, @3, @4, @5]mutableCopy];
  ```

- 字面量语法创建数组或字典时，务必确保值里不含nil，否则会抛出异常

# 4. 多用类型常量，少用#define预处理指令

- 类型常量包含类型信息，编译器会进行类型检查，要优于预处理指令

  ```cpp
  //预处理指令
  #define kAmimationDuration 1.0
  //类型常量
  static const NSTimeInterval kAmimationDuration = 1.0;
  ```

- 如果局限于编译单元之内，则在变量前加k（kAmimationDuration）；如果在类之外可见，以类名为前缀（ClassNameAmimationDuration）

- 对类之外要暴露,用extern申明为全局变量

  ```objectivec
  //.h
  extern const NSTimeInterval ClassNameAmimationDuration;
  //.m
  const NSTimeInterval ClassNameAmimationDuration = 1.0;
  ```

  # 5. 对于选项配置多用枚举

  - 定义枚举变量

    ```objectivec
    enum EOCConnectionState {
        
    }
    ```

  - ```objectivec
    //  单选
    typedef NS_ENUM(NSInteger, UIViewAnimationTransition) {
        UIViewAnimationTransitionNone,
        UIViewAnimationTransitionFlipFromLeft,
        UIViewAnimationTransitionFlipFromRight,
        UIViewAnimationTransitionCurlUp,
        UIViewAnimationTransitionCurlDown,
    };
    
    //  多选
    typedef NS_OPTIONS(NSUInteger, UIViewAutoresizing) {
        UIViewAutoresizingNone                 = 0,
        UIViewAutoresizingFlexibleLeftMargin   = 1 << 0,
        UIViewAutoresizingFlexibleWidth        = 1 << 1,
        UIViewAutoresizingFlexibleRightMargin  = 1 << 2,
        UIViewAutoresizingFlexibleTopMargin    = 1 << 3,
        UIViewAutoresizingFlexibleHeight       = 1 << 4,
        UIViewAutoresizingFlexibleBottomMargin = 1 << 5
    };
    ```
