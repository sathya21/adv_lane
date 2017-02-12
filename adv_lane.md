
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

[image1]: ./output_images/orig_chess.png " chess Calibration"
[image2]: ./output_images/unistorted_road.png "Road"
[image3]: ./output_images/color_binary_image.png " Binary Example"
[image4]: ./output_images/image_with_src_polygon.png "Image with source polygon"
[image5]: ./output_images/polynomials.png "Polynomial computation"
[image6]: ./output_images/example_poly_output.png "Filled Polynomial"
[video1]: ./project_video.mp4 "Video"

## [Rubric](https://review.udacity.com/#!/rubrics/571/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README
###Camera Calibration

####1. Briefly state how you computed the camera matrix and distortion coefficients. Provide an example of a distortion corrected calibration image.

The code for this step is contained in the first code cell of the IPython notebook  example.ipynb

I first prepared the object points for chessboard. Then all the calibration images were iterated  to find the chessboard corners. When chessboard corner is found, it was appended to objpoints and imgpoints. objpoint is 3D point in real world space and imgpoint is 2D point in image space. Once this is done, objpoints and imgpoints were passed to cv2.calibrateCamera to get camera calibration and distortion coefficients. This was applied to undistort function to get the undistorted image. Original and corrected image used for calibration can be found below

![alt text][image1]

###Pipeline (single images)

####1. Provide an example of a distortion-corrected image.
Output of the camera calibration was passed to one of the images and the distortion was corrected. Distortion corrected image can be found below
![alt text][image2]


####2. Describe how (and identify where in your code) you used color transforms, gradients or other methods to create a thresholded binary image.  Provide an example of a binary image result.
I used combination of color and gradient thresholding which can be found in code cell 3 of example.ipynb. Results of color binary and combined binary can be found below

![alt text][image3]


####3. Describe how (and identify where in your code) you performed a perspective transform and provide an example of a transformed image.

The code for my perspective transform in code cell 4 of example.ipynb.  Based on the image, i had chosen the following source and destination co-ordinates and did a perspective transform and then got the warp perspective
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


![alt text][image4]


####4. Describe how (and identify where in your code) you identified lane-line pixels and fit their positions with a polynomial?

Then I plotted a histogram and identified the left and right peaks. From that i used sliding windows to follow the line till its peak. Code for this can be found in code cell 5 of example.ipynb. Results can be found below

![alt text][image5]

####5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in  code cell 8 of example.ipynb.

####6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in code cell 9 of example.ipynb.  Here is an example of my result on a test image:

![alt text][image6]

---

###Pipeline (video)

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./final_video.mp4)

---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Getting the right source and destination points for perspective transformation was the biggest challenge. It took some time to understand and get the correct values

When the polygon is finally plotted, there is a slight spill on to the left side and little bit on the top right side. This could have an issue as the car could think portion of the left side is drivable whereas it has solid yellow line.

To make it more  robust, some more iterations of  perspective transaction is needed to get it perfect.  
