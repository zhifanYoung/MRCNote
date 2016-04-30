# MVVMReactiveCocoaLearn

## 
viewModel 的主要职责就是从 model 层获取 view 所需的数据，并且将这些数据转换成 view 能够展示的形式.

## 
信号源
* RACStream
* RACSignal
* RACSubject
* RACSequence
 
订阅者
* RACSubscriber
* RACMulticastConnection 

调度器
* RACScheduler 

清洁工
* RACDisposable

ReactiveCocoa 主要由以下四大核心组件构成：
* 
信号源：RACStream 及其子类；
* 
订阅者：RACSubscriber 的实现类及其子类；
* 
调度器：RACScheduler 及其子类；
* 
清洁工：RACDisposable 及其子类。

## 
RACSignal 可以向订阅者发送三种不同类型的事件：

* 
next ：RACSignal 通过 next 事件向订阅者传送新的值，并且这个值可以为 nil ；
* 
error ：RACSignal 通过 error 事件向订阅者表明信号在正常结束前发生了错误；
* 
completed ：RACSignal 通过 completed 事件向订阅者表明信号已经正常结束，不会再有后续的值传送给订阅者。
* 
注意，ReactiveCocoa 中的值流只包含正常的值，即通过 next 事件传送的值，并不包括 error 和 completed 事件，它们需要被特殊处理。通常情况下，一个信号的生命周期是由任意个 next 事件和一个 error 事件或一个 completed 事件组成的。

## 
* 
map flattenMap 用于把信号源内容映射为新的信号源
* 
map方法，根据原信号创建了一个新的信号，并且变换了信号的输出值。这两个信号具有明显的先后顺序关系。而flattenMap方法，直接生成了一个新的信号，这两个信号并没有先后顺序关系，属于同层次的平行关系

## 
ReactiveCocoa开发中常见用法。
* 
7.1 代替代理:

    rac_signalForSelector：用于替代代理。
* 
7.2 代替KVO :

  rac_valuesAndChangesForKeyPath：用于监听某个对象的属性改变。
* 
7.3 监听事件:

    rac_signalForControlEvents：用于监听某个事件。
* 
7.4 代替通知:

   rac_addObserverForName:用于监听某个通知。
* 
7.5 监听文本框文字改变:

   rac_textSignal:只要文本框发出改变就会发出这个信号。
* 
7.6 处理当界面有多次请求时，需要都获取到数据时，才能展示界面

  rac_liftSelector:withSignalsFromArray:Signals:当传入的Signals(信号数组)，每一个signal都至少sendNext过一次，就会去触发第一个selector参数的方法。
使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。

### 

```
代码示例
// 1.代替代理
    // 需求：自定义redView,监听红色view中按钮点击
    // 之前都是需要通过代理监听，给红色View添加一个代理属性，点击按钮的时候，通知代理做事情
    // rac_signalForSelector:把调用某个对象的方法的信息转换成信号，就要调用这个方法，就会发送信号。
    // 这里表示只要redV调用btnClick:,就会发出信号，订阅就好了。
    [[redV rac_signalForSelector:@selector(btnClick:)] subscribeNext:^(id x) {
        NSLog(@"点击红色按钮");
    }];

    // 2.KVO
    // 把监听redV的center属性改变转换成信号，只要值改变就会发送信号
    // observer:可以传入nil
    [[redV rac_valuesAndChangesForKeyPath:@"center" options:NSKeyValueObservingOptionNew observer:nil] subscribeNext:^(id x) {

        NSLog(@"%@",x);

    }];

    // 3.监听事件
    // 把按钮点击事件转换为信号，点击按钮，就会发送信号
    [[self.btn rac_signalForControlEvents:UIControlEventTouchUpInside] subscribeNext:^(id x) {

        NSLog(@"按钮被点击了");
    }];

    // 4.代替通知
    // 把监听到的通知转换信号
    [[[NSNotificationCenter defaultCenter] rac_addObserverForName:UIKeyboardWillShowNotification object:nil] subscribeNext:^(id x) {
        NSLog(@"键盘弹出");
    }];

    // 5.监听文本框的文字改变
   [_textField.rac_textSignal subscribeNext:^(id x) {

       NSLog(@"文字改变了%@",x);
   }];

   // 6.处理多个请求，都返回结果的时候，统一做处理.
    RACSignal *request1 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {

        // 发送请求1
        [subscriber sendNext:@"发送请求1"];
        return nil;
    }];

    RACSignal *request2 = [RACSignal createSignal:^RACDisposable *(id<RACSubscriber> subscriber) {
        // 发送请求2
        [subscriber sendNext:@"发送请求2"];
        return nil;
    }];

    // 使用注意：几个信号，参数一的方法就几个参数，每个参数对应信号发出的数据。
    [self rac_liftSelector:@selector(updateUIWithR1:r2:) withSignalsFromArray:@[request1,request2]];


}
// 更新UI
- (void)updateUIWithR1:(id)data r2:(id)data1
{
    NSLog(@"更新UI%@  %@",data,data1);
}```



