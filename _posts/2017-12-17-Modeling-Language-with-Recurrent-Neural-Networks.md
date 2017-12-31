---
mathjax: true
title: Modeling Language with Recurrent Neural Networks
---
In this post a basic recurrent neural network (RNN), a deep neural network structure, is implemented from scratch in Python. An improved version, the Long Short-Term Memory (LSTM) architecture, that can deal better with long-term information is implemented as well.

## Recurrent Neural Network (RNN)

# Forward pass
A forward pass of a recurrent neural network takes as input features both the ones in `x` and the ones that are the output of the previous stage, `prev_h` in this case. A tangens hyperbolicus activation function is used.

$$h_{next} = \tanh(W_x x + W_h h_{prev} + b)$$

Note that, although they could be concatenated and multiplied by one big weight matrix, for interpretability a separation in weights @@W_x@@ and @@W_h@@ is chosen.

<script src="https://gist.github.com/ArnoutDevos/9c71c0114ebfa4f83ac2d74711e53f6d.js"></script>

In the code, `x`  is the input feature vector of size `(N,D)` and `prev_h` is the hidden state from the previous timestep of size `(N,H)`. The `meta` variable stores those variables needed for the backward pass.

## Long Short-Term Network