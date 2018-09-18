# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/Output_solidWhiteRight.jpg

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of following 7 steps.

1) I converted an image to grayscale
2) I defined a kernel size and applied Gaussian smoothing to ease out noise
3) I defined parameters for Canny edge detection and appled it on the smoothed image in step 2
4) I defined a four sided polygon which formed my region of interest for applying the hough transform
5) I defined the hough transform parameters
6) I ran the hough transform on the Canny edge detected image and iterated over the output to draw lines on a blank image of same size
7) I drew the lines on the hough transformed image as shown below

![alt text][image1]

In order to draw a single line on the left and right lanes, I modified the draw_lines() function. This was done in following steps

1) I separated the points from the Hough Transform output that in terms of being part of left or right lane by figuring out their slopes.
A negative slope refers to left lane and a positive one refers to right lane.
2) I averaged the slope of the segments formed, to obtain an average slope for the left lane and one for the right lane.
3) With average slopes and intercepts calculated for the left lane and right lane lines, I further calculated the bottom left and right vertices using the line equation (Y = slope * X + Intercept) and assuming Y to be the end of the Y axis of the image. This helps in marking the lane line all the way to the bottom.
4) I now have figured out the top left and right vertices. The top left vertex is the right most point in the left lane cluster of points (found in step 1). The top right vertex is the left most point in the right lane cluster of points (found in step 1) 
5) Now having gotten the vertices, I drew the single lane lines for left and right side using the cv2.line function


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming of the pipeline, would be when a vehicle hits sharp curves. For example in step 1 while descrbing the draw_lines() function in previous answer, the vaules in the limit value for slopes are set to slope > 0.5 instead of just slope > 0 for right lane and slope < -0.5 for left lane instead of just slope < 0. The reason for this is that in the annotated video, at brief points there can be lane lines generated that depart off the lane and hence the slope limtation helps lane detection stay on course throughout the video. So when curve approaches, the slope limit values may cause lane line go unacknowledged.

Another shortcoming could be, the logic of the code perhaps takes a hit, if the input video stream contains a lane change maneuver. Of course then, the region of interest is unable to shift dynamically and lane detection through this pipeline may go awry.

Further if the illumination changes, because of alternating sun and shade on the road, the edge detection picks up the sun-shade contrast lines instead of lane lines because the gradient in the former is higher. This is a scenario in challenge.mp4 video in the file 'test_videos'

Note:
For the challenge.mp4, the region vertices are only changed which are as below
vertices = np.array([[(250, 650), (600, 450), (750, 450), (1100, 650)]], dtype=np.int32)


### 3. Suggest possible improvements to your pipeline

A possible improvement would be the addition of ability to identify lane lines irrespective of intrusion of higher gradient changes like when it happens because of sudden change of illumination on the road 

Another potential improvement could be to express the x and y value of each region masking vertex as percentage of the img.shape values so that the frame size would matter less (in the sense that choosing region masking vertices is no more manual for different frame sizes).
