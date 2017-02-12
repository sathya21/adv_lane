##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

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

[image1]: ./examples/orig_chess.png "original chess"
[image2]: ./examples/undistorted_chess.png "undistorted chess"
[image3]: ./test_images/straight_lines1.jpg.jpg "Undistorted Road"
[image4]: ./examples/distorted_road.png "Undistorted Road"
[image5]: ./examples/color_binary_image.png "Color Binary Example"
[image6]: ./examples/combined_binary_image.png "Combined Binary Example"
[image7]: ./examples/image_with_src_polygon.png "Image with source polygon"
[image8]: ./examples/perspective_image_with_destination.png "Perspective image with destination"
[image9]: ./examples/polynomials.png "Polynomial computation"
[image10]: ./examples/example_poly_output.png "Filled Polynomial"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Advanced-Lane-Lines/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook located in the first code cell of "./example.ipynb"

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection.  

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image1]
![alt text][image2]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
Output of the camera calibration was passed to one of the images and the distortion was corrected. Distortion corrected image can be found below
![alt text][image3]
![alt text][image4]


####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used combination of color and gradient thresholding which can be found in code cell 3 of "./example.ipynb". Results of color binary and combined binary can be found below

![alt text][image5]
![alt text][image6]


####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform in code cell 4 of "./example.ipynb".  Based on the image, i had chosen the following source and destination co-ordinates and did a perspective transform and then got the warp perspective
```
top_right_src = [710, 463]
top_left_src = [575, 463]
bottom_right_src = [1060, 680]
bottom_left_src = [265, 680]

top_right_dest = [900, 0]
top_left_dest = [190, 0]
bottom_right_dest = [900, 680]
bottom_left_dest = [190, 680]

#src coordinates
src = np.float32([
    top_right_src, # top right
    bottom_right_src, # bottom right
    bottom_left_src, # bottom left
    top_left_src # top left
])

#dest coordinates
dst = np.float32([
    top_right_dest, # top right
    bottom_right_dest, # bottom right
    bottom_left_dest, # bottom left
    top_left_dest # top left
])
```

I verfied that my perspective transformation worked by checking the source and warped image. In the warped image, both the lines appear in parallel


![alt text][image7]
![alt text][image8]


####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I plotted a histogram and identified the left and right peaks. From that i used sliding windows to follow the line till its peak. Code for this can be found in code cell 5 of "./example.ipynb". Results can be found below

![alt text][image9]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in  code cell 8 of "./example.ipynb".

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code cell 9 of "./example.ipynb".  Here is an example of my result on a test image:

![alt text][image10]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Getting the right source and destination points for perspective transformation was the biggest challenge. It took some time to understand and get the correct values

When the polygon is finally plotted, there is a slight spill on to the left side and little bit on the top right side. This could have an issue as the car could think portion of the left side is drivable whereas it has solid yellow line.

To make it more  robust, some more iterations of  perspective transaction is needed to get it right.  
