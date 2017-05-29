# **Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./examples/car_not_car.png
[image2]: ./new_examples/HOG_examples.png
[image3]: ./examples/sliding_windows.jpg
[image4]: ./new_examples/test_images.png
[image5]: ./new_examples/overlapping.png
[image7]: ./new_examples/not_overlapping.png
[video1]: ./test.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
### Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf. 

You're reading it!

### Histogram of Oriented Gradients (HOG)

#### 1 . Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the IPython notebook titled `vehicle_detection.ipynb` in the functions `get_hog_features` and `extract_features`

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![alt text][image1]

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`).  I grabbed random images from each of the two classes and displayed them to get a feel for what the `skimage.hog()` output looks like. 

Here is an example using the `YCrCb` color space and HOG parameters of `orientations=8`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:


![alt text][image2]

#### 2. Explain how you settled on your final choice of HOG parameters.

In the lesson I had played around with a number of HOG parameters as well. I settled on using the orientations, pixels_per_cell and cells_per_block parameters, because these seemed to be the most significant and influential.

#### 3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using scikit-learn's `LinearSVC`. I split the data using scikit-learn's `train_test_split` function to create training data and test data. This code is under the header in the iPython notebook titled "Using a Linear Classifier".

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I decided to, by default, start at the edge of the image and search across the entire image, but this variable could be adjusted according to where it was important to look in the image. When I actually ran the pipeline on the video, I used different start and stop values for the window search. I decided to use an overlap of 0.5, so as to not miss anything important, but not any smaller, or it would take a much longer time.

![alt text][image3]

####2 . Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

I decided to use the YCrCb color space, and searched on all HOG channels with spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are some example images:

![alt text][image4]
---

### Video Implementation

#### 1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./test.mp4)


#### 2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![alt text][image5]

### Here the resulting bounding boxes, after removing the overlapping boxes:
![alt text][image7]



---

### Discussion

#### 1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

Here I'll talk about the approach I took, what techniques I used, what worked and why, where the pipeline might fail and how I might improve it if I were going to pursue this project further. My pipeline implementation was pretty straightforward, following the ideas drawn from the lesson. However, there are some areas where my pipeline can be most obviously improved. One example is that the boxes identifying cars are rather wobbly. An improvement can be made to make this more stable. Additionally, a few false positives tend to pop up, especially for an occasional object or car in the left lane. Improved filtering can be put in place to minimize the number of false positives.

