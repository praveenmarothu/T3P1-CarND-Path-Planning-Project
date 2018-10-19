
##  Implementation
The data from the sensor fusion module is used to determine the next state of the car. 

### The Process : Desired lane and speed
1. Get the current car lane and set the left and right safeness
    1. If `car_lane == 0 ` then `left_safe = false`
    2. if `car_lane == 2 ` then `right_safe = false`
3. Analyse every other cars from the sensor fusion data, 
    1. if the other car is in the same lane and is too close, slow down to match ahead car speed and set flag  `ahead_close`to true
    2. if the other car is in the left lane and is too close, then set flag `left_safe` to false
    3. if the other car is in the right lane and is too close, then set the flag `right_safe` to false
4. After the iteration through the sensor fusion data, the status of the flags is used to determine the next state. 
     1. if ahead car too close, slow down to match ahead car speed.
         1. if `left_safe` flag is true then move to left lane.
         2. if left is not safe and `right_safe` flag is true then move to right lane.
         3. if left and right lanes are not safe, then keep the same lane maintaining ahead car speed.
     3. if not close or no cars ahead, maintain max lane speed

Below helper functions are used.
```
getCarLane(int d)
isSafeDistance(double car_s,double ocar_s)
```
After the above process we arrive at a desired speed or the desired lane for the car. 

### The Process : Lane change trajectory
With the desired speed/lane  information, the unprocessed points from the previously generated path will be kept, adding additional points generated as follows:
-   A spline is generated using the  [spline function](http://kluge.in-chemnitz.de/opensource/spline/) :
    -   Two points from the previous path are used to ensure the new path neatly follows it.
    -   Three points ahead of the car, in the desired lane. If it is different from the starting lane, the spline will make it a smooth lane change.
-   As many points as necessary are sampled from the spline to create a smooth trajectory

