# ROS Melodic and Gazebo 9 Docker with GUI

## Build Image and Run a Container:

Install docker:

```
sudo apt-get install docker.io
```

Create a directory to store a dockerfile:

```
mkdir dockerfile
```

Create a file named "Dockerfile" and add the following code (make sure to change DISPLAY):

```
FROM ros:melodic-robot-bionic

# update packages and install dependencies
RUN apt-get update && apt-get install -y \
    ros-melodic-rviz \
    wget

# install Gazebo 9
RUN sh -c 'echo "deb http://packages.osrfoundation.org/gazebo/ubuntu-stable `lsb_release -cs` main" > /etc/apt/sources.list.d/gazebo-stable.list'
RUN wget http://packages.osrfoundation.org/gazebo.key -O - | apt-key add -
RUN apt-get update && apt-get install -y \
    gazebo9 libignition-math2-dev \
    ros-melodic-gazebo-ros-pkgs ros-melodic-gazebo-ros-control \
    && rm -rf /var/lib/apt/lists/*

# set missing environment variable needed to run Qt applications
ENV QT_X11_NO_MITSHM 1

# make catkin workspace and source directory
RUN mkdir -p /catkin_ws/src

# ros package path
ENV ROS_PACKAGE_PATH=/catkin/src/:/opt/ros/melodic/share

# rosdep install in catkin workspace
RUN rosdep install -y -r --from-path /catkin_ws/src

# IP address of your computer to display gazebo
ENV DISPLAY=................
```

Build the image (change the name of the image by replacing NAME with something else):

```
cd dockerfile

sudo docker build -t "NAME:Dockerfile"
```

You can view images using the following command (shows the repo names and IDs ):

```
sudo docker images
```

Run a container with the following command (make sure to replace IMAGEID with the ID of the image):

```
sudo docker run --name rosmelodicgazebo -it --privileged -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix IMAGEID
```

## Running Additional Bash Sessions

To get a list of all containers created, use the following command (includes names and IDs):

```
sudo docker ps
```

Using the name of the container as the ID, an additional bash session can be started in the same container by running:

```
sudo docker exec -it rosmelodicgazebo bash
```

In case the container is exited, the following command will run it again (then start another bash session):

```
sudo docker start rosmelodicgazebo
```

## Gazebo

Make sure to update/upgrade:

```
sudo apt-get update && sudo apt-get upgrade
```

Run gazebo to test if a GUI will display:

```
gazebo
```

## ROS

source ros:

```
source /opt/ros/melodic/setup.bash
or
source ros_entrypoint.sh
```

Go into the catkin workspace already created:

```
cd catkin_ws
```

Run roscore:

```
roscore
```

Install and launch a package to test the GUI display:

```
 sudo apt-get install ros-$ROS_DISTRO-fetch-gazebo-demo
 cd catkin_ws
 roslaunch fetch_gazebo simulation.launch
```

