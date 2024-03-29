# Hand-Gesture-Recognition-for-Drones using OpenPose and LSTM RNN

This project focuses on building a motion gestures recognition and classification system which would be feasible to mount on a small drone.
The whole idea of this project is to operate a drone in non-telemetric ways. This means that if a drone loses all the communication with its controller and is lost, then it should actively work only based on gesture recognition.

We are using 4 basic motion gestures in this project to be recognized and classified. We have created the video dataset by ourselves so you can feel free to create your own dataset.

For this vision-based drone controlling system, we have implemented the following procedure:

## Dataset:
We created a dataset with 178 videos where each video contains one gesture. We have created this dataset keeping in mind the angle from which the drone would be viewing the human responsible to give gesture commands in order to get the full impact of the drone captures. We have considered 4 different body types in our dataset and have made variations in the angles and the distances from which the videos were captured. These videos have been recorded from our cellphones (iPhone, Pixel and OnePlus for different resolutions and quality).
The 4 gestures we are considering in this project are:
1. Target
2. Forward
3. Descend
4. Stop

Dataset link:
https://drive.google.com/file/d/1EAHHvDdLDwlT0E0jBlWKs7h8cLhmu7nf/view?usp=sharing

## Approach 1:
Instead of making a classifier from the ground up, we decided to use the OpenPose posture recognition framework from Carnegie Mellon University's Perceptual Computing Lab for representation. Using the body model, OpenPose allowed us to extract the body pose keypoints from videos or images.
We then implemented long-short term memory recurrent neural network classifier on the sequence of keypoints extracted from each frame of the videos.

Our working pipeline worked as follows:

1. Convert the videos into frames 
2. Parse all the videos using OpenPose
3. Basic data preprocessing
4. Train the LSTM classifier

### 1. Convert the videos into frames:
Used a python script to convert all the videos into the respective frames and saved a video and its respective frames into a single folder.

### 2. Parse all the videos using OpenPose:
Applied OpenPose on the whole video dataset. OpenPose is capable of outputting a video output with a keypoint skeleton on the detected keypoints and a json file with 23 detected keypoints. We used the json output for each videoframe and saved these json files per frame for each video. We then clubbed all the framewise json files into one big json file which represented the video in the keypoint json format. Repeated this for all 178 videos.

### 3. Basic data preprocessing:
As we are using a labelled dataset, we loaded the train, test and validation .csv files with a video-label format for the train and validation dataset and just the video names for the test dataset.
We then converted these big json files into numpy arrays in order to make the computation for the LSTM classifier easy.

### 4. Train the LSTM classifier:
Considered 120 videos for training, 29 for validation and 29 for testing.
For the architecture of LSTM RNN, we referred to guillaume-chevalier's github repo: https://github.com/guillaume-chevalier/LSTM-Human-Activity-Recognition
We trained for 50 epochs and the training iterations were 120 * 50 = 6000 iterations
We set the batch size to be 512 and used ReLu activation function and ADAM optimizer for our model

Unfortunately our model underfitted in the OpenPose - LSTM approach.
Iter: 512,  Accuracy test: 1.0, Accuracy train: 0.255859375, time: 2.966365337371826

## Approach 2:
Image classification approach with fast.ai

Dataset link:
https://drive.google.com/file/d/1ZXRQK7WM_7VXHiE8ao5FeDbrZfUl1x8C/view?usp=sharing

As motion gestures is a challenge we are facing, we decided to take it step by step and try the basic approach of steady gesture recognition and classification.
Before any work can be done a dataset needs to be converted into a DataBunch object, and in the case of the computer vision data - specifically into an ImageDataBunch subclass. Since our images are placed in folders whose names correspond to the image labels, we will use the ImageDataBunch.from_folder() function to create an object that contains our image data. This is super useful and makes it incredibly easy to read the data into our model.
Fast.ai can automatically split our data into train and validation sets, so we don't even need to create these on our own.
The only hyperparameters we need are the path-variable pointing to our data set, the size of the inputs and the batch size for each gradient descent iteration.
The function argument called get_transforms() which returns a list of available image transformations upon call.
The parameter valid_pct which controls the percentage of images that will be randomly chosen to be in the validation set.
With this method we obtained an accuracy of 0.78


## Conclusion:
There is still so much to work on and improve in order to get to the goal we set. We aim to work on the OpenPose - LSTM approach and improve our model in order to get better results.
We intend to explore different analytical and empirical approaches to solve this problem more efficiently. One of the starting point could be implementing a reinforcement learning approach or using a sliding window approach on a group of labeled frames in the dataset to serve the purpose of motion gesture recognition.
