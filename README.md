## Advanced Lane Finding Project

![Final Result Gif](./output_images/ProjectOut.gif)

In this project, The goals / steps of this project are the following:

* Compute the camera calibration matrix and distortion coefficients given a set of chessboard images.
* Apply a distortion correction to raw images.
* Use color transforms, gradients, etc., to create a thresholded binary image.
* Apply a perspective transform to rectify binary image ("birds-eye view").
* Detect lane pixels and fit to find the lane boundary.
* Determine the curvature of the lane and vehicle position with respect to center.
* Warp the detected lane boundaries back onto the original image.
* Output visual display of the lane boundaries and numerical estimation of lane curvature and vehicle position.


[//]: # (Image References)

[image1]: ./output_images/ChessImgOrig.jpg "Original image of Chess board"
[image2]: ./output_images/ChessUndist.jpg "Undistorted image of Chess board"
[image3]: ./output_images/TestImgOrig.jpg "Original Test image"
[image4]: ./output_images/TestUndistImg.jpg "Undistorted Test image"
[image5]: ./output_images/UndistTestImg.jpg "Undistorted Test image with src & dst points"
[image6]: ./output_images/WarpedTestImg.jpg "Warped Test image"
[image7]: ./output_images/ThresholdApplied.jpg "Threshold Applied on test image"
[image8]: ./output_images/VisualizeLane.jpg "Fit Visual"
[image9]: ./output_images/RadiusOfCurve.png "Radius of Curvature"
[image10]: ./output_images/PolyFit.png "Polynomial Fit"
[image11]: ./output_images/lanelines.jpg "Output"
[video1]: ./ProjectOutput.mp4 "Video"


### Code:
This project requires python 3.5 and the following dependencies:
- [NumPy](http://www.numpy.org/)
- [matplotlib](http://matplotlib.org/)
- [OpenCV](http://opencv.org/)
- [MoviePy](http://zulko.github.io/moviepy/)


### Step 1: Distortion Correction
In this step, I used the OpenCV functions `findChessboardCorners` and `drawChessboardCorners` to identify the locations of corners on a series of pictures of a chessboard taken from different angles.
Then I applied the distortion correction co-efficients to test images. I just showed here one example:

#### Distorted Chess Image
![image1]
#### Undistorted Chess Image
![image2]
#### Distorted Test Image
![image3]
#### Undistorted Test Image
![image4]

### Step 2: Perspective Transform
Perspective transform means is to transform the undistorted image to a bird eye view of the lane. To do that, I first applied the OpenCV functions `getPerspectiveTransform` and `warpPerspective` which take a matrix of four source points on the undistorted image and remaps them to four destination points on the warped image(Red & Blue colored points). Here is one of the examples:

#### Undistorted Test Image
![image5]
#### Warped Test Image
![image6]


### Step 3: Apply Binary Thresholds
In this step I attempted to convert the undistorted color image to different color spaces and create binary thresholded images which highlight only the lane lines and ignore everything else. 
To find the optimum threshold I had to do trial and error, I would say this is the most challenging part of the project. The following are the color space and threshold that I found the best:

- The S Channel from the HLS color space, with a min threshold of 180 and a max threshold of 255
- The L Channel from the LUV color space, with a min threshold of 225 and a max threshold of 255
- The B channel from the Lab color space, with a min threshold of 155 and an upper threshold of 200

![image7]


### Steps 4, 5 and 6: Fitting a polynomial to the lane lines, calculating vehicle position and radius of curvature:

#### Polynomial Fit
After applying calibration, thresholding, and a perspective transform to a road image, I have a binary image where the lane lines stand out clearly. I first take a histogram along all the columns in the lower half of the image. With this histogram I am adding up the pixel values along each column in the image. In my thresholded binary image, pixels are either 0 or 1, so the two most prominent peaks in this histogram will be good indicators of the x-position of the base of the lane lines.Then I chose number of sliding windows (it is 9 for this project) to scan thorugh the image from bottom to top to gether the sample points of the left and right lanes.

I used two methods here:
- Blind Search
- Found Lane

'Blind Search' is used either at the very begining or when lane lines are not found using 'Found Lane' method.  
In the 'Found Lane' you know where the lines are you have a fit! In the next frame of video you don't need to do a blind search again, but instead you can just search in a margin (here is it 100) around the previous line position.

#### Example of Polynomial fit
![image10]
#### Applying Sliding Window Method on Warped Test Image
![image8]

#### Vehicle Position
I used the below formula to calculate position of the vehicle:

Position = (Xleft_InterceptBottomImg + Xright_InterceptBottomImg)/2.

Lane.line_base_pos = abs((X/2 - Position)*3.7/480)  ( The gap between lanes is 480 pixels)   

#### Radius of Curvature
The radius of curvature at any point xxx of the function x=f(y)x = f(y)x=f(y) is given as follows:

![image9]


### Last Step : Final Output, Issues faced and scope of improvement
I colored green on the area between left and right lanes like this: 

![image11]

The video pipeline developed in this project did a fair job of detecting the lane lines in the test video provided for the project, which shows a road in basically ideal conditions, with fairly distinct lane lines, and on a clear sunny day. But it would be a bit challenge to find the optimum threshold which works for most of the scenario. I like to learn about adaptive thresholding and have a plan to apply on it in the future to observe the performance. I have not yet tested the pipeline on additional video streams which could challenge the pipeline with varying lighting and weather conditions, road quality, faded lane lines, and different types of driving like lane shifts, passing, and exiting a highway. I am planning to record my own video and apply what I have learned from this exciting tutorial from Udacity.


