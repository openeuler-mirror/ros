# 理解动作
## 背景
行为是ROS 2中的一种通信类型，用于长时间运行的任务。它们由三个部分组成：目标（goal），反馈（feedback）和结果（result）。

行为是建立在主题（topics）和服务（services）之上的。它们的功能类似于服务，但行为可以被取消。与只返回单个响应的服务不同，行为还提供稳定的反馈。

动作使用客户端-服务器模型，类似于发布者-订阅者模型（在理解话题中有描述 ）。一个"动作客户端"节点向一个"动作服务器"节点发送一个目标，服务器节点确认目标并返回一系列反馈和结果流。

![image](image/Action-SingleActionClient.gif)

## 任务

### 1 设置

启动两个 turtlesim 节点, `/turtlesim` 和 `/teleop_turtle`.

打开一个新的终端并运行：

``` console
ros2 run turtlesim turtlesim_node
```

打开另一个终端并运行：

``` console
ros2 run turtlesim turtle_teleop_key
```

### 2 使用动作

当你启动`/teleop_turtle` 节点时，你将在终端中看到以下消息:

``` console
Use arrow keys to move the turtle.
Use G|B|V|C|D|E|R|T keys to rotate to absolute orientations. 'F' to cancel a rotation.
```

让我们关注第二行，它对应一个动作。（第一条指令对应之前在 topics 教程 中讨论的 "cmd_vel" 话题。）

请注意，在美国的 QWERTY 键盘上，字母键 `G|B|V|C|D|E|R|T` 组成了围绕 
`F` 键的“方框”（如果你没有使用 QWERTY 键盘，请查看 [此链接](https://upload.wikimedia.org/wikipedia/commons/d/da/KB_United_States.svg)
以便跟随)。 每个键在 F 周围的位置对应 turtlesim 中的方向。例如，按下 E 将使乌龟的方向旋转到左上角。

请注意运行``/turtlesim``节点的终端。每次按下其中一个按键，都会向``/turtlesim``节点中的动作服务器发送一个目标。目标是将乌龟旋转到面向特定方向。当乌龟完成旋转时，应该显示一个传达目标结果的消息：

``` console
[INFO] [turtlesim]: Rotation goal completed successfully
```

按下``F``键将取消正在执行的目标。

尝试在乌龟完成旋转之前按下``C``键，然后再按下``F``键。在运行``/turtlesim``节点的终端中，你将看到以下消息：

``` console
[INFO] [turtlesim]: Rotation goal canceled
```

不仅客户端（你在teleop中输入的内容），而且服务器端（``/turtlesim``节点）也可以停止目标的执行。当服务器端选择停止处理目标时，称为“中止”目标。

在第一个旋转完成之前，请尝试按下 D 键，然后按下 G 键。在运行``/turtlesim``节点的终端中，您将看到以下消息：

``` console
[WARN] [turtlesim]: Rotation goal received before a previous goal finished. Aborting previous goal
```

这个动作服务器选择中止第一个目标，因为它收到了一个新的目标。它也可以选择其他操作，比如拒绝新目标或在第一个目标完成后执行第二个目标。不要假设每个动作服务器在收到新目标时都会选择中止当前目标。
### 3  ros2节点信息

要查看一个节点提供的动作列表，例如这里的 /turtlesim，请打开一个新的终端并运行以下命令：

``` console
ros2 node info /turtlesim
```

它将返回``/turtlesim``的订阅者、发布者、服务、动作服务器和动作客户端的列表：

``` console
/turtlesim
  Subscribers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /turtle1/cmd_vel: geometry_msgs/msg/Twist
  Publishers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /rosout: rcl_interfaces/msg/Log
    /turtle1/color_sensor: turtlesim/msg/Color
    /turtle1/pose: turtlesim/msg/Pose
  Service Servers:
    /clear: std_srvs/srv/Empty
    /kill: turtlesim/srv/Kill
    /reset: std_srvs/srv/Empty
    /spawn: turtlesim/srv/Spawn
    /turtle1/set_pen: turtlesim/srv/SetPen
    /turtle1/teleport_absolute: turtlesim/srv/TeleportAbsolute
    /turtle1/teleport_relative: turtlesim/srv/TeleportRelative
    /turtlesim/describe_parameters: rcl_interfaces/srv/DescribeParameters
    /turtlesim/get_parameter_types: rcl_interfaces/srv/GetParameterTypes
    /turtlesim/get_parameters: rcl_interfaces/srv/GetParameters
    /turtlesim/list_parameters: rcl_interfaces/srv/ListParameters
    /turtlesim/set_parameters: rcl_interfaces/srv/SetParameters
    /turtlesim/set_parameters_atomically: rcl_interfaces/srv/SetParametersAtomically
  Service Clients:

  Action Servers:
    /turtle1/rotate_absolute: turtlesim/action/RotateAbsolute
  Action Clients:
```

请注意，/turtlesim``中的/turtle1/rotate_absolute``动作位于``动作服务器``下。这意味着``/turtlesim``对``/turtle1/rotate_absolute``动作进行响应并提供反馈。

/teleop_turtle``节点的名称为/turtle1/rotate_absolute``，位于``动作客户端``下，这意味着它发送该动作名称的目标。要查看，请运行：

``` console
ros2 node info /teleop_turtle
```

将返回：

``` console
/teleop_turtle
  Subscribers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
  Publishers:
    /parameter_events: rcl_interfaces/msg/ParameterEvent
    /rosout: rcl_interfaces/msg/Log
    /turtle1/cmd_vel: geometry_msgs/msg/Twist
  Service Servers:
    /teleop_turtle/describe_parameters: rcl_interfaces/srv/DescribeParameters
    /teleop_turtle/get_parameter_types: rcl_interfaces/srv/GetParameterTypes
    /teleop_turtle/get_parameters: rcl_interfaces/srv/GetParameters
    /teleop_turtle/list_parameters: rcl_interfaces/srv/ListParameters
    /teleop_turtle/set_parameters: rcl_interfaces/srv/SetParameters
    /teleop_turtle/set_parameters_atomically: rcl_interfaces/srv/SetParametersAtomically
  Service Clients:

  Action Servers:

  Action Clients:
    /turtle1/rotate_absolute: turtlesim/action/RotateAbsolute
```

### 4 ros2 action list

要识别ROS图中的所有动作，请运行以下命令：

``` console
ros2 action list
```

将返回：

``` console
/turtle1/rotate_absolute
```

目前ROS图中只有一个动作。它控制着乌龟的旋转，就像您之前看到的那样。您还已经知道，对于这个动作，有一个动作客户端（位于``/teleop_turtle``）和一个动作服务器（位于``/turtlesim``），可以使用``ros2 node info <node_name>``命令查看。

#### 4.1 ros2 action list -t

动作也有类型，类似于主题和服务。要找到``/turtle1/rotate_absolute``的类型，请运行以下命令：

``` console
ros2 action list -t
```

将返回：

``` console
/turtle1/rotate_absolute [turtlesim/action/RotateAbsolute]
```

在每个动作名称右侧的括号中（在这种情况下只有``/turtle1/rotate_absolute``），是动作类型``turtlesim/action/RotateAbsolute``。当你想要从命令行或代码中执行一个动作时，你将需要它。

### 5 ros2 action info

你可以使用以下命令进一步检查``/turtle1/rotate_absolute``动作：

``` console
ros2 action info /turtle1/rotate_absolute
```

将返回：

``` console
Action: /turtle1/rotate_absolute
Action clients: 1
    /teleop_turtle
Action servers: 1
    /turtlesim
```

这告诉我们之前在每个节点上运行``ros2 node info``时学到的内容：/teleop_turtle``节点具有一个动作客户端，而/turtlesim``节点具有一个``/turtle1/rotate_absolute``动作的动作服务器。

### 6 ros2 interface show

在发送或执行动作目标之前，您还需要了解动作类型的结构。

回想一下，在运行命令``ros2 action list -t``时，您识别了``/turtle1/rotate_absolute``的类型。在终端中输入以下带有动作类型的命令：

``` console
ros2 interface show turtlesim/action/RotateAbsolute
```

将返回：

``` console
# The desired heading in radians
float32 theta
---
# The angular displacement in radians to the starting position
float32 delta
---
# The remaining rotation in radians
float32 remaining
```

在第一个 --- 之上的部分是目标请求的结构（数据类型和名称）。接下来的部分是结果的结构。最后一部分是反馈的结构。

### 7 使用ros2 action send_goal

现在让我们通过命令行发送一个动作目标，使用以下语法：

``` console
ros2 action send_goal <action_name> <action_type> <values>
```

`<values>` 需要以 YAML 格式提供。

请密切关注turtlesim窗口，并在终端中输入以下命令：

``` console
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: 1.57}"
```

你应该会看到乌龟旋转，并在终端中看到以下消息：

``` console
Waiting for an action server to become available...
Sending goal:
   theta: 1.57

Goal accepted with ID: f8db8f44410849eaa93d3feb747dd444

Result:
  delta: -1.568000316619873

Goal finished with status: SUCCEEDED
```

所有目标都有一个唯一的ID，在返回消息中显示。你还可以看到名为``delta``的结果字段，它是相对于起始位置的位移。

要查看此目标的反馈，请在``ros2 action send_goal``命令中添加``--feedback``：

``` console
ros2 action send_goal /turtle1/rotate_absolute turtlesim/action/RotateAbsolute "{theta: -1.57}" --feedback
```

终端将返回以下消息:

``` console
Sending goal:
   theta: -1.57

Goal accepted with ID: e6092c831f994afda92f0086f220da27

Feedback:
  remaining: -3.1268222332000732

Feedback:
  remaining: -3.1108222007751465

…

Result:
  delta: 3.1200008392333984

Goal finished with status: SUCCEEDED
```

您将继续接收反馈，直到完成目标。剩余弧度。

## 总结

动作类似于服务，允许您执行长时间运行的任务，提供定期反馈，并可取消。

一个机器人系统可能会使用动作进行导航。一个动作目标可以告诉机器人前往一个位置。当机器人导航到该位置时，它可以沿途发送更新（即反馈），然后在到达目的地后发送最终结果消息。

Turtlesim具有一个动作服务器，动作客户端可以向其发送旋转乌龟的目标。在本教程中，您可以内省该动作``/turtle1/rotate_absolute``，以更好地了解动作是什么以及它们是如何工作的。


## 相关内容

您可以在[ROS 2](https://design.ros2.org/articles/actions.html)了解有关动作设计决策的更多信息。
本文引用：   
-1:[https://fishros.org/doc/ros2/humble/Tutorials.html](https://fishros.org/doc/ros2/humble/Tutorials.html)      

-2:[https://github.com/ros2/ros2_documentation/blob/humble/source/Tutorials.rst](https://github.com/ros2/ros2_documentation/blob/humble/source/Tutorials.rst)     
