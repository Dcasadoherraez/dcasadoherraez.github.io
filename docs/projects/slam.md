1. <a href="#section:A-LOAM" data-reference-type="ref"
   data-reference="section:A-LOAM">A-LOAM</a>.
2. <a href="#section:ORB-SLAM2" data-reference-type="ref"
   data-reference="section:ORB-SLAM2">ORB-SLAM2</a>.
3. <a href="#section:System-Structure" data-reference-type="ref"
   data-reference="section:System-Structure">System Structure</a>.

## A-LOAM <span id="section:A-LOAM" label="section:A-LOAM"></span>

[A-LOAM](https://github.com/HKUST-Aerial-Robotics/A-LOAM) is an advanced
implementation of [LOAM](https://github.com/laboshinl/loam_velodyne) (LIDAR
Odometry and Mapping) that uses Eigen and Ceres C++ library. The algorithm
remains the same, and it is based on pointcloud matching of detected edges and
surfaces. 

The edges and surfaces are mainly identified by measuring a smoothness term
![equation](https://latex.codecogs.com/svg.image?s). 

![equation](https://latex.codecogs.com/svg.image?s&space;=&space;\frac{1}{|\mathcal{S}|\centerdot||X_{k,&space;i}^L||}||\sum&space;{X_{k,&space;i}^L&space;-&space;X_{k,&space;j}^L}||)

Where ![equation](https://latex.codecogs.com/svg.image?|\mathcal{S}|) is the
number of points in the local point cloud, and
![equation](https://latex.codecogs.com/svg.image?X_{m,&space;n}^L) is a point at
position (m,n) expressed in LIDAR coordinate frame.

Those neighboring points with highest score will be classified as edges while
lower scores will correspond to surfaces.

A variation of this equation is used by the authors of
[F-LOAM](https://github.com/wh200720041/floam) by considering only the points in
the horizontal plane for surface feature detection, as it assumes the robot will
only move in a direction parallel to the ground.

For the matching step, LOAM uses point to line distance for the edges and point
to plane distance for the surfaces, and finally estimates the motion by
performing Levenberg-Marquardt optimization of the edge and surface matches.


## ORB-SLAM2 <span id="section:ORB-SLAM2" label="section:ORB-SLAM2"></span>
ORB-SLAM2 is based on feature matching between frames, and performing two types
of bundle adjustment, local and global. In this approach, loop closing has been
discarded as we are only interested in the relative measurements to improve the
LIDAR estimation.

Its working principle can be summarized as follows.

Initialization starts by finding initial ORB correspondences, computes the
homography and fundamental matrix so that the best one can be selected for
planar and non-planar images, extracts motion hypotheses and performs initial
BA.

Then, the system is divided into two threads, 1) tracking, 2) local mapping,
where loop closing is discarded for this experiment due to its unreliability in
highly complex environments.

* Tracking: Localizes the camera each frame and decides when to insert new
keyframes to the map.
* Local mapping: Adds points to the map and performs local Bundle Adjustment.

## System Structure <span id="section:System-Structure" label="section:System-Structure"></span>
The system was implemented to operate offline, combining the results from A-LOAM
and ORB-SLAM2 with a simple Kalman filter. The results for an easy and medium
difficulty runs are displayed at the beginning of the post. The image below shows the structure
of the system. 


<div  style="text-align: center">
<figure>
<img class="image-container" src="https://drive.google.com/uc?export=view&id=19dCyRxT2q8Z7RnS5Kgd6NwNOI1sOUVru"/><figcaption aria-hidden="true">System structure diagram</figcaption>
</figure>
</div>
