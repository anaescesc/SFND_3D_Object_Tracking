# Track an Object in 3D Space
### FP.1 Match 3D Objects
For each match of the vector *matches* its location (x, y) is calculated. BBs of the current frame that containing that location are stored in *currMtchBxs* vector. 

The process is repeated with BBs of previous frame. In this case BB of previous frame, that contains the  match, is linked to a BB of the current frame. If this link exists, the *times* variable is increased (field *second* of the vector <map> *currPrevMatches*).

After check *matches*  vector, we analyze the links. For each BB of the current frame we select the BB of the previous frame with the highest value of the *time* variable. If the BB ID of the previous frame has not been registered yet, it will be added to *map bbBestMatches*. Otherwise and if the *time* variable of this link is greater than the registered one, the registered value will be updated.

### FP.2 Compute Lidar-based TTC
To avoid possible outliers of the point cloud, the median has been used. As the targets are going to be the front vehicles and the axis used the x. We assume that the back of vehicles will always be straight and will not have an arrow shape. For this reason, the median of the x values has been used.

To do this, the current and previous point cloud will be sort in ascending order of x.Then the median of both will be taken. With these two values and the time elapsed between clouds (calculated with the rate) we will calculate the TTC.

Development has been done in the *computeTTCLidar* function of the *camFusion_Student.cpp* file.

### FP.3  Associate Keypoint Correspondences with Bounding Boxes
This task has been developed in the *clusterKptMatchesWithROI* method of the *camFusion_Student.cpp* file. At first the Euclidean distance of all matches, which are inside of the BB's ROI, is calculated. The Euclidean distance is obteined between the keypoints of the current and the previous frame.

With this distance list, the median is calculated. Then, the list is filtered using thresholds (proportional to median). A the end, filter's result are saved  in the vector *kptMatches* of the BB.

### FP.4 Compute Camera-based TTC
This task has been developed in  *computeTTCCamera* method of the file *camFusion_Student.cpp*. To do it, distances (h1 and h0) have been calculated to multiple matches. Distances greater than thresholds (100 and epsilon) have been used to calculate distance ratio. This ratio has been saved in a vector and its mean has been used to calculate TTC. 

### FP.5  Performance Evaluation 1
With the development of the median in Task 2, the TTC's estimation from lidar is more reliable than from camera. In order to do this task, I have changed the filtering that is done in Task 2. In this case, I have taken the minimum distance in X in both the current and the previous cloud. No data processing is performed. With this change it is observed that the lidar measurement is unstable.

The following three images show how filtering of cloud affects. The result without median is shown on the left and the result with median on the right.

![Example 1](/Examples/Example1.png)
Format: ![Alt Text](url)

![Example 2](/Examples/Example2.png)
Format: ![Alt Text](url)

![Example 3](/Examples/Example3.png)
Format: ![Alt Text](url)
