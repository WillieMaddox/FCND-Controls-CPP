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

