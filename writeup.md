# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./examples/hough_lines_out.jpg
---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. 
1. Grayscale conversion : Converts the image to grayscale so it can be fed into the line detection function.

2. Blurring + Canny Edge detection : Uses the OpenCV GaussianBlur function to filter out spurious edges from the grayscale image. This is then fed to the Canny edge detection function to generate an image with object/lane edges. The Canny edge detection algorithm selects pixels above *high_threshold* as strong edges, and pixels between a *low_threshold* and *high_threshold* are selected only if connected to strong edges. This stage uses a low:high threshold of 60:140 for images.

3. Region of Interest masking : The camera is positioned such that the lanes will always be in the lower part of the frame. So this stage uses a trapezoidal region of interest to mask out the areas where lane edges are not expected to be found.

4. Hough lines : This stage identifies lane edges using a transformation to Hough space to identify multiple edge pixels in a straight line. The output of OpenCV HoughLinesP function is an array of line segments. In order to accurately determine and extend these to length of the lane edges as seen in an image frame, this calls a function *draw_lines* on the output of HoughLines.

*draw_lines* : The purpose of this function is to determine the left and right lane edges, and extrapolate the lane markers to the top and bottom of the lane edges as seen in the image frame. This is done by iterating over line segments from the HoughLines output, and separating them into 2 sets depending on their slope - a positive slope indicates it is a part of the right edge and a negative slope means it is part of the left edge. This stage computes the average slope and the average (x,y) co-ordinates of the right and left edge line segments. The lane edges are extrapolated to the top of region of interest and lower edge of image using the slope and average (x,y). 

![alt text][image2]

In some test images/video frames, stray edges were left over from the previous steps which were not part of either of the left or right lane lines. To filter these stray edges out, setting a threshold of 0.2  for the slopes was found to be effective. 

5. Final weighted image : The final step of the pipeline is to superimpose the line edges onto the original image. 


### 2. Identify potential shortcomings with your current pipeline

1. One shortcoming with my current pipeline is that I have hard-coded the region of interest. While it works for the presented cases, it may break if the camera angle or road curvature is changed.

2. Stray edges left over from the Canny edge detection may interfere with accurate lane detection. Currently, these are filtered out using a fixed slope threshold which may not work for all cases.


### 3. Suggest possible improvements to your pipeline

1. The pipeline needs to be made smart enough to work in various situations (eg. low light/shadowy conditions, winding roads). Machine learning can be used to classify these kinds of images into different categories and the code parameters can be tuned for these.

2. The draw_lines function can be improved to further filter out stray edges. One way is to give a tighter bound on the slopes.

3. The code in the pipeline can be optimized to run in shorter time.  
