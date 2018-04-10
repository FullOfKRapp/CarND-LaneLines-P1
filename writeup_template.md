# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images/solidWhiteCurve.jpg "Picture before any processing"
[image2]: ./test_images/grayscale_solidWhiteCurve.jpg "Grayscale"
[image3]: ./test_images/blur_solidWhiteCurve.jpg "Gaussian Blur (note how the features are slightly less distinctive than the previous picture"
[image4]: ./test_images/canny_edges_solidWhiteCurve.jpg "Canny Edge Detection"
[image5]: ./test_images/cropped_solidWhiteCurve.jpg "Crop it like its hot"
[image6]: ./test_images/hough_solidWhiteCurve.jpg "Hough Transform"
[image7]: ./test_images/output_solidWhiteCurve.jpg "Final Image with Lanes Drawn"

---

### Reflection

### 1. Pipeline Description

#### Overview

My pipeline consists of:
1. Convert the RGB images to grayscale
2. Apply Gaussian Blur to smooth the image and "remove the noise"
3. Perform Canny edge detection to detect the edges of my image (lanes are edges)
4. Crop the image to show only the relevant area we want to look at (ie: where the lanes are in relation to a picture)
5. Hough transform to find lanes within our region of interest 
6. Draw lines to draw a single, solid line over the left lane line and a single, solid line over the right lane line
7. Merge lines on to original photo

#### Slide Show

Lets take you through my fun little slide show to show you a little bit more about what is meant by all of those steps.

Here we are with our original image of a solid white curve. 

![alt text][image1]

As you can tell, this is a VERY exciting picture. Why? Its not. I was being sarcastic. But now prepare to be excited...

#### Grayscale

We start out by converting the picture in to Grayscale so that we can isolate one channel and more clearly point out the edges on the image. If we are only dealing with one channel, we can just worry about the change in pixel integrity in that one channel. This makes our job much easier than if we were dealing with all 3 RGB channels.

![alt text][image2]

#### Gaussian Blur

The Canny Edge detection algorithm is sensitive to noise. What do I mean by "noise" in this instance? Think of "noise" as "false edges." So we do not want our algorithm to capture these "false edges", we only want "real edges." How can we eliminate noise? We can blur the image. 

If you look at an image, there can be thousands of tiny edges when you process the image with your eye ("false edges"), but if you blur it together (envision going from 20/20 vision to 20/50 vision), you will actually end up only noticing the more drastic edges (real edges). We do this by utilizing the Gaussian blur technique (https://en.wikipedia.org/wiki/Gaussian_blur).

This method blurs the image so that we only capture edges we care about and its output looks something like this: 

![alt text][image3]

#### Canny Edge Detection

Now we want to capture the edges of the image, which we are going to do by utilizing Canny Edge detection. What this is going to do is go across the image in a pixel by pixel fashion and look for drastic changes in pixel intensity. The line of thought here goes "if there is a large change in pixel intensity, you have found an edge." You can read more about this here (https://en.wikipedia.org/wiki/Canny_edge_detector) or how it is implemented in OpenCV here (http://opencv-python-tutroals.readthedocs.io/en/latest/py_tutorials/py_imgproc/py_canny/py_canny.html)

This produces the following image:

![alt text][image4]

#### Image Cropping

Now if we think about lane lines, there is a function of them that we can exploit. Namely, that they are only going to be in the bottom part of the frame when the camera is located like it is on our test images. So we can set up a boundary for the crop that eliminates everything else and just focuses on that field of vision.

![alt text][image5]

#### Hough Transform

Now we use Hough Line Transform to detect lines in the cropped images. The quick explanation of the Hough Transform process is that if you analyze one particular edge of a photo (as a point), we can actually represent every possible line through that edge with a sinusoidal curve in polar coordinates (preferable so that you can deal with vertical edges). If you repeat that for every edge, the point (inpolar coordinates) with the most curve crossings identifies an imaginary line in the original image (think x, y space) that passes through the most edges. The idea here is that using this alogrithm will generate a list of potential points/ line segments that could be lane lines.

Learn more here: https://en.wikipedia.org/wiki/Hough_transform

#### Draw Lines

In order to draw a single line on the left and right lanes, I modified the draw_lines() function that Udacity gave us to work with. 

##### Improving the Draw Lines function
Hough lines is generating a list of lines or points (start and end points) that it then hands off to Draw Lines to finish up. As it is given by Udacity, draw lines just takes the points Hough Lines gives it and connects them as line segments. This is not exactly a lane line, but several line segments that all lie on one of the lane lines.

We want two lane lines, so we need to first take our list of line segments and divide them up in to right and left lane lines. We do this by: 
1. Any line segment that has a positive slope belongs in the left lane
2. Any line segment that has a negative slope belongs in the right lane
3. Any line segment that has a steep vertical slope or horizontal slope can be thrown out because it is not a lane line

Therefore, we’ll seperate the right and left lane lines by:
 
If slope is between 0 and some small positive number then put the slope and center values in the left lists

If slope is between 0 and some small negative number then put the slope and center values in the right lists

Now, if you have the slope and the average of the midpoints, you can use the properties of a line to figure out the point at the end range that you want to reach. Then just connect the line and you get:

![alt text][image6]

#### Merge Lines on to original photo

Finally, we combine our original image and our newly drawn lines and we get:

![alt text][image7]

### 2. Identify potential shortcomings with your current pipeline

A few potential shortcomings are:

1. Lane curvature is an issue. On the test images, my pipeline has problems when the lane begins to curve (which violates some of the assumptions of the underlying algorithm used.) In order to account for curvature, we will have to bring in some more advanced techniques.

2. What if the behavior of the car and the cars around us were different? For instance, if there was a car directly in front of the field of vision of my cropped out area, it would register lines from the car in front of me. Will it pick up some false positive line segments from the sides of the car in the field of vision that would register as lane lines? Probably. Another example: what would happen when the car took a drastic u-turn? Or what about when the elevation of the road changed? My algorithm assumes that the lines have a certain slope to them, when they do not meet that slope, the algorithm will fail. 

3. What if our camera location was different? When the camera view changes position, the assumptions about slopes and where the lines are located on the road are no longer correct. So I would be cropping an incorrect area to find lane lines. 

Overall, this is not a very generalized solution. It solves a particular problem for a particular sensor input.


### 3. Suggest possible improvements to your pipeline

Some possible improvements would be: 

1. Take in to account the color of the lines when identifying them. If I were to continue working on this, I would convert the picture in to the HSV/HSL space and isolate everything that is either white or yellow (color of the lines in all of the videos and images). Then I would do a very similar pipeline from there.

2. Build in a way to handle the lane curvature.
