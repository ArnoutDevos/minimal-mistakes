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

A (less verbose) runnable Python file can be found [on GitHub](https://github.com/ArnoutDevos/Grad-CAM).

## Model
