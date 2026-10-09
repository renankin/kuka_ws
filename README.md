Follow the guides include in the `docs` folder to check how to install and use ROS 2 for controlling and visualising the laser scanner and KUKA robot in the welding cell.

## Topics published by scancontrol

The scan control driver makes available the following topic:

```bash
/scancontrol_pointcloud
```

Inside this topic, we have the following messages:

```bash
sensor_msgs/msg/PointCloud2
```

These messages are essentially the laser scan data filtered by the partial profile settings.
