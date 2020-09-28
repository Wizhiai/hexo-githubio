---
title: OC lock锁
top: false
cover: false
toc: true
mathjax: true
date: 2020-09-28 13:47:31
password:
summary:
tags:
categories:
---



### NSRecursiveLock trylock 认知

- 在同一个线程中多次 lock 不会造成死锁 但是可能会造成线程等待
- 如果锁在 线程A 中 lock 了 并没有解锁, 那么在其他线程如果调用了该锁除𝑡𝑟𝑦𝑙𝑜𝑐𝑘外除trylock外的方法就会造成 该线程的等待,直到 线程A的锁unlock
- 如果锁在 线程A 中 lock 了 并没有解锁, 在线程A中 trylock 返回 YES ,并再 lock 一次,此时需要增加 unlock 操作
- 如果锁在 线程A 中 lock 了 并没有解锁,在其他线程 trylock 返回NO , 如果这个线程没有调用 lock 方法 即 只调用trylock 或者 unlock 方法, 不会造成线程等待,该线程的任务会继续走下去, 造成没有 lock 的现象

> 测试代码:



```objectivec
@interface LockViewController ()
@property(nonatomic, strong) NSRecursiveLock *myLock;
@end
@implementation LockViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    _myLock = [[NSRecursiveLock alloc] init];
    [self testBlock];
}
    //加锁
- (void)lock {
    if ([_myLock tryLock]) {
        NSLog(@"%@ try lock ",[NSThread currentThread]);
        return;
    } else{
        NSLog(@"%@ try lock fail",[NSThread currentThread]);
    }
    NSLog(@"%@  lock",[NSThread currentThread]);
    [_myLock lock];
}
    //解锁
-(void)unlock {
    NSLog(@"%@  unlock",[NSThread currentThread]);
    [_myLock unlock];
}
- (void)testBlock{
    dispatch_async(dispatch_get_global_queue(0, DISPATCH_QUEUE_PRIORITY_DEFAULT), ^{
        NSThread *thread = [NSThread currentThread];
        thread.name = @"add";
        for (int i = 0; i < 2; i++) {
            [self lock];
            NSLog(@"%@  add ",[NSThread currentThread]);
            [self unlock];
        }
    });
    dispatch_async(dispatch_get_global_queue(0, DISPATCH_QUEUE_PRIORITY_DEFAULT), ^{
        NSThread *thread = [NSThread currentThread];
        thread.name = @"remove";
        for (int i = 0; i < 1; i++) {
            sleep(3);
            if ([_myLock tryLock]) {
                NSLog(@"%@   remove",[NSThread currentThread]);
            };
        }
    });
}
```

Objectivec

### trylock 的应用场景

trylock 的唯一的特性就是 **不会阻塞线程** 也就是说如果 线程A中 lock 了,但是在线程B 中可以不管锁是不是上锁状态,直接执行,此时可以用 trylock

调试辅助代码,给NSRecursiveLock增加了一个类目用runtime截获 lock 和unlock 方法 打印日志:



```objectivec
+(void)load {

    Method lock = class_getInstanceMethod(self, @selector(lock));
    Method testLock = class_getInstanceMethod(self, @selector(testLock));
    method_exchangeImplementations(lock, testLock);

    Method unlock = class_getInstanceMethod(self, @selector(unlock));
    Method testUnLock = class_getInstanceMethod(self, @selector(testUnlock));
    method_exchangeImplementations(unlock, testUnLock);
}

- (void)testLock{
    [self testLock];
    NSLog(@"testLock +++++ ");
}

- (void)testUnlock {
    [self testUnlock];
     NSLog(@"testUnlock +++++ ");
}
```


