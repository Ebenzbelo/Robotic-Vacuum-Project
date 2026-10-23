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

## METHODOLOGY
As earlier stated, the driving behavior can be captured by three states: 
- drive to a specified location,
- drive randomly,
- and stop.

### Overview 
Although there are three internal states to this system, there are only two overall drive modes.
- Drive to the dock.
- Drive to the center of the room, then begin the random pattern.

The mode is set by a chart input, driveMode. In both behaviors, the first step is to drive to a specified location. Thus, both drive modes first enter the DriveToLocation state. Then, if driveMode == 1, the task is complete. If driveMode == 2, begin the random drive.
 
The system can leave the DriveToLocation state when positionReached == 1.

The random vacuum task consists of driving until bumping into a wall, rotating a random amount, and repeating the bump/rotate sequence. The task is complete after the bump sensor registers more than 50 times.

The "drive to location" behavior occurs in three steps, in the following order: pure rotation to the correct orientation, pure translation to the correct location, and then stop.

The "random drive" behavior consists of two actions: find the wall and rotate. During random driving, the robot repeatedly cycles through these actions.

Hence, the driving mode framework is built. Specific behaviours are set to be added to the framework of the Stateflow chart. 

This chart will output the commands for velocity and angular velocity. These will be passed to the Simulink model of the robot. In pure rotation, you will set velocity, v, to 0 and angular velocity, w, to 1. In pure translation, v=3 and w=0. When stopped, both values should equal 0.

Four additional inputs from Simulink help track the robot's progress during a given behavior.
desiredDist – desired distance to destination
desiredRot – desired rotation to destination
elapsedDist – instantaneous elapsed distance
elapsedRot – instantaneous elapsed rotation

These provide the conditions for transitioning between the DriveToLocation substates: as soon as the current rotation or distance meets or exceeds the target, the task is complete.

The DriveToLocation behavior is now complete. The RandomDrive behavior is similar, consisting of a pure translation while in FindWall and a pure rotation in RotateRandom.

After bumping into a wall, the robot transitions to the random rotation behavior. This is detected by a bump sensor and recorded in the Simulink signal, bump. A value of 1 indicates a detection.

To achieve a random rotation, you can create a random variable, tRotate, that represents the amount of time to remain in the rotation state. You can then use this value as the argument in a temporal operator to determine when to transition back to the FindWall state.

When a given drive mode task is complete, send a flag back to Simulink. 

Finally, to uncross the signals, change the port order in the Stateflow chart.

STEP 1: 
The states DriveToLocation, RandomDrive, and TaskComplete are created with the DriveToLocation designed as the default state.

Now, add transitions from DriveToLocation to RandomDrive and to TaskComplete. Using the variables positionReached and driveMode, add the necessary conditions to implement the behavior described above.
Add a state entry action to initialize the value of positionReached to 0.

The Data scope of positionReached is set to Local Data. driveMode is a user input, so its scope should be Input Data.

STEP 2:
Add the appropriate transition and condition based on a counter, bumpCount. Set bumpCount to 0 upon entering the RandomDrive state.
 
Define the Symbol as Local Data.

Then, create the states PureRotate, PureTranslate, and Stop as substates of DriveToLocation. Add the transitions to implement the described sequence.

STEP 3:
Create states FindWall and RotateRandom as substates of RandomDrive, as well as the necessary transitions. FindWall should be the default state for this level of hierarchy.

Hence, the framework for the driving mode logic of a robotic vacuum has been built, but no detailed functionality yet.

STEP 4:
Add state entry actions to PureRotate, PureTranslate, and Stop to set the appropriate values for v and w in each state.
Define the scope of v and w so that they can be passed to the Simulink model.
Add an entry action to the Stop state to set positionReached to 1.

Then, add the conditions to the transitions between the DriveToLocation substates. Set all of the new Symbols to Input Data.
 
Note: The elapsed rotation angle needs to be wrapped between 0 and 2π, so the value of interest is mod(elapsedRot,2*pi).

STEP 5:
Add state entry actions to FindWall and RotateRandom to set the velocity, v, and angular velocity, w, in each state. Use the same values as the PureTranslate and PureRotate states.

Add a condition on the transition from FindWall to RotateRandom such that the transition is valid when a bump is detected. Define bump as Input Data.
 
Then, add a condition action that increments bumpCount by 1 when the condition is true.

STEP 6:
Add the following MATLAB command to the RotateRandom entry action. The command generates a random number between 2.5 and 4.
 
tRotate = rand(1)*1.5+2.5;
 
Use tRotate and a temporal operator to transition from RotateRandom to FindWall after 2.5-4 seconds. Remember to Resolve undefined symbols to define tRotate as Local Data.

Set complete to 1 upon entering the TaskComplete state. Define this as Output Data in the Symbols panel.
Then, navigate to Simulink and connect the unconnected signals with their corresponding ports on the Stateflow chart, based on the signal names.

STEP 7:
Change the input port of bump to port 2, so the resulting input port order is as follows.
1 – driveMode
2 – bump
3 – desiredDist
4 – desiredRot
5 – elapsedDist
6 – elapsedRot


## REFERENCE 

<a href="https://matlabacademy.mathworks.com/?page=1&sort=featured">Stateflow Onramp - MathWorks Inc.</a>
