# **Finding Lane Lines on the Road** 

## Writeup Template

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./examples/grayscale.jpg "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I .... 

My pipeline has 6 stemps. First, I got grayscale image using cvtColor() of open cv.
Second, I called GaussianBlur() in order to eliminate outliers.
Third, I got all edges using  Canny(). It executes Canny edge detection algorithm.
Forth, some area was selected by fillPoly(). The shape is like U. I tried to select around lane lines.
Fifth, I took some linear lines from the output of canny detection algorithm using HoughLInesP().
Finally, I drew red lines on the images. It took so long to code drawing routine.

I'll try to explain draw_line() in details.
it's simple when the input is normal image.I just drew all lines.
However, when dealing with videos, time must be considered.
In order to work with time, I defined the numpy array "gRed" for remembering lines. 
the size of gRed is the same as the one channel of image. 
gRed keeps the value of how often the pixcel is considered to draw. 
when the pixcel is lane line,the value is increased. when the pixcel is not lane line, it is decreased.
When I draw, I first do it on spare space.  After that, I used gRed to decide if drawing must be done or not.
Most of the operation was done using numpy array because it was much faster than multiple for-loop.
Essential code is following.

    #I intended to remain lines for a short time.
    select4plus = (temp == 255) & (gRed < 30)
    
    #if red pixel is found, it's worth making it longer. because lane lines don't change all of a sudden.
    gRed[select4plus] += 3
    
    #if it's not a red pixel, decrease slowly...
    gRed[ ~select4plus & (0 < gRed) ] -= 1

    #filter gRed that has more than 3
    sel =  gRed > 2
    img[sel] = (255,0,0)

 


[image1]: ./test_images_output/solidWhiteCurve.jpg            "solidWhiteCurve"
[image1]: ./test_images_output/whiteCarLaneSwitch.jpg         "whiteCarLaneSwitch"
[image1]: ./test_images_output/solidWhiteRight.jpg            "solidWhiteRight"
[image1]: ./test_images_output/solidYellowCurve.jpg           "solidYellowCurve"
[image1]: ./test_images_output/solidYellowCurve2.jpg          "solidYellowCurve2"
[image1]: ./test_images_output/solidYellowLeft.jpg            "solidYellowLeft"
[image1]: ./test_images_output/whiteCarLaneSwitch.jpg         "whiteCarLaneSwitch"   


### 2. Identify potential shortcomings with your current pipeline


when the lane line is changed so fast, drawing is not perfect. 


### 3. Suggest possible improvements to your pipeline
The way I've chosen to get rid of outlier is is so weak in terms of long period of unexpected bright pixcels like sunlight.
A possible improvement would be to draw the red line using average value of lines. 

Another potential improvement could be to use trapezoid in order to filter the image. U shape I used is hiding some area when crossing the lane line accidently.
