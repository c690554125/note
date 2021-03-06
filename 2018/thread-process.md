## 进程与线程
本篇主要先了解浏览器的进程和线程，为事件循环的内容做铺垫。

## 进程
简单点理解，可以独立运行的程序一次执行的过程。看作是一个工厂，工厂之间一般都是独立的，各干各的。

## 线程
归进程管辖，也能拥有资源，独立运行。进程里可以有多个线程。看作是工厂里的工人，很多工人。

## 内存
按我自己的理解，电脑硬盘只负责存储内容，如果开个游戏，会把游戏内容读取出来放到内存里面去跑，这样我们才能玩。

## 三者关系
一个工厂（**进程**）有自己的的地盘（**分配的内存空间**），工厂里有很多工人（**线程**），工人们在同一个工厂干活，可以共享这个工厂的大部分公共空间，比如厕所，食堂，宿舍。

## 浏览器的进程
浏览器是多进程的，一个tab就是个进程，还有插件，一个激活使用的插件也占一个进程。因为进程之间相互独立，所以某一个崩了，不会影响其他的进程。

## 浏览器最重要的进程——浏览器内核
内核也是多线程的，一个tab选项卡有一个自己的内核（Chrome应该是指V8引擎）。内核有一般有如下常驻线程：
* GUI渲染线程
* Javascript引擎线程（通常说的单线程指的就是它）
* 定时器触发器线程
* 事件触发线程
* 异步Http请求线程

### GUI渲染引擎
渲染浏览器界面的HTML元素，当界面需要**重绘**和**回流**时，该线程就会执行。**该线程和Javascript引擎线程互斥。**

### Javascript引擎线程（主线程）
解释JS脚本的。通常说的JS是单线程的，就是由此而来。为啥是单线程？为了简单！假设有这么个情况（多线程），两个人（JS引擎线程假如有2个）同时拿枪指着你的脑袋（DOM元素），一个让你站着（插入），一个让你趴着（删除），你到底听谁的？**该线程和GUI渲染引擎互斥。**

>GUI渲染线程和Javascript引擎线程为什么互斥？
因为前者是渲染HTML（DOM和CSS），后者是可以操作DOM和CSS的。如果同时运行，会造成GUI渲染线程绘制元素数据前后表现不一致。所以这2个线程，一定是一个被挂起，另一个在运行。JS操作时，GUI渲染挂起，更新的内容保存在一个队列中，等JS空闲了再执行GUI渲染。

>JS会阻塞页面加载，2个线程互斥，大量且复杂的JS代码，会让JS引擎运行时间变长，导致GUI渲染挂起，一直等待JS引擎空闲才回去渲染。

### 定时触发器线程
浏览器的定时器***setTimeout***和***setInterval***，JS引擎线程并不负责定时器的计时，可想而知，JS引擎如果一直在倒计时数数，其他代码就无法运行了，阻塞整个代码的运行。事实上，定时器是异步的，就是因为JS引擎线程并没有管他们，而是交由定时触发器线程在计时。

### 事件触发线程
比如***click***事件，当点击事件触发时，该线程会把事件处理函数放到消息队列的队尾，等待JS引擎按顺序处理点击事件的回调函数。
>个人以为是只响应DOM事件，不过看网上大多数文章（基本都是同样内容copy，没啥深入的），时间出发线程还管辖了ajax事件和定时器事件的触发。有个疑问，已经有了定时触发器线程和异步Http请求线程，为啥事件触发线程还会跟ajax事件和定时器事件挂钩？（8月17日自答，考虑下Node里面的Event核心模块，Node是基于事件的，其他模块基本都继承了Event模块。类比这里，ajax事件和定时器事件也应该交由（继承自）事件触发线程来统一处理）

### 异步http请求线程
顾名思义，发送XMLHttpRequest连接的，连接发出后，浏览器新开一个线程来管理发出的请求，当请求回来或者挂掉，状态变更，如果设置了回调函数，该线程就会产生状态变更事件放到JS引擎的处理队列的队尾等待处理。
