# in-outdoor-navigation-copter
Final year project to navigate a copter indoor and outdoor

This file is used for explaining the final year project done by Xianzhen. Some necessary softwares, simulation tools, operation sysytem and how to configure the operation environment will be introduced in this file.

Operation system
This project is developed and tested on Ubuntu 16.04.

Robot Operation System
ROS Kinetic Kame is used in this project. 

Install gazebo simulator and ROS
1. A script called "ubuntu_sim_ros_gazebo.sh" is used to install Gazebo with ROS. 
The script can be downloaded on https://raw.githubusercontent.com/PX4/Devguide/master/build_scripts/ubuntu_sim_ros_gazebo.sh.
2. Before using the script, you should make the user a member of the group "dialout" follwing the commands below.
   sudo usermod -a -G dialout $USER
   Logout and login again (the change is only made after a new login) 
3. source ubuntu_sim_ros_gazebo.sh
For detailed steps, please read the intructions on https://dev.px4.io/en/setup/dev_env_linux.html

After that Gazebo 7 and ROS kinetic will be installed. A ROS workspace is created at ~/catkin_ws/. 
Please check ~/catkin_ws/src to see if there are two folders called "mavros" and "mavlink". If no, please clone this two package from https://github.com/mavlink and then build your ROS workspace using "catkin build".

PX4 Firmware
We will be using PX4 v1.8.0 throughout our tutorial.
1. cd ~/catkin_ws/src
#it could take a while
2. git clone https://github.com/PX4/Firmware.git
3. cd Firmware
4. git checkout v1.8.0
5. make posix_sitl_default gazebo

Now you should see a window pop out and a drone is centered in the middle of the environment, but let's close the window for now. 

Modifying your 'bashrc' so that your environment remains the same every time you open a new terminal:
open a new terminal:
# Use your favorite editor, we will be using gedit
# NOTE: you will need to use ROOT to edit bashrc
1. sudo gedit ~/.bashrc

# in the new terminal, scroll down to the bottom and add the following lines to the bottom
# if you used apt to install MAVROS (method 1), there would be no setup.bash under ~/catkin_ws/devel/
# and you can safely neglect the first step.
2. source ~/catkin_ws/devel/setup.bash
3. source ~/catkin_ws/src/Firmware/Tools/setup_gazebo.bash ~/catkin_ws/src/Firmware/ ~/catkin_ws/src/Firmware/build/posix_sitl_default
4. export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/catkin_ws/src/Firmware
5. export ROS_PACKAGE_PATH=$ROS_PACKAGE_PATH:~/catkin_ws/src/Firmware/Tools/sitl_gazebo

Now you can try to launch MAVROS and Gazebo simulation at the same time using 
1. roslaunch px4 mavros_posix_sitl.launch

You can also check the mavros state using 
1. rostopic echo /mavros/state 

Using GAAS project 
1. git clone git@github.com:generalized-intelligence/GAAS.git

You will see four folders in ~/GAAS:
demo: everything we need for this tutorial can be found in this folder;
hardware: it contains relevant information on how to build the hardware;
simulator: it contains drone simulation models, world models etc. ;
software: it contains other packages such as ObstacleMap, SLAM, Local and Global path planner etc

Next, add customized model path to bashrc by:
1. echo "export GAZEBO_MODEL_PATH=${GAZEBO_MODEL_PATH}:(GAAS_PATH)/simulator/models" >> ~/.bashrc

Copy models and config files to corresponding PX4 folders:
# if you cloned GAAS to a different place. 
#REPLACE (GAAS_PTH) WITH A REAL PATH 
1. cp -r (GAAS_PATH)/simulator/models/* ~/catkin_ws/src/Firmware/Tools/sitl_gazebo/models/
2. cp -r (GAAS_PATH)/GAAS/simulator/worlds/* ~/catkin_ws/src/Firmware/Tools/sitl_gazebo/worlds/
3. cp -r (GAAS_PATH)/GAAS/simulator/posix-config/* ~/catkin_ws/src/Firmware/posix-configs/SITL/init/ekf2/

Control a drone using python script
1. cd  /home/guo/GAAS/demo/tutorial_3
# make the drone takeoff
2. python px4_mavros_run.py 
#  make the drone fly in a square way
3. python square.py
## You should make the drone take off first before giving any mission commands.

Simulating SLAM navigation
Please refer to "https://gaas.gitbook.io/guide/build-your-own-autonomous-drone-part-3-using-slam-in-gps-denied-environment-for-position-estimation" for detailed information about how to implementing SLAM navigation using GAAS.

Combination of SLAM and GPS navigation 
1. This is a launch file in ~/home/guo/catkin_ws/src/Firmware/launch called slam'launch
2.  Replace "iris_stereo_gray_no_gps" in <arg name="vehicle" default="iris_stereo_gray_no_gps"/> with "iris_stereo_gray_gps"

Then you can use the launch file as the way in SLAM navigation

To enable vision and GPS as the source of external position estimation, select the top left GEAR button and select "Parameters", input "vision" in the search and tick "vision position fusion" and "GPS fusion". Click "save" to save the modification.

Next, hit "Clear" , select "Tools" and choose "reboot vehicle" to make modifications take effect.

Now you can simulation a combination of SLAM and GPS navigation. 
If you want to stop GPS, please use
1. gpssim stop
If you want to restar GPS, please use
2. gpssim start
# These two commands should be typed in the twrminal where you type "roslaunch px4 slam.launch"

There are some useful websites for your project.
1. GAAS gitbook : https://gaas.gitbook.io/guide/
2. General ROS introduction: http://wiki.ros.org/
3. MAVROS: http://wiki.ros.org/mavros
4. MAVLINK: https://mavlink.io/
5. Gazebo: http://gazebosim.org/
6. PX4 development guide:https://dev.px4.io/en/ 


