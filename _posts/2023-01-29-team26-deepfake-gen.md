---
layout: post
comments: true
title: Deepfake Generation
author: Freddy Aguilar, Luis Frias
date: 2022-01-30
---

> Deepfake Generation
<!--more-->
{: class="table-of-content"}
* TOC
{:toc}


> We chose to explore the topic of [deepfake generation](https://github.com/aerophile/awesome-deepfakes). 

# Introduction
Recent years have seen a tremendous advancement in deep fake technology, with new models and algorithms emerging that enable more convincing and realistic picture swaps. There are many uses for the skill of seamlessly and convincingly swapping out a person's face, from entertainment to political influence. But as deep fake technology spreads, so is the need to comprehend its strengths and weaknesses. The first model is based on Generative Adversarial Networks (GANs), and the second model is based on a modified version of the VGG16 CNN architecture. In this article, we will compare and contrast these two deep fake generation methods for picture swap. In order to learn more, we will evaluate each model's performance and take into account its advantages and disadvantages. We intend to provide light on the current state of deep fake creation for picture swap and add to ongoing discussions regarding its appropriate use by comparing these models side by side.

# Deep Fake Generation?
Deep fake generation is the process of producing synthetic media that closely resembles the appearance and behavior of actual people is known as "deep fake generation. This technology can produce incredibly lifelike images, films, and audio. For the intention of limiting the scope of the article, we will focus on pictures swaps. Face swapping is a specific type of deep fake generation that involves replacing one person's face with another person's face in an image or video. 

Face swapping typically entails feeding the network pairs of photos, each of which has the face of one person and the face of another. The network then learns to recognize the features and characteristics of each person's face and the relationship between them.

Once trained, the face swap model can be used to create new pictures or movies with the faces of two persons switched. It is feasible to create highly realistic face swaps that are challenging to discern from actual photographs by carefully tweaking the network's parameters after training it on a sizable collection of facial images.


# Setup and Data Preparation
The following dataset used is a subset of the the Presidents' Face Dataset which includes faces of Joe Biden, Donald Trump, and George W. Bush. In addition we chose select frames from both speechs from Trump and Biden
# Model Structure Comparison
 ### Faceswap's architecture is summarized as:
1. Encoder that gathers features of both faces input. The encoder has the following steps:
    * 3 Convolutional Layers
    ```
        var_x = Conv2DBlock(128, activation="leakyrelu")(var_x)
        var_x = Conv2DBlock(256, activation="leakyrelu")(var_x)
        var_x = Conv2DBlock(512, activation="leakyrelu")(var_x)
    ```
    * 2 Dense Layers
    ```
        var_x = Dense(self.encoder_dim)(Flatten()(var_x))
        var_x = Dense(4 * 4 * 1024)(var_x)
    ```
    * 1 Upscale layer
    ```
        var_x = UpscaleBlock(512, activation="leakyrelu")(var_x)
    ```
2. Decoder upscales ours latent space and creates an output similar to input image. The decoder has the following steps:
    * 3 Upscale layers
    ```
        var_x = UpscaleBlock(256, activation="leakyrelu")(var_x)
        var_x = UpscaleBlock(128, activation="leakyrelu")(var_x)
        var_x = UpscaleBlock(64, activation="leakyrelu")(var_x)
    ```
    * 1 Convolutional layer
    ```
        var_x = Conv2DOutput(3, 5, name=f"face_out_{side}")(var_x)
    ```

 ### DeepFaceLab's architecture is summarized as
 1. Encoder that gathers features of both faces input. The encoder has the following steps:
    * 4 Convolutional Layers
    ```
        var_x = Conv2DBlock(128, activation="leakyrelu")(input_)
        var_x = Conv2DBlock(256, activation="leakyrelu")(var_x)
        var_x = Conv2DBlock(512, activation="leakyrelu")(var_x)
        var_x = Conv2DBlock(1024, activation="leakyrelu")(var_x)
    ```
    * 2 Dense Layers
    ```
        var_x = Dense(self.encoder_dim)(Flatten()(var_x))
        var_x = Dense(8 * 8 * self.encoder_dim)(var_x)
    ```
    * 1 Upscale layer
    ```
        var_x = UpscaleBlock(self.encoder_dim, activation="leakyrelu")(var_x)
    ```
2. Decoder upscales ours latent space and creates an output similar to input image. The decoder has the following steps:
    * 3 Upscale layers
    ```
        var_x = UpscaleBlock(self.encoder_dim, activation="leakyrelu")(var_x)
        var_x = UpscaleBlock(self.encoder_dim // 2, activation="leakyrelu")(var_x)
        var_x = UpscaleBlock(self.encoder_dim // 4, activation="leakyrelu")(var_x)
    ```
    * 1 Convolutional layer
    ```
        var_x = Conv2DOutput(3, 5, name=f"face_out_{side}")(var_x)
    ```
# DeepFaceLab & Implementation

# FaceSwap & Implementation 
The folowing [colab](https://colab.research.google.com/drive/1_j_0N9uCR47ms5paXTKgQVTq1M4GX-9M?usp=sharing) will be used as the setup but there is also a [locally based](https://github.com/deepfakes/faceswap/blob/master/INSTALL.md) installation method based on your hardware configuration. 

**NOTE: Colab does not support deepfake training unless you pay premium and keep the session active throughout the whole training prcoess. The local based repo provided by u/deepfake is better or run the repo off a virtual machine with a GPU.**

The process can be divided into three parts:

1. Extract
    
    This step will take photos from a `src` folder and extract faces into an `extract` folder. In the extract folder, there will be all the faces recognized and used for training. Be sure to delete or remove any incorrectly identified faced. It is best to have a seperate folder for each person extracted.


2. Train

    This step will take photos from the `extract` folders containing pictures of both faces and train a model that will be saved inside the `models` folder. This step will be a long process and be sure to have an appropiate GPU. I used up all the credit of my GCloud VM and struggled with my local RTX 3070 due to low VRAM.


3. Convert

    This step will take photos or vidoes from any set in the `extract` folder and apply new faces into `converted` folder based on what model from the `models` folder was used.



# Quantitative Results Comparison

# Conclusion

# Video Demo
The following are videos of small speeches by Biden and Trump Side-by-Side between their originals and respective model.

## Biden's Speech
### Faceswap
<iframe width="560" height="315" src="https://www.youtube.com/embed/b2eK4vkO-vs" frameborder="0" allowfullscreen></iframe>

### DeepFakeLab
<iframe width="560" height="315" src="https://www.youtube.com/embed/uafp6ioelHU" frameborder="0" allowfullscreen></iframe>

## Trump's Speech
### Faceswap
<iframe width="560" height="315" src="https://www.youtube.com/embed/2OEhs59hy9k" frameborder="0" allowfullscreen></iframe>

### DeepFakeLab
<iframe width="560" height="315" src="https://www.youtube.com/embed/fEJp0K8WCk4" frameborder="0" allowfullscreen></iframe>


# References
[Fast Face-swap Using Convolutional Neural Networks](https://arxiv.org/abs/1611.09577) - [repo](https://github.com/deepfakes/faceswap#overview)

[DeepFaceLab: A simple, flexible and extensible face swapping framework](https://arxiv.org/pdf/2005.05535v4.pdf) - [repo](https://github.com/iperov/DeepFaceLab)

[Presidents' Face Datatset](https://www.kaggle.com/datasets/sergiovirahonda/presidentsfacesdataset)

