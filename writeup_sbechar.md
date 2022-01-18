# **ECE 495 A1** 

## sbechar

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./test_images_output/grey.jpg "Grayscale"
[image2]: ./test_images_output/gauss.jpg "Gaussian Blur"
[image3]: ./test_images_output/canny.jpg "Cannny Edge Detection"
[image4]: ./test_images_output/roi.jpg "Region of Interest"
[image5]: ./test_images_output/hough.jpg "Hough Line Detection"
[image6]: ./test_images_output/ransac.jpg "RANSAC Fitting"
[image7]: ./test_images_output/finished.jpg "End Result"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 8 steps. 

First, I converted the images to grayscale, I forgot about the pipeline suggested in the A1_intro-notes document so I created my pipeline based on what we covered in the lectures. I also got good results from the canny edge detector from only using a greyscale image with no colour thresholding, but perhaps they could be better if I were to implement it on a version 2.0. 
![][image1]

Next, I applied smoothed the image using Gaussian Blur with a 5x5 kernal to remove sources of noise from the image. I tried with larger kernals but I found the smoothing was too intense and could not properly identify edges.
![][image2]

My third step, is to apply the Canny Edge Detection algorithm. I tried with multiple different thresholding and found that when I try and move the thresholding higher, only the more prominent edges get detected. This is good, however, upon trying different images, I found I had to use a much lower threshold to detect the various types and shapes of lines. Perhaps here, I could have better results with the colour thresholding.
![][image3]

After, I cropped the output of the edge detection to only the region concerned with the road. I found this helped the next step of Hough Line detection incredibly. Luckily, all of the images had the camera in a similar position on the vehicle so the lines would all appear in relatively the same position. The trapazoid I cropped to was much larger than the one provided in the slides. The top was just under half the screen and was about 100 pixels wide, to give a little room for where the lines converge. At the bottom, the region extended to the bottom corners of the screen.
![][image4]

My fifth step, is to apply the Hough Line detection algorithm. After a tremendous amount of trial and error, I converged on rho=1, theta=pi/180, threshold=5, min_line_len=5, max_line_gap=45. Having chosen the correct size buckets, the threshold and other values became less impactful and were chosen mostly based on intuitive reasoning.
![][image5]

My sixth step was to categorize the line segments generated in the previous step into lines with a positive slope and lines with a negative slope. I found a lot of lines were small, close together and had almost a vertical slope. These lines significantly affected the next step of line fitting, so I decided to filter these lines out to greater increase my probability of success.

Next, I took all the starting and ending points of the line segments in each bucket and applied the RANDSAC algorithm. I wanted to use a high min_sample rate such as 10-20, but some of my buckets only had 4 points so I had to settle lower. I think this resulted in some fitting that was eroneous in the videos. I used the slope and intercept obtained from the fit to extrapolate starting and stopping points for the averaged lines.
![][image6]

Finally, I applied the fitted lines onto the original image using the weighted function to obtain the finished result!
![][image7]


### 2. Identify potential shortcomings with your current pipeline


My pipeline preformed exceptional on all videos except for the Yellow video. Here, there were a few incorrect fittings of the line for a single frame and caused the line to jump.

Upon comparing P1_example to my video clips, my lines tend to be slightly more jittery and slightly bounce up and down between frames.

### 3. Suggest possible improvements to your pipeline

A possible improvement would be to revisit the Hough Line detection parameters to see if I can obtain more clear line segments. This would reduce the amount of incorrect categorization of lines that lead to incorrect fitting by RANDSAC. Also, by filtering the line segments less, perhaps I could obtain more data points for min_samples and achieve a better performance of the algorithm.

To fix the smoothing, perhaps I could keep a historical record of the previous frame and only allow the new lines to be so far from the lines last seen. This smoothing/averaging could lead to less jittery movement.
