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


We can see it is of type LaserScan. Lets try and visualize it using RViz.

```
$ rosrun rviz rviz
```

Click on Add. Then 
















### And coding style tests

Explain what these tests test and why

```
Give an example
```

## Deployment

Add additional notes about how to deploy this on a live system

## Built With

* [Dropwizard](http://www.dropwizard.io/1.0.2/docs/) - The web framework used
* [Maven](https://maven.apache.org/) - Dependency Management
* [ROME](https://rometools.github.io/rome/) - Used to generate RSS Feeds

## Contributing

Please read [CONTRIBUTING.md](https://gist.github.com/PurpleBooth/b24679402957c63ec426) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/your/project/tags). 

## Authors

* **Billie Thompson** - *Initial work* - [PurpleBooth](https://github.com/PurpleBooth)

See also the list of [contributors](https://github.com/your/project/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Hat tip to anyone whose code was used
* Inspiration
* etc

