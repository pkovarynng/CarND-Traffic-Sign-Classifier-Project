# **Traffic Sign Recognition** 

## Writeup

### You can use this file as a template for your writeup if you want to submit it as a markdown file, but feel free to use some other method and submit a pdf if you prefer.

---

**Build a Traffic Sign Recognition Project**

The goals / steps of this project are the following:
* Load the data set (see below for links to the project data set)
* Explore, summarize and visualize the data set
* Design, train and test a model architecture
* Use the model to make predictions on new images
* Analyze the softmax probabilities of the new images
* Summarize the results with a written report


[//]: # (Image References)

[image1]: ./examples/train-valid-test-vis.png "Data Set Exploratory Visualization"
[image2]: ./examples/fake-data-gen-step-1.jpg "Fake Data Generation - Step 1"
[image3]: ./examples/fake-data-gen-step-2.jpg "Fake Data Generation - Step 2"
[image4]: ./examples/grayscale-conv.jpg "Grayscale Conversion Example"
[image5]: ./examples/orig-fake-step1-example.jpg "Fake Data Generation - Step 1 Example"
[image6]: ./examples/orig-fake-step2-example.jpg "Fake Data Generation - Step 2 Example"
[image7]: ./examples/test/0.jpg "0"
[image8]: ./examples/test/1.jpg "1"
[image9]: ./examples/test/13.jpg "13"
[image10]: ./examples/test/18.jpg "18"
[image11]: ./examples/test/25.jpg "25"

## Rubric Points
### Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
### Writeup / README

#### 1. Provide a Writeup / README that includes all the rubric points and how you addressed each one. You can submit your writeup as markdown or pdf. You can use this template as a guide for writing the report. The submission includes the project code.

You're reading it! and here is a link to my [project code](https://github.com/pkovarynng/CarND-Traffic-Sign-Classifier-Project/blob/master/Traffic_Sign_Classifier.ipynb)

### Data Set Summary & Exploration

#### 1. Provide a basic summary of the data set. In the code, the analysis should be done using python, numpy and/or pandas methods rather than hardcoding results manually.

I used the pandas library to calculate summary statistics of the traffic
signs data set:

* The size of training set is 34799
* The size of the validation set is 4410
* The size of test set is 12630
* The shape of a traffic sign image is (32, 32, 3)
* The number of unique classes/labels in the data set is 43

#### 2. Include an exploratory visualization of the dataset.

Here is an exploratory visualization of the data set. It is a bar chart showing how the labels are represented in the data. It is apparent that some labels like 38 (Keep right), 2 (Speed limit (50km/h)), etc. are represented with a higher number of images than others like 0 (Speed limit (20km/h)), 42 (End of no passing by vehicles over 3.5 metric tons), etc.

![alt text][image1]

### Design and Test a Model Architecture

#### 1. Describe how you preprocessed the image data. What techniques were chosen and why did you choose these techniques? Consider including images showing the output of each preprocessing technique. Pre-processing refers to techniques such as converting to grayscale, normalization, etc. (OPTIONAL: As described in the "Stand Out Suggestions" part of the rubric, if you generated additional data for training, describe why you decided to generate additional data, how you generated the data, and provide example images of the additional data. Then describe the characteristics of the augmented training set like number of images in the set, number of images for each class, etc.)

To add more data to the data set, I used the following techniques because they generate images that look like they were real, but the traffic sign will still look different, so it is a good additional image for training the model.

##### Flipping images horizontally

The following two columns show the labels for which I flipped the images horizontally. By flipping images with the label on the left-hand side I got an image for the label on the right-hand side, and vice versa.
|                                      |                                       |
|:------------------------------------:|:-------------------------------------:|
| 19,Dangerous curve to the left (180) | 20,Dangerous curve to the right (300) |
| 33,Turn right ahead            (599) | 34,Turn left ahead              (360) |
| 36,Go straight or right        (330) | 37,Go straight or left          (180) |
| 38,Keep right                 (1860) | 39,Keep left                    (270) |

The following images were flipped horizontally too, but the result got the same label here as the original image:

|                                                 |
|:-----------------------------------------------:|
| 11,Right-of-way at the next intersection (1170) |
| 12,Priority road                         (1890) |
| 17,No entry                               (990) |
| 18,General caution                       (1080) |
| 22,Bumpy road                             (330) |
| 26,Traffic signals                        (540) |
| 30,Beware of ice/snow                     (390) |
| 35,Ahead only                            (1080) |

##### Flipping image vertically

I flipped some images vertically, too, the result got the same label as the original image:

|                                                 |
|:-----------------------------------------------:|
| 1,Speed limit (30km/h) 1980                     |
| 5,Speed limit (80km/h) 1650                     |

##### Rotating images by 180 degrees

I rotated some images by 180 degrees. The resulting image got the same label as the original image:

|                                                 |
|:-----------------------------------------------:|
| 32,End of all speed and passing limits (210)    |
| 40,Roundabout mandatory (300)                   |

Traffic sign 40: Sometimes it is installed upside down (not sure which one is the official if there is one)

##### Rotating images by 120 and 240 degrees

I rotated one label by 120 degrees and 240 degrees. The resulting images got the same label as the original:

|                                                 |
|:-----------------------------------------------:|
| 40,Roundabout mandatory (300)                   |

An example for one of the above transformations on a training set image (horizontal flip):

![alt text][image5]

I set a target number first. Once the number of the images for a given label reached this target number, I stopped generating more images for that label. By this I wanted to avoid that some images will over-represented again in the training data set.

I set the target number to 1800.

The visualization of the training data set before and after this step of the fake data generation:

![alt text][image2]

Then in a following code cell I generated some more fake images based on the training set augmented in the previous step and applied some random transformations on the images in it. The random transformations were shifting the image by max 8 pixels in both directions and then rotating the image by a random angle between -15 and 15 degrees.

This second step of fake image generation used the same target number and, as in the first step, stopped generating fake images for a label if that target number was reached.

An example for the above transformations on a training set image (random transformation):

![alt text][image6]


The visualization of the training data set before and after this 2nd step of the fake data generation:

![alt text][image3]

Then, I decided to convert the images to grayscale to see if there is any improvement in the validation accuracy of the model. I could see minimal improvement.

Here is an example of a traffic sign image before and after grayscaling.

![alt text][image4]

As a last step, I normalized the image data so that the data has mean zero and equal variance.


#### 2. Describe what your final model architecture looks like including model type, layers, layer sizes, connectivity, etc.) Consider including a diagram and/or table describing the final model.

My final model is a modified version of the LeNet5 architecture. It consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x1 grayscale image   				    | 
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 28x28x12 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 14x14x12 				|
| Convolution 5x5     	| 1x1 stride, valid padding, outputs 10x10x24 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 5x5x24    				|
| Fully connected		| outputs 220                 					|
| RELU  				|         							    		|
| Dropout				| keep propbability 0.55						|
| Fully connected		| outputs 128                 					|
| RELU  				|         							    		|
| Dropout				| keep propbability 0.55						|
| Fully connected		| outputs 43                 					|
 


#### 3. Describe how you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

To train the model, I used an Adam optimizer. I halved the original learning rate to 0.0005 and double the epochs. The batch size I left at 128 and have not touched any other existing hyperparameters. Added a new one, though: the keep probabilty for the drop out layers. It was helpful to prevent overfitting the model to the training set.

#### 4. Describe the approach taken for finding a solution and getting the validation set accuracy to be at least 0.93. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem.

My final model results were:
* training set accuracy of 94.7%
* validation set accuracy of 93.9%
* test set accuracy of 93.1%

The architecure I chose is the well known LeNet5.

I beleived the LeNet5 will work well with the traffic sign data because it seemed to have sufficient number of layers for the problem at hand, and also the number of neurons in the layers could still be increased, if needed, so that more redundant learning can happen in them.

Without any tweaking the LeNet5 model was overfitting. Then I added dropouts and added more neurons to the layers beleiving that more redundant learning can happen. The more neurons I added the higher the accuracy on the training and validation sets went. The final model goes over the required accuracy of 93%, so I stopped tweaking.

### Test a Model on New Images

#### 1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are five German traffic signs that I found on the web:

![alt text][image7] ![alt text][image8] ![alt text][image9] 
![alt text][image10] ![alt text][image11]

The Yield and the General Caution shields are a little turned, so initially I beleived that they may be a little difficult to recognize for the network. The Road Work shield looks a little shabby, so I was interested if that affects recognition. The Speed limit (20km/h) sign was three black things attached to its edge, I was interested in seeing if it still can be recognized easily. The Speed limit (30km/h) is pretty clear I did not expect to see any problems with that.

#### 2. Discuss the model's predictions on these new traffic signs and compare the results to predicting on the test set. At a minimum, discuss what the predictions were, the accuracy on these new predictions, and compare the accuracy to the accuracy on the test set (OPTIONAL: Discuss the results in more detail as described in the "Stand Out Suggestions" part of the rubric).

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Road Work      		| Road Work   									| 
| General Caution		| General Caution                               |
| Yield					| Yield											|
| Speed limit (20km/h)	| Speed limit (20km/h)			 				|
| Speed limit (30km/h)	| Speed limit (30km/h) 							|


The model was able to correctly guess 4 of the 5 traffic signs, which gives an accuracy of 80%. This compares favorably to the accuracy on the test set of 93.1%.

#### 3. Describe how certain the model is when predicting on each of the five new images by looking at the softmax probabilities for each prediction. Provide the top 5 softmax probabilities for each image along with the sign type of each probability. (OPTIONAL: as described in the "Stand Out Suggestions" part of the rubric, visualizations can also be provided such as bar charts)

The code for making predictions on my final model is located in the 28th cell of the Ipython notebook.

For the first image, the model is relatively sure that this is a General caution sign (probability of 100%), and the image does contain a General caution sign . The top five soft max probabilities were

| Label			            |     Probability	        					| 
|:-------------------------:|:---------------------------------------------:| 
| General caution  		    | 100%         									| 
| Traffic signals		    | 0.00%                                         |
| Pedestrians			    | 0.00%											|
| Road narrows on the right	| 0.00%			 	                			|
| Children crossing     	| 0.00% 			            				|

For the second image, the model is relatively sure that this is a Speed limit (20km/h) sign (probability of 100%), and the image does contain a Speed limit (20km/h) sign . The top five soft max probabilities were

| Label			            |     Probability	        					| 
|:-------------------------:|:---------------------------------------------:| 
| Speed limit (20km/h)      | 100%         									| 
| Speed limit (60km/h)	    | 0.00%                                         |
| Stop			            | 0.00%											|
| Speed limit (120km/h)	    | 0.00%			 	                			|
| Speed limit (30km/h)     	| 0.00% 			            				|

For the third image, the model is relatively sure that this is a Yield sign (probability of 99.19%), and the image does contain a Yield sign . The top five soft max probabilities were

| Label			            |     Probability	        					| 
|:-------------------------:|:---------------------------------------------:| 
| Yield                     | 99.19%       									| 
| No vehicles	            | 0.30%                                         |
| Traffic signals		    | 0.22%											|
| Bicycles crossing	        | 0.21%			 	                			|
| Bumpy road     	        | 0.05% 			            				|

For the fourth image, the model is relatively sure that this is a Road work (probability of 90.23%), and the image does contain a Yield sign . The top five soft max probabilities were

| Label			            |     Probability	        					| 
|:-------------------------:|:---------------------------------------------:| 
| Road work                 | 90.23%       									| 
| Road narrows on the right	| 6.17%                                         |
| Bicycles crossing		    | 3.07%											|
| Bumpy road	            | 0.47%			 	                			|
| Children crossing     	| 0.06% 			            				|

For the fifth image, the model is relatively sure that this is a Speed limit (20km/h) (probability of 95.23%), but the image contains a Speed limit (30km/h) sign . The top five soft max probabilities were

| Label			            |     Probability	        					| 
|:-------------------------:|:---------------------------------------------:| 
| Speed limit (20km/h)      | 90.23%       									| 
| Speed limit (30km/h)      | 2.42%       									| 
| Stop		                | 2.31%											|
| Roundabout mandatory	    | 0.03%			 	                			|
| Speed limit (60km/h)     	| 0.01% 			            				|

### (Optional) Visualizing the Neural Network (See Step 4 of the Ipython notebook for more details)
#### 1. Discuss the visual output of your trained network's feature maps. What characteristics did the neural network use to make classifications?


