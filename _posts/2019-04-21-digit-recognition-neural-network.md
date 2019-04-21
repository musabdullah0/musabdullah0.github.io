---
layout: post
title: "digit recognition neural network"
date: 2019-04-21
img: "/assets/neural-network.jpeg"
desc: "feedforward neural network implementing stochastic gradient descent"
mathjax: true
---
Image recognition is an ability of the brain taken for granted until it is
further explored. The number of neurons working in tandem to achieve
such a miraculous feat of recognizing a scribble on a sheet of paper as a
number is amazing.

What I've been working on over the past week or so is
a much more simplified problem. Given a 28 x 28 pixel greyscale image of a
centered digit, I wanted to predict the number it represented. To do this,
I used the MNIST database of handwritten digits which had 60,000 training
images which I would use to teach my neural network to recognize digits and
10,000 test images which I would use to gauge my network's accuracy.
I followed [Michael Nielson's online book](http://neuralnetworksanddeeplearning.com/chap1.html)
closely to write the Python script and also found [3Blue1Brown's series on
neural networks](https://www.youtube.com/watch?v=aircAruvnKk) to be extremely
helpful. I'll summarize what I learned here, but I'll leave the full explanation
to Michael Nielson.

Our training data consists of 28 x 28 pixel images, so we'll flatten the images
into a 784 entry long vector which will serve as our input layer to the network.
We'll have a hidden layer in between with an arbitrary number of neurons and the
output layer will consist of 10 neurons representing the 10 digits: the one with
the highest activation will be the network's guess at the number fed in.

Every layer is connected to the layer in front with a certain weight loosely
representing it's importance to the neuron it is connected to. There is also a
bias associated with each neuron which is added to the *weighted sum* of the
inputs. If this sum is greater than 0, the neuron will output a 1 and if if it
is less than or equal to 0, a 0 is outputted. This is done by feeding the
weighted sum through an activation function such as the sigmoid(large positive
outputs map to a 1, while large negative outputs map to a 0).

$$ \sigma(x) = \frac{1}{1 + e^{-x}} $$

We also need some sort of function to gauge how good/bad our network is doing
as an average over all the training examples and as a function of the network
so that we can take a derivative of it later when computing the gradient.

$$ C = \frac{1}{2n} \sum_x || (y(x) - a^L(x)) ||^2 $$

  * The 1/2 is there just to cancel out the coefficient of the derivative and
  make it simpler
  * the 1/n and the sum are just taking an average
  * the errors are squared to penalize larger error by a greater proportion than
  small errors
  * the big L superscript on a means the last layer

Now we're ready to begin:

## Step 1: forward propagation
We start with random weights and biases over a uniform distribution from 0 to 1
and feed the input vector all the way through. A notation we use to simplify stuff further is the superscript 'l' that tells us which layer we are in. We also introduce an intermediate variable z that stores the weighted input to a neuron prior to feeding it thru the sigmoid function. a is the output vector.

$$ z^l \equiv w^l a^{l-1} + b^l $$

$$ a^l = \sigma(z^l) $$

## Step 2: find output error

We need a way of blaming neurons for the error based on how much they messed it
up, so we introduce a new variable delta that tells you the error of a neuron.

$$ \delta ^l_j \equiv \frac{\partial C}{\partial z_j^l} $$

This makes sense because if the derivative of the cost function with respect to
a neurons weighted input is high, it will cause a big change (the neuron really
messed up) and if it low, then the neuron didn't do anything wrong (wasn't
his fault). We want to find the error in the output layer as a vector, so let's
take that derivative.

rewriting the derivative with respect to the output activations...

$$ \delta_j^L = \frac {\partial C}{\partial a_j^L}
  \frac {\partial a_j^L}{\partial z_j^L}$$

since $ a_j^L = \sigma(z_j^L)$

chain rule will give us...

$$ \delta_j^L = \frac {\partial C}{\partial a_j^L}
  \sigma \prime (a_j^L)$$

written more simply: $ (a^L - y) \odot  \sigma \prime (z^L)$

## Step 3: backpropagate error

All we know so far is the error in the output neurons, so we could mess with the
weights in the connections between the hidden neurons and the outputs and the
output biases, but that isn't all we have. We need to calculate the errors in the
layers before the output layer and then start tuning the weights and biases.

$$ \delta^l = ((w^{l+1})^T) \delta^{l+1})\odot \sigma \prime (z^l) $$

I'll be honest, I'm not 100% on the proof for this, but the gist is that
multiplying the transpose of the next weight matrix moved the error from that
layer back to the current layer, and then chain rule again will put the sigma
prime at the end.

## Step 4: calculate gradient

Now that we know who (which neurons) is responsible and by how much, we have to
compute the gradient of the cost function. This is the essence of **gradient
descent** because if we have a positive gradient, we want to move in the opposite
direction proportionally to the gradient such that we reach a minimum. This is
given by...

$$ \frac{\partial C}{\partial w^l_{jk}} = a_k^{l-1} \delta^l_j $$

and

$$ \frac {\partial C}{\partial b_j^l} = \delta^l_j $$

## Step 5: update weights and biases

Since we have 60,000 training examples, calculating the gradient for every single
one, then averaging them, and then updating the weights and biases will take ages.
This is why we do **stochastic gradient descent**: taking *mini-batches* of size m,
calculate the gradient for them, update the weights and biases, and then move to
the next batch. We update according to these rules:

$$ w^l -> w^l - \frac{\eta}{m} \sum_x \delta^{x,l}(a^{x,l-1})^T $$

$$ b^l -> b^l - \frac{\eta}{m} \sum_x \delta^{x,l} $$

where eta is the **learning rate**, a constant that tells you by what
factor to change the weights and biases.
