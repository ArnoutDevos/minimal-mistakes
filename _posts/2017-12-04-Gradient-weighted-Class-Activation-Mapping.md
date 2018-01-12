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
categories: Project
published: false
title: Grad-CAM in TensorFlow: what does a neural network look at?
---
Grad-CAM is a technique for "visually interpreting" the predictions of a Convolutional Neural Network (CNN)-based model. This technique essentially uses the gradients of any target concept (a predicted class such as "cat"), flowing into the final convolutional layer to produce a coarse localization map attending regions in the image that are important for prediction of the concept.

This implementation follows the original paper of Grad-CAM called ["Visual Explanations from Deep Networks via Gradient-based Localization"](https://arxiv.org/pdf/1610.02391.pdf).

<!---
A (less verbose) runnable Python file can be found [on GitHub](https://github.com/ArnoutDevos/Grad-CAM).
-->

## Image Reading and Manipulation
We will use the OpenCV library to read in images, crop them first and then resize them. This is necessary in order for the images to be used with the pretrained VGG16 neural network.

<script src="https://gist.github.com/ArnoutDevos/3d325a0b442251b6648f7983259e90ee.js"></script>



## Guided Backpropagation
One of the principles of Grad-CAM is *guided* backpropagation. For this purpose we want to respecify the TensorFlow RELU activation function gradient, which is used to backpropagate in our deep neural network classifier.

<script src="https://gist.github.com/ArnoutDevos/909b3a862d834f31b901c104dde692bc.js"></script>

