## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

** Advanced Lane Finding Project **

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

[image1]: ./camera_cal/calibration2.jpg "Original Chessboard"
[image2]: ./output_images/chessboard/drawn_calibration2.jpg "Chessboard with corners"
[image3]: ./output_images/chessboard/undistort_calibration2.jpg "Undistorted Chessboard"
[image4]: ./test_images/straight_lines1.jpg "Original Straight Line"
[image5]: ./output_images/straight_undistort.png "Undistort Straight Line"
[image5]: ./output_images/straight_color_gradient.png "Color and Gradient Straight Line"
[image6]: ./output_images/straight_transformed.png "Transformed Straight Line"
[image7]: ./test_images/test1.jpg "Original Curve"
[image8]: ./output_images/curve.png "Color and Gradient Curve"
[image9]: ./output_images/curve_transformed.png "Transformed Curve"
[image10]: ./output_images/curve_lane.png "Lane Curve"
[image11]: ./output_images/curve_output.png "Output Curve"
[video1]: ./project_video_output.mp4 "Output Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in lines 50 through 100 of the file called `pipeline.py`.  

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![Original Chessboard][image1] ![Chessboard with corners][image2] ![Undistorted Chessboard][image3]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![Undistort Straight Line][image5]

I saved the mst and dist calculated from the camera calibration process into a pickle file (calibration.p). Then I load them from the pickle file, and using `cv2.undistort()` method.

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image. The code for this step is contained in lines 117 through 153 of the file called `pipeline.py`. .  Here's an example of my output for this step. 

![Color and Gradient thresholds][image6]

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `perspective_transform()`, which appears in lines 185 through 189 in the file `pipeline.py`.  The `perspective_transform()` function takes as inputs an image (`img`), as well as source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[585,455], [250, 680], [1050, 680], [700, 455]])
dst = np.float32([[300, 0], [300, 720], [1000, 720], [1000, 0]])
```

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![Before Transform][image8] ![Transformed][image9]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![Curved Lanes][image10]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines 331 through 350 in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![Final Output][image11]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Sometimes the lanes detected don't make sense. When sanity check fails, the pipeline will ignore current frame and use lanes detected from last successful frame. The pipeline sometimes fails when the dotted line is not clear, for example, limited dotted parts, one solution is pick a picture which  has limited dotted parts, tune the color and gradient thresholds to make sure lanes in this kind of frames can be correctly detected.
