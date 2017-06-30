# **Finding Lane Lines on the Road** 
---
## Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consists of the following steps:
1. Convert image to grayscale

![alt text][image1]


2. Explicitly apply some Gaussian smoothing
  * While reportedly performed internally in OpenCV's Canny function, extra smoothing was applied in lectures and it seems beneficial by reducing the detection of less well-defined edges.

![alt text][image2]


3. Detect edges using Canny algorithm
  * The threshold values are based on experimentation during the quizes to identify yellow and white lines.

![alt text][image3]


4. Isolate edges in region of interest

![alt text][image4]


5. Find line segments using Hough transform
  * The parameters are based on what worked well in lecture quizes.
  * Raw line segments are shown in green on the image for the next step.

6. Sort line segments and merge like segments into single line

![alt text][image5]


7. Superimpose identified lane lines on actual image

![alt text][image6]


In order to draw a single line on the left and right lanes, I modified the draw_lines() function by making seperate functions to do the following:
1. Use each line segment's slope to determine the lane line (left/right) of which it may be a part
2. Since some line segments may not be related to either lane, they sometimes make the averaged line not match the true lane line.
  * Horizontal and vertical lines are assumed to be invalid and removed from the pool.  Some horizontal lines were detected while processing at least one of the test videos was processed.
    ![alt text][image7]

  * Other outliers (line segments whose slopes were not close to norm) were also identified and removed.
3. Lines were merged by taking the mean slope and mean intercept before constructing a new line that extends from the bottom to a little lower than the middle of the image.


### 2. Identify potential shortcomings with your current pipeline

The challenge video is an easy place to start talking about short comings.  My current pipeline does not handle curvier roads.

Two offset parallel lane lines would end up being merged into one line that fits neither line.


### 3. Suggest possible improvements to your pipeline

One possible improvement would be to modify the region of interest based on expected camera view/angle, lane width ranges and max road curvature.

If all frames/images are known to be the same shape, creating the region of interest once and then applying to all frames may save a few repeated instructions.

When processing video, knowledge of the previous frames lane lines could help detect lane lines in the next frame.  Sometimes the detected lane lines jump or are jittery when compared to the last or next frames based on the edges that happen to be detected.  Having the last frame's lane line influence the current frame's line average would improve smoothness.

Normalization:
* Current outlier detection does not take into consideration line lengths or intercepts.  A few bad short lines could overpower one long good line.  Similarly, if intercepts are disperate, they obviously are not the same line and should be removed from the pool of line segments to later merge.
* The line length should also be used when merging lines into one.

Generate a line for each side of lane line (two for left and two for right) and fill between them to have a prettier display (instead of line down middle with uniform thickness).  This could also alleviate sometimes picking one of the two sides of a lane line and discarding the other side as outliers because the slope is different enough.


[//]: # "Image References"

[image1]: ./examples/whiteCarLaneSwitch_1gray.jpg "Grayscale"
[image2]: ./examples/whiteCarLaneSwitch_2blurgray.jpg "Blurred Grayscale"
[image3]: ./examples/whiteCarLaneSwitch_3edges.jpg "Detected edges"
[image4]: ./examples/whiteCarLaneSwitch_5maskededges.jpg "Masked edges"
[image5]: ./examples/whiteCarLaneSwitch_6lines.jpg "Lines"
[image6]: ./examples/whiteCarLaneSwitch_7output.jpg "Output"
[image7]: ./examples/horizontal_artifact.jpg "Bad Horizontal Line Example"