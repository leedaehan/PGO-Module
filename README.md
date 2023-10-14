# PGO-Module
+ This repository is a Pose graph optimization module which is able to be combined with your LIO / LO algorithm
    + Pose graph optimization module is based on [LIO-SAM](https://github.com/TixiaoShan/LIO-SAM) paper
    + Loop-detection is based on radius search and ICP is used to calc matching
 
+ Note : You can use FAST-LIO / Faster-LIO with PGO-Module in this package
+ Note2 : You can also use your LIO / LO algorithm with this PGO-Module using `git submodule add` your package in third_party

<br>

## Dependencies
+ ROS (it comes with `Eigen` and `PCL`)
+ [GTSAM](https://github.com/borglab/gtsam)
    ```shell
    wget -O gtsam.zip https://github.com/borglab/gtsam/archive/refs/tags/4.1.1.zip
    unzip gtsam.zip
    cd gtsam-4.1.1/
    mkdir build && cd build
    cmake -DGTSAM_BUILD_WITH_MARCH_NATIVE=OFF -DGTSAM_USE_SYSTEM_EIGEN=ON ..
    sudo make install -j16
    ```

## How to build and use
+ Get the code and build
    ```shell
    cd ~/your_workspace/src
    git clone https://github.com/leedaehan/PGO-Module.git --recursive

    cd ..
    catkin build -DCMAKE_BUILD_TYPE=Release
    . devel/setup.bash
    ```
+ If you want to run FAST-LIO (change config files in third_party/`FAST_LIO`)
    ```shell
    roslaunch pgo_module fast_lio_sam.launch lidar:=ouster
    roslaunch pgo_module fast_lio_sam.launch lidar:=velodyne
    roslaunch pgo_module fast_lio_sam.launch lidar:=livox
    ```
+ If you want to run Faster-LIO (change config files in third_party/`faster-lio`)
    ```shell
    roslaunch pgo_module faster_lio_sam.launch lidar:=ouster
    roslaunch pgo_module faster_lio_sam.launch lidar:=velodyne
    roslaunch pgo_module faster_lio_sam.launch lidar:=livox
    roslaunch pgo_module faster_lio_sam.launch lidar:=hesai
    ```
+ If you want to run your LIO / LO algorithm with PGO-Module
    ```shell
    cd ~/your_workspace/src/pgo_module
    git submodule add <Your Github Repository URL> third_party/<Package name>
    ```
    For example
    ```shell
    cd ~/your_workspace/src/pgo_module
    git submodule add https://github.com/username/mydependency.git third_party/mydependency
    ```

<br>

### Structure
+ odom_pcd_cb
    + pub realtime pose in corrected frame
    + keyframe detection -> if keyframe, add to pose graph + save to keyframe queue
    + pose graph optimization with iSAM2
+ loop_timer_func
    + process a saved keyframe
        + detect loop -> if loop, add to pose graph
+ vis_timer_func
    + visualize all **(Note: global map is only visualized once uncheck/check the mapped_pcd in rviz to save comp.)**
