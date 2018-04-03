# **Finding Lane Lines on the Road** 

## Writeup Template

### You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps:
1. I converted the images to grayscale
2. Gaussian Blur
3. Canny edge detection
4. Crop the image relative to size of the picture
5. Hough transform to identify line segments
6. Draw lines to draw a single, solid line over the left lane line and a single, solid line over the right lane line

In order to draw a single line on the left and right lanes, I modified the draw_lines() function by ...

If you'd like to include images to show how the pipeline works, here is how to include an image: 

![alt text][image1]


### 2. Identify potential shortcomings with your current pipeline


One potential shortcoming would be what would happen when the car took a drastic u-turn.
When the elevation changed
In a construction zone where the lines are not there.
When the camera view changes, perhaps like in the challenge video, you end up with a solution

This is not a very generalized solution. It solves a particular problem.

Another shortcoming could be ...


### 3. Suggest possible improvements to your pipeline

A possible improvement would be to take in to account the location of other cars
Fuse in data from other cameras to determine that this is the whole view
Train a model to understand lines

Another potential improvement could be to ...
