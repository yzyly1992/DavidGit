### Deep learning

#### Day1

##### Preparation

Operation System: Linux(Best) -- Ubuntu, Linux Mint or Other Debian; Mac OS; Windows. This tutorial will use Ubuntu as example.

If you not install Python3 on your system, please install the latest version:

```shell
sudo apt-get install python3
```

Baken: Tensorflow(GPU better than CPU)

[[Tensorflow]](https://www.tensorflow.org/) is a popular open source machine learning framework.

We also need pip3 and setuptools to install `Tensorflow` successfully:

```shell
sudo apt-get install python3-pip
sudo apt-get install python3-setuptools
```

Then, use the pip3 to install the `Tensorflow` accordingly:

Attention: GPU packages require a [CUDA®-enabled GPU card](https://www.tensorflow.org/install/gpu). Please check before install.

```shell
# Current release for CPU-only
sudo pip3 install tensorflow

# Nightly build for CPU-only (unstable)
sudo pip3 install tf-nightly

# GPU package for CUDA-enabled GPU cards
sudo pip3 install tensorflow-gpu

# Nightly build with GPU support (unstable)
sudo pip3 install tf-nightly-gpu
```

Core Library: Keras

Keras is a high-level neural networks API, written in Python and capable of running on top of [TensorFlow](https://github.com/tensorflow/tensorflow), [CNTK](https://github.com/Microsoft/cntk), or [Theano](https://github.com/Theano/Theano).

Install Keras through pip3:

```shell
sudo pip3 install keras
```



#### Day2

##### IMDB Movie reviews sentiment classification

Dataset of 25,000 movies reviews from IMDB, labeled by sentiment (positive/negative). Reviews have been preprocessed, and each review is encoded as a [sequence](https://keras.io/preprocessing/sequence/) of word indexes (integers). For convenience, words are indexed by overall frequency in the dataset, so that for instance the integer "3" encodes the 3rd most frequent word in the data. This allows for quick filtering operations such as: "only consider the top 10,000 most common words, but eliminate the top 20 most common words".

As a convention, "0" does not stand for a specific word, but instead is used to encode any unknown word.

