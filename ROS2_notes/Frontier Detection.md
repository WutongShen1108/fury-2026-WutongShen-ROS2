# ROS2自主探索项目日志

## 日期
2026-07-28

## 主题
Frontier Exploration 前沿探索准备阶段：地图解析与Frontier检测

---

# 1. 项目背景

本阶段目标是在已有 ROS2 + Nav2 导航系统基础上，进一步实现自主探索能力。

此前已经完成：

- SLAM 建图
- AMCL 定位
- Nav2 导航
- Costmap分析
- Nav2参数调优
- ROS2 Action Client控制导航

当前目标：

让机器人能够：

1. 读取当前环境地图
2. 分析未知区域边界
3. 自动生成探索目标点
4. 调用Nav2完成自主移动

---

# 2. 当前系统架构

## 已完成导航架构

RViz / Python节点
↓
NavigateToPose Action
↓
NAV2
↓
Controller Server
↓
/cmd_vel
↓
机器人

---

## 自主探索目标架构

SLAM Toolbox

 |

  /map

    |

Frontier Explorer

    |

探索目标点 Goal Pose

    |

NavigateToPose Action

    |

Nav2

    |

机器人探索


---

# 3. ROS2 Action Client验证

## 目标

验证Python节点可以代替RViz发送Nav2导航目标。

---

## 实现

创建：


simple_nav_client


功能：


Python节点

  ↓

NavigateToPose Action

  ↓

Nav2

  ↓

机器人运动


---

## 测试结果

机器人成功执行导航任务。

反馈：


Distance remaining:
0.14 m

Distance remaining:
0.00 m


结果：

- Goal成功发送
- Nav2 Action Server正常响应
- Controller正常工作
- 机器人成功到达目标点

说明：

**Nav2控制接口已经可以被程序调用，为后续自主探索提供基础。**

---

# 4. Map Reader地图读取节点

## 4.1 目的

Frontier Exploration需要实时获取机器人当前地图。

地图来源：


/map


消息类型：


nav_msgs/msg/OccupancyGrid


---

## 4.2 OccupancyGrid数据

地图中每个栅格的状态：

| 数值 | 含义 |
|---|---|
| -1 | 未知区域 Unknown |
| 0 | 空闲区域 Free |
| 100 | 障碍物 Occupied |

---

## 4.3 创建Map Reader节点

创建ROS2 Python包：


map_reader


节点功能：

订阅：


/map


读取：

- 地图宽度 width
- 地图高度 height
- 地图分辨率 resolution
- 栅格占用信息

示例：


Width: 249
Height: 377
Resolution: 0.05 m/cell


---

# 5. ROS2 QoS问题调试

## 问题

节点启动后：


Frontier detector started...


但是无法接收地图数据。

---

## 排查

执行：

```bash
ros2 topic info /map -v

发现QoS不匹配。

map_server
Reliability:
RELIABLE

Durability:
TRANSIENT_LOCAL
map_reader

初始：

Reliability:
RELIABLE

Durability:
VOLATILE
原因

/map属于静态地图话题。

map_server会保存最后一次地图消息，新加入节点需要使用：

TRANSIENT_LOCAL

才能获取已有地图。

解决

修改订阅QoS：

ReliabilityPolicy.RELIABLE

DurabilityPolicy.TRANSIENT_LOCAL

重新编译：

colcon build --symlink-install

source install/setup.bash

成功接收：

/map
