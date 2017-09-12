# **Finding Lane Lines on the Road** 
---

## Reflection

### 1. Pipeline Construction

The `pipeline` function is what I used to recognize and mark lane lines on an image. There are five consecutive steps in the pipeline. First, the image of interest is converted into grayscale. This step is straightforward. Second, I apply Gaussian smoothing to blur the image to reduce noise. An input for this step is the kernel size one applies, I use a square box of 5. Third, I apply Canny Edge detection to find edges in the smoothed image. This step requires the low and high threshold of the gradient value as input. After some tuning, I find 50 as low threshold and 150 as high threshold to be a good combination. Forth, I apply a polygon with 4 vertices to select out the region of the image that contains the road. I choose the 4 vertices so that it forms a trapezoid with its bottom lying at the bottom of the image and it's top around the half-height of the image where the road usually ends. Lastly, I identify the two lines on the image by the Hough Transform and subsequent averaging and extrapolation. Parameters for Hough Transform used in my pipeline are: minimum line length of 20, maximum gap to merge lines of 20 and the minimum crosses in each element box to determine a line of 20.

I want to expand on the last step above (which is done in the function `draw_lines()') and elaborate on implementation details because it has large impact on final result. Recall our final goal is to find two lane lines, one on the left and the other on the right, that bounds the region on the road where the vehicle could go. What Hough Tranform produces are many line segments riding on the edge between bright and dark blocks. I first group these scattered segments by their slope: those have slopes larger than 0.1 belong to left-line group, those have slopes smaller than -0.1 belong to right-line group. (I am using a threshold whose absolute value is greater than 0. to filter out noise lines that can't be part of the lane lines.) Within each group, I find the slope, center and length of each segment and average the slopes and centers of all the segments weighted by the length of corresponding segment to get the averaged slope and center. Finally, I extrapolate each line vertically to as low as bottom of the image and as high as the top of my chosen region of interest.

### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
