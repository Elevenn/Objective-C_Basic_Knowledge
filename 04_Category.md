# Category

* **Category 的使用场合是什么？**
  * 当类的属性方法太多时
* **Category 的实现原理**
  * Category 编译后的底层结构是 struct category\_t，里面装有对象方法、类方法、属性、协议信息
  * 在程序运行时 runtime 会把 category 里的数据整合到类信息中去（类对象 元类对象中）
* **Category 和 Class Extension 的区别是什么？**
  * Class Extension 是在编译时 它的数据就已经包含在类信息中去了
  * Category 是程序在运行时把数据合并到类信息中去
* **Category 中有 load 方法吗？load 方法是什么时候调用的？load 方法能继承吗？**
  * 有 load 方法
  * load 方法在 runtime 加载类、分类的时候调用
  * load 方法可以继承，但是一般不会去主动调用，都是让系统自动调用
* **load initialize 方法的区别是什么？它们在 category 中的调用顺序？以及出现继承时他们之间的调用过程？**
  * 调用方式不同
    * load 是根据函数地址直接调用
    * initialize 是通过 objc\_msgSend 调用
  * 调用时刻也不同
    * load 方法是在 runtime 加载类分类方法是调用（只会调用一次）
    * initialize 方法是第一次接收到消息的时候调用 每一个类只会 initialize 一次（但父类的 initialize 方法可能会调用多次）
* **Category 能否添加成员变量？如果可以 如何给 Category 添加成员变量？**
  * 不能添加 但是在分类中添加成员变量只会帮你生成 getter setter 方法的声明 不会生产成员变量和 getter setter 方法的实现
  * 但是可以间接添加，通过 关联对象 来实现有成员变量的效果
    * ~~添加一个全局变量 来强行实现 getter setter 方法的实现不可行 因为是全局的 被复用的时候值会被覆盖~~
    * ~~也可以创建一个字典 来存取值 key 为 self 的内存地址 保证存的值不会被覆盖 但是还是有问题~~
      1. ~~字典是全局的~~ 
      2. ~~可能会存在线程安全问题~~
      3. ~~工作量大 每次新增新的属性要很多操作~~

![](/assets/category底层结构.png)![](/assets/Category_关联对象.png)

### + load 方法

* 该方法会在 runtime 加载类、分类时调用
* 每个类、分类的 load 方法只会在程序运行过程中调用一次
* 调用顺序
  * 先调用类的 load
    * 按照编译先后顺序调用
    * 调用子类的 load 方法之前会先调用父类的 load
  * 再调用分类的 load
    * 按照编译先后顺序调用

#### + initialize 方法

* 该方法会在类第一次接收到消息时调用
* 调用顺序
  * 先调用父类的 initialize，再调用子类的 initialze
  * 先初始化父类 再初始化子类，每个类只会初始化一次
* initialze 和 + load 很大的区别是 +initialize 是通过 objc\_msgSend 进行调用的 所以有以下特点：
  * 如果子类没有实现 + initialze 方法，会调用父类的 + initialze （父类的 + initialze 可能会被调用多次）
  * 如果分类实现了 + initialze，就会覆盖类本身的 + initialze

#### 关联对象

* 添加关联对象
  ```Objective-C
  void objc_setAssociatedObject(id _Nonnull object, const void * _Nonnull key,
                                id _Nullable value, objc_AssociationPolicy policy)
  ```
* 获得关联对象

  ```Objective-C
  id _Nullable objc_getAssociatedObject(id _Nonnull object, const void * _Nonnull key)
  ```
* 移除所有的关联对象
  ```Objective-C
  void objc_removeAssociatedObjects(id _Nonnull object)
  ```

* 关联对象并不是储存在被关联对象本身内存中的

* 关联对象储存在 runtime 维护的一个全局统一的一个 AssociationsManager 中

* 移除关联对象：传个 nil 值给关联对象



