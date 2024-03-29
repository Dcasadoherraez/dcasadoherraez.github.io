## EKF-SLAM


The Extended Kalman Filter was one of the first approaches of SLAM by
performing a local linearization of a nonlinear system. It couples the
current state of the robot only to the previous state and estimates is
probability of being at the current state. The algorithm is divided into
two main steps, prediction and correction.

The state vector has a 3 + 2N, composed by the pose in the 2D plane and the 2D coordinates of N
landmarks. It is defined as

![equation](https://latex.codecogs.com/svg.image?x_t&space;=&space;(x,&space;y,&space;\theta,&space;m_{1,x},&space;m_{1,y},&space;m_{2,x},&space;m_{2,y}&space;...))

The covariance matrix is 2N x 2N defined as

![equation](https://latex.codecogs.com/svg.image?\Sigma_{t}=\begin{pmatrix}\Sigma_{xx}&space;&&space;\Sigma_{xm}\\\\\Sigma_{mx}&space;&&space;\Sigma_{mm}\end{pmatrix})

The input vector (odometry vector) is defined as

![equation](https://latex.codecogs.com/svg.image?u_t&space;=&space;(v_t,&space;\delta_1,&space;\delta_2))


The measurements come in the form of id, range and bearing, and they are interpreted as a hash map in the C++ code.

![equation](https://latex.codecogs.com/svg.image?z_i&space;=&space;(id,&space;r_i,&space;\phi_i))

Note that all the measurements must come already asscociated into the
algorithm.


### Prediction step


In the prediction step, the estimated state is computed based on the
motion model and an initial uncertainty (covariance) matrix
is predicted from previous uncertainty and the motion model jacobian
G_t.

![equation](https://latex.codecogs.com/svg.image?\label{eq:&space;motion}&space;&space;&space;&space;x_t^-&space;=&space;x_{t-1}&space;&plus;&space;\begin{pmatrix}&space;&space;&space;&space;v_t&space;cos(\theta&space;&plus;&space;\delta_1&space;)&space;\\\\&space;&space;&space;&space;v_t&space;sin(\theta&space;&plus;&space;\delta_1&space;)&space;\\\\&space;&space;&space;&space;\delta_1&space;&plus;&space;\delta_2&space;&space;&space;&space;&space;\end{pmatrix})

![equation](https://latex.codecogs.com/svg.image?\label{eq:&space;cov}&space;&space;&space;&space;\Sigma_{t}^{-}&space;=&space;G_t&space;\Sigma_{t&space;-&space;1}&space;G_{t}^T&space;&plus;&space;R_t)

The jacobian of the motion model corresponds to the top-left component
of a 3 + 2N identity matrix, and it is defined as

![equation](https://latex.codecogs.com/svg.image?G_t^x&space;=&space;\begin{pmatrix}&space;&space;&space;&space;1&space;&&space;0&space;&&space;-&space;v_t&space;sin(\theta&space;&plus;&space;\delta_1&space;)&space;\\\\&space;&space;&space;&space;0&space;&&space;1&space;&&space;&space;&space;v_t&space;cos(\theta&space;&plus;&space;\delta_1&space;)&space;\\\\&space;&space;&space;&space;0&space;&&space;0&space;&&space;1&space;&space;&space;&space;&space;\end{pmatrix})

At this point, it is interesting to expand the computation of the
covariance matrix and observe it can be decomposed into three parts.

![equation](https://latex.codecogs.com/svg.image?\Sigma_{t}^{-}&space;=&space;&space;\begin{pmatrix}G_t^x\Sigma_{xx}(G_t^x)^T&space;&&space;G_t^x\Sigma_{xm}\\\\(G_t^x\Sigma_{xm})^T&space;&&space;\Sigma_{mm}&space;\end{pmatrix}&space;&space;&plus;&space;R_t)

This is a possibility of parallelizing computations, as the components
do not depend on each other.


### Correction step


After the first pose and covariance estimate, the correction computes a
gain to reduce the error based on the landmark observations. As it will
be noted, parallelization can be introduced at this stage to improve the
performance.

First, if the landmark has not been initialized yet, its location within
the map is computed as the sum of the robot pose estimate plus the
relative range sensor measurement.

![equation](https://latex.codecogs.com/svg.image?\label{eq:lm}&space;m_i&space;=&space;x_t^-(x,&space;y)&space;&plus;&space;\begin{pmatrix}&space;r&space;cos(\phi_i&space;&plus;&space;x_t^-(\theta))&space;\\\\&space;v_t&space;sin(\phi_i&space;&plus;&space;x_t^-(\theta))&space;\\&space;\end{pmatrix})

In order to be able to compare the expected distance from landmark to
robot, the squared euclidean distance q is computed.

![equation](https://latex.codecogs.com/svg.image?\label{eq:delta}&space;&space;&space;&space;\begin{aligned}&space;&space;&space;&space;\delta&space;&&space;=&space;m_i&space;-&space;x_t^-(x,&space;y)&space;\\\\&space;&space;&space;&space;q&space;&&space;=&space;\delta&space;\delta^T&space;&space;&space;&space;\end{aligned})

Once defined, the predicted measurement is given as

![equation](https://latex.codecogs.com/svg.image?\label{eq:z_p}&space;&space;&space;&space;z_t^{i-}&space;=&space;\begin{pmatrix}&space;&space;&space;&space;\sqrt{q}&space;\\\\&space;&space;&space;&space;atan2(\delta_y,&space;\delta_x)&space;-&space;x^-(\theta)&space;\\&space;&space;&space;&space;\end{pmatrix})

The jacobian of the predicted measurement will have the size
2Nx2N + 3. In the subcomponent for each landmark, the first three
columns refer to the derivative with respect to the robot pose. The
other two non-empty columns correspond to the observed landmark index.

![equation](https://latex.codecogs.com/svg.image?H_{low,&space;t}^i&space;&space;=$$&space;$$\label{eq:h_l}\frac{1}{q}&space;\begin{pmatrix}-\sqrt{q}&space;\delta_x&space;&&space;-\sqrt{q}&space;\delta_y&space;&&space;0&space;&&space;0&space;&&space;...&space;&&space;\sqrt{q}&space;\delta_x&space;&&space;\sqrt{q}&space;\delta_y&space;&space;&&space;..&space;&&space;0\\\delta_y&space;&&space;-\delta_x&space;&&space;-q&space;&&space;0&space;&&space;...&space;&&space;-\delta_y&space;&&space;\delta_x&space;&&space;..&space;&&space;0\end{pmatrix})

It is important to note in this case, that the computation of each
landmark does not interfere with each other. Therefore the matrix $H$
can be constructed by blocks, using batches of landmark detections that
can be parallelized for computation.

Performance would benefit from this specially in the case where
landmarks and features need to be matched, which is out of the scope of
this algorithm, and simulated in the experiments as a small time delay.

The last steps of EKF SLAM are trivial and can be observed in the
algorithm pseudocode.

<div >
<figure>
<img class="image-container" style="width:45%" src="https://github.com/Dcasadoherraez/cpp-threads/raw/main/ekf-slam/media/pseudocode.png" />
</figure>
</div>

## EKF SLAM multithreading


As seen in the previous section, there are two main areas of
experimentation for multithreading in EKF SLAM. The first one is in the
computation of the covariance matrix prediction. The second
one is batch-computing the jacobian of the observation for each landmark.

Two additional threads were used in order to display the robot movement
in a 2D image as well as publishing of the current time. All the writing
within threads is handled using unique locks for a data mutex in the
class instance.

### Parallelize covariance prediction


The parallelization of the covariance leads to four separate functions,
*GetTopLeft*, *GetTopRight*, *GetBottomLeft* and *GetBottomRight*. Each
one executed in one thread. The number of landmarks for this experiment
was set to 10, which mainly influences the size of the matrix
computation.

The average time for the multithreaded case averaged to 0.15ms while the
sequential run was an order of magnitude lower at 0.0125ms. Varying the
number of landmarks was limited by the Eigen C++ library to 100, which
made the change in performance barely noticeable for having more or less
landmarks.

<div style="text-align: center">
<figure>
<img class="image-container" src="https://github.com/Dcasadoherraez/cpp-threads/blob/main/ekf-slam/media/sigma_par.png?raw=true" />
</figure>
</div>

### Parallelize measurement prediction jacobian


The measurement prediction jacobian was parallelized by separating
different landmark observations into different threads. As the
computation is performed in constant time, the runtime reduction is not
noticeable without a modification to the code.

A 10ms delay was added to each landmark computation, simulating the process of
matching and association in a real world system. In addition, at maximum, six
observations were made at the same time by the robot, so having a feature batch
size of one is reasonable for an eight core CPU.

Performing the operations sequentially lead to the addition of the
runtimes for each landmark. If the robot observes 6 landmarks, the delay
is 60.3ms, if it observes 2 landmarks, the delay is 20.3ms.

However, parallelization improved this by dividing all the work into
multiple threads, thus taking always 10.3ms no matter how many landmarks
are seen.

<table>
<caption><span id="fig:acc">Observation jacobian parallelization</span></caption>
<tbody>
<tr class="odd">
<td style="text-align: left;"><figure>
<img width="100%" src="https://github.com/Dcasadoherraez/cpp-threads/blob/main/ekf-slam/media/h_t.png?raw=true" id="fig:acc" />
</figure></td>
<td style="text-align: left;"><figure>
<img width="100%" src="https://github.com/Dcasadoherraez/cpp-threads/blob/main/ekf-slam/media/correction_step_parallel.png?raw=true" id="fig:loss" />
</figure></td>
</tr>

</tbody>
</table>

### Results and discussion


The following table shows
the results mentioned earlier, demonstrating the two sides of
multithreaded programming. It can be observed how thread creation is
expensive. Spawning multiple threads for small problems reduces
efficiency by increasing throughput. Meanwhile, computations that
require time such as feature matching and detection in a robot SLAM
system can be spread across multiple threads. In such a case, an
increase in throughput also means an increase in performance.

|               | Covariance Computation (ms) | H_t Computation (ms)     |
|---------------|-----------------------------|--------------------------|
| Single thread | 0.0125                      | n of observations * 10.3 |
| Multi thread  | 0.12                        | 10.3                     |



# References
<a id="1">[1]</a> 
Cyrill Stachniss.
EKF-SLAM
https://www.youtube.com/watch?v=X30sEgIws0g&t=11s

<a id="2">[2]</a> 
Joan Solà.
Simultaneous localization and mapping with the extended Kalman filter.
https://www.iri.upc.edu/people/jsola/JoanSola/objectes/curs_SLAM/SLAM2D/SLAM%20course.pdf
