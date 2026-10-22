# Robotic-Vacuum-Project

Robotic Application 💯
## Objective 
The project is aimed at building a Robot Vacuum - Driving mode. Having studied the application of hierarchy in the Stateflow Model, I will use hierarchy to model something similar to the driving logic of a robotic vacuum that drives in two modes: docking or vacuuming.

## The Design 
The robot's instructions for each of these modes were:
SeekDock – drive to the dock and then stop.
Vacuum – drive to the corner of the room and then follow a zig-zag pattern.

the robot requires two driving behaviors: drive to a location and follow a zig-zag pattern. These can be implemented as the top-level states in a hierarchy. The substates for each are the particular states required to implement each behavior. For example, when driving to a location, the robot's motion can be broken down into: orient using pure rotation, drive using pure translation, and stop.

However, my point of focus is to implement a driving pattern in which the robot can either dock or drive a random pattern around the room.
Thus, DriveToLocation and RandomDrive are two superstates. My Stateflow chart will also have a TaskComplete state in which no further driving commands are sent, as represented below:
<img width="1041" height="235" alt="Sample" src="https://github.com/user-attachments/assets/9dd5f7a3-0d64-46b2-993a-ef76501d833f" />

For this project, the driving dynamics of the robotic vacuum have been built in Simulink. I will connect simulated sensor data and other information from this model to your Stateflow chart in order to set the desired velocity and angular velocity commands to be sent to the robot.
