# ROS Simulation

Using RViz and Gazebo in ROS.

## Getting Started

These instructions will get you a working example of a ROS system. This system uses the turtlebot source code. It the physics engine Gazebo to simulate the turtlebot. It also uses RViz to visulize the data.

### Prerequisites

You will need a working version of ROS. This tutorial was tried and tested in ROS Kinetic. You can check which version of ROS you have by running the command:

```
$ ls /opt/ros
```

### Installing

You first will need to install all of the dependencies.

```
$ sudo apt-get install ros-kinetic-joy ros-kinetic-teleop-twist-joy ros-kinetic-teleop-twist-keyboard ros-kinetic-laser-proc ros-kinetic-rgbd-launch ros-kinetic-depthimage-to-laserscan ros-kinetic-rosserial-arduino ros-kinetic-rosserial-python ros-kinetic-rosserial-server ros-kinetic-rosserial-client ros-kinetic-rosserial-msgs ros-kinetic-amcl ros-kinetic-map-server ros-kinetic-move-base ros-kinetic-urdf ros-kinetic-xacro ros-kinetic-compressed-image-transport ros-kinetic-rqt-image-view ros-kinetic-gmapping ros-kinetic-navigation ros-kinetic-interactive-markers
```

You will also need to make sure you have git installed.

```
$ sudo apt-get install git
```

Next make a ROS workspace. This will contain all the code you are working on.

```
$ mkdir -p ~/catkin_ws/src
```

Download the source code:

```
$ cd ~/catkin_ws/src/
$ git clone https://github.com/ROBOTIS-GIT/turtlebot3_msgs.git
$ git clone https://github.com/ROBOTIS-GIT/turtlebot3.git
$ git clone https://github.com/ROBOTIS-GIT/turtlebot3_simulations.git
```

We then need to make the files

```
$ cd ~/catkin_ws/
$ catkin_make
```

Source the workspace

```
source ~/catkin_ws/devel/setup.bash
```

## Running

Now we have the source code lets see how we are able to get it up and running.

### Physics Simulation

Gazebo is a physics engine in which we will run our simulations. Before executing the simulation we need to specifiy the model of the turtlebot.

```
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_empty_world.launch
```

### Viewing Sensor Data

Lets first inspect the data. We can inspect the data using a new terminal. Lets first see what data our robot is publishing:

```
$ rostopic list
```

Lets then see what the scan data comprises of.

```
$ rostopic info /scan
```

We can double check its publishing correctly using:

```
$ rostopic echo /scan
```

You will notice that all ranges are inf. That is because the robot is being simulated in an open world, with no obstacles in sight. Lets manually add an obstacle. To do that click the box icon in the toolbar of gazebo.


Lets try and visualize the data in RViz. To open RViz we can run the following command

```
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_gazebo_rviz.launch
```

Lets see if we can understand what we did. The first thing we launched was the ```turltebot3_empty_world.launch```. Inside that file we will see the following:

```
<launch>
  <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>
  <arg name="x_pos" default="0.0"/>
  <arg name="y_pos" default="0.0"/>
  <arg name="z_pos" default="0.0"/>

  <include file="$(find gazebo_ros)/launch/empty_world.launch">
    <arg name="world_name" value="$(find turtlebot3_gazebo)/worlds/empty.world"/>
    <arg name="paused" value="false"/>
    <arg name="use_sim_time" value="true"/>
    <arg name="gui" value="true"/>
    <arg name="headless" value="false"/>
    <arg name="debug" value="false"/>
  </include>

  <param name="robot_description" command="$(find xacro)/xacro --inorder $(find turtlebot3_description)/urdf/turtlebot3_$(arg model).urdf.xacro" />

  <node pkg="gazebo_ros" type="spawn_model" name="spawn_urdf" args="-urdf -model turtlebot3_$(arg model) -x $(arg x_pos) -y $(arg y_pos) -z $(arg z_pos) -param robot_description" />
</launch>
```

We can see we first create an arguement named model. With a starting position 0,0,0. If we wanted to move the robot we could change the starting position. The next include includes the ```empty_world.launch``` file. You can find this file in:

```
$ cd /opt/ros/kinetic/share/gazebo_ros/launch/
$ cat empty_world.launch
```

Finally the gazebo node is launched. The second file is the customized RViz launch file ```turtlebot3_gazebo_rviz.launch```. Inside that file we will see the following:

```
<launch>
  <arg name="model" default="$(env TURTLEBOT3_MODEL)" doc="model type [burger, waffle, waffle_pi]"/>

  <include file="$(find turtlebot3_bringup)/launch/turtlebot3_remote.launch">
    <arg name="model" value="$(arg model)"/>
  </include>

  <node name="rviz" pkg="rviz" type="rviz" args="-d $(find turtlebot3_gazebo)/rviz/turtlebot3_gazebo_model.rviz"/>
</launch>
```

Inside this file we can see that we are launching the node ```rviz``` with a custom save file ```turtlebot3_gazebo_model.rviz```. Inside that is where all the correct setting have been checked which allow RViz to straight away know what you want to display. Lets see if we can mimic this behaviour without using the launch file. Launch RViz by default:

```
$ rosrun rviz rviz
```

Firstly change the Fixed Frame from map to odom. Next lets say we want to visualize the laser scan data. We can do that by clicking add on the bottom left and then selecting laser scan. Once you had the laser scan topic, next you need to change the scan topic to ```/scan```. You will notice that we get an error. That base_scan does not exist.

```
$ roslaunch turtlebot3_bringup turtlebot3_remote.launch
```

Why did it do that? It publishes the coordinate transformation such as base_footprint and odom and base_todo


### Driving the Simulation

Lets now see how we could drive the robot around a room. To do this lets open a more complex world.

```
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_autorace.launch
```

In another terminal launch the node which takes the keyboard commands and converts them into velocity commands for the robot:

```
$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
```

Lets see if we can visualize data while driving around. We will use the preconfigured rviz launch file.

```
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_gazebo turtlebot3_gazebo_rviz.launch
```

### Mapping

We are also able to create maps using RViz and Gazebo. This example has an implementation of similtaneous localization and mapping [SLAM](https://en.wikipedia.org/wiki/Simultaneous_localization_and_mapping). Lets see how it works and if we can visualize the data in RViz.

```
export TURTLEBOT3_MODEL=waffle_pi
roslaunch turtlebot3_gazebo turtlebot3_house.launch
```

Lets launch the slam visualization

```
$ export TURTLEBOT3_MODEL=waffle_pi
$ roslaunch turtlebot3_slam turtlebot3_slam.launch slam_methods:=gmapping
```

We will notice that the robot is now creating a map object as well as displaying the laser scanner. Lets see what happens if we drive around the room. To drive launch the teleop package.

```
$ roslaunch turtlebot3_teleop turtlebot3_teleop_key.launch
```

We can see we are able to map a house using our robot. For this tutorial I wont be going into the details of SLAM, however lets figure out how it was plotted to RViz. The first thing we will notice is that it is published to a ```/map``` topic. Lets see what type of message it is publishing:


```
$ rostopic info /map
```

We will notice it is of type [nav_msgs/OccupancyGrid](http://docs.ros.org/melodic/api/nav_msgs/html/msg/OccupancyGrid.html). I have included some of the documentation below:

```
# This represents a 2-D grid map, in which each cell represents the probability of
# occupancy.

Header header 

#MetaData for the map
MapMetaData info

# The map data, in row-major order, starting with (0,0).  Occupancy
# probabilities are in the range [0,100].  Unknown is -1.
int8[] data
```

That makes sense so we are expecting a 2D array with probabilities between 0-100 of whether or not an object is there. Lets see if we can see this in on the map topic. We can view the raw map topic using:

```
$ rostopic echo /map
```

### Creating a world in Gazebo





## Authors

* **Carl Hildebrandt** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)



## Acknowledgments

* http://emanual.robotis.com/docs/en/platform/turtlebot3/overview/


