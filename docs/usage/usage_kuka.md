## Connect with KUKA driver via RSI

We first need to launch the `kuka_rsi_driver`:

```shell
ros2 launch kuka_rsi_driver startup.launch.py robot_model:=kr20_r1810_2 robot_family:=cybertech
```

If we want to start this with RVIz we need use the following:

```bash
ros2 launch kuka_rsi_driver startup_with_rviz.launch.py robot_model:=kr20_r1810_2 robot_family:=cybertech
```

After successful startup, the `robot_manager` node has to be activated to start the cyclic communication with the robot controller:

```shell
ros2 lifecycle set robot_manager configure
ros2 lifecycle set robot_manager activate
```

After we activate the controller, we need to run the RSI Program in the robot, for example either `rsi_joint_pos_4ms` or `rsi_joint_pos_12ms`

After that, the robot and ROS 2 will start to exchange data.

## Start visualisation from robot using RSI simulator

To start the visualisation of the robot with RVIZ we can first start the `kuka_rsi_driver`:

```bash
ros2 launch kuka_rsi_driver startup_with_rviz.launch.py robot_model:=kr20_r1810_2 robot_family:=cybertech
```

Initially the robot will initially spawn "collapsed".

We then need to start the `kuka_rsi_simulator`:

```bash
ros2 launch kuka_rsi_simulator kuka_rsi_simulator.launch.py
```

For this to work we also need to activate the `robot_manager` to start all nodes:

```bash
ros2 lifecycle set robot_manager configure
ros2 lifecycle set robot_manager activate
```
