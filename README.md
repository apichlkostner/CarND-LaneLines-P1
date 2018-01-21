# **Finding Lane Lines on the Road** 

## Purpose of this document

This document is part of a course assingment in which lane detection for autonomous vehicles should be implemented using classical approaches of computer vision.

---

Base for the lane detection are images taken from a camera on the car roof.

Example picture:
![Example image][image2]

[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"
[image2]: ./test_images/solidWhiteCurve.jpg "Solid white curve"
[image2_processed]: ./test_images_output/solidWhiteCurve.jpg "Solid white curve"
[image_detpipeline]: ./docu_images/detection_pipeline.png "Detection pipeline"


## The lane detection pipeline

The lane detection pipeline has the following steps:

### 1) Segmentation using color thresholds
Lanes on the street can have a white or a yellow color. To reduce errors in edge detection the images are first segmented using these colors.

The white parts are detected using thr RGB color space in which all three dimensions have to be higher than 200.
To detect the yellow parts the image is converted to HSV color space and segmented using the thresholds [20, 60, 60] and [35, 200, 200].

The two masks are combined with an or operation.

### 2) Edge detection
With the image from step 1 the edges are detected using the canny edge detector.

### 3) Reduce to region of interest
The image with the edges is then reduced by a mask of the region of interest.

### 4) Hough transformation
From the image with the edges detected a hough transformation is done which gives a list of lines (start point and end point)

### 5) Split set of lines for left and right lane marking

The list of lines is split in two lists for the left and the right lane side.
To achieve this first the gradients of the lines are calculated. Only lines within a plausible range are removed as outliers (for example horizontal lines from the road marking).
Then lines with positive gradients belong to the right lane and lines with negative gradients belong to the left lane.

### 6) Average and extrapolate the lines
The points from the lines are then fit with a least square estimator and extended to the region of interest.

### 7) Draw the lines
The lines of the left and right lane are then drawn to a image which is returned as result.

## Results of the pipeline with intermediate steps

In the following image six different test images are processed by the pipeline.
1) Original image
2) Segmentation by color range
3) Edge detection
4) Cut by region of interest
5) Lines by hough transformation merged with original image
5) Fit the two lines of the left and right lane marking
6) Result of the pipeline, image merged with the two lines of the lane

![image_detpipeline]

An example with a bigger image:

Original image:
![Original image][image2]

Processed by lane detection pipeline:
![Processed image][image2_processed]

## Potential shortcomings with the current pipeline
The current pipeline only works reliable in special situations when the street is not too winding and when there are no crossings.

The lane markings have be clearly visible. If the light conditions were bad the pipeline wouldn't work well.


## Possible improvements to the pipeline

To improve the pipeline the lanes should be modelled as higher order curves. Then winding roads could be approximated better and the trajectory control algorithm would have a better input.

Since the pipeline should work on a video stream the history of the lane could be used for better lane detection.



## Approches which were discarded
* Using morphological transformations on the segmented image (closing) didn't improve the result.
* Color normalization didn't improve the result. Maybe this helps when the light conditions have more variance.
* First try with approximating the lane lines only with the highest and lowest detected point was too noisy.
* Simple split of the lines from the hough transformation using the x coordinates didn't work on winding roads.

## Results on the videos
The results on the test videos are in the folder "test_videos_output"

[solidWhiteRight.mp4](test_videos_output/solidWhiteRight.mp4')

[solidYellowLeft.mp4](test_videos_output/solidYellowLeft.mp4')

[challenge.mp4](test_videos_output/challenge.mp4')
