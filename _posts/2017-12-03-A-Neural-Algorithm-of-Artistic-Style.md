---
mathjax: true
title: A Neural Algorithm of Artistic Style
---
In this post we will implement the style transfer technique from the paper "A Neural Algorithm of Artistic Style". The general idea is to take two images, and produce a new image that reflects the content of one but the artistic "style" of the other. We will do this by first formulating a loss function that matches the content and style of each respective image in the feature space of a deep neural network, and then performing gradient descent on the pixels of the image itself.

## Model
We need to take the advatange of a CNN structure which (implicitly) understands image contents and styles. Rather than training a completely new model from scratch, we will use a pre-trained model to achieve our purpose - called "transfer learning".

We will use the VGG19 model. Since the model itself is very large (>500Mb) you will need to download the VGG-19 model and put it under the model/ folder. The comments below describe the dimensions of the VGG19 model. We will replace the max pooling layers with average pooling layers as the paper suggests, and discard all fully connected layers.

We use the VGG 19-layer model by from the paper "Very Deep Convolutional Networks for Large-Scale Image Recognition" and store its path in the variable VGG_MODEL. In order to use this VGG model we need to substract the mean of the images originally used to train the VGG model from the new input images to be consistent. This affects the performance greatly.

The `load_vgg_model()` function returns a model for the purpose of 'painting' the picture. It takes only the convolution layer weights and wrap using the TensorFlow Conv2d, Relu and AveragePooling layer.

<script src="https://gist.github.com/ArnoutDevos/fb9654a0e9908f7e320046dfee36791a.js"></script>

## Input images
We need to define some constants for the inputs and we will be uisng RGB images with a 640 x 480 resolution, but you can easily modify the code to accommodate different sizes. Because of the mean substraction, that messes up the image visually,mentioned in the previous section, two helper functions `load_image()` and `recover_image()` are used.

<script src="https://gist.github.com/ArnoutDevos/e2e3a1734b81930f0719138bd156fd6b.js"></script>

Using the previously defined helper function we can load the input images. The vgg model expects image data with `MEAN_VALUES` subtracted to function correctly. `load_image()` already handles this. The subtracted images will look funny.

CODE load_images.py

## Random Image Generator
The first step of style tranfer is to generate a starting image. The model will then gradually adjust this starting image towards target content/style. We will need a random image generator. The generated image can be arbitrary and doesn't necessarily have anything to do with the content image. But, generating something similar to the content image will reduce our computing time.

CODE generate_noise_image.py


Now we can check by visualizing images we generated. Keep in mind that `noise_ratio = 0.0` produces the original subtracted image, while `noise_ratio = 1.0` produces a complete random noise. Notice that the visulized images are not necessarily more clear with lower noise_ratio. The image sometimes looks sharper when some intermediate level of noise is added. Because the noise is random, borders/edges will stick out compared to more regular surfaces. This leads to a 'sharper' perception. This ofcourse only holds when the noise levels are low enough to not make the borders/edges completely disappear (you sum the noise to the original image)

CODE visualize_images.py

# Loss functions
Once we generate a new image, we would like to evaluate by how much it maintains contents while approaching the target style. This can be defined by a loss function. The loss function is a weighted sum of two terms: *content loss* and *style loss*.

## Content Loss
Let's first write the content loss function of equation (1) from the paper. Content loss measures how much the feature map of the generated image differs from the feature map of the source image. We only care about the content representation of one layer of the network (say, layer @@l@@), that has feature maps @@A^l\inR^{1xH_lxW_lxN_l}@@Aℓ∈ℝ1×Hℓ×Wℓ×Nℓ. @@N_l@@ is the number of filters/channels in layer @@l@@, @@H_l@@ and @@W_l@@ are the height and width. We will work with reshaped versions of these feature maps that combine all spatial positions into one dimension. Let @@F_l \in R^{M_lxN_l}@@Fℓ∈ℝMℓ×Nℓ be the feature map for the current image and @@P_l \in R^{M_lxN_l}@@Pℓ∈ℝMℓ×Nℓ be the feature map for the content source image where @@M_l = H_l x W_l}@@Mℓ=Hℓ×Wℓ is the number of elements in each feature map. Each row of @@F_l@@Fℓ or @@P_l@@Pℓ represents the vectorized activations of a particular filter, convolved over all positions of the image.

The content loss is then given by:

$$L_c = \frac{1}{2}\Sigma_{i,j}(F_{ij}^l - P_{ij}^l)^2$$

We are only concerned with the "conv4_2" layer of the model.

CODE content_loss_func.py

## Style Loss
Now we can tackle the style loss of equation (5) from the paper. For a given layer ℓ, the style loss is defined as follows:

First, compute the Gram matrix G which represents the correlations between the responses of each filter, where F is as above. The Gram matrix is an approximation to the covariance matrix -- we want the activation statistics of our generated image to match the activation statistics of our style image, and matching the (approximate) covariance is one way to do that. There are a variety of ways you could do this, but the Gram matrix is nice because it's easy to compute and in practice shows good results.

Given a feature map Fℓ of shape (1,@@M_l@@Mℓ,@@N_l@@Nℓ), the Gram matrix has shape (1,@@N_l@@Nℓ,@@N_l@@Nℓ) and its elements are given by:

Gℓij=∑kFℓikFℓjk
Assuming Gℓ is the Gram matrix from the feature map of the current image, Aℓ is the Gram Matrix from the feature map of the source style image, then the style loss for the layer ℓ is simply the Euclidean distance between the two Gram matrices:

Eℓ=14N2ℓM2ℓ∑i,j(Gℓij−Aℓij)2
In practice we usually compute the style loss at a set of layers  rather than just a single layer ℓ; then the total style loss is the weighted sum of style losses at each layer by wℓ:

Ls=∑ℓ∈wℓEℓ
$$L_s=\Sigma$$

In our case it is a summation from conv1_1 (lower layer) to conv5_1 (higher layer). Intuitively, the style loss across multiple layers captures lower level features (hard strokes, points, etc) to higher level features (styles, patterns, even objects).

CODE style_loss_func.py

## Total loss
L=αLc+βLs
$$L = \alpha L_c + \beta L_s$$

CODE total_loss.py

## Building a TensorFlow session and model
CODE tfsess_model.py

## Run
Now we run the model which outputs the painted image every 100 iterations. You can find those intemediate results under output/ folder. Notice on CPU it usually takes almost an hour to run 1000 iterations. Take your time!