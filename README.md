## Advanced Lane Finding

---
**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.

[//]: # (Image References)

[image1]: ./output_images/undistort_output.jpg "Undistorted"
[image2]: ./output_images/undistort_output2.jpg "Undistorted"
[image3]: ./test_images/test2.jpg "Road Transformed"
[image4]: ./output_images/binary_combo_example.jpg "Binary Example"
[image5]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image6]: ./output_images/histogram.jpg "Histogram"
[image7]: ./output_images/color_fit_lines.jpg "Fit Visual"
[image8]: ./output_images/example_output.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./ad_lane_line.ipynb".  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

![alt text][image1]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image2]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image3]

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image as implemented in the method pipeline(). After various combinations, the lane lines are clearly visible. Here's an example of my output for this step.
![alt text][image4]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warp()`,  The `warp()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.
After tuning the result, I chose the source and destination points in the following manner:

```python
src = np.float32([[585, 460],[695, 460],[1126, 720],[203, 720]])
dst = np.float32([[275, 0],[1025,0], [960, 720], [320, 720]])
```

This resulted in the following source and destination points:

|  Source   | Destination |
|:---------:|:-----------:|
| 585, 460  |   275, 0    |
| 695, 460  |   1025, 0   |
| 1126, 720 |  960, 720   |
| 203, 720  |  320, 720   |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.
![alt text][image5]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Firstly, I take a histogram along all the columns in the lower half of the image, and get the plot like this:
![alt text][image6]

Then, I implemented a sliding window to find the nonzero pixels. With all these points collected, use the numpy np.polyfit to fit them in to 2nd order polynominal lines, one for each side of the lane. See below for an example:
![alt text][image7]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

For the curvature, I used 3.7/700 meters per pixel for the x dimension and 30/700 meters per pixel for the y dimension. The average of the left curvature and the right curvature is set to be the curvature of the lane.

For the center offset, according to the tips, I assumed that the camera is mounted to the center of the car. So the center of the image is the center of the car. I compute the offset of the lane center from the center of the image (converted from pixels to meters). If the offset is positive, the car is on the right side of the lane center; otherwise, it is on the left side of the lane center.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

Here is an example of my result on a test image:
![alt text][image8]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?
For challenge videos, due to lighting conditions and the shadow, the pipeline is not working very well. Definitely, a more comprehensive and powerful binary image pipeline is needed to handle a more complex environment. For example, more color space combination and thresh parameters could be tested. In addition, the Lane() class has lots of room to optimize to better treat the new detection. Also, some filter techniques could be used in the future to handle the noise. Last but not least, rather than using the hardcode way to define the source points manually for perspective transform, it will be better to try some algorithms to automate the process.
