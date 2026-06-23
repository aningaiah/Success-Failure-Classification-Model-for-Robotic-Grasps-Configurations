# [Success/Failure Classification Model for Robotic Grasps

# Configurations]

```
[Aditya Ningaiah, an796]
Department of Mechanical and Aerospace Engineering, Rutgers University
```
## Abstract

Robotic grasping remains a challenging task in fields such as industrial and
manufacturing automation. Classical approaches in order to solve this problem use
contact mechanics theory such as force-closure and antipodal grasps which rely heavily
on human-engineered features like object edges. While sound in theory, these
approaches typically fail when confronted with real-world abnormalities or
less-than-optimal images of the object. This project aims to address these gaps by
building a robust custom computer vision Convolutional Neural Network (CNN) model
capable of classifying 96 x 96 RGB image patches as either having viable or non-viable
grasp configurations. Using the Cornell Grasp Dataset, a classical baseline utilizing
Canny Edge Detection and a Support Vector Machine (SVM) will be developed
alongside a custom 4-block 2D CNN which is able to learn grasp features directly from
the raw image pixels. These two approaches will then be evaluated against each other
not only using standard metrics of performance but also with a Noise Robustness Test
which will empirically demonstrate how the CNN outperforms the classical benchmark as
image quality degrades. Key results indicated that on all key metrics such as accuracy,
precision, recall, and F-1 score the CNN outperformed the classical benchmark.
Introducing Gaussian noise to the image patches also illustrated the robustness of the
CNN model in comparison as the classical approach was sensitive to the added noise.
Only at high levels of noise did both the classical and CNN model converge and trend
towards the majority-class baseline of approximately 63%.

## Problem Definition

In robotic manipulation tasks, determining whether a grasp configuration is successful
remains a critical challenge. In order for a robot to successfully pick up an object, it must
identify viable contact points with the object from visual data. The classical approach to
this problem heavily relies hand-crafted feature extractors, (i.e. Canny Edge Detection)
which frequently fail when subjected to textureless surfaces, shadows, and noisy sensor
data. A need for a more robust method for interpreting geometry and texture without
relying on manually engineered features has risen as a result of this.

## Materials and Methods

An open-source dataset (The Cornell Grasp Dataset) was used. This dataset
consists of a collection of annotated positive (viable) and negative (fail) planar grasp
attempts mapped out as 2D rectangles onto objects. Each object has multiple grasp
attempts to allow for a variety of possible successful and unsuccessful grasp attempts.


The input of the model takes a 96 x 96 pixel RBG image patch. This image patch
is mathematically cropped and rotated around the grasp center based on the grasp
annotation rectangle. This ensures the model does not have to learn how to orient the
robot and can look solely at geometry and texture between the horizontal bounds of the
grippers. The output is a binary classification score between zero and one, where one
indicates a mechanically viable force-closure grasp while zero indicates a failure.
Outputs can lie anywhere in between this range where different grasp configurations for
the same object can be ranked and decided which is the most optimal for the robot.
TensorFlow and Keras libraries were used to build the CNN pipeline, data
augmentation and for callbacks for model state management. OpenCV was used for
image processing and transformations, along with use in classical Canny Edge
Detection. Scikit-Learn was used to develop the baseline SVM implementation for the
classical benchmark, feature scaling, and metric calculation.
The experiment is a comparative study between a custom 4-block 2D
convolutional neural network with deep learning derived features and a baseline support
vector machine which uses hand-engineered classical features. In the CNN, the four
convolutional blocks each use MaxPooling2D and BatchNormalization as a way to
regularize the data by downsampling and keeping only the strongest signals in the set
and rescales the convolution outputs so it remains consistent as it passes through each
convolution layer. Activation values pass through the convolution blocks into the
classifier head where the 3D array is flattened into a 1D array. This array is passed
through two dense layers with 256 and 128 neurons respectively and is finally passed
through a classification output layer which uses a sigmoid activation function which
constrains output values between a 0 and 1.
Key hyperparameters include the four convolution blocks which halve the spatial
resolution while doubling the filter count to preserve the representational capacity of the
image patch. In the dense layers, the dropout amount goes from 0.5 in the first layer to
0.3 in the second layer. The dropout prevents overfitting and is set high because the
Cornell Grasp Dataset is small while the dense layer has many parameters. The second
dense layer reduces this to 0.3 because the image representation is compressed to 128
units and excessive dropout will discard too much signal before the sigmoid function in
the classification layer. Learning rate patience and early stop patience are also included
to prevent overfitting.
For mechanistic priors, the classical baseline uses a Canny edge detection +
classic SVM approach derived from rigid body physics. This assumes that a viable grasp
must involve the robotic fingers clamping on opposing, physical boundaries. In the CNN
pipeline, each image patch is mathematically cropped and rotated so that the robotic
jaws are always perfectly horizontal, allowing a rigid spatial constraint to be enforced on
the dataset.


## Results

For the training phase, the CNN model is able to minimize the training loss after 24
epochs before stopping early. We are able to see that the validation set and training set
also converge at this point meaning that the model has generalized well to the training
data. When comparing standard performance metrics such as accuracy, precision,
recall, and F1, the CNN model outperforms the classical approach on every benchmark
with each value recorded on the bar below.
When noise is introduced to the image patches, it highlights where the CNN truly
outperforms the Canny Edge Detection + SVM approach as it maintains its robustness
up until it is exposed to high amounts of gaussian noise and trends towards the
majority-class baseline.
A Grad-CAM also shows what the CNN is looking at when determining whether a
proposed configuration will succeed or fail. The heat map over the input image to show
which pixels the model is looking at when making a decision. These points of high
concentration in the model coincide with the jaw contact zones and the physical edge of
the object. This is important for the CNN since it was never trained with explicit
mechanistic rules like the Canny + SVM approach, but instead learned the rules of
force-closure theory by itself.


## Key Takeaways

Classical methods such as Canny Edge Detection look at local pixel intensity where if a
sharp color gradient exists, it marks it as an edge. The custom CNN however is able to
learn shape context, understanding the relationship between empty space between the
jaws, boundary of the object, and center of mass. This allows the CNN to have a strong


resiliency to image and sensor noise, showing marked improvements in a metric where
the classical methods tend to fail.

## Future Direction and Applications

The current model evaluates 3-DOF from a top down approach. The next progression of
this model would be to incorporate depth data to allow for a 6-DOF grasp model,
allowing the arm to approach the object at different angles and navigate complex
geometries. Moving the architecture to use 3D point clouds allow for diverse applications
such as soft body grasping models. By connecting this model to real hardware, this
model has potential to be applied to a variety of real-world industries such as moving
towards fully automated warehouses and medical/surgical robotics.

## LLM usage

I used Claude to assist in code debugging/generation and explanations during this
project. Examples of some prompts I used were:
-Asking what common lightweight robot grasping datasets there are?
-Why use a sigmoid activation function vs. a classification loss function in the final layer?
-How does Canny Edge Detection work with an SVM?
-Check where there are any inconsistencies in the code logic

## Acknowledgments

OneOneLiu (2020). _cornell_grasp._ [online] Kaggle.com. Available at:
https://www.kaggle.com/datasets/oneoneliu/cornell-grasp.


