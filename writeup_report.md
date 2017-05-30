**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Normalize the features and randomize a selection for training and testing.
* Implement a sliding-window technique and use the trained classifier to search for vehicles in images.
* Run the pipeline on a video stream project_video.mp4 and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.

[//]: # (Image References)
[image1]: ./output_images/example1.png
[image2]: ./output_images/example2.png
[image3]: ./output_images/example3.png
[image4]: ./output_images/example4.png
[video1]: ./out.mp4

## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

The code for this step is contained in the first code cell of the IPython notebook.  

I started by reading in all the `vehicle` and `non-vehicle` images. Then I shuffled the image data for cars and non-cars. A function "get_hog_features" is defined to return HOG features and visualization. 
The HOG features are extacted by using `skimage.feature.hog()` with the `YCrCb` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`.

Here is an example of one of each of the `vehicle` and `non-vehicle` classes with the HOG visualizaton of the image:

![alt text][image1]


####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using color histogram features, spatial color features and Histogram of Oriented Gradient (HOG) features, implemented in the second code cell.  Test size is set to 0.2 which means that 20% of the dataset becomes the test set.

---

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I use Hog Sub-sampling Window Search as the sliding window approach that allows us to only have to extract the Hog features once. The third code cell in Jupyter notebook defines a single function find_cars that's able to both extract features (including color histogram features, spatial color features and HOG features )and make predictions. Then the bounding boxes are found for all the positive predictions. 

I choose cells_per_step = 2 which results in a search window overlap of 75%. I choose two different scales after comparing the vehicle detection results of test images based on different scales. 

Below shows the bouding boxes drawn on the same test image with scale 1.3 and 1.8.

![alt text][image2]


####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

After finding the positive detections and the related bounding boxes, in the fourth code cell in Jupyter notebok, I created a heatmap and thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap. I constructed bounding boxes to cover the area of each blob detected. 

To optimize the performance of the classifier, I randomize the data by calling random.shuffle() before passing to the classifier. I searched on scales of using YCrCb 3-channel HOG features plus spatially binned color and histograms of color in the feature vector. 

Below are the final bounding boxes and the heat map for the same example:
![alt text][image3]

---

### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a [link to my video result](./out.mp4)


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

In the sixth code cell in Jupyter notebok, I recorded the positions of positive detections in each frame of the video. For each six consecutive frames, I collected all the bounding boxes for these six frames and then integrated a heat map for all six frames. Then I applied a threadhold to remove false positives. The final bounding boxes are down onto the last frame of these six frames. 


---

###Discussion

#### Discussion includes some consideration of problems/issues faced, what could be improved about their algorithm/pipeline, and what hypothetical cases would cause their pipeline to fail.

There are some frames where the bounding boxes are drawn onto non-vehicles such as the following example.
![alt text][image4]

The following steps might be helpful to improve the pipeline:

* Combine with varying window sizes such as a smaller window size to detect cars further to the camera.
* Try different combinations of HOG parameters to achieve a higher accuracy on the classifier.
* Apply data augmentation with different test sets to train the classifier to identify non-veihicles such as trees or shadowing area.