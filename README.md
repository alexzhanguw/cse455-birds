# CSE 455 Birds Kaggle Competition Spring 2023!

### Video Deliverable
https://drive.google.com/file/d/14u5UdPLVrQGzENDHyoxZeAY0OOqUvpOE/view?usp=sharing

*UPDATE:* After recording this, I tried one final model with the remaining GPU Time I had left and achieved a score of 0.823. This used the same approach, except I swapped ResNet18 out for ResNet50. Most of the behavior was the same, but my training and validation accuracies with this model were actually a little bit worse. Nonetheless, this model still performed the best for the Competition, which I attribute to overfitting.

## Introduction

I really wanted to explore a new direction of computer vision outside of what we learned in class for this final project, but I couldn't find a group and wasn't ready to commit to an idea when project proposals were due. However, I really enjoyed participating in this Kaggle competition as well! This README will serve as my website deliverable which covers this project's problem description, previous work used, my approach, datasets used, my results, and some discussion regarding the overall project.

![c71-2](https://github.com/alexzhanguw/cse455-birds/assets/135690578/50dabb93-0ca0-4e56-9a3f-f0a1a996f5e3)

## Problem Description

I competed in the default final project. This final project was [a bird classification Kaggle competition](https://www.kaggle.com/competitions/birds23sp/overview), where the class was working with a provided dataset, consisting of 38,562 bird images in different training data input files and their corresponding species labels, with the goal of training a model that could accurately produce a predictions file for a provided test file containing unseen bird images. To be more specific, there were 555 given bird species, and our predictions file was aiming to achieve the highest accuracy in tagging each image in the test file to its corresponding label among the 555 given bird species. Our results were shown against our fellow classmates on the Kaggle competition leaderboard.

![Warbler-speciess-1080x675](https://github.com/alexzhanguw/cse455-birds/assets/135690578/d95a5890-24d6-415d-9401-bae5d4e6d7ac)

## Previous Work

As a starting point, I used code from the transfer learning notebooks provided on the course website: I mainly used [this notebook](https://colab.research.google.com/drive/1kHo8VT-onDxbtS3FM77VImG35h_K_Lav?usp=sharing) as a baseline and took parts such as testing for accuracy from [this notebook](https://colab.research.google.com/drive/1EBz4feoaUvz-o_yeMI27LEQBkvrXNc_4?usp=sharing). Specifically, from the first notebook, I closely followed the training model of ReNet18 as a starting point and the prediction process. However, I changed and tried out my own data augmentations and also split the training data set into a training set and validation set with a 90/10 split, which caused a deviation in how I was generating the dataloaders, though I did also use the first example notebook as a starting point for my data processing and loading. I also added an option to use the Adam optimizer instead of the provided optimizer parameters and an accuracy check at the end of each epoch to gauge whether I wanted to commit to a training run or not. This helped me save time and know whether there was a poor training run. There was also a seeding block that I followed; I found it very interesting to see how seeding can affect models and its effects in Kaggle competitions. [This](https://wandb.ai/sauravmaheshkar/RSNA-MICCAI/reports/How-to-Set-Random-Seeds-in-PyTorch-and-Tensorflow--VmlldzoxMDA2MDQy) is the website where I found this information. Seeding also allowed me to get deterministic runs, which helped me save some time when training. On top of these additions and changes, I tried a lot of hyperparameter tuning to see better results than the previous works I used, in terms of performance for this dataset.

![merb10108262035](https://github.com/alexzhanguw/cse455-birds/assets/135690578/1c232ab6-12c7-4f2e-897d-02168a127f18)

## My Approach

When I first started this project, I was a bit overwhelmed by what to do. This was also my first Kaggle competition so getting started was a bit daunting. From lectures, I knew that I wanted to try out a pretrained model and apply transfer learning to these to make my predictions. Since there was a provided notebook which did exactly that on the course website, I decided to use it as a baseline. I first made sure to understand what the notebook was doing and then looked into the provided data and made sure to understand what each folder in the input data was meant for. My initial approach was to try hyperparameter tuning. I used a grid search with the transfer learning to birds notebook mentioned above, now adjusted to use the Kaggle dataset, searching over epochs, learning rate, momentum, decay, and data augmentation parameters. Unfortunately, this was not very successful and ended up burning significant GPU time. This was frustrating, and I ended up deleting this original notebook so I no longer have my original attempt's results, but I think my validation accuracy ended at round 65% or lower. I made a validation split from the original training set to better gauge my progress, this also made the training a tiny bit faster since there was less data in the training set. I next tried to switch the optimizer to Adam, but this also did not help much. I also tried to bring in a [new bird dataset](https://www.kaggle.com/datasets/gpiosenka/100-bird-species) but could not match the species and labels between the datasets since some were missing, so I gave up on integrating the new dataset. My first significant progress was from looking more into data augmentation. Data augmentation allows "new" training data to be generated. This creates a larger dataset to train with. Data augmentation has also been shown to prevent overfitting in models while still being able to increase their accuracy. Specifically, I added a new data augmentation for my data to be resized and cropped, horizontally flipped, rotated, "color jittered," and normalized by the [PyTorch ImageNet standard](https://github.com/pytorch/examples/blob/main/imagenet/main.py#L233-L234). This allowed me to achieve above 70% on my validation accuracy. With this progress, I decided that I would try starting to train for a series of epochs with the larger data augmentation provided in the baseline transfer learning to birds notebook, and then switching to the new data augmentation I experimented with and training the same previously trained model with this new data augmentation for another series of epochs. In the end, I added another data augmentation on top of this and then trained the same model with the final augmented dataset. The difference between the second and final data augmentation was the parameters for the scaling transformation. I also increased the number of epochs, reduced learning rates as epochs increased, and increased decay as epochs increased to reduce overfitting and get better results. I also added seeding as stated above. My final change was switching ResNet18 out for ResNet50. I only had enough GPU to train once with ResNet50, and while the training accuracy with my same approach was lower, it performed better on the competition. With these changes, I was able to reach over 80% on my validation accuracy and a final Kaggle score of 0.823.
![new-study-changes-thinking-on-evolution-of-flightless-birds-650x432](https://github.com/alexzhanguw/cse455-birds/assets/135690578/33940107-3811-45eb-9b36-288a3809c2f0)

## Datasets

For this competition, I only used the provided [dataset](https://www.kaggle.com/competitions/birds23sp/data). As stated above, I tried to bring in another bird classification dataset from Kaggle, but their setups were different, and it would be difficult to have the other dataset map in the same format as the provided dataset. Thus, I did not bring in this dataset, but in the future, I would like to try harder to incorporate this data file along with the provided dataset. The provided dataset consists of the following: a test folder, which consists of many bird images with no given corresponding labels and which our model makes its predictions on; a train folder, which consists of 555 subfolders, each with numerous images of birds of a specific species; a labels file, containing the correct mapping of training images to their bird class labels (which are represented by numbers); a names file, containing the names of all the species bird species provided in the dataset; and finally a sample file, which is a sample predictions file.

![26tb-cockatoos-02-superJumbo](https://github.com/alexzhanguw/cse455-birds/assets/135690578/d38a3305-7a77-434f-8482-1911d77d22cb)

## Results

For my best results, I received an evaluation score of 0.82300 for the public Kaggle score. On multiple runs, I have achieved training accuracy of 99% and have reached in the high 80%'s and even low 90%'s for my validation accuracy on multiple runs as well. In my most recent run, with ResNet50, my training accuracy was 0.978908 while my validation accuracy was 0.870625. My ending loss was 0.378, though I had lower losses during training.

![6470ee02085acb0018feaf1a](https://github.com/alexzhanguw/cse455-birds/assets/135690578/2e24dec3-1ed2-4ec8-b28e-0e4db203933a)

## Discussion

My first problem I encountered was trying to bring in new datasets, since the provided dataset was provided in a specific format. Like I mentioned above, this made it difficult in trying to format the new datasets to match the provided dataset. The problem that initially scared me the most was forgetting to apply the correct data augmentations to my test set as well. While not all of the same data augmentations used in the training set should be applied to the test set, certain ones should, since my model was learning features that may have been affected by these augmentations. Thus, when I did not augment my test data, my results and predictions were extremely poor, which was a little frightening when I first saw them. Another problem I faced was trying to have checkpoint .pkl files persist in a folder I created in the working directory of the notebook. I wanted to use this files to save me time in training by being able to start from the model at a previously trained epoch. I spent a non-trivial amount of time trying to solve this issue, but in the end, I used seeding to make my results deterministic, and I also realized that I would be training in a single run, so I didnt need to have my checkpoint .pkl files to persist after a run. If I was to keep working on this project, I would spend more time trying to bring in the dataset I have mentioned. I feel that providing new data to my model could increase my accuracy, since I am currently training for above 15 total epochs, which increases the chance of overfitting. With new and more data, I feel that I could decrease the number of epochs, while still being able to achieve better accuracy. Another future step I would take is to explore more and different pretrained models, such as ResNet models apart from ResNet18, EffecientNet, or VGG19. I was not able to do so much with this exploration this time, because I was committed mostly to ResNet18, and only began briefly experimenting with ResNet50. Unfortunately, I ran out of GPU quota in Kaggle of 30+ hours, so this exploration was somewhat shortlived. However, I was able to get one training run in with ResNet50, which performed worse in my training and validation accuracy, but did better in the overall Kaggle competition. At that point, I decided that I should just use what I have already done and that my laptop (which sounded like it was going to implode on multiple occasions) should have a rest. In the future, I still think it would be interesting to compare the results of even more pretrained models, though. In the end, the main novelty of my approach, on top of hyperparameter tuning, seeding and sing different pretrained models, was adding multiple and different training layers, each with different data augmentations. I believe that it was beneficial, since this approach produced my best results and was better in comparison to when I stuck with one of the single data augmentations I had used and trained for the same number of overall epochs. Ultimately, I think that I completed the task/problem/challenge to a satisfactory extent, as I have achieved a public score of above 80% for the Kaggle competetion.

I have included [my Kaggle notebook](https://github.com/alexzhanguw/cse455-birds/blob/main/birds.ipynb) in this repository for reference.

![360_F_419845144_4O5PeH7pjeGXu5sL1T9OKk5LEOeiZikU](https://github.com/alexzhanguw/cse455-birds/assets/135690578/25b0a878-e55f-4e5d-b62d-092835a8d5b4)

