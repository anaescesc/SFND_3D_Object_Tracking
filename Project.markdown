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

### FP.6  Performance Evaluation 2
To do this task, the median developed in section 2 has been reimplemented to calculate the TTC Lidar. In the *Graph.xlxs* file all the experiments done can be found. From this analysis the next conclussions have been obteined:
1. Respect of lidar :
     1.  Detectors and descriptors not afects to Lidar estimation. This estimation is calculated from de ROI obteined by yolo detection.
   1. Lidar estimation is used as a reference. It is stable and it responds correctly to what happen on the scene. In the last part of dataset, where the vehicle is braking (brake lights activated), the TTC decreased considerably. Visually we can also see in the images how the front vehicle is closer.
2. Respect of detector and descriptors :
   1. The TTC estimation of the camera is very sensitive to the change of detector and descriptor.
   2. The results obtained with the NN method are much worse than with the KNN. With the NN method, the results obtained are not valid.
   3. Using the FAST detector, the time estimates obtained are negative, in most of the route.
   4. With the BRISK detector (KNN) it can be seen how the results in the first and last part of the dataset are stable. It becomes unstable when the vehicle goes from accelerating to decelerating.
   5. Behaviors obtained with ORB are not valid.
   6. With the AKAZE detector the estimates obtained in the first part of the dataset are stable. But by changing the sign of acceleration they become unstable.
   7. The best results of modern detectors are obtained with SIFT as detector and BRISK and FREAK as descriptors. Although the estimates are not as good as with lidar.
   8.  Regarding the HARRIS and SHITOMASI detectors, curiously these are the ones that present the best results.
3. Final conclusions:
   1. Lidar TTC estimates are more accurate and reliable than those obtained by the camera.
   2. Camera estimates are very sensitive to changes in descriptor and detectors.
   3. It is necessary to filter the matches with which the TTC of the camera is estimated.
   4. It is necessary to implement an acceleration model. The velocity model is not sufficient for the estimation.

