
## Run the main driver in scancontrol

We can run the main driver in scancontrol with:

```bash
ros2 launch micro_epsilon_scancontrol_driver load_driver.launch
```

This will start publishing the topic `/scancontrol_pointcloud`


## Display the scanner in RViz

We can display the scanner in RViz by using the following launch file:

```bash
ros2 launch micro_epsilon_scancontrol_driver test_driver.launch
```

By default it will launch with the scanner model `26x0_29x0_25`

If we want to use the 30xx series, we can set the argument `scancontrol_type`:

```bash
ros2 launch micro_epsilon_scancontrol_driver test_driver.launch scancontrol_type:=30xx_25
```

## Publishing point cloud from scanner in RViz

The point cloud is being published with reference to the frame `scancontrol_frame`.

Apparently there is no transformation that can convert from the two fixed frames available in ROS 2 which are the `base_link` and `aperture_frame`

We can synthetically create a transformation between the `aperture_frame` and the `scancontrol_frame` with `tf2_tools`:

```bash
ros2 run tf2_ros static_transform_publisher 0 0 0 0 0 0 aperture_frame scancontrol_frame
```

This will create a separate node (`/static_transform_publisher_xxxxx`) and another topic `tf_static` that will allow us to visualise the point cloud in RVIz along with the scanner.

Then we can run:

```bash
ros2 launch micro_epsilon_scancontrol_driver test_driver.launch scancontrol_type:=30xx_25
```

And click Add -> By Topic and select `PointCloud2` under `/scancontrol_pointcloud` to add the point cloud.