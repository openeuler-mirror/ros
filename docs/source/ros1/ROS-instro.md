# ROS简介

## 1.1 机器人时代与ROS诞生

他们速度很快，具有非凡智慧与致命力量；他们不受生命周期桎梏，是未来科技的希望。机器人和仿生机器，正要从实验室的襁褓中迸裂而出，投入市场的广阔天地，运算能力的大幅进步加上最新的机器人脑部设计，为机器人科技带来前所未有的改革，毋庸置疑，机器人即将改变人类工作场所及生活状态。神爱世人，人亦钟情所造之物，如今有幸跟随时代步伐，一起创造世界，借不灭之躯，走遍险远之地，看尽世之奇伟瑰怪。


曾经，机器人创新的门槛非常高。如果你想在任何应用领域开发出有分量的产品，你需要建立一整套能够实现你想法的系统：包括硬件设备，当然还有控制系统，界面接口，以及让机器人运行并作为测试平台的检测工具。“没有什么是现成的设备，除了一些很差，闭源的东西外。”

![机器人操作系统 Robot Operating System](pics/ROS.png)

随着机器人领域的快速发展和复杂化，代码复用和模块化的需求日益强烈，已有的开源系统已不能很好地适应需求，2010年Willow Garage公司发布了开源机器人操作系统ROS。

ROS软件的开发自始至终采用开放的BSD协议，在机器人技术研究领域逐渐成为一个被广泛使用的平台。

  Willow Garage公司和斯坦福大学人工智能实验室合作以后，在2009年初推出了ROS0.4，这是一个测试版的ROS，现在所用的系统框架在这个版本中已经具有了初步的雏形。之后的版本才正式开启了ROS的发展成熟之路。


## 1.2 历代ROS版本

ROS1.0版本发布于2010年，基于PR2机器人开发了一系列机器人相关的基础软件包。随后ROS版本迭代频繁，目前已经发布到了Lunar。 目前使用人数最多的是Kinetic和melodic这两个Long Term Support版本。2020年5月，ROS1最后一个版本更新，后续会进入ROS2.0的时代。

  |    ROS版本    |        发布时间     |
  | :------:    | :------:           |
  |Noetic Ninjemys|2020.5|
  | Melodic Morenia  | 2018.5 |
  | Lunar Loggerhead     |   2017.5 |
  | Kinetic Kame     |   2016.5 |
  | Jade Turtle     |   2015.5 |
  | Indigo lgloo     |   2014.7 |
  | Hydro Medusa    |   2013.9 |
  | Groovy Galapagos     |   2012.12 |
  | Fuerte Turtle     |   2012.4 |
  | Electric Emys     |   2011.8 |
  | Diamondback    |  2011.3  |
  | C Turtle       |  2010.8 |
  | Box TurtleBox Turtle    | 2010.3 |



![](pics/melodic_with_bg.png)

## 1.3  什么是ROS
机器人是一个系统工程，它涉及机械、电子、控制、通信、软件等诸多学科。以前，开发一个机器人需要花很大的功夫，你需要设计机械、画电路板、写驱动程序、设计通信架构、组装集成、调试、以及编写各种感知决策和控制算法，每一个任务都需要花费大量的时间。因此像电影《钢铁侠》中那样，仅靠一个人的力量造出一个动力超强的人形机器人机甲是不可能的。

![](pics/happy-bot.jpeg)
然而随着技术进步，机器人产业分工开始走向细致化、多层次化，如今的电机、底盘、激光雷达、摄像头、机械臂等等元器件都有不同厂家专门生产。社会分工加速了机器人行业的发展。而各个部件的集成就需要一个统一的**软件平台**，在机器人领域，这个平台就是机器人操作系统ROS。

ROS是一个适用于机器人编程的**框架**，这个框架把原本松散的零部件耦合在了一起，为他们提供了**通信架构**。ROS虽然叫做**操作系统**，但并非Windows、Mac那样通常意义的操作系统，它只是连接了操作系统和你开发的ROS应用程序，所以它也算是一个**中间件**，基于ROS的应用程序之间建立起了沟通的桥梁，所以也是运行在Linux上的**运行时环境**，在这个环境上，机器人的感知、决策、控制算法可以更好的组织和运行。

以上几个关键词（框架、中间件、操作系统、运行时环境）都可以用来描述ROS的特性，作为初学者我们不必深究这些概念，随着你越来越多的使用ROS，就能够体会到它的作用。

## 1.4 ROS特点

ROS具有这些特点：
* **分布式 点对点**

  ROS采用了分布式的框架，通过点对点的设计让机器人的进程可以分别运行，便于模块化的修改和定制，提高了系统的容错能力。

* **多种语言支持**

  ROS支持多种编程语言。C++、Pyhton已经在ROS中实现编译，是目前应用最广的ROS开发语言，Lisp、C#、Java等语言的测试库也已经实现。为了支持多语言编程，ROS采用了一种语言中立的接口定义语言来实现各模块之间消息传送。通俗的理解就是，ROS的通信格式和用哪种编程语言来写无关，它使用的是自身定义的一套通信接口。
  
* **开源社区**

  ROS具有一个庞大的社区ROS WIKI(http://wiki.ros.org/ )，这个网站将会始终伴随着你ROS开发，无论是查阅功能包的参数、搜索问题还是。当前使用ROS开发的软件包已经达到数千万个，相关的机器人已经多达上千款。此外，ROS遵从BSD协议，对个人和商业应用及修改完全免费。这也促进了ROS的流行。

## 1.5 ROS优缺点

ROS为我们开发机器人带来了许多方便，然而它也确实存在一些问题：

  |    优点    |        缺点     |
  | :------:    | :------:           |
  | 提供框架、工具和功能     |   通信实时性能有限 |
  | 方便移植    |  系统稳定性尚不满足工业级要求  |
  | 庞大的用户群体       |  安全性上没有防护措施 |
  | 免费开源    |  仅支持Linux(Ubuntu) |

总体来说，ROS更适合科研和开源用户使用，如果在工业场景应用（例如无人驾驶）还需要做优化和定制。 为了解决实际应用的问题，ROS2.0做了很大的改进，目前正在开发之中，未来表现如何值得期待。

## 1.6 展望
ROS的发展逐渐的趋于成熟，近年来也逐步是面对着Ubuntu的更新而更新，这说明ROS已经初步进入一种稳定的发展状态，每年进行一次更新的频率同时还保留着长期支持的版本，这使得ROS在稳步的前进发展同时，也有着开拓创新的方向。目前越来越多的机器人、无人机甚至无人车都开始采用ROS作为开发平台，尽管ROS在实用方面目前还存在一些限制，但前途非常光明。

2018年ROS2 1.0版将发布，未来ROS2将如何影响机器人领域，我们拭目以待。相信在人工智能的大发展、人机交互越来越密切、互联网+的大时代，ROS会发挥出越来越重要的作用。