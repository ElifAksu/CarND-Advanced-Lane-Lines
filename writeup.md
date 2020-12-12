## **Project 2: Advanced Lane Finding
## Writeup

## In this project the lane lines on the road are found. The detailed information of the pipeline is presented below. 

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

[image1]: (./output_images/undist_straigth_lines1.jpg=250x) "Undistorted road image"
[image2]: ./output_images/gradx_straigth_lines1.png "Gradx"
[image3]: ./output_images/grady_straigth_lines1.png "Grady"
[image4]: ./output_images/color_binary_straigth_lines1.png "Color threshold"
[image5]: ./output_images/dir_binary_straigth_lines1.png "Dir threshold"
[image6]: ./output_images/mag_binary_straigth_lines1.png "Magnitude threshold"
[image7]: ./output_images/combined_straigth_lines1.png "Combined threshold"
[image8]: ./output_images/combined_warped_straigth_lines1.png "Warped"
[image9]: ./output_images/fit_poly_straigth_lines1.png "Fit Poly"
[image10]: ./output_images/search_around_straigth_lines1.png "Search Around Poly"
[image11]: ./output_images/fit_area_straigth_lines1.png "Fit Area"
[image12]: ./output_images/fit_area_text_straigth_lines1.png "Fit Area and Text"
[image13]: ./output_images/undistort_calibration_image.png "undistort calibration image"
[image14]: ./output_images/calibration_drawcorners.png "Draw Corners on chessboard image"
[image15]: ./output_images/roi.png "Region of interest"
[image16]: ./output_images/hist.png "Histogram"
[video1]: ./project_video_output.mp4 "Video"


---

### Camera Calibration

I start by preparing "object points", which will be the (x, y, z) coordinates of the chessboard corners in the world. Here I am assuming the chessboard is fixed on the (x, y) plane at z=0, such that the object points are the same for each calibration image.  Thus, `objp` is just a replicated array of coordinates, and `objpoints` will be appended with a copy of it every time I successfully detect all chessboard corners in a test image.  `imgpoints` will be appended with the (x, y) pixel position of each of the corners in the image plane with each successful chessboard detection. Chessbord corners are drawen as follow image. 

![alt text][image14]

I then used the output `objpoints` and `imgpoints` to compute the camera calibration and distortion coefficients using the `cv2.calibrateCamera()` function.  Camera calibration parameters are written in 'camera_calibration.p' file I applied this distortion correction to the test image using the `cv2.undistort()` function and obtained this result:

![alt text][image13]

### Pipeline (single images)

#### 1. Distortion Correction of Road Image

With using camera calibration parameters which i found from the chessboard images,  I applied the distortion correction to one of the test images like this one:

![alt text][image1]

#### 2. Thresholded Binary Image
In order to extract the lines, some threshold methods are used. These threshold methods are:
	1. Sobel Absolute Threshold Orient X
	2. Sobel Absolute Threshold Orient Y
	3. Sobel Magnitude Threshold
	4. Sobel Direction Threshold
	5. Color Threshold 
I used a combination of these thresholds to generate a binary image.  Here's an example of my output for this step. 

![alt text][image7]

#### 3. Warp Image 

The code for my perspective transform includes a function called `warper_func()`. The `warper_func()` function takes as inputs an image, uses source (`src`) and destination (`dst`) points.  I chose the hardcode the source and destination points while investigating the region of interest. The region of interest should include the lane lines as follows.  

![alt text][image15]

This resulted source and destination points:

| Source        | Destination   | 
|:-------------:|:-------------:| 
| 150, 720      | 200, 720      | 
| 590, 450      | 200, 0        |
| 700, 450      | 1050, 0       |
| 1180, 720     | 1080, 720     |

I verified that my perspective transform was working as expected by drawing the `src` and `dst` points onto a test image and its warped counterpart to verify that the lines appear parallel in the warped image.

![alt text][image8]

#### 4. Fit polynomial to lane lines

Histogram of the warped image is computed. When the histogram is investigated, it can be seen that the peaks are directly related to the lane pixels. 

![alt text][image16]

#### 5. Describe how (and identify where in your code) you calculated the radius of curvature of the lane and the position of the vehicle with respect to center.

I did this in lines # through # in my code in `my_other_file.py`

#### 6. Provide an example image of your result plotted back down onto the road such that the lane area is identified clearly.

I implemented this step in lines # through # in my code in `yet_another_file.py` in the function `map_lane()`.  Here is an example of my result on a test image:

![alt text][image6]

---

### Pipeline (video)

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (wobbly lines are ok but no catastrophic failures that would cause the car to drive off the road!).

Here's a [link to my video result](./project_video.mp4)

---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further.  
