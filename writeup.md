## Vehicle Detection Project (Project 5)
### This is the final project of term1 for Udacity Self Driving Car Nanodegree Program

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
[image1]: ./output_images/car_notcar.png
[image2]: ./output_images/HOG_car.png
[image3]: ./output_images/HOG_noncar.png
[image4]: ./output_images/YCrCb_car.png
[image5]: ./output_images/YCrCb_noncar.png
[image6]: ./output_images/findcars1.png
[image7]: ./output_images/heatmap_gray.png
[image8]: ./output_images/heatmap.png
[image9]: ./output_images/findcars2.png
[image10]: ./output_images/multiwindow1.png
[image11]: ./output_images/multiwindow2.png
[image12]: ./output_images/multiwindow3.png
[image13]: ./output_images/final1.png
[image14]: ./output_images/final2.png


[video1]: ./project_video.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Histogram of Oriented Gradients (HOG)

#### 1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the  code cell 1, 2 and 3 of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I randomly selected one car image and one non-car image to compare their HOG features. The parameters of the HOG feature I am using is orient = 9 pix_per_cell = 8 cell_per_block = 2. The images are shown below.

![alt text][image2]
![alt text][image3]


#### 2. Color Space

I tried various combinations of parameters and finally decided to use YCrCb transformation because I can use it get better results. The YCrCb images are shown below.

![alt text][image4]
![alt text][image5]



#### 3. Parameter Choosing

**HOG Features**: all the 3 channels for extracting the HOG features.

**Binned Color Features**: spatial_size = (32, 32)

**Color Histogram Features**: color_space = 'YCrCb'

The parameter is I am using is as below:

color_space = 'YCrCb' 

spatial_size = (32, 32)

hist_bins = 32  

orient = 9  

pix_per_cell = 8  

cell_per_block = 2  

hog_channel = 'ALL' 

spatial_feat = True 

hist_feat = True  

hog_feat = True



#### 4. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I used the LinearSVC model of sklearn to train the model and finally I get around 99.16% accuracy on the test set

### Sliding Window Search

#### 1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to search window positions at scale = 1.5 and came up with this:

![alt text][image6]

In the above images, ystart = 400, ystop = 656.

Then I applied Heat Map to remove Multiple Detections & False Positives. Images are shown below:

![alt text][image7]
![alt text][image8]

The image results above are actually not bad. At first I tried directly using the scale = 1.5 and heatmap with threshold = 1 to build the pipeline for the video. The output video has some false positives and the window is not stable.

![alt text][image9]

To solve these issues, I tried different combination of the windows according to the location and size of the car in each frame of the video. After trail and error, finally I decided to use 6 scale sets. see below:

**ystart_ystop_scale = [(350, 480, 1), (350, 480, 1.3), (400, 600, 1.5),(400, 600, 1.7),(400, 600, 2),(500, 700, 2.5)]**

![alt text][image10]
![alt text][image11]
![alt text][image12]

When applying multiscale search, it will be more accurate detecting cars. A smooth pipeline is also added to reduce the false positive and make the output stable.

#### 2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I used 6 windows with different scales and use YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image13]
![alt text][image14]

---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)

Here's a [link to my test video result](./test_video_output_final.mp4)

For the project video, I used the processed lane finding video from Project 4 so that I can combine the lane finding and vehicle detection together.

Here's a [link to my project video result](./project_video_final.mp4)




---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I took a lot of time finding the proper window scale and ystart, ystop, xstart, xstop values. Finaly I conbined 6 different window search to get a relatively good result.
