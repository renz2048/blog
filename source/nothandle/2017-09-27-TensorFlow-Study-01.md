---
layout: false
title: TensorFlow Study 01
date: 2017-09-27
categories: deeplearn
mathjax: true
update: 2017-09-27
---
**TensorFlow守则**
- 使用`图（graph）`来表示计算任务
- 在被称之为`会话（Session）`的上下文（context）中执行图
- 使用`tensor`表示数据
- 通过`变量（Variable）`维护状态
- 使用feed和fetch可以为任意的操作（arbitrary operation）赋值或者从其中获取数据

所谓守则就是不明白为什么，不一定要遵守，但是一定要会背的东西，附上2015修订版中小学生守则：
```
爱党爱国爱人民。
好学多问肯钻研。
勤劳笃行乐奉献。
明礼守法讲美德。
孝亲尊师善待人。
诚实守信有担当。
自强自律健身心。
珍爱生命保安全。
勤俭节约护家园。
```
这么一对比，是不是就明白了**TensorFlow守则**对于目前处于0系列学习阶段的我们意味着什么。

在完全不明白的情况下，用自己的话复述一遍毫无意义，接下来我就为大家复述一遍:)。

TensorFlow的一个**图**就可以代表一个计算的任务，这里的**图**很明显不是picture的含义，我们有必要附上一下**graph**的英语解释：
> a planned dawing, consisting of a line or lines, showing how two or more sets of numbers are related to each other

试着翻译成英式汉语：
> 一个计划图，包含一些线，展示给我们两个或多个数集是如何互相关联。

这个**图**就是线性图的简称了，但是最开始不明所以的时候给我造成了潜意识的理解困难。用流程图更好解释，与图画等区分开。

**会话**同样给我带来了误导，我现在倾向于认为此**Session**与TCP/IP里的**session**风马牛不相及，相不相及我说了不算，还是附上**session**的权威解释（我的**微软必应词典**是这么信心十足的告诉我的，好了，乱入什么广告，微软又不给钱）：
> 1. a period of time that is spent doing a particular activity
> 2. a formal meeting or series of meetings of a count, a parliament, etc.; a period of time when such meetings are held
> 3. a school or university year
> 4. an occasion whrn people meet to play music, especially lrish music, in a pub/bar

个人觉得后面三个是无关紧要的释意，但是论文养成的凑字数的毛病尽显无遗，博客应该简洁......

Session被强行翻译成**会话**，仿佛让我闻到了0X2D的味道。网络里的session翻译成**会话**无可厚非，意思是这个值用来从保持 a period of time that is spent doing a **talk**，抽象的理解为维持一段会话，但是TensorFlow这个Session是 a period of time that is spent doing a **count**，如此这般还要翻译成**会话**，不妥。

tensor的解释我只能照搬**权威**的解释：
> a muscle that tightens or stretches part of the body

How are you? I'm fine, fuck you!

我只能默默地看了看日渐雄起的弘二头肌，hi,tensor。

一个略显牵强的联想：`tensor->张肌->张量->张与缩->变化->变量->数据`，也不知道是不是这样理解，但是不可否认，这个tensor代表了月之阴晴圆缺，人之悲欢离合。变化需要值来量化,也就是数据。tensor数据结构用来在op剑传递，你可以将它看做一个n维的数组或列表。

tensor组成：
- rank静态类型
- shape
- type

至于Variable...日渐难以自圆其说了，上**权威**：
> a situation, number or quantity that can vary or be vried

这个数或者说量，就是用来维持状态（？）的

feed（喂）和fetch（拉取）用来在图的执行过程中赋值和获取数据。

TensorFlow守则就讲到这里了。用教程里的一句话总结：
> 一个TensorFlow图*graph*描述了计算的过程。为了计算，图必须在`Session`里被启动。`Session`将图的op分发到诸如CPU或GPU的设备上，同时提供执行op的方法。这些方法执行后，将产生的tensor返回。

接下来我们将正式进入到幼儿园阶段的学习，也就是0系列。大部分的人都和我一样，必须要将自己作为一个黑箱，不断地输入数据，这个过程只能尽力去理解，但是本质上一无所知。我们只能依靠不断地输入，来调整自己的神经通路，最终达到理解的目的。这和深度学习有异曲同工之妙。下面的内容分为两部分，构建图，执行图。做到就OK了。另外，鉴于我对这个翻译的极度不满，将对部分名词使用原生英语代替。
# 构建graph
graph的构建由op（操作节点）组成，op由输入、构造器、输出组成，当然第一个op即源op不需要任何输入。例子如下：
```
import tensorflow as tf

# 创建一个常量op，产生一个1x2矩阵
# constant应该就是一个常量构造器
matrix1 = tf.constant([[3., 3.]])
# 创建另一个常量构造器，产生一个2x1矩阵
matrix2 = tf.constant([[2.], [2.]])

# 这里创建了一个执行矩阵乘法的matmul op，上面的两个op的输出作为本op的输入
product = tf.matmul(matrix1, matrix2)
```
# 启动graph
我们已经构造好了，接下来需要执行启动。我们创建一个`Session`对象，由该对象执行操作。嗯，对象，没有错，
```
# 创建对象
sess = tf.Session()

# 调用run触发op的执行
result = sess.run(product)
print result

# 任务完成，关闭Session
sess.close()
```
`run`触发了三个op并发执行，这点耐人寻味，不知是否是制作教程的人理解错误还是TensorFlow果有此能耐，毕竟第三个矩阵乘法在没有取得输入的情况下如何执行，如果真的是这种情况，那么就应了郭光灿院士的话，量子计算机将给深度学习带来突破瓶颈的进展，因为量子计算机的多态计算很适合此并发。
# 交互式Session
上面的例子我们使用Session对象来启动garph，面向对象编程大家都有经验
下面的方法使用`InteractiveSession`代替`Session`类，使用`Tensor.eval()`和`Operation.run()`方法代替`Session.run()`，这么做据说是为了便于用于IPython之类的交互环境：
```
# 进入一个交互式 TensorFlow 会话.
import tensorflow as tf
sess = tf.InteractiveSession()

x = tf.Variable([1.0, 2.0])
a = tf.constant([3.0, 3.0])

# 使用初始化器 initializer op 的 run() 方法初始化 'x' 
x.initializer.run()

# 增加一个减法 sub op, 从 'x' 减去 'a'. 运行减法 op, 输出结果 
sub = tf.sub(x, a)
print sub.eval()
```
