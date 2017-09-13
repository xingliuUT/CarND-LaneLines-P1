# **Finding Lane Lines on the Road** 

---
[//]: # (Image References)

[rawImg]: ./test_images/solidYellowCurve2.jpg "raw"

[grayImg]: ./test_images_output/gray_solidYellowCurve2.jpg "gray"

[gaussImg]: ./test_images_output/gauss_solidYellowCurve2.jpg "gauss"

[wcannyImg]: ./test_images_output/wcanny_solidYellowCurve2.jpg "wcanny"

[maskedImg]: ./test_images_output/masked_solidYellowCurve2.jpg "masked"

[houghImg]: ./test_images_output/hough_solidYellowCurve2.jpg "hough"

---

## Reflection

### 1. Pipeline Construction

The `pipeline` function is what I used to recognize and mark lane lines on an image. 

![alt text][rawImg]

There are five consecutive steps in the pipeline. 

First, the image of interest is converted into grayscale. This step is straightforward. 

![alt text][grayImg]

Second, I apply Gaussian smoothing to blur the image to reduce noise. An input for this step is the kernel size one applies, I use a square box of 5. 

![alt text][gaussImg]

Third, I apply Canny Edge detection to find edges in the smoothed image. This step requires the low and high threshold of the gradient value as input. After some tuning, I find 50 as low threshold and 150 as high threshold to be a good combination. 

![alt text][wcannyImg]

Forth, I apply a polygon with 4 vertices to select out the region of the image that contains the road. I choose the 4 vertices so that it forms a trapezoid with its bottom lying at the bottom of the image and it's top around the half-height of the image where the road usually ends. 

![alt text][maskedImg]

Lastly, I identify the two lines on the image by the Hough Transform and subsequent averaging and extrapolation. Parameters for Hough Transform used in my pipeline are: minimum line length of 20, maximum gap to merge lines of 20 and the minimum crosses in each element box to determine a line of 20.

![alt text][HoughImg]

I want to expand on the last step above (which is done in the function `draw_lines()') and elaborate on implementation details because it has large impact on final result. Recall our final goal is to find two lane lines, one on the left and the other on the right, that bounds the region on the road where the vehicle could go. What Hough Tranform produces are many line segments riding on the edge between bright and dark blocks. I first group these scattered segments by their slope: those have slopes larger than 0.1 belong to left-line group, those have slopes smaller than -0.1 belong to right-line group. (I am using a threshold whose absolute value is greater than 0. to filter out noise lines that can't be part of the lane lines.) Within each group, I find the slope, center and length of each segment and average the slopes and centers of all the segments weighted by the length of corresponding segment to get the averaged slope and center. Finally, I extrapolate each line vertically to as low as bottom of the image and as high as the top of my chosen region of interest.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be when the brightness contrast between the road and the lines are below the low threshold or above the high threshold I set fixed in applying Canny Edge detection. This scenario is real because there are road conditions when the sunlight is extra bright and suddenly there's dark shadow from a tree or building in the surrounding. 

Another shortcoming could be when the road in sight is not a straight line. This is a problem because in the pipeline I have assumed that the lane lines are straight and averaged over the positive and negative slopes of lines identified by the Hough Transform to boil them down to two lines. However, in a real life situation, there could be sharp turns, for example, ramps up and down a high way. There could also be cases where the road is under maintenance so the lane is temporarily signaled by traffic cones and has significant curves.


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to adjust the thresholds in Canny edge detection step so that they float with respect to the absolute brightness in the neighborhood pixels: when the neighborhood is too bright or too dark, decrease the low and high thresholds.

Another potential improvement could be to use nonlinear ways to trace the lane lines other than straight-line extrapolation. 
