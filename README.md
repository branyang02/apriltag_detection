<!-- ABOUT THE PROJECT -->
## Object Localization using AprilTag ROS for Robotics


### Built With

* Ubuntu 18.04
* [ROS Melodic](http://wiki.ros.org/melodic/Installation/Ubuntu)
* [OpenCV](https://docs.opencv.org/3.4/d7/d9f/tutorial_linux_install.html)



<!-- GETTING STARTED -->
## Getting Started
1. **Install ROS USB driver**

    1. Clone ROS USB camera driver
        ```sh
       mkdir -p catkin_ws/src && cd catkin_ws/src
       git clone https://github.com/bosch-ros-pkg/usb_cam.git
       ```
    2. Create USB camera directory
       ```sh
       cd ..
       catkin_make
       ```
    3. Set up ROS environment
       ```sh
       source ./devel/setup.bash  # need to run this command everytime a new terminal window is opened
       ```
    4. Build ennvironment
       ```sh
       cd src/usb_cam
       mkdir build && cd build
       cmake ..
       ```
    5. Check available cameras/webcams (make sure camera is connected)
        ```sh
        sudo apt-get install v4l-utils
        v4l2-ctl --list-devices

        # sample output
        # Integrated Camera (usb-0000:00:1a.0-1.6):
        #         /dev/video0
        ```
    6. Launch USB camera
        ```sh
        cd ..
        cd launch
        roslaunch usb_cam usb_cam-test.launch
        ```
2. **Install AprilTag_ros driver**
    1. Clone AprilTag repo
        ```sh
        cd ~/  # or choose other installation location
        git clone https://github.com/AprilRobotics/apriltag.git
        ```
    2. Install AprilTag
        ```sh
        cd apriltag
        mkdir build && cd build
        cmake ..
        sudo make install
        ```
    3. Clone AprilTag_ros repo and install
        ```sh
        cd ~/catkin_ws/src
        git clone https://github.com/AprilRobotics/apriltag_ros.git
        cd ..
        catkin_make
        ```
3. **Camera Calibration**
    1. Install ROS camera calibration package
        ```sh
        sudo apt-get install ros-melodic-camera-calibration
        ```
    2. Perform camera calibration
        ```sh
        roslaunch usb_cam usb_cam-test.launch  # turn on USB camera
        rosrun camera_calibration cameracalibrator.py --size 8x6 --square 0.024 image:=/usb_cam/image_raw camera:=/usb_cam
        ```
    The example above uses an 8x6 checkerboard with 24mm squares (Calibration uses the interior vertex points of the checkerboard, so an "9x7" board uses the interior vertex parameter "8x6" as in the example below). 

    _Download the official ROS 8x6 checkerboard [here](http://wiki.ros.org/camera_calibration/Tutorials/MonocularCalibration?action=AttachFile&do=view&target=check-108.pdf)._

    Move checkerboard up/down/left/right until "CALIBRATE" button turns green, then click on "CAILIBRATE", wait for the system to complete calibration calculation, then click on "SAVE", then "COMMIT".

    _When you click on the "Save" button after a succesfull calibration, the data (calibration data and images used for calibration) will be written to /tmp/calibrationdata.tar.gz._

## Usage
1. **Using AprilTag_Ros and Detecting AprilTags**
    1. go to /apriltag_ros/apriltag_ros/config and configure **settings.yaml** and **tags.yaml**.
    
        check out http://wiki.ros.org/apriltag_ros/Tutorials/Detection%20in%20a%20video%20stream for parameter setup.

        1. settings.yaml 
            ```yaml
            tag_family:        'tag36h11'
            tag_border:        1 
            tag_threads:       2 
            tag_decimate:      1.0 
            tag_blur:          0.0 
            tag_refine_edges:  1 
            tag_refine_decode: 0 
            tag_refine_pose:   0 
            tag_debug:         0 
            publish_tf:        true
            ```
        2. tags.yaml (Values based on the diagram below)
            ```yaml
            standalone_tags:
              [
                {id: 10, size: 0.15},
                {id: 20, size: 0.1},
                {id: 30, size: 0.07}
              ]
            tag_bundles:
              [
                {
                  name: 'my_bundle',
                  layout:
                    [
                      {id: 0, size: 0.05, x: 0.0000, y: 0.0000, z: 0.0, qw: 1.0, qx: 0.0, qy: 0.0, qz: 0.0},
                      {id: 4, size: 0.05, x: 0.0548, y: -0.0522, z: 0.0, qw: 1.0, qx: 0.0, qy: 0.0, qz: 0.0},
                      {id: 3, size: 0.05, x: -0.0580, y: -0.0553, z: 0.0, qw: 1.0, qx: 0.0, qy: 0.0, qz: 0.0},
                      {id: 2, size: 0.05, x: 0.0543, y: 0.0603, z: 0.0, qw: 1.0, qx: 0.0, qy: 0.0, qz: 0.0},
                      {id: 1, size: 0.05, x: -0.0582, y: 0.0573, z: 0.0, qw: 1.0, qx: 0.0, qy: 0.0, qz: 0.0}
                    ]
                }
              ]
              ```
              ![Image from iOS](https://user-images.githubusercontent.com/107154811/193395390-64fb8e61-8caa-4620-9a18-d40dadf45851.jpg)
        3. Go to apriltag_ros/apriltag_ros/launch, add the following lines to **continuous_detection.launch**
            ```launch
             <arg name="camera_name" default="/usb_cam" />
             <arg name="camera_frame" default="usb_cam" />  
             <arg name="image_topic" default="image_raw" />
             ```
         

## Usage
1. Launch camera
    ```sh
    roslaunch usb_cam usb_cam-test.launch
    ```
2. Launch AprilTag detection
    ```sh
    roslaunch apriltag_ros continuous_detection.launch
    ```
3. View results
    ```sh
    rostopic echo /tag_detections
    ```
4. Visualize results
    ```sh
    rqt_image_view
    ```
    select tag_detections_image from drop-down menu in rqt ImageView
5. Run RViz
    ```sh
    rosrun rviz rviz
    ```
    1. Select appropiate Fixed Frame.
    2. Click "Add" on bottom right in RViz, under "By display type", scroll down to "TF", select and click OK.
    3. The following interface should appear:
    ![image](https://user-images.githubusercontent.com/107154811/193395706-a692427b-5fc0-4f4f-8c2b-cedd928a8de3.png)






<!-- Distance -->
## Detecting distance between two objects (tags)








See the [open issues](https://github.com/github_username/repo_name/issues) for a full list of proposed features (and known issues).

<p align="right">(<a href="#readme-top">back to top</a>)</p>

