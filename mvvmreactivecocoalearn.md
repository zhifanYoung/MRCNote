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
map方法，根据原信号创建了一个新的信号，并且变换了信号的输出值。这两个信号具有明显的先后顺序关系。而flattenMap方法，直接生成了一个新的信号，这两个信号并没有先后顺序关系，属于同层次的平行关系

