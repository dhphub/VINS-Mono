# VINS-Mono
## A Robust and Versatile Monocular Visual-Inertial State Estimator

VINS-Mono is a real-time SLAM framework for **Monocular Visual-Inertial Systems**. It uses an optimization-based sliding window formulation for providing high-accuracy visual-inertial odometry. It features efficient IMU pre-integration with bias correction, automatic estimator initialization, online extrinsic calibration, failure detection and recovery, loop detection, and global pose graph optimization. VINS-Mono is primarily designed for state estimation and feedback control of autonomous drones, but it is also capable of providing accurate localization for AR applications. This code runs on **Linux**, and is fully integrated with **ROS**. For **iOS** mobile implementation, please go to [VINS-Mobile](https://github.com/HKUST-Aerial-Robotics/VINS-Mobile).

**Authors:** [Tong Qin](https://github.com/qintony), [Peiliang Li](https://github.com/PeiliangLi), [Zhenfei Yang](https://github.com/dvorak0), and [Shaojie Shen](http://www.ece.ust.hk/ece.php/profile/facultydetail/eeshaojie) from the [HUKST Aerial Robotics Group](http://uav.ust.hk/)

**Videos:**

<a href="https://www.youtube.com/embed/mv_9snb_bKs" target="_blank"><img src="http://img.youtube.com/vi/mv_9snb_bKs/0.jpg" 
alt="euroc" width="240" height="180" border="10" /></a>
<a href="https://www.youtube.com/embed/g_wN0Nt0VAU" target="_blank"><img src="http://img.youtube.com/vi/g_wN0Nt0VAU/0.jpg" 
alt="indoor_outdoor" width="240" height="180" border="10" /></a>
<a href="https://www.youtube.com/embed/I4txdvGhT6I" target="_blank"><img src="http://img.youtube.com/vi/I4txdvGhT6I/0.jpg" 
alt="AR_demo" width="240" height="180" border="10" /></a>

EuRoC dataset;                  Indoor and outdoor performance;                         AR application;

<a href="https://www.youtube.com/embed/2zE84HqT0es" target="_blank"><img src="http://img.youtube.com/vi/2zE84HqT0es/0.jpg" 
alt="MAV platform" width="240" height="180" border="10" /></a>
<a href="https://www.youtube.com/embed/CI01qbPWlYY" target="_blank"><img src="http://img.youtube.com/vi/CI01qbPWlYY/0.jpg" 
alt="Mobile platform" width="240" height="180" border="10" /></a>

 MAV application;               Mobile implementation

**Related Papers**
* **VINS-Mono: A Robust and Versatile Monocular Visual-Inertial State Estimator**, Tong Qin, Peiliang Li, Zhenfei Yang, Shaojie Shen (Submitted to ***T-RO***) [pdf](https://github.com/HKUST-Aerial-Robotics/VINS-Mono/blob/master/support_files/paper/tro2017tong.pdf) 
* **Autonomous Aerial Navigation Using Monocular Visual-Inertial Fusion**, Yi Lin, Fei Gao, Tong Qin, Wenliang Gao, Tianbo Liu, William Wu, Zhenfei Yang, Shaojie Shen (Submitted to ***JFR***) [pdf](https://github.com/HKUST-Aerial-Robotics/VINS-Mono/blob/master/support_files/paper/jfr2017yi.pdf)  

*If you use VINS-Mono for your academic research, please cite at least one of our related papers.*

## 1. Prerequisites
i. **Ubuntu** and **ROS**
Ubuntu 14.04 16.04.
ROS Indigo, Kinetic. [ROS Installation](http://wiki.ros.org/indigo/Installation/Ubuntu)
additional ROS pacakge
```
    sudo apt-get install  ros-YOUR_DISTRO-tf ros-YOUR_DISTRO-message-filters ros-YOUR_DISTRO-image-transport
```

ii. **Ceres Solver**
Follow [Ceres Installation](http://ceres-solver.org/installation.html), remember to **make install**.
(Our testing environment: Ubuntu 14.04, ROS Indigo, OpenCV 2.4.8, Eigen 3.2.0) 

## 2. Build VINS-Mono on ROS
Clone the repository and catkin_make:
```
    cd ~/catkin_ws/src
    git clone git@github.com:HKUST-Aerial-Robotics/VINS-Mono.git
    cd ../
    catkin_make
    source ~/catkin_ws/devel/setup.bash
```

## 3. Performance on EuRoC dataset
1.Download [EuRoC MAV Dataset](http://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets). Although it contains stereo cameras, we only use one camera.

2.Open three terminals, launch the vins_estimator , rviz and play the bag file respectively. Take MH_05 as example
```
    roslaunch vins_estimator euroc.launch 
    roslaunch vins_estimator vins_rviz.launch
    rosbag play YOUR_PATH_TO_DATASET/MH_05_difficult.bag 
```

3.(Optional) Visualize ground truth. We write a naive benchmark publisher to help you visualize the ground truth. It uses a naive strategy to align VINS with ground truth. Just for visualization. not for quantitative comparison on academic publications.
```
    roslaunch benchmark_publisher publish.launch  sequence_name:=MH_05_difficult
```
 (Green line is VINS result, red line is ground truth). 
 
4.(Optional) You can even run EuRoC **without extrinsic parameters** between camera and IMU. We will calibrate them online. Replace the first command with:
```
    roslaunch vins_estimator euroc_no_extrinsic_param.launch
```
**No extrinsic parameters** in that config file.  Waiting a few seconds for initial calibration. Sometimes you cannot feel any difference as the calibration is done quickly.

## 4. AR Demo
1.Download the [bag file](https://www.dropbox.com/s/s29oygyhwmllw9k/ar_box.bag?dl=0), which is collected from HKUST Robotic Institute. For friends in mainland China, download from [bag file](https://pan.baidu.com/s/1geEyHNl).

2.Open three terminals, launch the ar_demo, rviz and play the bag file respectively.
```
    roslaunch ar_demo 3dm_bag.launch
    roslaunch ar_demo ar_rviz.launch
    rosbag play YOUR_PATH_TO_DATASET/ar_box.bag 
```
We put one 0.8m x 0.8m x 0.8m virtual box in front of your view. 

## 5. Run with your device 

Suppose you are familiar with ROS and you can get a camera and an IMU with raw metric measurements in ROS topic, you can follow these steps to set up your device. For beginners, we highly recommend you to first try out [VINS-Mobile](https://github.com/HKUST-Aerial-Robotics/VINS-Mobile) if you have iOS devices since you don't need to set up anything.

1. Change to your topic name in the config file.

2. Camera calibration: We support the PINHOLE model and the [MEI model](http://www.robots.ox.ac.uk/~cmei/articles/single_viewpoint_calib_mei_07.pdf). You can calibrate your camera with any tools you like. Just write the parameters in the config file in the right format.

3. Camera-Imu extrinsic parameters: If you have seen the config files for EuRoC and AR demos, you can find that we just use coarse values. If you familiar with transformation, you can figure out the rotation and position by your eyes or via hand measuerments. Then write these values into config as the initial guess. Our estimator will refine extrinsic parameters online. If you don't know anything about the camera-IMU transformation, just ignore the extrinsic parameters and set the **estimate_extrinsic** to **2**, and rotate your device set at the beginning for a few seconds. When the system works successfully, we will save the calibration result. you can use these result as initial values for next time.

4. Other parameter settings: Details are included in the config file.

5. Device Performance: (global shutter camera + synchronized high-end IMU, e.g. VI-Sensor) > (global shutter camera + synchronized low-end IMU, e.g. camera+DJI Flight controller) ? (global camera + unsync high frequency IMU) > (global camera + unsync low frequency IMU) > (rolling camera + unsync low frequency IMU). **DO NOT** start with a rolling shutter camera and unsync IMU (such as DJI M100 + Logitech web camera) at beginning. 


## 6. Acknowledgements
We use [ceres solver](http://ceres-solver.org/) for non-linear optimization and [DBoW2](https://github.com/dorian3d/DBoW2) for loop detection, and a generic [camera model](https://github.com/hengli/camodocal).

## 7. Licence
The source code is released under [GPLv3](http://www.gnu.org/licenses/) license.

We are still working on improving the code reliability. For any technical issues, please contact Tong QIN <tong.qin@connect.ust.hk> or Peiliang LI <pliap@connect.ust.hk>.

For commercial inquiries, please contact Shaojie SHEN <eeshaojie@ust.hk>

<!---
VINS-Android and VINS-Mapping will be released soon...
-->

