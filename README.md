# Docker-container-with-GPU-access
Creating a Docker container with GPU access, ROS 2 and Gazebo
## OS
This was tested on Win11 with WSL2. It should work on native Ubuntu as well but that's not guaranteed.
## Check for GPU access
Check for recognized GPU(s) using:
```bash
nvidia-smi
```
The result should look like this:
![[Pasted image 20250301192419.png]]
NOTE: if using a WSL setup DO NOT update Graphic Drivers from Ubuntu but just from Windows, Linux drivers will be updated accordingly.
## Install Docker
Install Docker Desktop:
- [Ubuntu](https://docs.docker.com/desktop/setup/install/linux/ubuntu/)
- [WSL](https://learn.microsoft.com/en-us/windows/wsl/tutorials/wsl-containers)
## Install Nvidia Container Toolkit
Install and configure the [Nvidia Container Toolkit](https://docs.nvidia.com/datacenter/cloud-native/container-toolkit/latest/install-guide.html).
## Create the container
Make sure to RUN DOCKER DESKTOP.
Create the Docker Container with the following command.
NOTE: `<your_image>` should be changed with one of the distros listed in the *LATEST CUDA* paragraph from [this page](https://catalog.ngc.nvidia.com/orgs/nvidia/containers/cuda).
```bash
docker run --gpus all -it -v /tmp/.X11-unix/:/tmp/.X11-unix/:rw --env=DISPLAY --name <your_container_name> nvidia/cuda:<your_image> bash
```


The arguments we’ve added are:
- `--gpus all`: runs the Docker container with full GPU access
- `-v /tmp/.X11-unix/:/tmp/.X11-unix/:rw`: Let’s break this down. The `-v` option allows Docker to mount a volume to the container, enabling the container to access directories on the host machine’s filesystem. The first `/tmp/.X11-unix/` is a directory on the host machine used by the X server for display. The second `:/tmp/.X11-unix/` is the location in the container where the volume is mounted, and it’s the same directory as on the host machine. The `:rw` part allows read/write access to the volume, which is necessary for displaying content.
- `-e DISPLAY=$DISPLAY`: This argument passes the `DISPLAY` environment variable from the host to the container. By default, a container doesn’t inherit environment variables from the host machine, so we must explicitly pass the `DISPLAY` variable to enable visualization.
- `--name <your_container_name>`: names the container


Once inside the container check for GPU access using:
```bash
nvidia-smi
```
## Configure the container
Install basic utilities:
```bash
apt-get update && apt-get install -y sudo nano python3-pip wget curl software-properties-common && rm -rf /var/lib/apt/lists/*

apt-get update && apt-get upgrade -y
```
## Install ROS2
Install ROS 2 [here](https://docs.ros.org/en/jazzy/Installation/Ubuntu-Install-Debs.html). At the time of writing Jazzy is the latest distribution, you may want to use a newer one in the future.

After the installation source the `bashrc` file:
```bash
# Open or create .bashrc file
nano ~/.bashrc

# Add this line at the end of the file (adjust the path to match your ROS distribution)
source /opt/ros/<your_ros2_version>/setup.bash

# Save and exit (Ctrl+X, then Y, then Enter) # Reload the .bashrc
source ~/.bashrc
```
NOTE: substitute `<your_ros2_version>` with your actual version.
## Install Gazebo
Make sure to choose a Gazebo version which is compatible with your ROS 2 version using this [table](https://gazebosim.org/docs/latest/ros_installation/).


Install Gazebo [here](https://gazebosim.org/docs/harmonic/install_ubuntu/) and test it with:
```bash
gz sim shapes.sdf
```
#### Sources
https://medium.com/@oelmofty/ros2-in-docker-why-and-how-b72b3880dc97

https://linuxconfig.org/setting-up-nvidia-cuda-toolkit-in-a-docker-container-on-debian-ubuntu

https://github.com/MOGI-ROS/Week-3-4-Gazebo-basics
