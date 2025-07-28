---
layout: post
title: ROS Navigation Project using SLAM
description: As part of a Systems Engineering Project 1, I have worked with a team on developing an autonomous navigation robotic system using Agilex LIMO robot, which simulates autonomous navigation around Changi Airport. In order to do so, we have created a miniture version of Changi Airport where our robot will be tested on. Using Robotic Operating System, we were able to program Agilex LIMO robot. Through RTAB-Map SLAM approach, the robot was able to map the environment and localize itself simultaneously real time. Waypoints for navigation were identified directly through the RTAB-Map interface, allowing us to set specific goal locations within the mapped environment,while the move_base navigation stack handled global and local path planning to avoid obstacles and determine the most efficient route. My contribution in building the miniature version of Changi Airport, writing Python script that enabled the robot to follow the waypoints and adjusting parameters of the move_base navigation stack gave me hands-on experience in integration of SLAM to real-world robot hardware.
skills: 
  - ROS 1
  - ROS Topics, Services, and Actionlib
  - RTAB-Map SLAM
  - Python
  - RViz
  - Path-planning
  - LiDAR & Depth Camera Data Processing
main-image: /limo_robot.webp
---

---

## Changi Airport Arena Design
<div style="display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; margin: 20px 0;">
  {% include image-gallery.html 
     images="/changi_1.jpeg, /changi_2.jpg" 
     height="350" 
  %}
</div><br>
We worked on building the layout on Changi Airport Terminal 1. To make it as similar and realistic as possible, we have made sure to include major areas like check-in areas, departure areas, etc, and we have recreated recognizable features like the iconic Kinetic Rain as well. This was to create environment where the robot could map the area using RTAB-Map SLAM and move between waypoints while avoiding obstacles using the move_base navigation stack, simulating autonomous navigation in Changi Airport.

<br>
### **Initial Arena Design**
<div style="display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; margin: 20px 0;">
  {% include image-gallery.html 
     images="/Initial_design_plan.jpg, /arena_cad.jpg" 
     height="350" 
  %}
</div> <br>
{% include image-gallery.html images="limo.webp" height="500" %} 
We have made use of systems approach to design the arena, making sure that the dimensions of the limo robot is considered. Robot has to be able to maneuver around with ample space without physically htting the walls. Also other requirements included that robot has to pass the center of the map. Hence our team initially came up with a design that included two levels, ramps, and two autogataes. However, we have decided to improve on it and simplify the design to single level due to feasibility and resource contstraints.
<br>

### **Final Arena Design**
<br>
{% include image-gallery.html images="arena_cad.png" height="800" %} 
<br>
<div class="arena-slideshow-container">

{% include image-gallery.html 
   images="/1.jpg, /2.jpg, /3.jpg"
   height="650"
   mode="slideshow"
   captions="1|2|3"
%}
</div>
<br>
This is the final CAD design of the arena, and we have started on building it with various materials. Materials for each components were carefully decided based on feasibility and budget.

{% include image-gallery.html images="/4.jpg" height="600" %} 

I have also worked on building the autogate to simulate the autogate installed at the entrance of the departure area in Changi airport. The autogate built will automatically open when it detects the robot approaching nearby using two ultrasonic sensor. Depending the direction in which the robot is coming from, the autogate will move away from the robot. It was programmed using Arduino Uno.


---
## Mapping the Entire Arena
While we were tasked on building the Changi Airport Terminal 1, other teams worked on other parts of Changi Airport. Together, we built a seemless arena of Changi Airport where our Limo robots will be tasked to navigate through all of the plots autonomously.

<div style="display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; margin: 20px 0;">
  {% include image-gallery.html 
     images="/Class_arena.jpg" 
     height="450" 
  %}
</div>

### **Using RTAB-Mapping** 
Our team selected RTAB-Map over alternatives like GMapping and Cartographer due to its robust ability to automatically localize the robot within a previously built map, eliminating the need to manually set the initial pose each time. RTAB-Map integrates well with ROS 1 (Melodic) and supports large, complex environments—an essential feature for mapping our full classroom arena, which combined multiple teams’ plots into one shared space. During implementation with the LIMO robot, we launched the following workflow:

```bash
roslaunch limo_bringup limo_start.launch
roslaunch dabai_u3.launch
roslaunch limo_rtabmap_orbbec.launch
roslaunch rtabmap_rviz.launch
```

<div style="display: flex; justify-content: center; gap: 20px; flex-wrap: wrap; margin: 20px 0;">
  {% include image-gallery.html 
     images="/completed_map.jpg, /completed_map_navigation.jpg" 
     height="400" 
  %}
</div>
This setup enabled real-time 2D/3D mapping using data from the Orbbec camera, allowing the robot to navigate through the arena while building a detailed and scalable environment map. The resulting map served as the backbone for waypoint navigation and obstacle avoidance, enabling the LIMO robot to plan efficient paths and localize itself reliably. The final mapping results were visualized using RViz, showcasing the effectiveness of RTAB-Map in dynamic, obstacle-rich environments.

---

## Navigation Code Implementation

### **Core Architecture**
```python
class WaypointNavigator:
    def __init__(self):
        rospy.init_node('waypoint_navigator')
        self.client = actionlib.SimpleActionClient('move_base', MoveBaseAction)
```
---
### **Framework Components:**

 - move_base action server for global & local path planning
 - TF transforms for map-based coordinate handling
 - Actionlib to send and track navigation goals one by one

**1. Waypoint Management**
```python
self.plot_paths = {
    "Plot1": [{"x": "1.378", "y": "1.427", "yaw": "0.567"}],
    # ... 8 predefined plots ...
    "Plot0": [{"x": "0.0", "y": "0.0", "yaw": "0.0"}]  # Home
}
```
**What this does:**

 - We store predefined coordinates (x, y, yaw) for each plot.
 - Plot0 is the home location, and the others represent specific waypoints around the arena.
 - Plot 0 is the center plot of the entire arena , it is the starting point for the Limo Robot before navigating to other plots.
 - Yaw values are stored as radians (rotation) so the robot faces the correct direction when it reaches each point.

**2.User input handling**
```python
selection = raw_input("Enter your plot sequence (e.g. 134, 1 3 4): ")
selected_plots = re.findall(r'\b[0-9]\b', selection)
```
**How it works:**

 - The user can type multiple waypoints at once (e.g., 1 3 4 or 134).
 - We use a small regex (re.findall) to clean up the input and only keep valid numbers between 0 and 9.
 - It makes the robot more flexible since you can plan multi-stop routes in one go.

**3. Navigation Control**
```python
for plot_num in selected_plots:
    plot_key = "Plot{}".format(plot_num)
    self.navigate_to(self.current_location, plot_key)
    self.current_location = plot_key

```
**What this does:**

 - Loops through each selected waypoint in order.
 - Calls navigate_to() to send a navigation goal for each plot.
 - Updates current_location so the robot knows where it’s coming from next.

   
**4. Waypoint Execution**
```python
def navigate_to(self, from_plot, to_plot):
    path = self.plot_paths[to_plot]
    for wp in path:
        goal = self.create_goal(wp["x"], wp["y"], wp["yaw"])
        self.client.send_goal(goal)
        self.client.wait_for_result()
```
**How it works:**

 - Converts the coordinates into a ROS navigation goal.
 - Waits for the robot to finish moving before sending the next goal (so it doesn’t skip).
 - Retries up to 10 times if it fails (e.g., if there’s an obstacle in the way).

**5. Fault Recovery**
```python
if state != actionlib.GoalStatus.SUCCEEDED:
    self.clear_costmaps()  # Clear obstacles from costmaps
    self.client.send_goal(goal)  # Retry the goal
```
 - If a goal fails, we clear the costmaps to reset any temporary obstacle data.
 - Then we retry the same goal up to 10 times before skipping it.
 - This makes the robot more robust when navigating in changing environments.
   

**6. Goal Creation**
```python
def create_goal(self, x, y, yaw):
    goal = MoveBaseGoal()
    goal.target_pose.header.frame_id = "map"
    q = quaternion_from_euler(0, 0, float(yaw))

```
**How it works:**

 - Converts (x, y, yaw) into a proper ROS navigation goal in the map frame.
 - Uses quaternion_from_euler to get the correct orientation so the robot faces the right way when it arrives.

---



## Our Robot Demo
{% include youtube-video.html id="kO21XZkHZq8" autoplay= "false" width= "900px" %}


