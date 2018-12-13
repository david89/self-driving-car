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

### Detecting yellow and white pixels

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

### Canny edge detection

[Canny edge detection](https://en.wikipedia.org/wiki/Canny_edge_detector) will allow us to find interesting edges in our image. However, in order to run the canny edge detection algorithm we first need to convert the image into gray scale, so we can compute the gradient easily. The second thing we need to do, is to apply a Gassian blur on the image in order to smooth edges.

This is what we get after running the Canny edge detection algorithm.

[canny_image]: ./canny_image.png "Canny image"
![alt text][canny_image]

### 2. Identify potential shortcomings with your current pipeline

One potential shortcoming would be what would happen when ... 

Another shortcoming could be ...

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to ...

Another potential improvement could be to ...
