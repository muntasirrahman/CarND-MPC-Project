# CarND-Controls-MPC
Self-Driving Car Engineer Nanodegree Program

---

## Dependencies

* cmake >= 3.5
 * All OSes: [click here for installation instructions](https://cmake.org/install/)
* make >= 4.1(mac, linux), 3.81(Windows)
  * Linux: make is installed by default on most Linux distros
  * Mac: [install Xcode command line tools to get make](https://developer.apple.com/xcode/features/)
  * Windows: [Click here for installation instructions](http://gnuwin32.sourceforge.net/packages/make.htm)
* gcc/g++ >= 5.4
  * Linux: gcc / g++ is installed by default on most Linux distros
  * Mac: same deal as make - [install Xcode command line tools]((https://developer.apple.com/xcode/features/)
  * Windows: recommend using [MinGW](http://www.mingw.org/)

* **Ipopt and CppAD:** Please refer to [this document](https://github.com/udacity/CarND-MPC-Project/blob/master/install_Ipopt_CppAD.md) for installation instructions.
* Simulator can be downloaded from the [releases tab](https://github.com/udacity/self-driving-car-sim/releases).
* The [DATA.md](./DATA.md) is a description of the data sent back from the simulator.


## Basic Build Instructions

1. Clone this repo.
2. Make a build directory: `mkdir build && cd build`
3. Compile: `rm -rf * && cmake .. && make`
4. Run it: `./mpc`.

## [Rubrics](https://review.udacity.com/#!/rubrics/896/view)

### Compilation

#### Your code should compile.
The code is compiled successfully without any change with cmake and make.

### Implementation

#### The Model

This project use Kinematic Model. The next state of car is modeled by following:

Position in x, y axis

`x​t+1 ​​= x​t ​​+ v​t ​​∗ cos(ψ​t​​) ∗ dt`

`y​t+1 ​​= y​t ​​+ v​t​​ ∗ sin(ψ​t​​) ∗ dt`

Orientation

`ψ​t+1 ​​= ψ​t​​ + ​​​​​v​t / L​f ​​​​∗ δ ∗ dt`


Velocity 

`v​t+1​​ = v​t ​​+ a​t ​​∗ dt`

Cross Track Error

`cte​t+1​​ = f(x​t​​) − y​t​​ + (v​t​​ ∗ sin(eψ​t​​) ∗ dt)`

Orientation Error

`eψ​t+1 ​​= ψ​t - ψ​des ​​+ (v​t​ / ​L​f​ ​​​∗ δ​t​​ ∗ dt)​​​`

where
`dt` time difference

`δ​t` steering angle

`at` actuator, acceleration

`Lf`  measures the distance between the center of mass of the vehicle and it's front axle.

The cost function is defined by counting all error contributed by particular elements with pre-defined weight. Based on vehicle's current state, a vector of actuator values is computed to make car follow trajectory path with minimum error.


 
#### Timestep Length and Elapsed Duration (N & dt)

Timestep length N value 10 with Elapsed duration (dt) value 0.1 are chosen because it works well during simulation. Those values (dt value 0.1, N 10 timesteps) matched with built-in latency time 100ms (0.1 second). 
Higher value of dt 1, with same N value (10) cause the prediction horizon is too far.
Higher value of N (100), with same dt value, creates too curvy prediction, which leads to erratic behavior. 
Other value combination e.g. N 5, dt 0.2 or N 20 dt 0.05 also produce erratic behavior.
 
#### Polynomial Fitting and MPC Preprocessing

The waypoints data is converted from map coordinate to vehicle coordinate. Each waypoint is substracted by car position, and transformed using standard vector transformation line 101 to 109 main.cpp.

A 3rd order polynomial fitting is applied on waypoint to get trajectory path. This trajectory path is compared with reference trajectory to compute successor state. 


#### Model Predictive Control with Latency

The latency is simulated by thread sleeps for 100ms. This 100 millisecond delay matches timestep interval 0.1, this could be use to simplify the calculation. The computed actuation value is used for the successor state. The Actuation value for current state uses previously value calculated 100ms before, except for the first timestep.

### Simulation

#### The vehicle successfully drive a lap around the track.
Take a look here:
[![Car simulation](http://img.youtube.com/vi/xnU3B59EsOY/0.jpg)](https://www.youtube.com/watch?v=xnU3B59EsOY)

