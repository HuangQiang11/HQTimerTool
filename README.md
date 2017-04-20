# HQTimerTool
NSTimer造成的内存泄露的解决方案</br></br>
HWWeakTimer使用block创建NStimer时，必须要在vc的dealloc方法中调用NSTimer对象的invalidate方法，否则会一直执行NSTimer绑定的事件，占用内存。</br></br>
本例利用__weak的安全性，当被修饰的vc对象被释放时，对象会等于nil，从而根据此来判断vc是否被释放，如果vc被释放后就会调用NSTimer对象的invalidate方法。</br></br>
主要代码：
```
+ (NSTimer *)hTimerWithTimeInterval:(NSTimeInterval)interval repeats:(BOOL)repeats target:(id)target timerBlock:(TimerBlock)timerBlock{
    __weak typeof(target) weakTarget = target;
    TimerBlock block = ^(NSTimer * t){
        if (weakTarget) {
            timerBlock(nil);
        }else{
            [t invalidate];
        }
    };
    
    NSTimer * timer = [NSTimer scheduledTimerWithTimeInterval:interval target:self selector:@selector(timerAction:) userInfo:[block copy] repeats:repeats];
    return timer;
}

+ (void)timerAction:(NSTimer *)timer{
    TimerBlock block = timer.userInfo;
    if (block) {
        block(timer);
    }
}

```
