GStreamer-based Camera Node for ROS 1
=====================================
This GStreamer based camera ROS node is from [https://github.com/ros-drivers/gscam](https://github.com/ros-drivers/gscam). Some changes are made to customize the original ROS node for the uses with Robotics SDK for TDA4:

* Added GStreamer pipelines that use [the GStreamer plugins optimized for TDA4 devices](https://github.com/TexasInstruments/edgeai-gst-plugins), and added NV12 encoding mode.
* Added an example `camera_info.yaml` for Logitech webcam C920 and associated LDC look-up-table file (required to use TI TDA4 LDC hardware accelerator).
* Added launch files (under 'launch' folder) for Logitech webcam (in MJPG mode and YUYV mode), and OV5640 (in YUYV mode).
* Dependency: following modules are already built and installed in the the Robotics SDK ROS Docker images.
    * [edgeai-tiovx-modules](https://github.com/TexasInstruments/edgeai-tiovx-modules)
    * [edgeai-gst-plugins](https://github.com/TexasInstruments/edgeai-gst-plugins)

## Usage

1. Camera Calibration and Rectification Map Generation: See corresponding parts of [mono_capture/README.md](../mono_capture/README.md).


2. Build the ROS node:
    ```
    cd $ROS_WS
    catkin_make --source /opt/robotics_sdk/ros1
    source devel/setup.bash
    ```

3. Launch the "gscam" ROS node:
Before launching the "gscam", please make sure to update `device` in the launch file to point to correct camera device (`/dev/videoX`).
    For capturing in MJPG (motion JPEG) mode,
    ```
    roslaunch gscam v4l_mjpg.launch
    ```
    For capturing in YUYV mode,
    ```
    roslaunch gscam v4l_yuv.launch
    roslaunch gscam v4l_ov5640.launch # for OV5640 CSI camera
    ```

**Note**: The measured framerate for the output topic can be less than the framerate set in the launch file, depending on the light condition of the scenes. This is a normal behavior inherited from `v4l2src` and the ISP settings of, e.g., USB webcam.

## Visualization on Remote Ubuntu PC
By default, the output topic `raw_image` is published in NV12 color format to make more efficient when the node is integrated with the vision vision CNN processing chain (including `ti_vision_cnn` ROS node) on TDA4. We provide a launch file for visualization on the remote Ubuntu (included in `ti_viz_nodes` ROS package).

In the PC Docker container,
```
roslaunch ti_viz_nodes gscam_nv12.launch
```
