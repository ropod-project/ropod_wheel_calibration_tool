# ropod_wheel_calibration_tool

## ecat_encoder_calibration (firmware v1015+)

Encoder calibration for smart wheels with firwmare v1015+. The entire calibration procedure is described at https://git.ropod.org/ropod/smartwheel/blob/master/encoder_calibration_and_motor_phasing.txt

### Usage instructions

This script takes care of the first part of the calibration procedure, namely the encoder calibration. Note that each wheel has to be calibrated separately.

To calibrate, make sure that EtherCAT communication with the wheels can be established (one can use SOEM's `slaveinfo` utility for that) and then simply run this script as

```
sudo common/scripts/ecat_encoder_calibration [network_interface_name] [slave_number]
```

where
* `[network_interface_name]` is the network interface of the EtherCAT connection and
* `[slave_number]` is the index of the smart wheel that needs to be calibrated (note: the index expected by this script is zero-based; on the other hand, `slaveinfo` returns a one-based index)

After running the script, the current status of the calibration state machine for the two motor encoders and the pivot encoder will be printed in the form

```
x - y - z
```

To calibrate, simply rotate the pivot and then the individual wheels until all (x, y, z) reach a state of 7 (success) or 8 (fail).

If the calibration is successful for all encoders, the second stage of the calibration (finding the optimal motor phasing) can take place.

###  Acknowledgements

The calibration script has been adapted from https://github.com/bnjmnp/pysoem/blob/master/examples/basic_example.py and reuses almost all of the slave setup code in that script.

## ecat_motor_phasing (firmware v1015+)

Script for identifying the optimal motor phasing on smart wheels with firwmare v1015+. The entire calibration procedure is described at https://git.ropod.org/ropod/smartwheel/blob/master/encoder_calibration_and_motor_phasing.txt

### Usage instructions

This script takes care of the second part of the calibration procedure, namely the part of identifying the optimal motor phasing. Note that each wheel has to be calibrated separately.

To calibrate, make sure that EtherCAT communication with the wheels can be established (one can use SOEM's `slaveinfo` utility for that) and then simply run this script as

```
sudo common/scripts/ecat_motor_phasing [network_interface_name] [slave_number] [duration_s]
```

where
* `[network_interface_name]` is the network interface of the EtherCAT connection
* `[slave_number]` is the index of the smart wheel that needs to be calibrated (note: the index expected by this script is zero-based; on the other hand, `slaveinfo` returns a one-based index)
* `[duration_s]` is the duration of the calibration in seconds

After running the script, velocity commands will be sent to the wheel's motors individually (in both cases, the command will run for `[duration_s]` seconds). No manual intervention is required during this process.

If the calibration is successful for both motors, the smart wheel is ready to be used.

### Acknowledgements

The calibration script has been adapted from https://github.com/bnjmnp/pysoem/blob/master/examples/basic_example.py and reuses almost all of the slave setup code in that script.

## pivot_calibration

A simple tool that automates the calibration process of the smart wheel encoder pivots. The component subscribes to measurements from the smart wheels and, after receiving a predefined number of encoder pivot measurements, averages those to calculate the pivot offsets in the wheel zero position (i.e. the position in which the wheels face forward). The pivot values are then saved in a file with the following format:

```
x.xxxxx, y.yyyyy, z.zzzzz, w.wwwww
```

where the order of the encoder pivot values should match the wheel order in the controller.

## Calibration Procedure

To calibrate the pivots, first align the smart wheels so that they face forward (this procedure is repeated from [the wiki](https://git.ropod.org/ropod/readme/wikis/Setting-pivot-zero-position) for convenience):
1. start the robot controller (either run `roslaunch ropod_low_level_control ropod_low_level_control.launch` or launch the teleop teleop and initialise the controller from there)
2. turn the wheels so that they roughly face forward by controlling the robot to move straight (the motor controller faces the back in this configuration)
3. (if using a calibration rig as described on the wiki) slide the calibration rig between the wheels and align the wheels so that they face forward completely

Once the wheels are aligned, run the component to read off the encoder pivot offsets:

```
roslaunch ropod_wheel_calibration_tool pivot_calibration.launch
```

## Dependencies

* `ropod_ros_msgs`

## Launch File Parameters

* `number_of_measurements`: Number of encoder pivot measurements to read for calculating the average pivot offset (default `50`)
* `pivot_file_path`: Path to a file in which the encoder pivots values should be saved (default `/home/ropod/pivots.cal`)
