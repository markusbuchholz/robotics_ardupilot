# Robotics Ardupilot

The following repository delivers ROS and ROS 2 environment with Ardupilot and Gazebo.

## For ROS:

```bash

cd ros_sitl/docker

```

## For ROS 2:

```bash

cd ros2_sitl/docker

```


## Build

```bash

sudo ./build.sh

```


## Run

```bash

sudo ./run.sh

```


## Run SITL (ArduPilot)

Notes:
  
- The flag ```-l``` is the localization ```lat,lon,alt,heading```. Check your favorite location with Google Maps.
- ```sim_vehicle.py --help ``` -prints all available commands and flags.


```bash

cd ../ardupilot

Tools/environment_install/install-prereqs-ubuntu.sh -y

. ~/.profile

sim_vehicle.py -v Rover -f rover --map --console -l 55.99541530863445,-3.3010225004910683,0,0

```

## Start QGC (outside Docker)


```bash
./QGroundControl.AppImage
```

### Expected results

![image](https://github.com/user-attachments/assets/5860e198-c270-4207-a68b-8cceb2c6b3ac)

