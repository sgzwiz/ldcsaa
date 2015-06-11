**最新版本请到 [GitHub](https://github.com/ldcsaa) 或 [OSChina](http://git.oschina.net/ldcsaa) 下载**


---


> # HP-Socket #
![http://bcs.duapp.com/jessma/blog/201306/Project-HP-Socket.png](http://bcs.duapp.com/jessma/blog/201306/Project-HP-Socket.png)
> HP-Socket 是一套通用的高性能 TCP/UDP Socket 组件，包含服务端组件、客户端组件和 Agent 组件，广泛适用于各种不同应用场景的 TCP/UDP 通信系统，提供 C/C++、C#、Delphi、E、Java 等编程语言开发接口。HP-Socket 对通信层实现完全封装，上层应用不必关注通信层的任何细节；HP-Socket 提供基于事件通知模型的 API 接口，能非常简单高效地整合到新旧应用程序中。为了让使用者能方便快速地学习和使用 HP-Socket，迅速掌握组件的设计思想和使用方法，特此精心制作了大量 Demo 示例，包括 PUSH 模型示例、PULL模型示例和性能测试示例等。HP-Socket 目前运行在 Windows 平台，将来会实现跨平台支持。

> ### 通用性 ###

  * 通信组件的唯一职责就是接受和发送字节流，绝对不能参与上层协议解析等工作；
  * 与上层使用者解耦、互不依赖，组件与使用者通过操作接口和监听器接口进行交互，组件实现操作接口为上层提供操作方法；使用者实现监听器接口把自己注册为组件的 Listener，接收组件通知。因此，任何使用者只要实现了监听器接口都可以使用组件；另一方面，甚至可以自己重新写一个实现方式完全不同的组件实现给使用者调用，只要该组件遵从组件的操作接口，这也是 DIP 设计原则的体现。

> ### 可用性 ###

> 可用性对所有通用组件都是至关重要的，如果太难用还不如自己重头写一个来得方便。因此，组件的操作接口和监听器接口设计得尽量简单易用（通俗来说就是“傻瓜化”），这两个接口的主要方法均不超过 5 个。另外，组件完全封装了所有的底层 Socket 通信，上层应用看不到任何通信细节，不必也不能干预任何通信操作，Socket 连接被抽象为 Connection ID，该参数作为连接标识提供给上层应用识别不同的连接。

> ### 高性能 ###

> 作为底层的通用组件，性能问题是必须考虑的，绝对不能成为系统的瓶颈。而另一方面，从实际出发，根据客户端组件与服务端组件的性能要求采用不同的 Socket 模型。组件在设计上充分考虑了性能、现实使用情景、可用性和实现复杂性等因素，确保满足性能要求的同时又不会写得太复杂。做出以下两点设计决策：
  * **客户端：**在单独线程中实现 Socket 通信交互，这样可以避免与主线程或其他线程相互干扰；I/O 模型选择 Event Select 通信模型。每个组件对象管理一个 Socket 连接。
  * **服务端：**采用高效的 IOCP 通信模型；利用缓存池技术，在通信的过程中，通常需要频繁的申请和释放内存缓冲区，建立了动态缓存池， 只有当缓存池中没有可用对象时才创建新对象，而当缓存对象过多时则会压缩缓存池；另外，组件的动态内存通过私有堆（Private Heap）机制分配，避免与 new / malloc 竞争同时又减少内存空洞。
  * **Agent：**对于代理服务器或中转服务器等应用场景，服务器自身也作为客户端向其它服务器发起大规模连接，一个 Agent 组件对象管理多个 Socket 连接，与服务端采用相同的技术架构，可以用作代理服务器或中转服务器的客户端部件。

> ### 伸缩性 ###

> 可以根据实际的使用环境要求设置组件的各项性能参数（如：工作线程的数量、各种缓存池的大小、收发缓冲区的大小、Socket 监听队列的大小、Accept 派发的数目以及心跳检查的间隔等）。

组件详细说明与使用方法：http://www.cnblogs.com/ldcsaa/archive/2013/06/12/3132634.html


---


> # JessMA Java MVC & REST 开发框架 #
![http://bcs.duapp.com/jessma/blog/201306/Project-JessMA.png](http://bcs.duapp.com/jessma/blog/201306/Project-JessMA.png)
> JessMA Java Web 应用开发框架是一套功能完备的高性能 Full-Stack Web 应用开发框架，内置稳定高效的 MVC 基础架构和 DAO 框架（已内置 Hibernate、Mybatis 和 JDBC 支持），集成 Action 拦截、Form Bean / Dao Bean / Spring Bean 装配、国际化、文件上传下载和缓存等基础 Web 应用组件，提供高度灵活的纯 Jsp/Servlet API 编程模型，完美整合 Spring，支持 Action Convention“零配置”，能快速开发传统风格和 RESTful 风格的 Web 应用程序，文档和代码清晰完善，非常容易学习。

JessMA 项目主页：https://code.google.com/p/portal-basic


---


> # VC-Logger #
![http://bcs.duapp.com/jessma/blog/201306/Project-VC-Logger.png](http://bcs.duapp.com/jessma/blog/201306/Project-VC-Logger.png)
> VC-Logger 是一个简单易用的 C++ 程序通用日志组件。设计时着重考虑三个方面：功能、可用性和性能：

**功能：** 本日志组件的目的是满足大多数应用程序记录日志的需求 —— 把日志输出到文件或发送到应用程序中，并不提供一些复杂但不常用的功能。本日志组件的功能包括：
  1. 把日志信息输出到指定文件
  1. 每日生成一个日志文件
  1. 对于 GUI 程序，可以把日志信息发送到指定窗口
  1. 对于Console应用程序，可以把日志信息发往标准输出 (std::cout)
  1. 支持 MBCS / UNICODE，Console / GUI 程序
  1. 支持动态加载和静态加载日志组件 DLL
  1. 支持 DEBUG/TRACE/INFO/WARN/ERROR/FATAL 等多个日志级别

**可用性：** 本日志组件着重考虑了可用性，尽量让使用者用起来觉得简便、舒心：
  1. 简单纯净：不依赖任何程序库或框架
  1. 使用接口简单，不需复杂的配置或设置工作
  1. 提供 CStaticLogger 和 CDynamicLogger 包装类用于静态或动态加载以及操作日志组件，用户无  需关注加载细节
  1. 程序如果要记录多个日志文件只需为每个日志文件创建相应的 CStaticLogger 或 CDynamicLogger 对象
  1. 只需调用 Log()/Debug()/Trace()/Info()/Warn()/Error()/Fatal() 等方法记录日志
  1. 日志记录方法支持可变参数
  1. 日志输出格式：<时间> <线程ID> <日志级别> <日志内容>

**性能：** 性能是组件是否值得使用的硬指标，本组件从设计到编码的过程都尽量考虑到性能优化：
  1. 支持多线程同时发送写日志请求
  1. 使用单独线程在后台写日志，不影响工作线程的正常执行
  1. 采用批处理方式批量记录日志

详细说明与使用方法参考：http://code.google.com/p/ldcsaa/wiki/VCLogger_Guide


---


> # Log-Cutter #
![http://bcs.duapp.com/jessma/blog/201306/Project-Log-Cutter.png](http://bcs.duapp.com/jessma/blog/201306/Project-Log-Cutter.png)
> Log-Cutter 是一个简单实用的日志切割清理工具。对于服务器的日常维护来说，日志清理是非常重要的事情，如果残留日志过多则严重浪费磁盘空间同时影响服务的性能。如果用手工方式进行清理，会花费太多时间，并且很多时候难以满足实际要求。例如：如何在每个星期六凌晨3点把超过 2G 大的日志文件进行切割，保留最新的 100M 日志记录？
> 网上没有发现能满足本座要求的日志切割工具，因此花了一些闲暇时间自己写了一个。由于要在多个平台上使用，为了方便采用 Java 实现。本工具命名为 Log-Cutter，主要有以下特点：

  1. 支持 Linux、Mac 和 Windows 等所有常见操作系统平台
  1. 支持命令行交互式运行
  1. 支持后台非交互式运行（Linux/MAC 下使用 daemon 进程实现，Windows 用系统 Service 实现）
  1. 支持三种日志清理方式（删除日志文件、切割日志文件或归档日志文件）
  1. 支持对 GB18030、UTF-8、UTF-16LE、UTF-16BE 等常用日志文件类型进行切割（不会发生切掉半个字符的情况）
  1. 高度可配置（程序执行周期、要删除的日志文件过期时间、要切割的日志文件阀值和保留大小等均可配置

详细说明与使用方法参考：http://code.google.com/p/ldcsaa/wiki/LogCutter_guide


---
