# Robotics Ardupilot

The following repository delivers ROS and ROS 2 environment with Ardupilot and Gazebo.

## Build

```bash

sudo ./build.sh

```

## Build in Docker


```bash
sudo ./run.sh

colcon build

source install/setup.bash

cd ../gz_ws

source install/setup.bash

source gazebo_exports.sh
 
```
## Start QGC (outside Docker)

```bash
./QGroundControl.AppImage
```

## Run SITL


Notes:

- IMPORTANT: Run GazbenSim first as it holds the ArduPilot plugin, which you can connect with the ArduPilot SITL (Rover) simulator!
  
- The flag ```-l``` is the localization (lat,lon,alt,heading). Check your favorite location with Google Maps.
- ```sim_vehicle.py --help ``` -prints all available commands and flags.
- in ```run.sh``` adjust these two lines for your host specific:

```bash
local_gz_ws="/home/markus/blueboat_ardupilot_SITL/gz_ws"
local_SITL_Models="/home/markus/blueboat_ardupilot_SITL/SITL_Models"
```
  
```bash

sudo docker exec -it blueboat_sitl /bin/bash

cd ../ardupilot

Tools/environment_install/install-prereqs-ubuntu.sh -y

. ~/.profile

sim_vehicle.py -v Rover -f gazebo-rover --model JSON --map --console -l 55.99541530863445,-3.3010225004910683,0,0

```
