# KVO

* **iOS 用什么方式实现对一个对象的 KVO ？（KVO 的本质是什么）**

  * runtime 会创建一个全新的类 让对象的 instance 的 isa 指向它
    * 新创建的这个前缀是 NSKvoNotifying\_ 开头的类是该对象的子类
  * 当属性被改变的时候会出发 Foundation 的 \_NSSetIntValueNotify 的函数
  * 调用 willChangeValueForKey:
  * 调用父类原来的 setter 方法
  * 调用 didChangeValueForKey:
  * 内部监听器会调用 observeValueForKey:ofValue:change:context: 方法

* **如何手动触发 KVO？**  
  * 手动调用 willChangeValueForKey: 和 didChangeValueForKey:

* **直接修改成员变量会触发 KVO 吗？**  
  * 不会触发 KVO

```Objective-C
- (void)viewDidLoad {
    [super viewDidLoad];

    self.person = [[XXPerson alloc] init];

    self.person.age = 10;

    [self.person addObserver:self 
                  forKeyPath:@"age" 
                     options:NSKeyValueObservingOptionOld | NSKeyValueObservingOptionNew 
                     context:nil];
}

- (void)observeValueForKeyPath:(NSString *)keyPath 
                      ofObject:(id)object 
                        change:(NSDictionary<NSKeyValueChangeKey,id> *)change 
                       context:(void *)context {
    NSLog(@"keyPath -> %@, object -> %@, change -> %@, content -> %@", keyPath, object, change, context);
}
```

![](/assets/code_KVO_基本使用.png)

