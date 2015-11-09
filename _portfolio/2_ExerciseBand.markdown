---
layout: post
title: Human Activity Recognition
description: Classifying gym exercises using IMU data
img: /img/EBand/Wristband.jpg
---

Project Duration: Aug 2015 - Sept 2015

Over the summer of 2015, I set out to use a wearable inertial motion unit (IMU) to classify common workout exercises by applying machine learning techniques. An inertial motion unit is basically a chip that has an accelerometer and a gyroscope (and sometimes a magnetometer). If it is worn on a user's wrist, it can track the user's movement through space. Usually accelerometers in wearables are used to count steps, e.g. a FitBit, but I was inspired by a start-up called <a href="https://www.atlaswearables.com/" target="_blank">Atlas Wearables</a> to try to use the IMU data to recognize exercises like squats, push-ups, bicep curls, etc.

With little background in machine learning, I decided to take a course on <a href="https://www.coursera.org/course/pgm" target="_blank">Probabilistic Graphical Models</a>  (PGMs). I used what I learned from the course to implement a graphical model called Hidden Markov Models (HMM) for exercise classfication. 

The exercises I chose to classify are: prisoner squats, push-ups, triangle push-ups, bicep curls, and sit-ups. 
The best learned model on these 5 exercises achieved 96% accuracy on classifcation and rep-counting on unseen test data and can distinguish between push-ups and triangle push-ups.

### Data Collection 

First of all, let's look at what the data looks like. I used a SparkFun 6 degrees of freedom <a href="https://www.sparkfun.com/products/10121" target="_blank">IMU</a>, which has a 3-axis accelerometer and 3-axis gyroscope, to collect the data. Here is what a 5 push-ups look like:

<div class="image_row">
	<img class="col half " src="{{ site.baseurl }}/img/EBand/PushUpsAccel.png" alt="accelerometer reading of a 5 push-ups" title="accelerometer reading of a 5 push-ups"/>
	<img class="col half " src="{{ site.baseurl }}/img/EBand/PushUpsGyro.png" alt="gyroscope reading of a 5 push-ups" title="accelerometer reading of a 5 push-ups"/>
</div>

Here is what 5 squats looks like:

<div class="image_row">
	<img class="col half" src="{{ site.baseurl }}/img/EBand/SquatsAccel.png" alt="accelerometer reading of a 5 " title="accelerometer reading of a 5 squats"/>
	<img class="col half " src="{{ site.baseurl }}/img/EBand/SquatsGyro.png" alt="gyroscope reading of a 5 squats" title="accelerometer reading of a 5 squats"/>
</div>

We can see that different exercises produce very different plots, which is great news since this data will be used to infer what exercise is being done. Given noisy signals from the IMU, an HMM can be used to classify the exercise that generated the signal and also determine rep counts.

Data was collected using the following beautifully designed apparatus:

<img class="half " src="{{ site.baseurl }}/img/EBand/Wristband.jpg" alt="my homemade wireless smartwatch" title="my homemade wireless smartwatch" style="clear:both"/>

We can see that on the sleek cardboard back there is an Arduino Uno, the red SparkFun IMU, a bluetooth module to allow for wireless data transmission, and a 9V battery to power the Arduino.

Jumping to the results, here is a result of classification where 3 bicep curls were classified correctly and the rep boundaries were also found accurately:

<img class="half " src="{{ site.baseurl }}/img/EBand/BicepCurlAccelRepBounds.png" alt="3 bicep curls classified" title="3 bicep curls classified" style="clear:both"/>

The vertical red lines indicate where the algorithm believes a rep has ended.

<h3> The learning model </h3>

Hidden Markov Models (HMMs) are a class of Dynamic Bayesian Networks that are great for sequential data. It's been applied heavily in speech recognition and computational biology. The basics of it are you have a sequence of states that are you can't observe (the X's below), and a sequence of corresponding evidence that you can observe (the O's below). Given a sequence of observations, we want to infer what hidden states generated the observation, and the likelihood of seeing that sequence of observations.

<img class="half " src="{{ site.baseurl }}/img/EBand/hmm.png" alt="the hmm graphical model" title="the hmm graphical model" style="clear:both"/>

For this problem, I modeled the hidden states as stages of an exercise. Each rep of an exercise from beginning to end involve the same sequence of states. For example for a push-up, you start with your arms extended, then you sink down, stop briefly, then push back up. Each stage corresponds with a different part of the  exercise's IMU waveform. With enough IMU data, we can learn what the waveform for each state should look like for each exercise. Given the IMU readings, we can then ask the question: what's the likelihood of seeing these readings if the exercise were a push-up? What if it were a sit-up? If the likelihood is higher if the exercise were a push-up, then the user is more likely to have done a push-up than a sit-up. Asking this question for all exercises allow us to classify what exercise the user performed.

<h3>How do we prep the data?</h3> 
We can't just fed the raw samples into a learning algorithm since there's too much of it! There's also plenty of noise in the raw data. So, the data was split into frames and simple features were extracted from each frame. These features are then used for training and classification.

<h4>Features</h4>

To try to keep features simple while summarizing the waveform, the features for each frame were its mean, variance, and difference in mean value with the previous frame. Each frame was set to 50 samples with a 25 sample overlap between frames in the final model. Each frame corresponds with a time slice and hence it can be labelled with a specific hidden state. More computationally expensive, and possibly more informative, features like spectral features were not used to try to simulate the case where the algorithm would be used in real-time on a more resource limited embedded system.

<h3>The final model</h3> 
I won't go deep into the details of HMMs here but the resulting model after a lot of tweaking and cross-validation. The model had 5 hidden states, which was chosen from cross-validation. The emission probabilities are a gaussian mixture model (GMM) with 20 components and diagonal covariance matrices. GMMs were chosen since GMMs can can approximate any density in multi-dimensional space given enough mixture components. The transition matrix was initialized to be a left-to-right circular matrix, meaning at a current state you can only transition to the current state or the next state. It's circular because in the last state you can transition back to the first. Enforcing these constraints on the transition matrix resulted in a great improvement in performance. It also helped identify rep boundaries, since we know we're probably finished a rep if we reached the ending state of the exercise. The HMM was trained using the expectation-maximization algorithm. 

Classifcation and rep counting was done using filtering, that is, by doing the forward computation. I chose filtering instead of smoothing to simulate a computation for the online case, where you may not have the luxury of doing smoothing. Rep boundaries were found by finding at each time slice the state with the highest probability. If that state is the last state in the exercise, I assume a rep has been completed and restart the forward computation at the next time slice. Usually, Viterbi decoding would be used to find the most likely sequence of states, but because the transition matrix is left-to-right (and circular), I can assume that the final state can only be reached by first traversing each of the previous states. I can then just find the maximum probability of the marginal distribution of each hidden state.


<h3>The Data Collected</h3> 
 The training data consisted of me doing 5 sets of varying reps (repetitions) of each exercise. Total number of reps is around 45 reps for each exercise.

The test data consisted of the same 5 exercises. Each exercise had 6 test files and the test data varied from 1 to 3 reps. I would purposefully vary the speed of my reps to generate more irregular data. Of course, the test data was not involved in the training or cross-validation of the model. Model accuracy was evaluated based on classification accuracy and rep-counting accuracy.

The cross-validation data was around 12 single reps of each exercise.



<h3>Improvements</h3> 
More Data!! I alone can generate a lot of data, but the data will be unique to how my body moves. HMMs can be very powerful and easily overfit the training data, especially when there is not much of it. I can tell that overfitting is a problem since accuracy is usually very good on the training data but sometimes much worse on the test data. There is also a tendency to get stuck in local-minima during EM training. The best model I got achieved 96% accuracy on both classfication and rep counting, but there were runs where accuracy was worse (around 80-90%) due to local minima. 

Better features. I didn't go through a very intricate feature engineering process and there could very well be features that better separate exercise classes.

Find a way to distinguish between exercise and non-exercise activity. Distinguishing between an exercise and say, walking, is non-trivial. Walking can have the same cadence as many exercises and is also very periodic. I tried doing simple thresholding of the loglikelihood of each exercise, hoping walking would fall below the threshold, but it wasn't a great solution. Sometimes the algorithm would think a bicep curl was done when it was in fact walking.
