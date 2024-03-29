
bili学习视频，源码分享：
https://www.bilibili.com/video/BV1uT4y1R75U/?spm_id_from=333.337.search-card.all.click&vd_source=238ae623fc8447b840a509ffd36fb24e

入门秘籍
https://rust-book.junmajinlong.com/ch100/02_understand_tokio_task.html

tokio是rust中使用最广泛的runtime，它性能高、功能丰富、便于使用，是使用Rust实现高并发不可不学的一个框架。

在学习tokio之前，当然是在cargo.toml中引入tokio。
```toml
// 开启全部功能的tokio，
// 在了解tokio之后，只开启需要的特性，减少编译时间，减小编译大小
tokio = {version = "1.4", features = ["full"]}
```

# 1. runtime
在Rust中，"runtime"是指运行时环境（runtime environment），它是一种软件框架，提供了在程序运行时所需的支持和服务。Rust是一种系统级编程语言，设计初衷是为了提供高性能和内存安全，因此它没有内建的运行时系统。与其他一些编程语言（如Java和C#）不同，Rust的运行时环境相对较小，主要由标准库提供的一些功能组成。
Rust的运行时环境主要包括：
内存分配器（Allocator）：Rust标准库提供了用于动态内存分配和管理的接口，例如Box、Vec等类型，这些类型使用的内存分配器是运行时环境的一部分。Rust默认使用的是系统分配器，但也可以自定义分配器。
线程调度（Thread scheduling）：Rust支持多线程编程，并提供了标准库中的thread模块，用于创建和管理线程。线程的调度和切换是由运行时环境负责的。
异常处理（Exception handling）：Rust使用panic和Result类型来处理异常情况。当发生panic时，运行时环境负责处理并提供相关的错误信息。
需要注意的是，Rust的运行时环境相对较小，这是为了保持语言的性能和安全性。在某些情况下，可能需要使用其他库或框架来提供更全面的运行时支持，例如Tokio用于异步编程、Rocket用于Web开发等。


# 2. tokio

## 2.1. 学习参考资料

tokio官方：
<https://tokio-zh.github.io/document/going-deeper/runtime-model.html>

Rust入门秘籍
<https://rust-book.junmajinlong.com/ch100/03_use_tokio_time.html#%E4%BB%BB%E5%8A%A1%E8%B6%85%E6%97%B6-tokiotimetimeout>

知乎：深入浅出Rust异步编程之Tokio
<https://zhuanlan.zhihu.com/p/107820568>

[揭开Rust Tokio的神秘面纱](https://www.zhihu.com/search?type=content&q=%E6%8F%AD%E5%BC%80Rust%20Tokio%E7%9A%84%E7%A5%9E%E7%A7%98%E9%9D%A2%E7%BA%B1)

## 2.2. 框架

tokio是一个基于Rust的异步编程框架，它提供了一组异步I/O、定时器和任务调度原语。tokio构建在rust的异步运行时（async runtime）之上，使用了非阻塞I/O和基于事件驱动的编程模型，使得开发者可以方便地编写高效异步代码。以下是一些Tokio框架的核心概念和组件：

### 2.2.1. 异步任务（async task）：

在tokio框架中，所有的操作都是以异步任务的形式运行的。异步任务由async/await语法定义，可以在其中使用await关键字等待其他异步操作的完成。

### 2.2.2. 异步I/O：

tokio提供了一组异步IO原语，包括异步读写文件、网络IO等，这些原语使用非阻塞的方式进行IO操作，从而允许在等待IO完成时继续执行其他任务，提高了程序的并发性能。

### 2.2.3. 定时器（Timers）：

tokio提供了定时器功能，可以创建延迟执行的任务或者周期性执行的任务。这使得开发者可以方便的处理定时任务，例如超时任务、定时任务调度。

### 2.2.4. 任务调度器（task scheduler）：

tokio使用一个任务调度器来管理异步任务的执行。任务调度器负责协调任务执行的执行顺序、资源分配等。开发者只需要将异步任务提交给任务调度器，由它来执行和调度。

### 2.2.5. 运行时（runtime）：

tokio异步运行时是tokio核心部分，它负责创建和管理异步任务的执行上下文。开发者可以创建tokio运行时来执行异步任务，并且可以配置运行时的参数，例如线程池的大小、定时器的精读等。

使用 Tokio 框架可以极大地简化异步编程的复杂性，提高代码的可读性和可维护性。通过 Tokio，开发者可以利用 Rust 的强类型和内存安全特性，编写高效、可靠的异步应用程序。

        +-----------------+
        |    Task Pool    |
        +-----------------+
               |       ^
               v       |
        +-----------------+
        |   Task Queue    |
        +-----------------+
               |       ^
               v       |
        +-----------------+
        |   Timer Wheel   |
        +-----------------+

Task Pool（任务池）：负责管理和调度异步任务的执行。它维护了一组线程（或者线程池），可以在这些线程上运行异步任务。任务池根据可用的线程资源和任务的优先级来分配任务执行的顺序。

Task Queue（任务队列）：保存待执行的异步任务。当任务池中的线程空闲时，它会从任务队列中获取任务并将其分配给空闲的线程执行。任务队列可以是单个队列，也可以是多个队列，具体取决于任务的类型和调度策略。

Timer Wheel（定时器轮盘）：用于管理定时器任务的触发。定时器轮盘将定时任务按照时间划分到不同的槽位中，每个槽位代表一个时间间隔。当时间流逝时，定时器会检查每个槽位中的定时任务是否到期，如果到期则触发相应的操作。
这些组件共同协作，使得 Tokio 能够高效地管理和调度异步任务的执行。任务池负责管理线程资源，将任务从任务队列中分配给线程执行。定时器轮盘则负责触发定时任务的执行，例如超时处理、定时任务调度等。这些组件的组合使得 Tokio 能够实现高性能的异步编程。
请注意，这只是一个简化的示意图，真实的 Tokio 框架中还有更多的细节和组件。

## 2.3. 优点

零成本开销：与其他语言的Future不同，Tokio的Future编译成状态机。用Future实现常见的同步，分配或其他不会增加额外开销成本。
并发：开箱即用，Tokio提供了一个多线程，工作窃取的调度程序。因此，当您使用tokio :: run开始使用应用程序时，您已经在使用所有计算机的CPU内核。
非阻塞IO：当访问网络时，Tokio将使用操作系统可用的最有效系统。在Linux上，这意味着epoll。这允许在单个线程上多路复用许多套接字并批量接收操作系统通知，从而减少系统调用。所有这些都可以减少应用程序的开销。

## 2.4. future

future是rust异步编程的核心。首先我们介绍什么是future。future是一段异步计算程序，可以在将来获取产生的数据。举例来说，获取数据库查询结果，RPC调用这些实际上都可以使用future来实现。通常实现future有两种模式，一种基于推模式，也被称为基于完成的模式，一种基于拉模式，也被称为基于就绪的模式。Rust的future库实现了基于拉模式的future。






# 补充
tokio 和 crossbeam 都是 Rust 生态系统中的库，用于处理并发和异步编程。
tokio 是一个用于构建异步应用程序的运行时（runtime）和工具集。它提供了异步任务调度、网络编程、文件操作等功能，使得编写高效的、非阻塞的异步代码变得更加简单。tokio 的设计目标是实现高性能的异步 I/O，使得应用程序能够充分利用现代计算机的多核处理器和异步 I/O 设备。
crossbeam 则是一个用于并发编程的库，提供了各种基于原语的并发构建块，例如通道、锁、原子操作等。它的目标是提供简单、高效、安全的并发原语，使得编写并发代码更加容易和可靠。
虽然 tokio 和 crossbeam 都与并发和异步编程相关，但它们的关注点略有不同。tokio 更注重于异步 I/O 和任务调度，而 crossbeam 则专注于提供并发原语。实际上，你可以在 tokio 中使用 crossbeam 提供的并发原语来构建更复杂的并发应用程序，以充分发挥 tokio 和 crossbeam 的优势。
总而言之，tokio 是一个用于构建异步应用程序的运行时和工具集，而 crossbeam 则是一个提供并发原语的库。它们可以在某些场景下一起使用，以帮助你构建高效且可靠的并发和异步应用程序。