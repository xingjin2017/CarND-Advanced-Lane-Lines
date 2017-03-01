##Project 4 Advanced Lane Lines
###This is the project write up for project 4 Advanced Lane Lines, which documents the steps taken to process the driving video and the results that have been generated.

Here is the [link to the project result video](https://youtu.be/OLAPB69Sdio).

---

**Advanced Lane Finding Project**

The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms to HSV to find yellow/white colors, gradients, and HLS saturation channel, to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view") to look at the lanes.
* Detect lane pixels using histogram and sliding windows, and fit to find the lane boundary.
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
[video1]: ./project_video.mp4 "Video"
[image7]: ./output_images/test_calibration1.jpg
[image8]: ./output_images/test_undistorted.jpg
[image9]: ./output_images/test_sampled_dots.png
[image10]: ./output_images/test_warped.png
[image11]: ./output_images/test_original.png
[image12]: ./output_images/test_undistorted.png
[image13]: ./output_images/test_yellow.png
[image14]: ./output_images/test_white.png
[image15]: ./output_images/test_gray.png
[image16]: ./output_images/test_gray_erosion.png
[image17]: ./output_images/test_sobel_x.png
[image18]: ./output_images/test_hls.png
[image19]: ./output_images/test_lane_warp.png
[image20]: ./output_images/test_fit_polynomials.png
[image21]: ./output_images/test_update_polynomials.png
[image22]: ./output_images/test_overlayed.png

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. This file is the project writeup. All source code of this project is located in the [Jupyter Notebook Here](https://github.com/xingjin2017/CarND-Advanced-Lane-Lines/blob/master/P4-Advanced-Lane-Finding.ipynb).

###Camera Calibration

####1. The calibration is done by reading the 20 checkboard calibration images provided, and try to find the corners in them (16 of them have the specified number of corners) using cv2.findChessboardCorners, and then call cv2.calibrateCamera to calibrate the camera calibration matrix and distortion matrix. 

The code for this step is located in the first block of the [Jupyter Notebook](https://github.com/xingjin2017/CarND-Advanced-Lane-Lines/blob/master/P4-Advanced-Lane-Finding.ipynb).

Here are one calibration image used and the corresponding undistorated image:

![alt text][image7]
![alt text][image8]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.

Here is one example of original image:

![alt text][image11]

Here is the corresponding undistorted image:

![alt text][image12]

####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color (yellow and white) filtering in the HSV color space, sobel x edge detection in the gray image with erosion, and the saturation channel filtering from HLS color space. Erosion is used to attempt to remove dark lines on the road from the edge detection, as they are not lane lines. Here's an example of my output for this step.  (note: this is not actually from one of the test images)

The source code is located in [combined_filter2](https://github.com/xingjin2017/CarND-Advanced-Lane-Lines/blob/master/P4-Advanced-Lane-Finding.ipynb) for filtering.

Here is the HSV color space yellow color filtering result:

![alt text][image13]

Here is the HSV color space white color filtering result:

![alt text][image14]

Here is a gray color image before sobel x edge detection:

![alt text][image15]

Here is the erosion applied gray image - it is intended for the challenge video, which has black lines on the road - not a very good example here for the white lane line:

![alt text][image16]

Here is the sobel edge detection with absolute value in the 'x' orientation:

![alt text][image17]

Here is the HLS saturation channel filtering:

![alt text][image18]

Here is the combined result warped for a birds-eye view:

![alt text][image19]

####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The perspective transform is done by selecting four points on the lane lines from an image, and specify the destination locations on the warped image:

```
src = np.float32([[577, 464], [715, 464], [1084, 672], [307, 672]])
dst = np.float32([[300, 200], [1030, 200], [1030, 700], [300, 700]])

M = cv2.getPerspectiveTransform(src, dst)
Minv = cv2.getPerspectiveTransform(dst, src)
```
This code is located in the [block '3'](https://github.com/xingjin2017/CarND-Advanced-Lane-Lines/blob/master/P4-Advanced-Lane-Finding.ipynb) in the Jupyter Notebook.

Here is the image that has the four sampled dots drawn:

![alt text][image9]

The corresponding warped image:

![alt text][image10]

####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

The fitting of polynomials are done in two functions.

One is the starting-from-scratch sliding window apporach in the function **fit_polynomials_with_sliding_window**. I made some additional changes there on top of the lecture examples, say by doing some linear prediction based on the already fitted windows - this maybe useful if several in-between windows don't have lane markers. This is yet to prove to be useful.

Here is one sample image for the fitting:

![alt text][image20]

Two is to utilize the fittings from a previous frame and do a neighborhood search. This is in the function **update_polynomials**.

Here is one sample image from update fitting:

![alt text][image21]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The curvature/position calculation is done in the function **calculate_curvature_and_center**.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented functions like **generate_lane_image** and **lane_polygon** to draw polygon overlays and project back to the original image. The code is in [block 6](https://github.com/xingjin2017/CarND-Advanced-Lane-Lines/blob/master/P4-Advanced-Lane-Finding.ipynb) of the Jupyter Notebook.

Here is one example of overlayed image:

![alt text][image22]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here is the [link to the project result video](https://youtu.be/OLAPB69Sdio). 

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

The project video is relatively straightforward to handle, I finished this one quickly, given not much adjustments using the examples from the lectures. But I really haven't made the challenge video work so far, even after a few days' effort. I found the followings are the problems / challenges to resolve for the challenge video and beyond:

1. Reliably detect the yellow colr and white color from lane lines - they must have certain characteristics that make them different. But the hard coded ranges don't work a lot of times for the challenge video.

2. For lighting condition change, I tried techniques like histogram equalization, and haven't got consistent results in the few tries. 

3. For the dark pavement lines on the road, tried erosion to find minimum and then filtering, it helped for certain situations.

4. For the challenge video, a lot of times, one lane line has only a few sparse segments in the image, this makes detection and fitting a polynominal difficult. The polynominal can tilt wildly at the top (or bottom if there is no white lane marker below).

I hope the lectures can provide links and pointers to more advanced materials in this area, so people trying these videos can have certain guidance to proceed and learn about the relevant techniques! That would be awesome.

