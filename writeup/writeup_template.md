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

### Detecting yellow pixels

We can operate on different color spaces in order to identify yellow pixels. After running some experiments, we found out that converting an image to HSV makes it really easy to identify yellow pixels and tune the required parameters.

[yellow_detection]: ./yellow_detection.png "Yellow detection experiment"
![alt text][yellow_detection]

### Detecting white pixels

Similarly to our previous point, we need to identify white pixels. In this case, we think the HSL color space makes it really easy to identify white colors, as the main parameter we care about is L (lightness).

[white_detection]: ./white_detection.png "White detection experiment"
![alt text][white_detection]

Now you may be wondering why it's useful to identify yellow pixels or white pixels in image for our pipeline. Without these two steps, this is what we get after running Canny edge detection algorithm:

[bad_canny_example]: ./bad_canny_example.png "Bad Canny example"
![alt text][bad_canny_example]

As you can see the result is very noisy. That's why we want to focus on the pixels that really matter.

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
