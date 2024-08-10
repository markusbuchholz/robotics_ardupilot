# Robotics Ardupilot

The following repository delivers ROS and ROS 2 environment with Ardupilot and Gazebo.

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

![image](https://github.com/user-attachments/assets/59dedf59-6c16-487d-9be0-96f8ea9465a1)
