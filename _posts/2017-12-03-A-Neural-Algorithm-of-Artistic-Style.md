---
gallery:
  - url: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/images/muse.jpg
    image_path: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/images/muse.jpg
    title: "Style image: Muse"
  - url: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/images/hollywood_sign.jpg
    image_path: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/images/hollywood_sign.jpg
    title: "Content image: Hollywood sign"
  - url: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/output/200.png
    image_path: https://raw.githubusercontent.com/ArnoutDevos/StyleTransferTensorFlow/master/output/200.png
    title: "Result of StyleTransfer after 200 iterations"
mathjax: true
title: A Neural Algorithm of Artistic Style
---
In this post we will implement the style transfer technique from the paper [A Neural Algorithm of Artistic Style](https://arxiv.org/abs/1508.06576). The general idea is to take two images, and produce a new image that reflects the content of one but the artistic "style" of the other. We will do this by first formulating a loss function that matches the content and style of each respective image in the feature space of a deep neural network, and then performing gradient descent on the pixels of the image itself.

A (less verbose) runnable Python file can be found [on GitHub](https://github.com/ArnoutDevos/StyleTransferTensorFlow).

{% include gallery caption="Two input images and one output image of the Style Transfer algorithm after 200 iterations." %}

## Model
We need to take the advatange of a CNN structure which (implicitly) understands image contents and styles. Rather than training a completely new model from scratch, we will use a pre-trained model to achieve our purpose - called "transfer learning".

We will use the VGG19 model. Since the model itself is very large (>500Mb) you will need to download the VGG-19 model and put it under the model/ folder. The comments below describe the dimensions of the VGG19 model. We will replace the max pooling layers with average pooling layers as the paper suggests, and discard all fully connected layers.

We use the VGG 19-layer model by from the paper "Very Deep Convolutional Networks for Large-Scale Image Recognition" and store its path in the variable VGG_MODEL. In order to use this VGG model we need to substract the mean of the images originally used to train the VGG model from the new input images to be consistent. This affects the performance greatly.

The `load_vgg_model()` function returns a model for the purpose of 'painting' the picture. It takes only the convolution layer weights and wrap using the TensorFlow Conv2d, Relu and AveragePooling layer.

<script src="https://gist.github.com/ArnoutDevos/fb9654a0e9908f7e320046dfee36791a.js"></script>

## Input images
We need to define some constants for the inputs and we will be using RGB images with a 640 x 480 resolution, but you can easily modify the code to accommodate different sizes. Because of the mean substraction, that messes up the image visually, two helper functions `load_image()` and `recover_image()` are used.

<script src="https://gist.github.com/ArnoutDevos/e2e3a1734b81930f0719138bd156fd6b.js"></script>

Using the previously defined helper function we can load the input images. The vgg model expects image data with `MEAN_VALUES` subtracted to function correctly. `load_image()` already handles this. The subtracted images will look funny.

<script src="https://gist.github.com/ArnoutDevos/85c4862df37582f7380269585a94b90e.js"></script>

## Random Image Generator
The first step of style tranfer is to generate a starting image. The model will then gradually adjust this starting image towards target content/style. We will need a random image generator. The generated image can be arbitrary and doesn't necessarily have anything to do with the content image. But, generating something similar to the content image will reduce our computing time.

<script src="https://gist.github.com/ArnoutDevos/84d5e1d3c93e781bae71d0c900292bb3.js"></script>


Now we can check by visualizing images we generated. Keep in mind that `noise_ratio = 0.0` produces the original subtracted image, while `noise_ratio = 1.0` produces a complete random noise. Notice that the visulized images are not necessarily more clear with lower noise_ratio. The image sometimes looks sharper when some intermediate level of noise is added. Because the noise is random, borders/edges will stick out compared to more regular surfaces. This leads to a 'sharper' perception. This ofcourse only holds when the noise levels are low enough to not make the borders/edges completely disappear (you sum the noise to the original image)

<script src="https://gist.github.com/ArnoutDevos/7f323379ca6664252fb93a7a0f87afc5.js"></script>

# Loss functions
Once we generate a new image, we would like to evaluate by how much it maintains contents while approaching the target style. This can be defined by a loss function. The loss function is a weighted sum of two terms: *content loss* and *style loss*.

## Content Loss
Let's first write the content loss function of equation (1) from the paper. Content loss measures how much the feature map of the generated image differs from the feature map of the source image. We only care about the content representation of one layer of the network (say, layer @@\ell@@), that has feature maps @@A^\ell \in \mathbb{R}^{1 \times H_\ell \times W_\ell \times N_\ell}@@. @@N_\ell@@ is the number of filters/channels in layer @@\ell@@, @@H_\ell@@ and @@W_\ell@@ are the height and width. We will work with reshaped versions of these feature maps that combine all spatial positions into one dimension. Let @@F^\ell \in \mathbb{R}^{M_\ell \times N_\ell}@@ be the feature map for the current image and @@P^\ell \in \mathbb{R}^{M_\ell \times N_\ell}@@ be the feature map for the content source image where @@M_\ell=H_\ell\times W_\ell@@ is the number of elements in each feature map. Each row of @@F^\ell@@ or @@P^\ell@@ represents the vectorized activations of a particular filter, convolved over all positions of the image.

Then the content loss is given by:

$$L_c = \frac{1}{2} \sum_{i,j} (F_{ij}^{\ell} - P_{ij}^{\ell})^2$$

We are only concerned with the "conv4_2" layer of the model.

<script src="https://gist.github.com/ArnoutDevos/6bcc3e5f5baff5703aee969150c7acfc.js"></script>

## Style Loss
Now we can tackle the style loss of equation (5) from the paper. For a given layer @@\ell@@, the style loss is defined as follows:

First, compute the Gram matrix G which represents the correlations between the responses of each filter, where F is as above. The Gram matrix is an approximation to the covariance matrix -- we want the activation statistics of our generated image to match the activation statistics of our style image, and matching the (approximate) covariance is one way to do that. There are a variety of ways you could do this, but the Gram matrix is nice because it's easy to compute and in practice shows good results.

Given a feature map @@F^\ell@@ of shape @@(1, M_\ell,  N_\ell)@@, the Gram matrix has shape @@(1, N_\ell, N_\ell)@@ and its elements are given by:

$$G_{ij}^\ell  = \sum_k F^{\ell}_{ik} F^{\ell}_{jk}$$

Assuming @@G^\ell@@ is the Gram matrix from the feature map of the current image, @@A^\ell@@ is the Gram Matrix from the feature map of the source style image, then the style loss for the layer @@\ell@@ is simply the Euclidean distance between the two Gram matrices:

$$E_\ell = \frac{1}{4 N^2_\ell M^2_\ell} \sum_{i, j} \left(G^\ell_{ij} - A^\ell_{ij}\right)^2$$

In practice we usually compute the style loss at a set of layers @@\mathcal{L}@@ rather than just a single layer @@\ell@@; then the total style loss is the weighted sum of style losses at each layer by @@w_\ell@@:

$$L_s = \sum_{\ell \in \mathcal{L}}  w_\ell E_\ell$$

In our case it is a summation from conv1_1 (lower layer) to conv5_1 (higher layer). Intuitively, the style loss across multiple layers captures lower level features (hard strokes, points, etc) to higher level features (styles, patterns, even objects). 
<script src="https://gist.github.com/ArnoutDevos/0cb0328aa09633d0abb057de7362234d.js"></script>

## Building a TensorFlow session and model
<script src="https://gist.github.com/ArnoutDevos/f42414e971d159a42e34255b35369701.js"></script>

## Total loss
The last thing we need to do is the most important: define what loss function to optimize. The total loss function is a weighted sum of the content loss and style loss by @@\alpha@@ and @@\beta@@ respectively.

$$L = \alpha L_c + \beta L_s$$

<script src="https://gist.github.com/ArnoutDevos/92c9a9a096762a5bf668e0889840d7e6.js"></script>

## Run
Now we run the model which saves the resulting image every 50 iterations. You can find those intermediate images in the `output/` folder.

<script src="https://gist.github.com/ArnoutDevos/ca35f2f5ac8c860f3a171eba113fa9c5.js"></script>

Note on that it usually takes almost half an hour to run 500 iterations on a 2015 Macbook Pro 15" base model CPU. Personally I preferred using Google Cloud Platform which speeded up calculations a lot.