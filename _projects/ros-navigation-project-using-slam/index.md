
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
main-image: /limo_robot.jpg
---

---

## Changi Airport Arena Design
{% include image-gallery.html images="changi_1.jpeg" height="400" %} 
{% include image-gallery.html images="changi_2.jpg" height="400" %} 
We worked on building the layout on Changi Airport Terminal 1. To make it as similar and realistic as possible, we have made sure to include major areas like check-in areas, departure areas, etc, and we have recreated recognizable features like the iconic Kinetic Rain as well. This was to create environment where the robot could map the area using RTAB-Map SLAM and move between waypoints while avoiding obstacles using the move_base navigation stack, simulating autonomous navigation in Changi Airport.


### Initial Arena Design
{% include image-gallery.html images="Initial_design_plan.jpg" height="400" %} 
{% include image-gallery.html images="arena_cad.jpg" height="400" %} 

Our team initially planned for a more complex design that included two levels, ramps, and gantries. However, we have decided to improve on it and simplify the design.

### Final Arena Design
{% include image-gallery.html images="arena_cad.png" height="800" %} 

This is the final CAD design of the arena, and we have started on building it with various materials. Materials for each components were carefully decided based on feasibility and budget.





## Our Robot Demo
{% include youtube-video.html id="kO21XZkHZq8" autoplay= "false" width= "900px" %}



<br>

## Adding a hozontal line
---

## Starting a new line
leave two spaces "  " at the end or enter <br>

## Adding bold text
this is how you input **bold text**

## Adding italic text
Italicized text is the *cat's meow*.

## Adding ordered list
1. First item
2. Second item
3. Third item
4. Fourth item

## Adding unordered list
- First item
- Second item
- Third item
- Fourth item

## Adding code block
```ruby
def hello_world
  puts "Hello, World!"
end
```

```python
def start()
  print("time to start!")
```

```javascript
let x = 1;
if (x === 1) {
  let x = 2;
  console.log(x);
}
console.log(x);

```

## Adding external links
[Wikipedia](https://en.wikipedia.org)


## Adding block quote
> A blockquote would look great if you need to highlight something


## Adding table 

| Header 1 | Header 2 |
|----------|----------|
| Row 1, Col 1 | Row 1, Col 2 |
| Row 2, Col 1 | Row 2, Col 2 |

make sure to leave aline betwen the table and the header
