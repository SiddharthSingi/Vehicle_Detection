# Vehicle_Detection

##Writeup Template
###You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./examples/HOG_example.jpg
[image3]: ./examples/sliding_windows.jpg
[image4]: ./examples/sliding_window.jpg
[image5]: ./examples/bboxes_and_heat.png
[image6]: ./examples/labels_map.png
[image7]: ./examples/output_bboxes.png
[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the second code cell of the IPython notebook Vehicle_Detection.ipynb

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![carhog](https://user-images.githubusercontent.com/26694585/27286431-bd324bbe-551d-11e7-9fbd-e266148b43cd.jpg)

![notcarhog](https://user-images.githubusercontent.com/26694585/27286435-bfb9fbfc-551d-11e7-858e-6aebe1d7939d.jpg)


I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like.

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![ycarhog](https://user-images.githubusercontent.com/26694585/27286455-d4ab255e-551d-11e7-88f3-3fedd0160f78.jpg)

#### 2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters. I tested with diffeerent color spaces like HLS, RGB, YUV, YCrCb and YCrCb worked the best for me. Other parameters I tried were orient=9, and pix_per_cell=16, but both did not help me so I used the default values.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using the following parameters in the 4th code cell of my IPython Shell:

* color_space = 'YCrCb'
* spatial_size = (16, 16)
* hist_bins = 32
* orient = 8
* pix_per_cell = 8
* cell_per_block = 2
* hog_channel = "ALL"
* spatial_feat=True
* hist_feat=True
* hog_feat=True

First, I created a list of all filenames of all car and notcar images. Then using the extract features function(defined in the 2nd code cell) for each of these images I formed their feature vectors consisting of spatial features, histogram features and hog features. These feature_vectors were then fitted to a LinearSVC() classifier and trained to get an test accuracy of 99.4%


### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

The sliding_window search was implemented in the pipeline() function defined in the 8th code cell.
I tried to change the parameters of the size of the windows, To reduce excessive windows, I used smaller windows near the horizon and larger windows closer to the car, The final scales to search were: (64, 64) sized windows between y_start_stop = [400, 530], and (128, 128) sized windows between y_start_stop = [400, 680]

![sliding_windows](https://user-images.githubusercontent.com/26694585/27286486-f37b574c-551d-11e7-94a3-c91c6872625c.jpg)


#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?
 
Ultimately I searched on three scales using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![test3](https://user-images.githubusercontent.com/26694585/27286518-0ddd108a-551e-11e7-9a86-5376473927ca.jpg)

![test4](https://user-images.githubusercontent.com/26694585/27286522-0fd50474-551e-11e7-845d-5f2f4fac1441.jpg)

![test5](https://user-images.githubusercontent.com/26694585/27286523-11d7c63a-551e-11e7-8bb0-33f6e82d640d.jpg)

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a link: [https://youtu.be/Kr-2pmElJCI](link)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

## Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

![heatmap](https://user-images.githubusercontent.com/26694585/27286593-5ed53e54-551e-11e7-8ce6-250ba87635c8.jpg)



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Before trying sliding window search I tried to use the hog sub-sampling method to identify the car windows, but this function identified many continous false positives next to each other either on the lane markings or the opposite side of the road. This made it difficult for me to use an appropriate threshold for heatmaps to avoid the false positives and hence I had to switch to the sliding window method.

To make the pipeline more robust an algorithm must be employed that uses the fact that a heatmap point or a vehicle cannot be found in the middle of the road all of a sudden. Also if a vehicle is detected then it must either increase in size if moving ahead, or reduce in size if moving closer to the bottom edge of the screen. These methods can give the final touches to this project and minimize any false positives that are being generated.
