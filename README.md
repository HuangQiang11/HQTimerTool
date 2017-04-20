# HQTimerTool
解决NSTimer造成的内存泄露</br>
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
