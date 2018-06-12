## Writeup Template

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

[image1]: ./examples/undistort_output.png "Undistorted"
[image2]: ./test_images/test1.jpg "Road Transformed"
[image3]: ./examples/binary_combo_example.jpg "Binary Example"
[image4]: ./examples/warped_straight_lines.jpg "Warp Example"
[image5]: ./examples/color_fit_lines.jpg "Fit Visual"
[image6]: ./examples/example_output.jpg "Output"
[video1]: ./project_video_output.mp4 "Video"
[video2]: ./challenge_video_output.mp4 "Video"
[video3]: ./harder_challenge_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

Each of the tasks is clearly marked in separate sections in the lanefinder.html jupyter notebook. In this writeup I will only add a discussion over the finer points of the project.

### Camera Calibration

Cells 3-5

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

Cell 7

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Cells 8-24, 109

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

Cells 8-10

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Cells 39-43

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Cell 44

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.


Cells 100-101

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Initially I tried with only the L channel from HLS cobined with the B channel from LAB. I found out that S can perfectly identify the yellow line while B can isolate the white line without all the artifacts that  Sobel gives. This method worked well on the mandatory project video. However it failed to give good results for the challenge videos.

Then I tried to strengthen the detection by combining in the Sobel magnitude thresholding. This method gave some better results overall, even though the challenge video intially seems worse. I consider it as a better result because the catastrofic detections are of smaller severity even though of higher frequency.

My intuition at this point is twofold:
1) I have not yet identified the proper thresholding mechanism
2) I need a metric to combine different thresholding methods, which entails a mechanism to detect bad detections. Create a hierarchy of different methods, if one fails retry with the next dynamically.

Unfortunatelly I currently don't have the time to explore either of those paths.


