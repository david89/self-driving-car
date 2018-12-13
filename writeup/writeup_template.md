# **Finding Lane Lines on the Road** 

---

## Objective

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

---

## Background

### Color spaces

Before getting into our pipeline for detecting lanes in images and videos, let's quickly go over some important background that is going to help us understand the first two steps of the pipeline.

#### [RGB](https://en.wikipedia.org/wiki/RGB_color_model)

This is the color space we usually read images in.

#### [HSV](https://en.wikipedia.org/wiki/HSL_and_HSV)

Is important to notice that during the RGB to HSV conversion process, some scaling happens ([more information](https://docs.opencv.org/2.4/modules/imgproc/doc/miscellaneous_transformations.html?#cvtcolor)). Therefore, the range of values we operate on are:
* H ∈ [0, 180]
* S ∈ [0, 255]
* V ∈ [0, 255]

If you are curious how to transform RGB values to HSV, we used [this color picker](https://alloyui.com/examples/color-picker/hsv) to find the corresponding HSV values for some colors like white or yellow.

#### [HSL](https://en.wikipedia.org/wiki/HSL_and_HSV)

Similarly to HSV, some scaling happens in the RGB to HSL conversion process. But another important detail is that opencv switches the order of saturation and lightness; so we are actually dealing with a HLS color space.

The range of values we operate on are:
* H ∈ [0, 180]
* L ∈ [0, 255]
* S ∈ [0, 255]

Additionally, we used [this color picker](https://www.w3schools.com/colors/colors_hsl.asp) to find the HSL values for some colors like white or yellow.

## Pipeline

In this particular project we are assuming that the colors of the lanes can either be white or yellow. So it's important to understand how can we detect those two colors.

### 1. Detecting yellow and white pixels

We can operate on several color spaces in order to identify yellow pixels. After running some experiments, we found out that converting an image to **HSV** makes it really easy to identify yellow pixels and tune the required parameters.

[yellow_detection]: ./yellow_detection.png "Yellow detection experiment"
![alt text][yellow_detection]

For white pixels we think the **HSL** color space makes it really easy to identify them, as the main parameter we care about is L (lightness).

[white_detection]: ./white_detection.png "White detection experiment"
![alt text][white_detection]

After detecting white and yellow pixels, we need to merge those two masks together.

[white_and_yellow_detection]: ./white_and_yellow_detection.png "White and yellow detection experiment"
![alt text][white_and_yellow_detection]

Now you may be wondering why it's useful to identify yellow pixels or white pixels in image for our pipeline. Without these two steps, this is what we get after running our pipeline:

[bad_canny_example]: ./bad_canny_example.png "Bad Canny example"
![alt text][bad_canny_example]

As you can see the result is very noisy. That's why we want to focus on the pixels that really matter.

### 2. Canny edge detection

[Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector) will allow us to find interesting edges in our image. However, in order to run the canny edge detection algorithm we first need to convert the image into gray scale, so we can compute the gradient easily. The second thing we need to do, is to apply a Gassian blur on the image in order to smooth edges.

This is what we get after running the Canny edge detection algorithm.

[canny_image]: ./canny_image.png "Canny image"
![alt text][canny_image]

### 3. Area of interest

The Canny edge detection algorithm has successfully identified the edges we are interested at and some others (like some vehicles). Now, the next step is to remove the edges we are not interested at by selecting a region of interest. In our case, we decided to use something like an isosceles trapezoid.

[region_of_interest]: ./region_of_interest.png "Region of interest image"
![alt text][region_of_interest]

### 4. Hough lines algorithm

The [Hough lines algorithm](https://docs.opencv.org/2.4/doc/tutorials/imgproc/imgtrans/hough_lines/hough_lines.html) is used to identify straight lines in an image. Therefore, this algorithm is suitable for getting the lines that identify the lanes in a canny image.

[hough_image]: ./hough_image.png "Hough image"
![alt text][hough_image]

### 5. Merging Hough lines

As you may have noticed, our transformed image contains multiple Hough lines. For the purpose of this project, we need to draw exactly one line for the left lane and exactly one line for the right line.

Before getting into merging Hough lines, let's separate lines into two sets: one for the lines that belong to the left line, and some other lines for the right line. An easy way of accomplishing that is to separate lines by their slope: lines with a positive slope belong to the right line, and lines with a negative slope belong to the left side (that sounds counterintuitive, but remember that the top side corresponds to y = 0, and the bottom side is y = maximum y's coordinate). Additionally, we are going to exclude some lines if they have an infinite slope (vertical lines), if they have a very small slope (horizontal lines), or if they are noisy (by looking at where in the region of interest, the line is located at).

After separating lines, we can run a linear regression model on each set of lines in order to find a representative line for each set. That's the only line we are going to draw (one for each side).

Finally, we need to extend the segment from the bottom of the image all the way to the top of the region of interest.

[processed_image]: ./processed_image.png "Processed image"
![alt text][processed_image]

## Identify potential shortcomings with your current pipeline

There are several issues with our current implementation:
* We are assuming that lanes are going to be yellow or white. However, that may not be always the case.
* If the angle of the camera changes, we would have to revisit the parameters we set up in our pipeline.
* The code for this project is totally untested under low lightning circumstances. It's possible that we have to tune some parameters for dark images.

## Suggest possible improvements to your pipeline

One big improvement we could make to the video processing pipeline is to consider previous frames while drawing the line in a certain frame. That way we could discard some noisy lines with slopes that are very different from what we have seen so far.

Another big improvement to the pipeline would be trying to identify the angle of the steering wheel.
