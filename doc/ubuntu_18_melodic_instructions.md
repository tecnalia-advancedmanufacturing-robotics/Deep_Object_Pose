# Instructions to use DOPE on Ubuntu 18 and ROS Melodic

This is a **fork** from the official DOPE ROS pkg [repository](https://github.com/NVlabs/Deep_Object_Pose). This fork contains changes and instructons necessary to **run this pkg on a docker (version > 19.03)** Ubuntu 18.04 with ROS Melodic.

- - -

**NOTE:**  As in the original instructions, these refer to inference only.

- - -

## Versions

- x86 machine with Ubuntu 18.04
- CUDA 10.2 installed on the machine
- ROS Melodic
- Docker 19.03.11


## Pre-installation instructions

1. **Install CUDA and CUDNN**: Follow [these](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#package-manager-installation) instructions.
2. **Install Docker**: Follow [these](https://docs.docker.com/engine/install/ubuntu/) instructions.
3. **Install NVIDIA container toolkit:** Follow [these](https://github.com/NVIDIA/nvidia-docker#quick-start) instructions.


## Installing the ROS package

- **Base system**: Ubuntu 18.04, ROS Melodic, NVIDIA GeForce MX130.
- **Docker image**: Ubuntu 16.04, ROS Kinetic.

### Set up workspace

First, create a catkin workspace

```sh
# If not installed:
sudo apt install python-catkin-tools

mkdir -p ~/catkin_ws/src
cd ~/catkin_ws/src
git clone <this_repo_url> dope

cd ..
rosdep update
rosdep install -y --from-paths src/ -y -r

catkin init
```

Configure a virtual environment for python dependencies

```sh
sudo apt install python3-pip python3-virtualenv

python -m venv venv
. venv/bin/activate

pip install --upgrade pip
pip install -r dope/requirements.txt
```

And finally, build workspace:

```sh
catkin build
```

**Download [the weights](https://drive.google.com/open?id=1DfoA3m_Bm0fW8tOWXGVxi4ETlLEAgmcg)** and save them to the `weights` folder, *i.e.*, `~/catkin_ws/src/dope/weights/`.

### Build docker image

Be sure to have `nvidia-container-toolkit` installed.

```sh
cd src/dope/docker
docker build -t nvidia-dope:kinetic-v1 -f Dockerfile.kinetic ..
```

- - -

**NOTE:** If something in configuration or camera intrinsics files (suh as image topics, commenting / uncommenting weight file paths...), it is better to do it before building the container than editting such files inside it.

- - -

## Run DOPE from docker

First, launch camera drivers (ex. `roslaunch openni2_launch openni2.launch`). It needs to be running before the container is started.

Then, run the container just created:

```sh
./run_dope_docker.sh [name] [host dir] [container dir]
```

- - -

**NOTE:** More info on the arguments can be found [here](docker/readme.md). However, some of the info in such file is not up to date (belongs original DOPE repo).

- - -

From inside the docker image:

```sh
cd catkin_ws
catkin init
catkin build

source /opt/ros/kinetic/setup.bash
source devel/setup.bash

roslaunch dope dope.launch
```

### Check GPU usage

On a separate terminal, run this command for periodical checks on GPU memory usage:

```sh
while true; do gpustat; sleep 1; done
```

## Additional information

The instructions above are specific to use on Ubuntu 18.04 + ROS Melodic + Docker, for further details, go to the **official DOPE repository** on this [link](https://github.com/NVlabs/Deep_Object_Pose).

