# pcml
Point Cloud and Machine Learning

## Requirements
* Ubuntu 14.04
* ROS indigo
* ROS Eigen
* Moveit!
* Kinect hardware, v1.x
* Kinect Linux driver (PrimeSense? SensorKinect?)
* openni_launch
 * $ sudo apt-get install ros-indigo-openni-launch
* openni_tracker
 * $ sudo apt-get install ros-indigo-openni-tracker
* libsvm (https://www.csie.ntu.edu.tw/~cjlin/libsvm/)
 * Put 'svm.h', 'svm.cpp' into lib/libsvm
* yaml-cpp (https://github.com/jbeder/yaml-cpp)
 * (Add -fPIC option to yaml-cpp/CMakeLists.txt
   $ mkdir build
   $ cd build  
   $ cmake ..
   $ make
 * Copy include/yaml-cpp directory and libyaml-cpp.a to pcml/lib directory
* (optional) CAD120 dataset
 * Fix inconsistency:  
   1. Subject1_annotations/having_meal/0510182019_obj1.txt, delete the last line starting '500,'.
* (optional) kinect_network package (github: https://github.com/pjsdream/kinect_network)

## Components
* msgs
 * FutureObstacleDistribution
 * FutureObstacleDistributions
* Nodes
 * future_obstacle_publisher  
     **Parameters**  
       input_stream_type (string, default: realtime)  
         One of "realtime", "cad120"  
       joints_type (string, default: upper_body)  
         One of "whole_body", "upper_body"  
       render (bool, default: false)  
       cad120_directory (string, required when input_stream_type = "cad120")  
     **Subscribed Topics**  
       TF topics published by openni_tracker (/head_1, /neck_1, etc.)
     **Published Topics**  
       future_obstacle_publisher/future_obstacle_distributions (pcml/FutureObstacleDistributions)
 * future_obstacle_visualizer  
     **Subscribed topics**  
       future_obstacle_publisher/future_obstacle_distributions (pcml/FutureObstacleDistributions)  
     **Published Topics**  
       /future_obstacle_distributions_marker_array (visualization_msgs/MarkerArray)
* Launches
 * future_obstacle_publisher.launch

## Build
* Package is organized using rosbuild  
  $ rosmake

## Run
$ roslaunch openni_launch openni.launch  
$ rosrun openni_tracker openni_tracker  
$ rosrun rviz rviz  
$ roslaunch pcml future_obstacle_publisher  
 * Make the openni_tracker track somebody
 * Modify .launch file parameters

## Run with Kinect v2 skeleton stream
* install kinect_network package (github: https://github.com/pjsdream/kinect_network)  
$ roslaunch kinect_network ip:=(...) port:=(...)  
$ roslaunch pcml kinect2_obstacle_publisher

## Train model (not complete yet)
* Run 'train' executable with '-d MODEL_DIRECTORY_PATH' option (required)
 * Example:  
   $ ./train -d /playpen/jaesungp/indigo_workspace/pcml/model1
 * Modify training parameters in the file 'config.yaml' in the MODEL_DIRECTORY_PATH. Training result is saved in the MODEL_DIRECTORY_PATH.
 * To disable log print by svm_train, modify 'lib/libsvm/svm.cpp' where static void info(...) function can be chosen.

