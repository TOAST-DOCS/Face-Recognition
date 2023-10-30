## AI Service > Face Recognition > Console User Guide

In the console, face recognition and analysis and face comparison features can be tested.

The following are the test methods using the console:

## Recognize and Analyze Faces

### Photo Upload for Face Recognition and Analysis
Photos can be uploaded using one of the three methods:
1. Click Upload Image button
2. Enter an image URL
3. Drag and drop an image

### Analyze
Once you upload the photo and click Analyze button, the analysis results appear on the right side of the screen.

![detect](http://static.toastoven.net/prod_facerecognition/FR_detect_en_v2.png

* [Result] Shows the area of the recognized face and confidence.
* [Response] Shows the JSON example that responds to the API call.


## Compare Faces

### Photo Upload for Comparing Faces
Photos can be uploaded using one of the three methods, and both the Reference Image and Comparison Image must be selected.
1. Click Upload Image button
2. Enter an image URL
3. Drag and drop an image

### Threshold Settings
Threshold is a reference value of similarity that determines if the face detected from the standard image matches the face recognized from the compared image.

### Compare
Once you upload the photo and click Compare button, the comparison results appear on the right side of the screen.

![compare](http://static.toastoven.net/prod_facerecognition/FR_compare_en_v2.png)

* [Result] Shows the comparison results and similarity of faces recognized from the Reference Image and Comparison Image.  
* [Response] Shows the JSON example that responds to the API call.
