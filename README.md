## Project: 3D Motion Planning
![Quad Image](./misc/enroute.png)

---


# Required Steps for a Passing Submission:
1. Load the 2.5D map in the colliders.csv file describing the environment.
2. Discretize the environment into a grid or graph representation.
3. Define the start and goal locations.
4. Perform a search using A* or other search algorithm.
5. Use a collinearity test or ray tracing method (like Bresenham) to remove unnecessary waypoints.
6. Return waypoints in local ECEF coordinates (format for `self.all_waypoints` is [N, E, altitude, heading], where the drone’s start location corresponds to [0, 0, 0, 0].
7. Write it up.
8. Congratulations! Your Done!

## [Rubric](https://review.udacity.com/#!/rubrics/1534/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  

You're reading it! Below I describe how I addressed each rubric point and where in my code each point is handled.

### Explain the Starter Code

#### 1. Explain the functionality of what's provided in `motion_planning.py` and `planning_utils.py`
These scripts contain a basic planning implementation that includes:
1) Provide basic callbacks and state transition functions.
2) Provide method `plan_path()` to compute a list of waypoints.
3) Reads obstacle data from `colliders.csv`.
4) Compute and set grid start and goal points.
5) Perform A star search to come up with a list of waypoints.
6) Send waypoints to simulator.

### Implementing Your Path Planning Algorithm

#### 1. Set your global home position
Read first line of colliders.csv (the line is `lat0 37.792480, lon0 -122.397450`)
Extract lat0 and lon0 as floating point values and use the `self.set_home_position()` method to set global home.

#### 2. Set your current local position
Getting local position by using `global_to_local()` with `global_position` and `global_home`.

#### 3. Set grid start position from local position
Getting grid start position using local position minus grid offset.

#### 4. Set grid goal position from geodetic coords
Goal lat lon is passed in as cmdline args. Getting grid goal position using exactly the same approach as grid start.

#### 5. Modify A* to include diagonal motion (or replace A* altogether)
Add NORTH_WEST, SOUTH_WEST, NORTH_EAST, SOUTH_EAST with cost of sqrt(2).
Also remove infeasible actions in `valid_actions()`.

#### 6. Cull waypoints 
Performs collinearity test. It works as follows:
1) Return original path if path contains two points or less.
2) Put first point in pruned_path.
3) Iterate through second point to second last point, perform collinearity test on
last point in pruned_path (which is the last turning point / starting point), current point and next point.
If test fails, current point should be a turning point. Put the point into pruned_path.
If test passes, current point is on the straight line between last turning point and next point. Do nothing in this case.
4) Put last point in pruned_path.
5) Return pruned_path.

Collinearity test done by examine the smallest magnitude eigen value of the covariance matrix of three points.
If three points are collinear, its covariance matrix should be low rank and smallest magnitude eigen value should be
approximately zero.

### Execute the flight
#### 1. Does it work?
It works!

### Double check that you've met specifications for each of the [rubric](https://review.udacity.com/#!/rubrics/1534/view) points.
