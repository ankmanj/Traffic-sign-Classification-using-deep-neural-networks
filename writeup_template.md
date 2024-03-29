**Traffic Sign Recognition** 

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

[image1]: ./examples/histogram_Dataset.png "Visualization"
[image2]: ./examples/grayscale.jpg "Grayscaling"
[image3]: ./examples/random_noise.jpg "Random Noise"
[image4]: ./examples/50.jpg "Traffic Sign 1"
[image5]: ./examples/60.jpg "Traffic Sign 2"
[image6]: ./examples/halt.jpg "Traffic Sign 3"
[image7]: ./examples/sign.jpg "Traffic Sign 4"
[image8]: ./examples/stop.jpg "Traffic Sign 5"

[augmentation1]: ./examples/translation.png
[augmentation2]: ./examples/rotation.png
[augmentation3]: ./examples/scaling.png
[augmentation4]: ./examples/brightness.png

[preprocessed]: ./examples/preprocessed.png

[predictions]: ./examples/predictions.png
[aug_hist]: ./examples/augmented_hist.png
[valid_hist]: ./examples/valid_hist.png

[LeNet]: ./examples/LeNet.png
[LeNet_2]: ./examples/LeNet_2.jpg
[softmax]: ./examples/softmax_bar.png

## Rubric Points
Here I will consider the [rubric points](https://review.udacity.com/#!/rubrics/481/view) individually and describe how I addressed each point in my implementation.  

---
###Writeup / README

Link to my project code [project code](https://github.com/ankith0073/Traffic-sign-Classification/blob/master/Traffic_Sign_Classifier.ipynb

###Data Set Summary & Exploration

####1. The dataset used to train the neural net for Traffic sign classification is German traffic sign dataset

The code for this step is contained in the second code cell of the IPython notebook.  

I used the pandas library to calculate summary statistics of the traffic
signs data set:

* The size of training set is 34799 images
* The size of test set is 12630
* The shape of a traffic sign image is 32 * 32
* The number of unique classes/labels in the data set is 43

####2. Exploratory visualization of the dataset 

The code for this step is contained in the third code cell of the IPython notebook.  

Here is an exploratory visualization of the data set. It is a bar chart showing how the data ...

![alt text][image1]

Looking at the distribution of each class of the dataset, we can see that classes are unbalanced, i.e Class 0 has 180 images and class 2 has nearly 1800 images

###Design and Test a Model Architecture

1. The important step in the neural network training is the data preprocessing,explained as below 
 
   * Augment the classes with less training examples         
   * Convert all images to grayscale 
   * Subtract the mean and make the imagees have same pixel variation i.e betweem [-1,1]
   * Histogram normalize the images to increase the contrast of the image      

 * Augment the classes with less training examples

   Due to imbalance in the dataset, Data augmentation needs to be done. i.e if a particular class has less than 1000 training examples, we augment that training class till the number of examples for that particular class becomes equal to 1000. For example, if class o has 250 training examples , we augment it with 750 more images to make the total 1000. It is done by the following steps
    * Take an image of a class whose training examples are less than 1000 in random
    * Apply Jittering(Translation, Rotation, Scaling, Brightness) with small random values.

    * Translation

    ![alt text][augmentation1]

    * Rotation 

    ![alt text][augmentation2]

    * Scaling 

    ![alt text][augmentation3]

    * Brightness  

    ![alt text][augmentation4]
 
 * The histogram of the training data after data augmentation looks like below
![alt text][aug_hist]

* Convert all the images to grayscale  
      Convert all the images to grayscale. This reduces the processing time of neural network,i.e Network needs to train on single channel than multiple channels. The conversion also in induced from the fact that traffic signs charecteristics are its shape not the color
   
 *  Subtract the mean and make the imagees have same pixel variation i.e betweem [-1,1]
       Make all the images zero mean and have the same standard deviation and histogram normalize the image. The grayscale image with zero mean and deviation from -1 to 1
       
 * Histogram normalize the images to increase the contrast of the image   
      Histogram normalisation makes the features which needs to be seen more visible. Tge grayscale images which zero mean centered and histogram normalised images are as shown below
![alt text][preprocessed]

The code for this step is contained in the fourth to sixteenth code cells of the IPython notebook.

2. Setting up training, testing and validation data.

 The data is already packed into train.p, Valid.p, test.p pickle files. The code for this step is contained in the 2nd code coll of the iPython notebook. But to give the data into the neural network for training it is  good practise to shuffle the data. So it is made sure that the training data is shuffled before training. Also when referred to 1st step, It gives an insight how artificial data is augmented to make the classes balanced.
  
  The histogram of the validation set is as shown below
  
  ![alt_text][valid_hist]

3. The initial model was trained with Lenet Nodel achieving an accuracy of 89% out of the box. The original Lenet model is tweaked to achieve a validation accuracy of ~94% 
The code for my final model is located in the 20th cell of the ipython notebook. 

My final model consisted of the following layers:

| Layer         		|     Description	        					| 
|:---------------------:|:---------------------------------------------:| 
| Input         		| 32x32x1 Grayscale image						| 
| Convolution 3x3     	| 1x1 stride, valid padding, outputs 28x28x6 	|
| RELU					|												|
| a.Max pooling	      	| 2x2 stride,  outputs 14x14x6   				|
| Convolution 3x3     	| 1x1 stride, valid padding, outputs 10x10x16 	|
| RELU					|												|
| b.Max pooling	      	| 2x2 stride,  outputs 5x5x16   				|
| Convolution 3x3     	| 1x1 stride, valid padding, outputs 10x10x16 	|
| RELU					|												|
| Max pooling	      	| 2x2 stride,  outputs 5x5x16   				|
| Convolution 3x3     	| 1x1 stride, valid padding, outputs 1x1x128 	|
| c.RELU				|												|
| Flatten	            | From a.(14x14x6) b.(5x5x16) c.(1x1x128)		|
| Fully connected		| 1704x100     									|
| Fully connected		| 100x84     									|
| Dropout				| Keep probability = 50%						|
| Fully connected		| 84x43     									|
|						|												|


4. Describe how, and identify where in your code, you trained your model. The discussion can include the type of optimizer, the batch size, number of epochs and any hyperparameters such as learning rate.

The above model was trained with the following parameters
  * Optimizer - Adam 
  * Learning rate - 0.008
  * Batch size - 128 images
  * Epochs - 70

The code for training the model is located in the 25th cell of the ipython notebook. 


5. Describe the approach taken for finding a solution. Include in the discussion the results on the training, validation and test sets and where in the code these were calculated. Your approach may have been an iterative process, in which case, outline the steps you took to get to the final solution and why you chose those steps. Perhaps your solution involved an already well known implementation or architecture. In this case, discuss why you think the architecture is suitable for the current problem. 

 The initial Implementation was with the out of the box architecture, LeNet was used initially to train the neural network whose architecture is as shown below 
 
![alt text][LeNet]

The following hyperparameters were used with validation accuracy of 93.3%

  * Learning rate - 0.008
  * Batch size - 128 images
  * Epochs - 70
  
 The next architecture used was the Sermanet/Yann Lecun for traffic sign classification as shown below 
  
![alt text][LeNet_2]

The following hyperparameters were used with validation accuracy of 92.9%

  * Learning rate - 0.008
  * Batch size - 128 images
  * Epochs - 70

The learning rate choice was made sure it is not very high to miss the global minimum, Not too low to make the training longer, So a value 0.008 was chosen

The Thirst for high accuracy wasnt quenched yet! so with an additional convolutional layer as in step 3 was used to obtain a validation accuracy of ~93.8%

Dropout was added to the last  fully connected layer to avoid overfitting.
 
The code for calculating the accuracy of the model is located in the 26th cell of the Ipython notebook.

My final model results were:
* validation set accuracy of 94.4% 
* test set accuracy of 92.5% 

###Test a Model on New Images

####1. Choose five German traffic signs found on the web and provide them in the report. For each image, discuss what quality or qualities might be difficult to classify.

Here are five German traffic signs that I found on the web:

![alt text][image4] ![alt text][image5] ![alt text][image6] 
![alt text][image7] ![alt text][image8]

The difficulties to classify are might be of following reasons...

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Priority road   		| Seems to be the easiest to classify									| 
| Right of way 			| Sign inside the traingle should be clearly visible									|
| Stop					| Stop text would be a challenge to detect								|
| 50 km/h	      		| Speed sign detectable, But speed limit might be a difficulty    					 				|
| 60 km/h   			| Speed sign detectable, But speed limit might be a difficulty       							|

Here are the results of the prediction:

| Image			        |     Prediction	        					| 
|:---------------------:|:---------------------------------------------:| 
| Priority road   		| Priority road									| 
| Right of way 			| Right of way									|
| Stop					| No passing									|
| 50 km/h	      		| 50 km/h    					 				|
| 60 km/h   			| No passing        							|


The model was able to correctly guess 3 of the 5 traffic signs, which gives an accuracy of 60%. This compares less favorably to the accuracy on the test set of 92.5%

The top 5 softmax predictions are as shown below 
![alt_text][softmax]

THe predictions for each test data is as shown below,
First column shows the Input traffic sign data, Second column is the traffic sign prediction for a given input image with the highest softmax probablity
![alt text][predictions]
