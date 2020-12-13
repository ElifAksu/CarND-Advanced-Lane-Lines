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

[image21]: ./images/undist_straigth_lines1.png "Undistorted"
[image22]: ./images/gradx_straigth_lines1.png "Gradx"
[image23]: ./images/grady_straigth_lines1.png "Grady"
[image24]: ./images/color_binary_straigth_lines1.png "Color threshold"
[image25]: ./images/dir_binary_straigth_lines1.png "Dir threshold"
[image26]: ./images/mag_binary_straigth_lines1.png "Magnitude threshold"
[image27]: ./images/combined_straigth_lines1.png "Combined threshold"
[image28]: ./images/combined_warped_straigth_lines1.png "Warped"
[image29]: ./images/fit_poly_straigth_lines1.png "Fit Poly"
[image10]: ./images/search_around_straigth_lines1.png "Search Around Poly"
[image11]: ./images/fit_area_straigth_lines1.png "Fit Area"
[image12]: ./images/fit_area_text_straigth_lines1.png "Fit Area and Text"
[image13]: ./images/undistort_calibration_image.png "undistort calibration image"
[image14]: ./images/calibration_drawcorners.png "Draw Corners on chessboard image"
[image15]: ./images/roi.png "Region of interest"
[image16]: ./images/hist.png "Histogram"
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

<p align="center">
  <img width="800" height="400" src="./images/undist_test.png ">
</p>


#### 2. Thresholded Binary Image
In order to extract the lines, some threshold methods are used. These threshold methods are:
	1. Sobel Absolute Threshold Orient X
	2. Sobel Absolute Threshold Orient Y
	3. Sobel Magnitude Threshold
	4. Sobel Direction Threshold
	5. Color Threshold 
I used a combination of these thresholds to generate a binary image.  Here's an example of my output for this step. 

<p align="center">
  <img width="800" height="400" src="./images/combined_test.png ">
</p>


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

<p align="center">
  <img width="800" height="400" src="./images/combined_warped_test.png ">
</p>

After calibration, thresholding and perspective transform to the road image, lane lines are seen clearly.
#### 4. Fit polynomial to lane lines

Histogram of the warped image is plotted. When the histogram is investigated, it can be seen that the peaks are directly related to the lane pixels. 

![alt text][image16]

Two highest peaks from the histogram are the starting points to search lane lines. Sliding windows are used to determine the lane pixels. A polynomial is fitted with `polyfit()` function on found  pixels. 

<p align="center">
  <img width="800" height="400" src="./images/fit_poly_test.png ">
</p>

 
#### 5. Search from Prior

The lane lines do not move a lot for each frame so, searching again and again with sliding windows is not necessary. In the next frame, the prior knowledge about the lines can be used. The margin is determined to find the search area. Lane pixels are found with these method rather than sliding windows. The example search area when the previos coefficients are used can be seen in the following image: 

<p align="center">
  <img width="800" height="400" src="./images/search_around_test.png ">
</p>

#### 6. Curvature of the Lane Lines
I fit a second order polynomial to the real lane data. The coefficients of the polynomial are provided from `polyfit()` function. The equation for radius of curvature is used to find left-right lane curvature radius. The avarage value of these two radius of curvatures gives the road curvature. 

The second calculation is the shifting of the ego vehicle, that is distance of the center.Ego vehicle position is assumed that the center of the image. The center of the road is calculated from the right- lane lines. The difference between center of the road and the car position gives the shift. The shift value is converted from pixels to meters.  




#### 6. Draw Detected Area on Original Image
The warper function calculates inverse of M. I draw polylines left-right lanes and fill the poly. After I get the result from warped image, I applied perspective transform again with the `inv_M` to get original image.  I put the text which shows the road curvature and shift values on world space. 

<p align="center">
  <img width="800" height="400" src="./images/fit_area_text_test.png ">
</p>
---

### Pipeline (video)



Here's a [link to my video result](./project_video_output.mp4)

---

### Discussion

Firstly, I selected source (`src`) and destination (`dst`) points with trials. To cover all images these points can be optimized. 
Also, There are lots of parameters which are mostly in the thresholding part. These parameters should also calibrated well. The threshold values need to tested with different road images to find best. 
As an improvement on a code, if there is no lane in the image frame the tracking algorithm can be implemented to do prediction for a while with using previous lane lines.   
When I tested my code with challenge video, I realized that the lines cannot found correctly on each frame. The reason of this, the changing conditions such as brightness affect my lane finding code.  
