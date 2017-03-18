**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/hog_vehicle.png
[image2]: ./output_images/hog_non_vehicle.png
[image3]: ./output_images/grid_example.png
[image4]: ./output_images/feature.png
[image5]: ./output_images/sample_heat.png

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

####1. Provide a Writeup / README that includes all the rubric points and how you addressed each one.  You can submit your writeup as markdown or pdf.  [Here](https://github.com/udacity/CarND-Vehicle-Detection/blob/master/writeup_template.md) is a template writeup for this project you can use as a guide and a starting point.  

You're reading it!

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is in the 3rd cell of the IPython notebook. Here, I have extracted the hog features using the `skimage.feature.hog()` function, as mentioned in the video lectures.

I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![HOG Image of Vehicle][image1]

![HOG Image of Non Vehicle][image2]

####2. Explain how you settled on your final choice of HOG parameters.

I then explored some color spaces and the best result was obtained from `YUV` color space. I tried different parameters of `hog`, and picked the one which had the most distinguishable features.

![features][image4]

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM in cell 8. The code uses `sklearn.svm` for the SVM classifier.
The features used for each image is `hog`, `bin_spatial` and `color_histogram`. Standard features have been used as those provided the best features.

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

In cell 10, the function `process_image` contains the crux of the sliding window search. I have used boxes of three sizes [60, 60], [96, 96], [128, 96]
Here I have made use of the fact that out grid search needs to be extensive (more overlap) for larger size boxes as compared to smaller images. The search range of small and large grid sizes also varies. If the car is far away, it will be somewhere in between half and 3 quarter of the y axis.

![An example of the 60x60 grid search region][image3]

####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

After trying various combination of color space, YUV worked best for me. Along with HOG, I have used spatially binned and histogram features as mentioned in the lecuture. Without these the results were not up to the mark.

![Sample images from pipeline][image4]
---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](https://youtu.be/D1nMaepGO2A)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I calculated the position of each vehicle as detected. The heatmap technique is used to filter out false positives. I have also added kind of memoization to average out the result of previous and current frame.
Along with this, the sliding window have been localised to specific region in the frame. As the search space for cars close to camera and cars far away from camera are in different locations of a frame. The overlap ratio is also as per the window size.

Another great technique to remove false positives was by adding a constraint on the width and height of the bounding box. The 

I then used `label()` to identify individual blobs in the heatmap. I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a series of frames of video, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the last frame of video:

### Here are six frames and their corresponding heatmaps:

![Sample test images with bounding box and heat map][image5]


---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I think the pipeline is decent as such. However, some of the technique involved could be largely improved like using SSD appriach instead of a simple Linear Classifier. Given the amount of data available it should not be very difficult to get better result on deep learning approach.

Another aspect that can be largely improved is averaging out the results from previous frame(s) onto the newer frame. Constraints like the centroid of the box and dimension of the box should be similar. In the current solution, I have used the previous frame but previous n frames can be used for that purpose.

