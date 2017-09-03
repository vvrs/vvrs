
‌This ROS package has nodes that computes linear and angular velocities/ steering angle for autonomous path following and obstacle avoidance.

# Dependencies
    Numpy
    Matplotlib
    
# Nodes
#### Path Publisher Node
This node, when supplied with a way-point in the layout, generates a sequence of way-points beginning at the given way-point till a stop-point (Point signifying an intersection) is reached. At intersections, the next way-point placed in a different road segment is picked at random.
```
Publications: 
    * /car/waypoints

Subscriptions: 
    * /car/update_waypoints_list
```
#### Control-Points Publisher Node
This node keeps track of the pair of way-points between which is the vehicle is currently traversing. It subscribes to a topic publishing t param and returns the appropriate set of control points required to generate the Bezier curve representing the path to be traversed.
```
Publications: 
    * /car/update_waypoints_list
    * /car/current_segment
    * /car/intersection_segment

Subscriptions: 
    * /car/waypoints
    * /car/t_param
```
#### Virtual Car Node
This node updates the pose of the virtual car, i.e, it moves the virtual
car. The motion is assumed to be taking place on the pre-specified Bezier
curve base path.
```
Publications: 
    * /car/t_param
    * /car/leader_point

Subscriptions: 
    * /car/variation_vel
    * /car/current_segment
```
#### Neighbours Node
This node subscribes to topic publishing all vehicles’ data (pose, veloc-
ity, dimensions) received from the plots. It then separates the vehicles as
four kinds of obstacles - Inlane, Cross-traffic, Alternate lane and publishes
them.
```
Publications: 
    * /car1/opposite_cars
    * /car1/cross_traffic_cars
    * /car1/inline_cars
    * /car1/neighbours

Subscriptions: 
    * /vehicles_info
    * /car1/leader_point
```
#### Stop-point Handler Node
This node publishes linear velocity v stop as zero when the virtual car ap-
proaches a stop-point. Else  v_<sub>max</sub> is set to v_<sub>max</sub>.
```
Publications: 
    * /car1/stop

Subscriptions: 
    * /car1/waypoints
    * /car1/leader_point
```
#### Cross Traffic Handler Node
This node publishes linear velocity v crosstraffic depending on the density
and proximity to obstacles categorized as Cross Traffic.
```
Publications: 
    * /car1/cross_traffic
 
Subscriptions: 
    * /car1/cross_traffic_cars
    * /car1/leader_point
```
#### Follow Overtake Handler Node
This node publishes linear velocity v followovertake and desired deviation
5from path d deviation depending on the density and proximity to obstacles
categorized as Inlane and Alternate lane.
```
Publications: 
    * /car1/follow_overtake

Subscriptions: 
    * /car1/leader_point
    * /car1/oppposite_cars
    * /car1/inline_cars
```
#### Variation Velocity Node
This node publishes linear velocity v desired , set as minimum of v stop , v crosstraf f ic , v f ollowovertake
```
Publications: 
    * /car1/variation_vel

Subscriptions: 
    * /car1/cross_traffic
    * /car1/intersection
    * /car1/follow_overtake
    * /car1/stop
```
#### Low-level Controller Node
This node subscribes to virtual car’s odometry and the car-like robot’s
odometry and publishes command velocities v cmd and ω cmd evaluated as
per the control law.
```
Publications: 
    * /car1/cmd_vel

Subscriptions: 
    * /car1/leader_point
    * /car1/odom
```
#### Odometry Publisher Node
This node subscribes to command velocities v cmd and ω cmd and updates
the car-like robot’s pose.
```
Publications: 
    * /car1/odom
    * /spawn

Subscriptions: 
    * /car1/cmd_vel
```
# Topics
|Topics | Message Types|
|--|--|
| /car/waypoints  |    **project_milestone_2/StringList**|
| /car/update_waypoints_list  |    **std_msgs/String**|
| /car/current_segment  |      **project_milestone_2/ControlPoints**|
| /car/intersection_segment  |     **project_milestone_2/ControlPoints**|
| /car/t_param    |    **std_msgs/Float64**|
| /car/leader_point   |    **nav_msgs/Odometry**|
| /car/variation_vel  |    **std_msgs/Float64**|
| /car/opposite_cars  |    **project_milestone_2/VehicleInfoArray**|
| /car/cross_traffic_cars  |   **project_milestone_2/VehicleInfoArray**|
| /car/inline_cars   |     **project_milestone_2/VehicleInfoArray**|
| /car/neighbours   |  **project_milestone_2/VehicleInfoArray**|
| /vehicles_info    |  **project_milestone_2/VehicleInfoArray**|
| /car/stop    |   **std_msgs/Float64**|
| /car/cross_traffic   |   **std_msgs/Float64**|
| /car/follow_overtake   |     **std_msgs/Float64**|
| /car/variation_vel   |   **std_msgs/Float64**|
| /car/cmd_vel   |     **geometry_msgs/Twist**|
| /car/odom   |    **nav_msgs/Odometry**|
| /spawn   |   **project_milestone_2/SpawnData**|
# Messages

### Standard ROS Message types

### Custom Message types
#### CarDimensions.msg
float64 **wheelbase**
float64 **track**
float64 **length**
float64 **width**
#### CarVelocity.msg
float64 **linear**
float64 **angular**
#### ControlPoints.msg
float64[2] **p1**
float64[2] **p2**
float64[2] **p3**
float64[2] **p4**
#### Location.msg
float64 **x**
float64 **y**
float64 **theta**
#### SpawnData.msg
string **name**
string **case**
Location **location**
CarDimensions **car**
### StringList.msg
string[] **data**
int32 **index**
### VariationData.msg
float64 **vel**
float64 **dev**
### VehicleInfo.msg
Location **location**
CarVelocity **velocity**
CarDimensions **car**
### VehicleInfoArray.msg
time **stamp**
VehicleInfo[] **vehicleInfoArray**
# Parameters

These parameters are loaded to ros parameter server using YAML files stored in config folder. 
```
/car/accel, /car/axle_length,  /car/decel, /car/k_ang, /car/k_x, /car/k_y, /car/length, /car/max_lin_vel, /car/max_steer_angle, /car/min_radius, /car/safe_distance, /car/sensor_range, /car/wheel_base, /car/width, /focus, /tail, /test_layout/adjacency_dict_filename, /test_layout/controlpoints_dict_filename, /test_layout/draw_border, /test_layout/draw_centerline, /test_layout/draw_path, /test_layout/min_points, /test_layout/wp_dict_filename
```
# Database

#### RNDF Parser

#### Generating layout

# Usage
#### catkin workspace and build
In order to use this ROS package, we should work within a catkin workspace. If we do not already have one:
```
cd ~
mkdir -p catkin_ws/src
cd catkin_ws/src
catkin_init_workspace
cd ..
catkin_make
```

At this point, copy this package into src directory and run below commands
```
cd ~/catkin_ws
catkin_make
```
  Sent with Mailtrack  
