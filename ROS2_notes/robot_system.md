机器人核心链路
              MuJoCo
                 |
                 |
        mujoco_sim_node
                 |
        -----------------
        |               |
        ↓               ↓
      /scan           /odom
        |               |
        |               |
        ↓               |
          slam_toolbox
                 |
                 ↓
               /map
                 |
                 ↓
                RViz
                
TF:
map
 |
odom
 |
base_link
 |
lidar_link
