# Project #3 - Controlling a 3D Quadrotor

The goal here is to implement a set of functions and parameters capable of controlling a quadrotor within various scenarios.

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
