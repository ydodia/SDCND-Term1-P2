#Project 2 - Self Driving Car ND Term 1

###Overview
This project uses a LeNet-5-type model to classify German traffic signs. The training set consists of 34,799 items; the test set, by contrast, consists of 12,630 items. There are 43 classes of signs to classify. The discussion below will detail the steps taken to build my model and some analysis along the way.

#### 1 - Data Inspection
A few quick characteristics of the data will help in constructing and thinking about how to build our model. One simple way to do this is to create a histogram of the data:

[histo]: ./1_histo.png
![Histogram of Input Data][histo]

From these three histograms, we can see that the data is certainly not uniform but rather clustered around the lower class IDs (0 - 17, or so). If we wanted to get more advanced, we could use this information to our advantage and do some statistical pre-processing. I don't take advantage of this distribution in this model.

Another good step would be to visual some examples of the data we are classifying. Below, I show 3 example, randomly chosen images from the *training* set.

[exTrainImgs]: ./2_exTrainImgs.png
![Example of Training Images - 3 randomly chosen][exTrainImgs]

Visualizing our data this way can help us to think of better ways to pre-process our data and design our model. As an example, utilizing the RGB color channels may help to provide a bit more info when input into the model. I chose this route and used each of the RGB channels as separate data. Each image is a **32x32x3**. The last dimension represents the three RGB color channels.

#### Building the Model - LeNet-5
My model is essentially a LeNet-5 model using the 3 RGB layers as input for each image. Pre-processing was simply normalization of each image by subtracting the mean of each image color channel's values and then dividing by the standard deviation. I chose this simple normaliztion for it's simplicity and necessity.

In summary, my model is broken down as follows:

###### Input
I'll use the LeNet architecture which accepts a 32x32 image composed of 3 color layers (RGB).

##### Architecture
** Layer 1: Convolutional ** The output shape will be 28x28x6.

** Activation ** ReLU

** Pooling ** Output shape is 14x14x6.

** Layer 2: Convolutional ** Output shape is 10x10x16

** Activation ** ReLU

** Pooling ** Output shape is 5x5x16

** Flatten ** Flatten the output shape of the final pooling layer such that it's 1D instead of 3D.

** Layer 3: Fully Connected ** This should have "X" outputs.

** Activation ** ReLU

** Layer 4: Fully Connected ** This should have "X" outputs.

** Activation ** ReLU

** Layer 5: Fully Connected (Logits) ** This should have "X" outputs.

###### Output
Return the result of the 2nd fully connected layer.

#### Model Overview
In layers 1 and 2, I used convolutional networks. In layers 3, 4 and 5 I used fully connected ones. This follows the flow of LeNet and the convnets aid in possible underfitting. The activations used are simple ReLUs, as they also help in providing a smooth, continuous approximation to the max(0,x) function. Additionally, I used the *dropout* technique in *Layer 4* with a value of *0.5*. This results in dropping half of the activations which aids to minimize overfitting.

#### Hyperparameter Choice
I chose my hyperparameters after some trial and error. The learning rate was kept fairly small at 2.5e-4 to allow for a more gradual and stable ascent in accuracy. 300 epochs and a batch size of 128 were chosen to allow better accuracy and stability as well. I found that if I had used larger batches the accuracy would jump wildly. If I used fewer epochs, say 100, I wouldn't have gotten some appreciable, if not still marginal, increases in accuracy; increasing the epochs showed me that there was much learning still left, given no change in the other hyperparameters.

#### Model Results
My model resulted in an accuracy of 94.7%. I was happy with this, given the simple model used and minimal tuning and pre-processing. I'm confident it's possible to devise a better pipeline/model to get an accuracy 98+%. If I were to aim for a relatively more complex model, I'd probably do some image pre-processing such as converting to HSV.

#### Test Model on New Images
To better understand how my model would work in a less sterile environment, I downloaded 6 German traffic signs off of the internet and used my model to test and classify them. The 6 images I used are:

[newImages]: ./3_newImages.png
![6 new test images taken from the web.][newImages]

These images don't appear to be difficult to classify. The lighting is good, providing good contrast between pixels. The angles and focus are good as well.

Running my model on these resulted in 66.7% or two-thirds accuracy; meaning, I classified only 4 out of 6 correctly. An analysis of the softmax probabilities gives us a cursory look at why 2 of the images weren't classified correctly.

[softmax]: ./4_softmax.png
![Top-5 Softmax values for the 6 test images.][softmax]

We can see right off that image 2, which is correctly ID 1, was seen as 11, 10, 2, 40 & 12 with somewhat similar probabilities for 11 & 10. Another pecularity is image 5 which has a correct ID of 36. However, the model seems to be certain that it's an ID 25 with a distant second of ID 36. I can only speculate that my preprocessing didn't help add more information. Image 5 might be a bit darker with the blue; however, image 6 is also a bit dark but was classified correctly with effectively 100% accuracy.

Focusing on image 5, I believe the contrast was poor. Additionally, the presence of other sign fragments may have confused it initially and biased it to those with a higher contrast (red/white bordering).

#### Summary
I used a rather simple architecture and pipeline, choosing to forego complicated preprocessing and a more complicated model for simplicity and ease of tuning, while still meeting the 93% accuracy requirement. I did notice in some forums that folks simple used a grayscale image (flattened into a single, monochrome color layer) and were able to to get higher accuracies.

My model resulted in 94.7% accuracy using the basic LeNet-5 model. If I have time, I'll go back and test out some pre-processing steps while keeping the model the same.  Some statistical work may also help to take advantage of the skewed distribution of the given data.

