# robot_localization

# installation 
``` bash
git clone https://github.com/cra-ros-pkg/robot_localization.git
```
or 
``` bash
sudo apt-get install -y ros-kinetic-robot-localization
```

# create new package 
``` bash
catkin_create_pkg imu_odom_t std_msgs rospy roscpp
```
1- param folder :
``` yaml
frequency: 100
sensor_timeout: 0.1
two_d_mode: false
transform_time_offset: 0.0
print_diagnostics: true
debug: false
# debug_out_file: /path/to/debug/file.txt
map_frame: map      # Defaults to "map" if unspecified
odom_frame: odom           # Defaults to "odom" if unspecified
base_link_frame: base_link # Defaults to "base_link" if unspecified
world_frame:         # map  or odom

imu0: imu/data
imu0_config: [false, false, false,
              true,  true,  true,
              false, false, false,
              true,  true,  true,
              true,  false, false]
imu0_queue_size: 10
imu0_nodelay: false
imu0_differential: false
imu0_relative: true

process_noise_covariance: [0.05, 0,    0,    0,    0,    0,    0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0.05, 0,    0,    0,    0,    0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0.06, 0,    0,    0,    0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0.03, 0,    0,    0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0.03, 0,    0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0.06, 0,     0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0.025, 0,     0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0.025, 0,    0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0.04, 0,    0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0.01, 0,    0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0,    0.01, 0,    0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0,    0,    0.02, 0,    0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0,    0,    0,    0.01, 0,    0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0,    0,    0,    0,    0.01, 0,
                           0,    0,    0,    0,    0,    0,    0,     0,     0,    0,    0,    0,    0,    0,    0.015]

initial_estimate_covariance: [1e-9, 0,    0,    0,    0,    0,    0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    1e-9, 0,    0,    0,    0,    0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    1e-9, 0,    0,    0,    0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    1e-9, 0,    0,    0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    1e-9, 0,    0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    1e-9, 0,    0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    1e-9, 0,    0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    1e-9, 0,    0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    1e-9, 0,     0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    1e-9,  0,     0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    0,     1e-9,  0,     0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    0,     0,     1e-9,  0,    0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    0,     0,     0,     1e-9, 0,    0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    0,     0,     0,     0,    1e-9, 0,
                              0,    0,    0,    0,    0,    0,    0,    0,    0,    0,     0,     0,     0,    0,    1e-9]
```
craete two yaml files 
for the first file (map_to_odom.yaml)
``` yaml
world_frame: map
```
for the second file (odom_to_base.yaml)
``` yaml
world_frame: odom
```

2- launch file :
``` xml
  <node pkg="robot_localization" type="ekf_localization_node" name="map_to_odom"
        clear_params="true" output="screen">
    <rosparam command="load" file="$(find imu_odom_t)/params/map_to_odom.yaml"/>
    <!--  Output topic remapping -->
    <remap from="odometry/filtered" to="/odom"/> 
  </node>

  <node pkg="robot_localization" type="ekf_localization_node" name="odom_to_base"
        clear_params="true" output="screen">
    <rosparam command="load" file="$(find imu_odom_t)/params/odom_to_base.yaml"/> 
  </node>
```

# https://github.com/weihsinc/robot_localization.git
