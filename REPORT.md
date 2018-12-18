# Project #3 - Controlling a 3D Quadrotor

The goal here is to implement a set of functions and parameters capable of controlling a quadrotor within various scenarios.  Specific instructions can be found in the README.md in the same directory as this file.

## Intro: determine mass (scenario 1) ##

The first scenario in the simulator, `1_Intro`, runs with the quad falling straight down.  This is due to the fact that the thrusts are simply being set to:

```
QuadControlParams.Mass * 9.81 / 4
```

Therefore, if the mass doesn't match the actual mass of the quad, it'll fall down.  To make the quad pretty much stay in the same spot, tune the `Mass` parameter in `QuadControlParams.txt`.  The correct mass was found to be:

```$xslt
Mass = 0.49
```

Performance Evaluation
 - tune the `Mass` parameter in `QuadControlParams.txt` to make the vehicle more or less stay in the same spot.

Result
```$xslt
PASS: ABS(Quad.PosFollowErr) was less than 0.500000 for at least 0.800000 seconds
```

This is what the simulation looks like with correct mass:

<p align="center">
<img src="animations/scenario1.gif" width="500"/>
</p>

## Body rate and roll/pitch control (Scenario 2) ##

The body rate controller calculates the desired 3-axis moment given the desired body rate and the current body rate.
The roll/pitch controller calculates the desired pitch and roll angle rates based on the desired global lateral acceleration, the current attitude of the quad, and the desired collective thrust command.

For this scenario, choose `Scenario -> 2_AttitudeControl` from the simulator menu.  This scenario begins with a single quad above the origin and is given a small initial rotation speed about its roll axis.  

To stabalize the motion, we implement 3 functions in `QuadControl.cpp`:
 - `GenerateMotorCommands()`
 - `BodyRateControl()`
 - `RollPitchControl()`

And tune 2 parameters in `QuadControlParams.txt`:
 - `kpPQR` to get the vehicle to stop spinning quickly but not overshoot.
 - `kpBank` to minimize settling time but avoid too much overshoot.
 
Reasonable parameters were found to be:
```$xslt
kpPQR = 100, 100, 1
kpBank = 14
```

Performance Evaluation
 - roll should less than 0.025 radian of nominal for 0.75 seconds (3/4 of the duration of the loop)
 - roll rate should less than 2.5 radian/sec for 0.75 seconds

Result
```
PASS: ABS(Quad.Roll) was less than 0.025000 for at least 0.750000 seconds
PASS: ABS(Quad.Omega.X) was less than 2.500000 for at least 0.750000 seconds
```

With the above parameters the simulations should look like this:

<p align="center">
<img src="animations/scenario2.gif" width="500"/>
</p>

## Position/velocity and yaw angle control (scenario 3) ##

Next, implement the position, altitude and yaw control for your quad.  For the simulation, `Scenario -> 3_PositionControl`.  This will create 2 identical quads, one offset from its target point, initialized with yaw = 0, and the second offset from its target point but initialized with yaw = 45 degrees.

To stabalize this motion, we implement 3 functions in `QuadControl.cpp`:
 - `LateralPositionControl()`
 - `AltitudeControl()`
 - `YawControl()`

And tune 2 parameters in `QuadControlParams.txt`:
 - `kpPosXY` Gain in the lateral position
 - `kpPosZ` Gain in the vertical position
 - `kpVelXY` Gain in the lateral velocity
 - `kpVelZ` Gain in the vertical velocity
 - `kpYaw` Gain for yaw
 - `kpPQR` The 3rd (z) component of the body rate gains.
 
Reasonable parameters were found to be:
```$xslt
kpPosXY = 32
kpPosZ = 32
kpVelXY = 12
kpVelZ = 16
kpYaw = 3
kpPQR = 100, 100, 8
```

Performance Evaluation
 - X position of both drones should be within 0.1 meters of the target for at least 1.25 seconds
 - Quad2 yaw should be within 0.1 of the target for at least 1 second

Result
```
PASS: ABS(Quad1.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Pos.X) was less than 0.100000 for at least 1.250000 seconds
PASS: ABS(Quad2.Yaw) was less than 0.100000 for at least 1.000000 seconds
```

With the above parameters the simulations should look like this:

<p align="center">
<img src="animations/scenario3.gif" width="500"/>
</p>

## Non-idealities and robustness (Scenario 4) ##

In this part, we will explore some of the non-idealities and robustness of a controller.  For this simulation, we will use `Scenario -> 4_Nonidealities`.  This configuration has 3 quads that are all are trying to move one meter forward.  However, each quad is a bit different:
 - The green quad has its center of mass shifted back
 - The orange vehicle is an ideal quad
 - The red vehicle is heavier than usual

To stabalize the motion, we add basic integral control to `AltitudeControl()`.

And tune 1 parameter in `QuadControlParams.txt`:
 - `KiPosZ` Gain in the accumulated vertical position over time.
 
Reasonable parameter for `KiPosZ` was found to be:
```$xslt
KiPosZ = 60
```

Performance Evaluation
 - position error for all 3 quads should be less than 0.1 meters for at least 1.5 seconds

Result
```
PASS: ABS(Quad1.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad2.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
PASS: ABS(Quad3.PosFollowErr) was less than 0.100000 for at least 1.500000 seconds
```

With the above parameters the simulations should look like this:

<p align="center">
<img src="animations/scenario4.gif" width="500"/>
</p>

## Tracking trajectories (Scenario 5) ##

Now that we have all the working parts of a controller, test it's performance on a trajectory.  For this simulation, use `Scenario 5_TrajectoryFollow`.  This scenario has two quadcopters:
 - the orange one is following `traj/FigureEight.txt`
 - the other one is following `traj/FigureEightFF.txt` - the same trajectory but with velocity (not just position) information.
 
Performance Evaluation
 - position error of the quad should be less than 0.25 meters for at least 3 seconds

Result
```
PASS: ABS(Quad2.PosFollowErr) was less than 0.250000 for at least 3.000000 seconds
```

With the two different trajectories, your drones' motions should look like this:

<p align="center">
<img src="animations/scenario5.gif" width="500"/>
</p>

## TODO: Extra Challenge 2 (Optional) ##

For flying a trajectory, is there a way to provide even more information for even better tracking?

How about trying to fly this trajectory as quickly as possible (but within following threshold)!

