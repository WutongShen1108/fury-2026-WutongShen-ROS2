# ROS2 自主探索开发

# 日期
2026-07-30

# 系统架构

MuJoCo 仿真环境

|

LiDAR (/scan)

|

SLAM Toolbox

|

Occupancy Grid Map (/map)

|

Map Reader Node

|

Frontier Detection

|

Best Frontier Selection

|

Nav2 NavigateToPose

|

机器人自主移动

# 已完成的工作

## Frontier Detection实现

实现功能：

- 遍历 Occupancy Grid
- 找到未知区域（Unknown）
- 判断未知区域与自由区域的边界
- 将 Grid 坐标转换为 World 坐标
- 发布 Frontier Marker

发布：

/frontiers

Rviz中成功显示红色 Frontier 点

## Best Frontier选择

实现 Greedy Frontier Selection

当前策略：

选择距离机器人最近的 Frontier

计算：

distance =
sqrt(
(x_robot-x_frontier)^2
+
(y_robot-y_frontier)^2
)

选择最小距离目标作为下一探索点

发布：

/best_frontier

RViz中显示绿色目标点

## Nav2 导航集成

完成 Frontier 和 Nav2 的连接

使用：

/navigate_to_pose

实现：

- 获取最佳 Frontier
- 转换为 PoseStamped
- 发送 Nav2 Navigation Goal
- 机器人自动移动

# 遇到的问题和解决方案

## 问题1： Explorer只能发送一次目标

现象：机器人到达第一个目标点后停止

原因：Explorer节点没有处理新的 Frontiers

解决：增加目标过滤机制

## 问题2：机器人重复探索同一个位置

现象：机器人重复选择同一个相同Frontier

原因：Greedy算法只考虑距离，没有记录已经访问过的目标

解决：过滤旧目标：filter_visited_frontiers()

# 当前局限

- 没有考虑探索收益
- 可能产生较长路径
- 探索效率不是最优
