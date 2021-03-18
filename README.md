# coin_ms4_demo
Contains all config files (TS and LTL specifications) and launch files for the different agents of the SSF COIN project MS4 demonstration.

The demonstration is now finished, a video of it can be found [here](https://www.youtube.com/watch?v=Gd-rqX04OdU).

<a href="url"><img src="/documentation/pictures/factory_demo_setup.png" align="center" height="301" width="376"></a>
<a href="url"><img src="/documentation/pictures/factory_demo_pic.png" align="center" height="331" width="399"></a>

## Host computer
The host computer is running the ROS core. It's important to set all robots and external element ROS_MASTER_URI to the host computer IP.

## Motion Capture System
A centralized and shared localization is provided my the motion capture system of the Smart Mobility Lab (Qualisys). A [ROS interface](https://github.com/KTH-SML/motion_capture_system) is used to communicate with all agents.

## Nexus - Assembly line robots
Assembly robots are Nexus Robotics 4WD Holonomic robots equipped with custom internal controller and a platform for package handling.

<a href="url"><img src="/documentation/pictures/nexus_assembly_line.png" align="center" height="471" width="412"></a>

Onboard computer is either:
* Nvidia Jetson TX2
* Nvidia Jetson Nano

Using [move_base](http://wiki.ros.org/move_base) for navigation:
* Global path planner: Navfn
* Local path planner: TEB

The robots are running the LTL planner and the robot-specific nodes from [ltl_automaton_nexus](https://github.com/KTH-DHSG/ltl_automaton_nexus).

To run the robot, launch from the onboard computer: `roslaunch coin_ms4_demo nexus_agent.launch agent_name:=<agent_name> obstacle_names:="[<other_agent_name1>, <other_agent_name2>]"` replacing <agent_name> and <other_agent_name#> by the nexus names (on the format **nexus0**).

### Agent model
The robot model used by the LTL planner has two components: workspace (2d region) model and load model.
The nexus robot TS config file can be found at [/config/nexus_ts.yaml](/config/nexus_ts.yaml).

#### Workspace model
The workspace is discretized in 15 regions and 3 stations (using the 2d pose region from [ltl_automaton_std_transition_systems](https://github.com/KTH-DHSG/ltl_automaton_core/tree/main/ltl_automaton_std_transition_systems)).

<a href="url"><img src="/documentation/pictures/nexus_ws.png" align="center" height="285" width="295"></a>

#### Load model
The load model is composed of 3 states:
* unloaded
* loaded_box
* loaded_assembly

<a href="url"><img src="/documentation/pictures/nexus_load.png" align="center" height="156" width="480"></a>

* "pick_box" only possible at "s0"
* "pick_assembly" only possible at "s1"
* "deliver_assembly" only possible at "s2"

### Agent task
The nexus robot LTL formula can be found at [/config/nexus_ltl_spec.yaml](/config/nexus_ltl_spec.yaml).

#### Hard task
Hard task is: `(⎕◊ loaded_assembly)∧(⎕◊ unloaded)`.

The robot should alternatively be loaded with the full assembly and unloaded.

#### Soft task
No soft task defined.




## Hebi arm manipulator
The manipulator is a HEBI Robotics A-2085-06 with 6 degrees of freedom. It is interfaced with ROS using the [hebiros package](http://wiki.ros.org/hebiros). It is endowed with a custom electromagnet end-effector for boxes handling.

<a href="url"><img src="/documentation/pictures/hebi_manipulator.png" align="center" height="433" width="515"></a>

The manipulator is running the LTL planner and its specific nodes from [ltl_automaton_6dof_hebi_arm](https://github.com/KTH-SML/ltl_automaton_6dof_hebi_arm).

To run the robot, launch from a computer on the network: `roslaunch coin_ms4_demo hebi_arm.launch`.

### Agent model
The manipulator model used by the LTL planner has two components: jointspace (6-DoF joint configurations) model and load model.
The manipulator robot TS config file can be found at [/config/6dof_hebi_ts.yaml](/config/6dof_hebi_ts.yaml).

#### Jointspace model
The jointspace is discretized in 3 configurations (using the 6dof configurations from [ltl_automaton_std_transition_systems](https://github.com/KTH-DHSG/ltl_automaton_core/tree/main/ltl_automaton_std_transition_systems)).

<a href="url"><img src="/documentation/pictures/hebi_js.png" align="center" height="195" width="371"></a>

#### Load model
The load model is composed of 2 states:
* unload
* loaded

<a href="url"><img src="/documentation/pictures/hebi_load.png" align="center" height="202" width="347"></a>

* "pick" only possible at "pick_ready"
* "drop" only possible at "drop_ready"

### Agent task
The manipulator LTL formula can be found at [/config/6dof_hebi_ltl_spec.yaml](/config/6dof_hebi_ltl_spec.yaml).

#### Hard task
Hard task is: `(⎕◊ loaded_assembly)∧(⎕◊ unloaded)`.

The robot should alternatively be loaded with the full assembly and unloaded.

#### Soft task
No soft task defined.



## Turtlebot - Delivery robot
The delivery robot used in the scenario is a [Turtlebot2](https://www.turtlebot.com/turtlebot2/). Embedded localization is not used and replaced by the motion capture system.

<a href="url"><img src="/documentation/pictures/turtlebot_pic.png" align="center" height="296" width="220"></a>

Using [move_base](http://wiki.ros.org/move_base) for navigation with default planner.

The robots is running the LTL planner and the robot-specific nodes from [ltl_automaton_turtlebot](https://github.com/KTH-DHSG/ltl_automaton_turtlebot).

To run the robot, launch from the onboard computer: `roslaunch coin_ms4_demo turtlebot_agent.launch initial_beta:=40"`

For perfomance reasons, it can be useful to run only the high level nodes (LTL planner, LTL Turtlebot node, HIL nodes) on a host computer instead of the Turtlebot onboard laptop. To do so, run the following on the host computer: `roslaunch coin_ms4_demo turtlebot_agent_high_level.launch`, and the following on the onboard computer: `roslaunch coin_ms4_demo turtlebot_agent_low_level.launch`.

### Agent model
The delivery robot model used by the LTL planner has 3 components: the workspace model, the load model and the battery model.
The delivery robot TS config file can be found at [/config/turtlebot_ts.yaml](/config/turtlebot_ts.yaml).

#### Workspace model
The workspace is discretized in 15 regions and 3 stations (using the 2d pose region from [ltl_automaton_std_transition_systems](https://github.com/KTH-DHSG/ltl_automaton_core/tree/main/ltl_automaton_std_transition_systems)).

<a href="url"><img src="/documentation/pictures/turtlebot_ws.png" align="center" height="324" width="255"></a>

#### Load model
The load model is composed of 3 states:
* unloaded
* loaded

<a href="url"><img src="/documentation/pictures/turtlebot_load.png" align="center" height="140" width="290"></a>

* "pick" only possible at "s2"
* "drop" only possible at "s3"

#### Battery model
The battery model is composed of 2 states:
* uncharged
* charged

<a href="url"><img src="/documentation/pictures/turtlebot_battery.png" align="center" height="129" width="263"></a>

* "charge" only possible at "s1"

### Agent task
The robot LTL formula can be found at [/config/turtlebot_ltl_spec.yaml](/config/turtlebot_ltl_spec.yaml).

#### Hard task
Hard task is: `(⎕ (uncharged →◊ charged)) ∧ (⎕◊ loaded) ∧ ⎕(loaded →◊ unloaded) ∧ ⎕(¬ r23 ∧ ¬ r26 ∧ ¬ r32 ∧ ¬ r35 )`.

* If the robot battery is uncharged, it has to charge it.
* AND The robot should alternatively be loaded and unloaded.
* AND The robot should avoid regions with obstacles (r23, r26, r32, r35) 

#### Soft task
Soft task is `((loaded ∧ s2)  → ((¬ unloaded)  U (loaded ∧ r36)))`.

The robot should go to region r36 after being loaded and before unloading (for a visual inspection task)



## Button box setup
Button boxes are used to send acknowledgement of task done by human workers. The [ros_raspberry_pi_button](https://github.com/KTH-SML/ros_raspberry_pi_button) package is used to interface the button boxes to the ROS network.

To run the button box, connect to the internal raspberry pi and run the following:
* **Assembly line button box:** `roslaunch coin_ms4_demo assembly_line_button_box.launch` (config file in [/config/button_box/assembly_line_button_box.yaml](/config/button_box/assembly_line_button_box.yaml))
* **Delivery button box:** `roslaunch coin_ms4_demo assembly_line_button_box.launch` (config file in [/config/button_box/delivery_button_box.yaml](/config/button_box/delivery_button_box.yaml))

