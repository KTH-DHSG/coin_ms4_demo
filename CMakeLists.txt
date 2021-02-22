cmake_minimum_required(VERSION 3.0.2)
project(coin_ms4_demo)

## Compile as C++11, supported in ROS Kinetic and newer
# add_compile_options(-std=c++11)

## Find catkin macros and libraries
## if COMPONENTS list like find_package(catkin REQUIRED COMPONENTS xyz)
## is used, also find other catkin packages
find_package(catkin REQUIRED COMPONENTS
  ltl_automaton_6dof_hebi_arm
  ltl_automaton_nexus
  ltl_automaton_turtlebot
  motion_capture_system
)
