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

[camera_cal]: ./output_images/camera_cal_1.png "Camera Calibration"
[undistort_1]: ./output_images/undistort_1.png "Undistorted image 1"
[undistort_2]: ./output_images/undistort_2.png "Undistorted image 2"
[warping_1]: ./output_images/warping_1.png "Warped Image 1"
[sobel_abs]: ./output_images/sobel_abs.png "Sobel Absolute"
[sobel_mgn]: ./output_images/sobel_mgn.png "Sobel Magnitude"
[sobel_dir]: ./output_images/sobel_dir.png "Sobel Direction"
[sobel_m+d]: ./output_images/sobel_m+d.png "Sobel Magnitude + Direction"
[hlS]: ./output_images/hlS.png "HLS Colorspace S-Channel"
[hLs]: ./output_images/hLs.png "HLS Colorspace L-Channel"
[laB]: ./output_images/laB.png "LAB Colorspace B-Channel"
[l+b]: ./output_images/l+b.png "HLS L-Channel + LAB B-Channel"
[l+b+m]: ./output_images/l+b+m.png "HLS L-Channel + LAB B-Channel + Sobel Magnitude"
[slid_wind]: ./output_images/slid_wind.png "Sliding window"
[drawn_back]: ./output_images/drawn_back.png "Drawn Back"
[video1]: ./project_video_output.mp4 "Video"
[video2]: ./challenge_video_output.mp4 "Video"
[video3]: ./harder_challenge_video_output.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Camera Calibration

For each of the given camera calibration images, I first used the OpenCV findChessboardCorners method to get the corners. Based on the success of that method I collected the object points and respective image points which where used to undistort the images. Corners where drawn. The corners as 2D points are defining a single quadrilateral shape. I used the corners of that shape to define src coordinates fed into getPerspectiveTransform which given the matrix necessary to unwarp the image.

Here is an example of such a generated image:

![Camera Calibration][camera_cal]


### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

I use a single camera calibration image to perform undistortion skipping the process of finding corners. Here is the output:

![undistorted image 1][undistort_1]

I repeated the process for the first of the test images. Here is the result:

![undistorted image 2][undistort_2]


#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

Here are the steps:

- Identify a quadrilateral shape the tightly bounds the lanes
- Perform perspective transform mapping the above shape to a rectangle of dimentions equal to the initial image

Here is a sample of the process so far:

![warped image 1][warping_1]

Then I experimented with several methods of thresholding and a variety of parameters for each:

- Sobel absolute thresholding: this gave a nice clear binary image, albeit weak at times

![sobel absolute][sobel_abs]

- Sobel Magnitude: this gave a stronger to absolute result, albeit with unecessary artifacts

![sobel magnitude][sobel_mgn]

- Sobel Direction: this by itself was too noisy and could not be used

![Sobel Direction][sobel_dir]

- Sobel Magnitude and Direction combined: the combination removed the artifacts from the Magnitude method, but overall gave a very weak result

![Sobel Magnitude + Direction][sobel_m+d.png]


Further to Sobel I experimented with colorspace based thresholding:

- HLS Colorspace S-Channel

![S-Channel][hlS]

- HLS Colorspace L-Channel

![L-Channel][hLs]

- LAB Colorspace B-Channel

![B-Channel][laB]

- HLS L-Channel + LAB B-Channel

![L-Channel + B-Channel][l+b]

-HLS L-Channel + LAB B-Channel + Sobel Magnitude

![L-Channel + B-Channel + Magnitude][l+b+m]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

See the section above.

I understand that it might give better results to threshold fist, transform after. However I haven't experimented with this order of steps.

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Two methods where implemented for detecting lane pixels in a thresholded image. The first performs the sliding window search and works with zero prior knowledge. The second method expects matrices of previously found lanes as a shortcut to improve performance. Here is the result of the sliding window search:

![Sliding window][slid_wind.png]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

Radius and distance where calculated by fitting a polynomial to each lane. The radious for each polynomial was calculated as per the equations given in the course material. The distance of the camera from the center of the lane is calculated by subtracting from the midpoint the average intercepts of the pixels for each lane.

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

The inverse perspective transform is used to draw the detected lane back into the original image, along with the radious and distance data. Here is an example:

![drawn back][drawn_back]

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


