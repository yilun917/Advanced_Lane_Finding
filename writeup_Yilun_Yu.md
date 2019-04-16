## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./output_images/Undistorted_straight_line.jpg "Undistorted"
[image2]: ./output_images/perspective_changed_straight_line.jpg "Road Transformed"
[image3]: ./output_images/binary_straight_line.jpg "Binary Example"
[image4]: ./output_images/warped_straight_lines.jpg "Warp Example"
[image5]: ./output_images/fit_straight_lines.jpg "Fit Visual"
[image6]: ./output_images/straight_lines1.jpg "Output"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points

### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Camera Calibration

#### 1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in "./Advanced_Lane_Findng.ipynb
"   

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result: 

![alt text][image1]

### Pipeline (single images)

#### 1. Provide an example of a distortion-corrected image.

To demonstrate this step, I will describe how I apply the distortion correction to one of the test images like this one:
![alt text][image2] I used local python IDE to process the image so that I can have cursor to get the exact coordinate on the image. I hand picked 4 points representing for cornors on the road. And then provide the desired location for them in an top view image. Later, in jupyter notebook, I then used these two matrix to calculate the transform matrix using cv2.warpPerspective().

#### 2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.

I used a combination of color and gradient thresholds to generate a binary image (thresholding steps at code cell 2 in ./Advanced_Lane_Findng.ipynb).  Here's an example of my output for this step.  (note: this is not actually from one of the test images)

![alt text][image3]

Color channel S was chosen with threshold (70, 180) were used. And Gradient threshold (30, 160) were chosen. The numbers are based on experiment. 

#### 3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform includes a function called `warper()`, which appears in the 3rd code cell of the ./Advanced_Lane_Findng.ipynb.  The `pers_change()` function takes as inputs an image (`img`) to get the top down view of it. The required source (`src`) and destination (`dst`) points are precalculated with earlier code.  I chose the hardcode the source and destination points in the following manner:

```python
src = np.float32([[[258.6, 695]],[[1047.55,695.87]],[[583.51, 465.73]],[[700,464.22]]])
dst = np.float32([[[258.6,695]],[[1047.55, 695]], [[258.6,0]],[[1047.55, 0]]])  
```

This resulted in the following source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 258.6, 695    | 258.6, 695    | 
| 1047.55,695.87| 1047.55, 695  |
| 583.51, 465.73| 258.6,0       |
| 700,464.22    | 1047.55, 0    |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image4]

#### 4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I did some other stuff and fit my lane lines with a 2nd order polynomial kinda like this:

![alt text][image5]
In this part, the code is almost identical to what is provided in earlier quiz.


#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

The curvature was done in 7th code cell of the ./Advanced_Lane_Findng.ipynb, And the position was done in 9th cell. The curvature part, I just used whats given in ealier quiz. For the center position, I first calculated the center of the fitted lane. Then compare the center of lane with the center of the image in X dimension which is also supposed to be the center of the veihcle. The position of the vehicle respect to center thus can be calculated by subtracting the two. 

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in 11th code cell of the ./Advanced_Lane_Findng.ipynb in the function `warp_back()`. I drew fill the space in between two lanes with green and then inversely transform the top down view to the original perspective and combine with the original image. Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

The video is reasonably working with inaccuracy at the bottom and a few frames of failure on the bridge. Here's my video output. [link to my video result](./output_project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

My pipeline is a bit inaccurate on the bottom of the image. And it will fail at the bridge in the video due to the high contrast in the video causing by sunlight and tree shadows. If I have more time, I think it would be better to implement what I did in project 1 into this fit. Combine both of the fitting to make it more accurate.
