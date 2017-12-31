---
mathjax: true
title: Modeling Language with Recurrent Neural Networks
---
In this post a basic recurrent neural network (RNN), a deep neural network structure, is implemented from scratch in Python. An improved version, the Long Short-Term Memory (LSTM) architecture, that can deal better with long-term information is implemented as well.

## Recurrent Neural Network (RNN)

# Forward pass
A forward pass of an RNN takes as input features both the ones in `x` and the ones that are the output of the previous stage, `prev_h` in this case. A tangens hyperbolicus activation function is used.

$$h_{next} = \tanh(W_x x + W_h h_{prev} + b)$$

Note that, although they could be concatenated and multiplied by one big weight matrix, for interpretability a separation in weights @@W_x@@ and @@W_h@@ is chosen.

<script src="https://gist.github.com/ArnoutDevos/9c71c0114ebfa4f83ac2d74711e53f6d.js"></script>

In the code, `x`  is the input feature vector of size `(N,D)` and `prev_h` is the hidden state from the previous timestep of size `(N,H)`. The `meta` variable stores those variables needed for the backward pass.

# Backward pass
In the backward pass of the RNN, all the necessary gradients are calculated to update the weights in both the @@W_x@@ and @@W_h@@ matrices. Here the variables stored in the `meta` variable of the forward pass are used again. For completeness, all gradients are calculated even when they don't necessarily make sense in the first place such as @@db@@.

<script src="https://gist.github.com/ArnoutDevos/29f6afb5b6da3091a7c4696e31f85004.js"></script>

In the code,

* `dnext_h`: gradients with respect to the next hidden state,
* `meta`: the variables needed for the backward pass,
* `dx`: gradients of the input features `(N, D)`   
* `dprev_h`: gradients of previous hiddel state `(N, H)`
* `dWh`: gradients w.r.t. feature-to-hidden weights `(D, H)`
* `dWx`: gradients w.r.t. hidden-to-hidden weights `(H, H)`
* `db`: gradients w.r.t bias `(H,)`

## Long Short-Term Network