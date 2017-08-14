
**Vehicle Detection Project**

The goals / steps of this project are the following:

* Perform a Histogram of Oriented Gradients (HOG) feature extraction on a labeled training set of images and train a classifier Linear SVM classifier
* Optionally, you can also apply a color transform and append binned color features, as well as histograms of color, to your HOG feature vector. 
* Note: for those first two steps don't forget to normalize your features and randomize a selection for training and testing.
* Implement a sliding-window technique and use your trained classifier to search for vehicles in images.
* Run your pipeline on a video stream (start with the test_video.mp4 and later implement on full project_video.mp4) and create a heat map of recurring detections frame by frame to reject outliers and follow detected vehicles.
* Estimate a bounding box for vehicles detected.


## [Rubric](https://review.udacity.com/#!/rubrics/513/view) Points
###Here I will consider the rubric points individually and describe how I addressed each point in my implementation.  

---

###Histogram of Oriented Gradients (HOG)

####1. Explain how (and identify where in your code) you extracted HOG features from the training images.

I first started by manually drawing boxes around cars for an image using co-ordinates. 
Then used templates method to search for cars in an image.
Here is an example of this:

![findcarsusingtemplates](https://user-images.githubusercontent.com/15799394/29290848-360ebbe0-815f-11e7-9d7a-a40d1a3ff35a.png)

I extracted color and spatial features on a sample image.Here is an example of othe features:

![r histogram](https://user-images.githubusercontent.com/15799394/29290862-3677f36c-815f-11e7-8839-1429ee06fdd7.png)
!![spatiallybinnedfeatures](https://user-images.githubusercontent.com/15799394/29290863-3688d966-815f-11e7-8931-d2d1e871f07b.png)


I started by reading in all the `vehicle` and `non-vehicle` images.  Here is an example of one of each of the `vehicle` and `non-vehicle` classes:

![examplecar](https://user-images.githubusercontent.com/15799394/29290845-360c6f98-815f-11e7-8e8f-ef15ca25ea45.png)

I then explored different color spaces and different `skimage.hog()` parameters (`orientations`, `pixels_per_cell`, and `cells_per_block`). 



####2. Explain how you settled on your final choice of HOG parameters.

I tried various combinations of parameters. Especially pixels_per_cell (16,16) and (8,8) and finally ended up using (8,8)

For final extraction of features (hog, spatial and color) i used several combinations of colors and endend up using 'YUV'.
Spatial_Size (16,16) and hist_bin =16 (color histograms)

Here is an example using the `YUV` color space and HOG parameters of `orientations=9`, `pixels_per_cell=(8, 8)` and `cells_per_block=(2, 2)`:

![hogvisual-car](https://user-images.githubusercontent.com/15799394/29290851-363455d0-815f-11e7-83e6-fdc0e15dd227.png)

####3. Describe how (and identify where in your code) you trained a classifier using your selected HOG features (and color features if you used them).

I trained a linear SVM using...i used 'ALL' hog channels, spatial and color features with a final feature length of 6108. It took me 15.98 seconds to train. I achieved an accuracy of 0.9924

###Sliding Window Search

####1. Describe how (and identify where in your code) you implemented a sliding window search.  How did you decide what scales to search and how much to overlap windows?

I didnt realize it in the beginning but eventually had to spend a lot of time fintuning the sizes for several test images.
I tried multiple combinations of y_start_end, x_start_end,xy_window,xy_overlap and eventually found the optimium values for each of the test images.

Here is an example of testimage 5 after applying the below parameters to sliding window() :
#Test5 configurations
x_start_stop = [700,1280]
y_start_stop = [400,550]
xy_window=(190, 128)
xy_overlap=(0.8, 0.8)

![parametertuningslidingwindows](https://user-images.githubusercontent.com/15799394/29290855-364f1758-815f-11e7-979c-69e77725193c.png)



####2. Show some examples of test images to demonstrate how your pipeline is working.  What did you do to optimize the performance of your classifier?

Ultimately I used 7 window sizes using get_multiple_window_sizes() by using narrowing down the y_start_stop,x_start_stop and other parameters.
see configurations below

    x_start_stop = [700,image.shape[1]]

    #Window sizes for test images
    size_1 = [(128,128), (0.65,0.65), (400,560)]   # img1
    size_2 = [(64,64), (0.5,0.5), (400,520)]       # img1,4
    size_3 = [(32,32),(0.65,0.65),(400,500)]       # img3
    size_4 = [(32,32),(0.5,0.5),(400,520)]         # img3
    size_5 = [(190,128),(0.7,0.7),(400,550)]       # img5
    size_6 = [(128,128),(0.8,0.8),(400,550)]       # img6
    size_7 = [(190,128),(0.8,0.8),(400,720)]       # ALL


I used YUV 3-channel HOG features plus spatially binned color and histograms of color in the feature vector, which provided a nice result.  Here are an example :

![hotwindowstestimage1](https://user-images.githubusercontent.com/15799394/29290853-36382872-815f-11e7-92a7-fa11624ab16e.png)


### Video Implementation

####1. Provide a link to your final video output.  Your pipeline should perform reasonably well on the entire project video (somewhat wobbly or unstable bounding boxes are ok as long as you are identifying the vehicles most of the time with minimal false positives.)
Here's a to my video result https://youtu.be/WfdOoMbstT0


####2. Describe how (and identify where in your code) you implemented some kind of filter for false positives and some method for combining overlapping bounding boxes.

I recorded the positions of positive detections in each frame of the video.  From the positive detections I created a heatmap and then thresholded that map to identify vehicle positions.  I then used `scipy.ndimage.measurements.label()` to identify individual blobs in the heatmap.  I then assumed each blob corresponded to a vehicle.  I constructed bounding boxes to cover the area of each blob detected.  

Here's an example result showing the heatmap from a test image, the result of `scipy.ndimage.measurements.label()` and the bounding boxes then overlaid on the image:

![heatmaptestimage1](https://user-images.githubusercontent.com/15799394/29290847-360e35d0-815f-11e7-8d5a-7ea1164edc55.png)

I then used pipleline to run the process on all 6 test images

### Here the resulting bounding boxes are drawn onto the 6 test images:

![pipeline-testimage1](https://user-images.githubusercontent.com/15799394/29290856-364f1dac-815f-11e7-9f82-8d17a346d838.png)




---

###Discussion

####1. Briefly discuss any problems / issues you faced in your implementation of this project.  Where will your pipeline likely fail?  What could you do to make it more robust?

I practised all the concepts first, especailly drawing boxes manually, using templates etc. I played around with extracting spatial, color histogram and hog features by fintuning various parameters on the test images that were provided.

I then extracted the cars and non cars. However since i had to assing parameters here i had to do lot of iterations on this step to improve my accuracy.
Sliding windows was another place i faced lot of difficulty. To correct lot of false positives i had to spend lot of time using the correct sizes, start end positions (x and y) and also overlap.

Applying thresholds was another major problem i faced since increasing the limits was causing issues with finding cars. I solved this by adding more sizes for sliding and optimizing the thresholds so i am atleast able to identify all the cars that are passing by.

I think manually finding parameters for feature extaction, sizes for windows, xy overlap and thresholds is very cumbersome.
My pipeline will fail when i encounter a size that i didnt handle.

Also i could not get my findingcars() method to work, hence i ended up using sliding windows(), which was very slow and ineffecient.
