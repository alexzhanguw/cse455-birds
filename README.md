# CSE 455 Birds Kaggle Competition Spring 2023!

## Introduction

I really wanted to explore a new direction of computer vision outside of what we learned in class for this final project, but I couldn't find a group and wasn't ready to commit to an idea when project proposals were due. However, I really enjoyed participating in this Kaggle competetion as well! This README will serve as my deliverable website which covers this project's problem description, previous work used, my approach, datasets used, my results, and some discussion regarding the overall project.

![c71-2](https://github.com/alexzhanguw/cse455-birds/assets/135690578/89a5f628-11c7-43b4-8474-0aea38669203)

## Problem Description

I competed in the default final project. This final project was [a bird classification Kaggle competition](https://www.kaggle.com/competitions/birds23sp/overview), where the class was working with a provided dataset, consisting of 38,562 bird images in different training data input files and their corresponding species labels, with the goal of training a model that could accurately produce a predictions file for a provided test file containing unseen bird images. To be more specific, there were 555 given bird species, and our predictions file was aiming to achieve the highest accuracy in tagging each image in the test file to its corresponding label among the 555 given bird species. Our results were shown against our fellow classmates on the Kaggel competition leaderboard.

![Warbler-speciess-1080x675](https://github.com/alexzhanguw/cse455-birds/assets/135690578/15c6b304-ae73-47cc-be21-1d3e9135e1bc)

## Previous Work

As a starting point, I used code from the transfer learning notebooks provided on the course webiste: I mainly used [this notebook](https://colab.research.google.com/drive/1kHo8VT-onDxbtS3FM77VImG35h_K_Lav?usp=sharing) as a baseline and took parts such as testing for accuracy from [this notebook](https://colab.research.google.com/drive/1EBz4feoaUvz-o_yeMI27LEQBkvrXNc_4?usp=sharing). Specifically, from the first notebook, I closely followed the data augmentation and dataloader generation. However, I changed and tried out my own data augmentations and also split the training data set into a training set and validation set with a 90/10 split, which caused a deviation in how I was generating the dataloaders. I also followed the training function closely, but I added an option to used the Adam optimizer instead of the provided optimizer parameters and an accuracy check at the end of each epoch to gauge whether I wanted to commit to a training run or not. There was also a seeding block that I followed; I found it very interesting to see how seeding can affect models and its effects in Kaggle competitions. [This](https://wandb.ai/sauravmaheshkar/RSNA-MICCAI/reports/How-to-Set-Random-Seeds-in-PyTorch-and-Tensorflow--VmlldzoxMDA2MDQy) is the website where I found this information. Seeding also allowed me to get determinstic runs, which helped me save some time when training.

![merb10108262035](https://github.com/alexzhanguw/cse455-birds/assets/135690578/4d57f716-94c9-45a6-874d-4d416f27a904)

## My Approach

When I first started this project, I was a bit overwhelmed by what to do. This was also my first Kaggle competetion so getting started was a bit daunting. From lectures, I knew that I wanted to try out a pretrained model and apply transfer learning to these to make my predictions. Since there was a provided notebook which did exactly that on the course website, I decided to use it as a baseline. I first made sure to understand what the notebook was doing and then looked into the provided data and made sure to understand what each folder in the input data was meant for. My intial approach was to try hyperparameter tuning. I used a grid search with the transfer learning to birds notebook mentioned above, now adjusted to use the Kaggle dataset, searching over epochs, learning rate, momentum, decay, and data augmentation parameters. Unfortunately, this was not very successful and ended up burning significant GPU time. This was frustrating, and I ended up deleting this original notebook so I no longer have my original attmept's results, but I think my validation accuracy ended at round 65%. I made a validation split from the original training set to better gauge my progress, this also made the training a tiny bit faster since there was less data in the training set. I next tried to switch the optimizer to Adam, but this also did not help much. I also tried to bring in a [new bird dataset](https://www.kaggle.com/datasets/gpiosenka/100-bird-species) but could not maatch the species and labels between the datasets since some were missing, so I gave up on integrating the new dataset. My first significant progress was from looking more into data augmentation. Data augmentation allows "new" training data to be generated. This creates a larger dataset to train with. Data augmentation has also have been shown to prevent overfitting in models while still being able to increase their accuracy. Specifically, I added a new data agumentation for my data to be resized and cropped, horizontally flipped, rotated, "color jittered," and normalized by the [PyTorch ImageNet standard](https://github.com/pytorch/examples/blob/main/imagenet/main.py#L209-L214). This allowed me to achieve a validation score of above 70 on my validation accuracy. With this progress, I decided that I would trya starting to train for a series of epochs with the larger data augmentation provided in the baseline transfer learning to birds notebook, and then switching to the new data augmentation and training the same previously trained model with this new data augmentation for another series of epochs. I finally added another data augmentation on top of this and then trained the same model with the final augmented dataset. The difference between the second and final data augmentation was the parameters for the scaling transformation. I also increased the number of epochs, reduced learning rates as epochs increased, and increased decay as epochs increased to reduce overfitting and get better results. I also added seeding as sated above. With these results, I was able to reach over 80% on my validation accuracy.

![new-study-changes-thinking-on-evolution-of-flightless-birds-650x432](https://github.com/alexzhanguw/cse455-birds/assets/135690578/60649b73-a350-4298-909e-f97525aeb3eb)
