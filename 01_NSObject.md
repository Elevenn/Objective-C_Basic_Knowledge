# NSObject 对象

* **一个 NSObject 对象占用多少内存？**

  * 系统分配了16个字节给 NSObject 对象 \(可以通过 malloc\_size 函数查看\)
  * 64位环境下 NSObject 内部只使用了8个字节 \(可以通过class\_getInstanceSize函数查看\)

  ---

* **对象的 isa 指针指向哪里？**

  * instance 对象的 isa 指针指向它的 class 对象
  * class 对象的 isa 指针指向它的 mate-class 对象
  * mate-class 对象的 isa 指针指向它的 基类 mate-class 对象
  * 基类的 mate-class 对象 isa 指针指向它自己

  ---

* **OC 的类信息存放在哪里？**

  * 对象方法、属性、成员变量、协议信息 存放在 class 对象里
  * 类方法 存放在 mate-class 对象里
  * 成员变量的具体值存放在 instance 对象里

```Objective-C
Student *stu = [[Student alloc] init];

#import <objc/runtime.h>
NSLog(@"实际使用了 %zu 个内存空间", class_getInstanceSize([Student class]));

#import <malloc/malloc.h>
NSLog(@"创建一个实例对象 实际分配了 %zu 个内存空间", malloc_size((__bridge const void *)(stu)));
```

---

#### OC 对象的分类

* instance 对象在内存中装有
  * **isa** 指针  
  * 其他**成员变量**
* class 对象在内存中装有 
  * **isa** 指针 
  * **superclass** 指针 
  * 类的**属性**信息（@property）
  * 类的**对象方法**信息（instance method）
  * 类的**协议**信息（protocol）
  * 类的**成员变量**信息（ivar）
  * ......
* meta-class 对象和 class 对象内部结构是一样的 但是用途不一样 其在内存中储存的信息主要包括
  * **isa **指针
  * **superclass** 指针
  * 类的**类方法**信息（class method）
  * ......
* #### isa 指针
* instance 的 isa 指向 class

  * 当调用对象方法时 会先通过 instance 的 isa 来找到 class 对象，最后找到对象方法的实现进行调用

* class 的 isa 指向 meta-class

  * 当调用类方法时 会先通过 class 的 isa 指针找到 meta-class 对象，最后找到类方法的

![](/assets/对象的isa和superclass指向.png)

* intance 的 isa 指向 class
* class 的 isa 指向 meta-class
* meta-class 的 isa 指向基类的 meta-class
* 基类的 meta-class 的 isa 指向它自己

* class 的 superclass 指针指向父类的 class

  * 如果 class 没有父类 则指向 nil

* meta-class 的 superclass 指针指向父类的 meta-class

  * 基类 meta-class 的 superclass 指针 指向 基类的 class

* instance 调用方法的轨迹

  * isa 找到 class 方法不存在 就调用 superclass 方法去父类方法里去找

* class 调用类方法的轨迹

  * isa 找 meta-class 方法不存在 就通过 superclass 找父类

#### Class 结构



