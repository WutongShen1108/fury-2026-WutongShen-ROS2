## Experiment 1 Inflation

A参数:
inflation_radius=0.1

现象:
机器人靠墙运动并碰撞

原因:
Costmap安全区域不足
##
B参数:
inflation_radius=1.0

现象:
机器人远离墙体
狭窄空间有概率无法通过

原因:
Costmap安全区域不足


## Experiment 2 Velocity


A参数:
max_vel_x=0.1

现象:
运动稳定但速度降低

原因:
线速度限制降低
##
B参数：
max_vel_x=0.1

现象:
速度快，急转弯容易偏离

原因:
线速度限制降低


## Experiment 3 Acceleration


A参数:
acc_lim_x: 0.5

现象:
转弯平滑

##
B参数：
acc_lim_x: 5.0

现象:
猛冲

原因:
加速度大


## Experiment 5 Goal Tolerance


A参数:
xy_goal_tolerance: 0.25

现象:
距离目标约25cm认为到达

##
B参数：
xy_goal_tolerance: 0.05

现象:
更精确，消耗时间长，原地打转
