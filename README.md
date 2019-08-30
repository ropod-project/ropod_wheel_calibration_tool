# ropod_wheel_calibration_tool

A simple tool that automates the calibration process of the smart wheel encoder pivots. The component subscribes to measurements from the smart wheels and, after receiving a predefined number of encoder pivot measurements, averages those to calculate the pivot offsets in the wheel zero position (i.e. the position in which the wheels face forward). The pivot values are then saved in a file with the following format:

```
x.xxxxx, y.yyyyy, z.zzzzz, w.wwwww
```

where the order of the encoder pivot values should match the wheel order in the controller.

## Calibration Procedure

To calibrate the wheels, first align the smart wheels so that they face forward (this procedure is repeated from [the wiki](https://git.ropod.org/ropod/readme/wikis/Setting-pivot-zero-position) for convenience):
1. start the robot controller (either run `roslaunch ropod_low_level_control ropod_low_level_control.launch` or launch the teleop teleop and initialise the controller from there)
2. turn the wheels so that they roughly face forward by controlling the robot to move straight (the motor controller faces the back in this configuration)
3. (if using a calibration rig as described on the wiki) slide the calibration rig between the wheels and align the wheels so that they face forward completely

Once the wheels are aligned, run the component to read off the encoder pivot offsets:

```
roslaunch ropod_wheel_calibration_tool wheel_calibration.launch
```

## Dependencies

* `ropod_ros_msgs`

## Launch File Parameters

* `number_of_measurements`: Number of encoder pivot measurements to read for calculating the average pivot offset (default `50`)
* `pivot_file_path`: Path to a file in which the encoder pivots values should be saved (default `/home/ropod/pivots.cal`)
