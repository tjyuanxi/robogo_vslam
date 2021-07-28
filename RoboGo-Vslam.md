# RoboGo-Vslam 部署说明

由于我们是基于AIBOX已有环境进行部署，所以需要在不影响AIBOX已有功能的前提下进行。因为AIBOX当前功能很齐全繁多，所以依赖的库文件较多。所以建议ROS安装在Docker上, 从而使相互的环境隔离，库文件相互不冲突。经过测试，ROS的库文件和AIBOX的库文件版本有较多冲突，特别使opencv、boost等库文件。

## 1、环境要求

AIBOX： 镜像版本要求1.2.8.191及以上版本

ROS: melodic

## 2、按照ROS 镜像

AIBOX已预装了Docker，所以无需重新安装docker，只需要安装镜像即可。

```
1、镜像链接：git clone https://github.com/atinfinity/jetson_ros_docker.git

2、进入下载的文件夹，加载docker文件: docker build -t jetson/ros:melodic-ngc .此步骤需要的时间较长。——此步骤过程中请保持网络通畅

3、启动dockers容器：sudo  ./launch_container.sh
```

执行完成后及进入了容器中，与AIBOX本身环境隔离了。注意此过程文件未固化。

## 3、安装依赖

通常Vsalm 模型都是依赖第三方ceres 库的，所以需要安装ceres，ceres需要编译安装。

### 3.1、安装ceres的依赖库：

```
sudo apt-get install libgoogle-glog-dev libgflags-dev
sudo apt-get install libatlas-base-dev
sudo apt-get install libeigen3-dev
sudo apt-get install libsuitesparse-dev
```

### 3.2、下载

```
git clone https://ceres-solver.googlesource.com/ceres-solver
```

### 3.3、编译安装

```
cd ceres-solver
mkdir build
cmake ..
make -j4
sudo make install
```

如安装遇到问题，请参与官方建议：

[ceres]: http://www.ceres-solver.org/installation.html



## 4、下载编译vslam模型

依次执行如下命令：

```
	mkdir src
	cd src
	git clone https://github.com/HKUST-Aerial-Robotics/VINS-Fusion.git
	cd ..
	catkin_make
```

注意执行catkin_make 的目录为与src文件夹同级目录，否则回报错

## 5、下载数据集

网站链接：

[EuRoC MAV Dataset]: https://projects.asl.ethz.ch/datasets/doku.php?id=kmavvisualinertialdatasets

```
wget XXXXXX.bag
```

请选择ROS bag， 自行选择下载。

## 6、运行

分别在三个窗口先后执行如下命令：

```
source ~/catkin_ws/devel/setup.bash ——根据具体环境进行修改成绝对路径
roslaunch vins vins_rviz.launch
```

```
source ~/catkin_ws/devel/setup.bash ——根据具体环境进行修改成绝对路径
rosrun vins vins_node ~/catkin_ws/src/VINS-Fusion/config/euroc/euroc_mono_imu_config.yaml 
```

```
source ~/catkin_ws/devel/setup.bash ——根据具体环境进行修改成绝对路径
rosbag play YOUR_DATASET_FOLDER/MH_01_easy.bag ——注意数据集的路径
```

![image-20210728170941722](C:\Users\ubt\AppData\Roaming\Typora\typora-user-images\image-20210728170941722.png)

## 注意：

本文引用了：https://github.com/HKUST-Aerial-Robotics/VINS-Fusion。多谢他们的无私分享