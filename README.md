# Image Classification

I took on this project after Cats Vs. Dogs to further explore the power of Convolutional Neural Networks. After following the tutorial under [Training Model](#training-model), I gained a basic understanding of the structure necessary to predict objects from the CIFAR-10 dataset, where my model achieved 78% training accuracy and 70% testing accuracy. I then went on to enhance the model based on more research, and my final model achieved **84% training accuracy and 78% testing accuracy**.

The details below are the steps I followed and notes I took throughout this project.

<br/>

## Background Understanding

[Tutorial Link](https://www.youtube.com/watch?v=zfiSAzpy9NM)
![CNN Diagram](diagram.png)

## Initial Setup

Run as Administrator:
```
python -m venv .\venv
.\venv\Scripts\Activate.ps1
pip install numpy matplotlib tensorflow scikit-learn
```

## Training Model

[Tutorial Link](https://www.youtube.com/watch?v=7HPwo4wnJeA)

## Enhancing Model

References: [Link](https://medium.com/@dipti.rohan.pawar/improving-performance-of-convolutional-neural-network-2ecfe0207de7), [Link](https://medium.com/mlearning-ai/7-best-techniques-to-improve-the-accuracy-of-cnn-w-o-overfitting-6db06467182f)

### Starting Model Analysis

Based on the tutorial:
- Train -> loss: 0.6388, accuracy: 0.7786
- Test  -> loss: 0.9121, accuracy: 0.6994

### Data Augmentation Analysis

[Random Flip](https://www.tensorflow.org/api_docs/python/tf/keras/layers/RandomFlip) felt like the most intuitive augmentation. However, surprisingly, the accuracy of the training data went down and the accuracy of the test data went up, both only marginally:
- Train -> loss: 0.6986 - accuracy: 0.7585
- Test  -> loss: 0.8344 - accuracy: 0.7181

[Random Rotation](https://www.tensorflow.org/api_docs/python/tf/keras/layers/RandomRotation) was considered next at factors of 0.2 and 0.05, but the model did not improve.

[Random Zoom](https://www.tensorflow.org/api_docs/python/tf/keras/layers/RandomZoom) was considered after at factors of 0.2 and 0.05, but the model did not improve again (reasonably so).

### Deeper Network Analysis

Added a third section in the CNN with a Conv2D layer of 128 filters followed by a MaxPooling2D layer:
- Train -> loss: 0.6301 - accuracy: 0.7814
- Test  -> loss: 0.7571 - accuracy: 0.7433

### Tuning Parameters Analysis

Increased number of epochs to 20:
- Train -> loss: 0.4643 - accuracy: 0.8370
- Test  -> loss: 0.8012 - accuracy: 0.7453
- For epoch 1 to 10, the unit change for training accuracy was >1%. However, for epoch 11 to 20, the unit change for training accuracy was <1%.
- Note that the testing accuracy increased <1% after these 10 more epochs.
- For these reasons, the number of epochs was reset to 10.

Changed optimizer to SGD:
- Train -> loss: 1.0382 - accuracy: 0.6365
- Test  -> loss: 1.0960 - accuracy: 0.6118

How to use SGD in tensorflow?
[Link](https://medium.com/analytics-vidhya/why-use-the-momentum-optimizer-with-minimal-code-example-8f5d93c33a53)
- Set momentum=0.9 and nesterov=True:
    - Train -> loss: 0.6494 - accuracy: 0.7738
    - Test  -> loss: 0.8214 - accuracy: 0.7288
- Added learning_rate=0.02
    - Train -> loss: 0.8243 - accuracy: 0.7225
    - Test  -> loss: 1.0006 - accuracy: 0.6729

How to choose best optimizer for training model?
[Link](https://towardsdatascience.com/7-tips-to-choose-the-best-optimizer-47bb9c1219e)
- Based on this, decided to leave optimizer as Adam

### Deeper Network Analysis 2

Removed first Dense layer (64 units):
- Train -> loss: 0.6537 - accuracy: 0.7743
- Test  -> loss: 0.7861 - accuracy: 0.7350

Instead changed first Dense layer to 128 units:
- Train -> loss: 0.5957 - accuracy: 0.7917
- Test  -> loss: 0.7920 - accuracy: 0.7361

Instead changed first Dense layer back to 64 units:
- Train -> loss: 0.6340 - accuracy: 0.7810
- Test  -> loss: 0.7882 - accuracy: 0.7309

Based on above observations, first Dense layer was set to 128 units.

### Batch Normalization Analysis

What is batch normalization?
[Link](https://towardsdatascience.com/batch-normalization-in-3-levels-of-understanding-14c2da90a338)

How to use batch normalization?
[Link](https://www.youtube.com/watch?v=yXOMHOpbon8)
- After all activation functions:
    - Train -> loss: 0.5333 - accuracy: 0.8158
    - Test  -> loss: 0.7948 - accuracy: 0.7288
- After all activation functions and pooling:
    - Train -> loss: 0.5165 - accuracy: 0.8216
    - Test  -> loss: 0.8295 - accuracy: 0.7170
- Before all activation functions:
    - Train -> loss: 0.4913 - accuracy: 0.8271
    - Test  -> loss: 0.7193 - accuracy: 0.7622

Based on above observations, decided to keep batch normalization layers before all activation functions only (that is, not after pooling).

Note: Realized after this analysis that change from [Deeper Network Analysis 2](#deeper-network-analysis-2) was not yet made:
- Train -> loss: 0.4550 - accuracy: 0.8414
- Test  -> loss: 0.6798 - accuracy: 0.7676

### Dropout Analysis

How to use dropout in tensorflow?
[Link](https://saturncloud.io/blog/how-to-properly-use-dropout-in-tensorflow-a-guide-for-data-scientists/)
- Before each batch normalization with rate=0.2:
    - Train -> loss: 0.6453 - accuracy: 0.7763
    - Test  -> loss: 1.1098 - accuracy: 0.6026
- After flatten layer with rate=0.2:
    - Train -> loss: 0.5786 - accuracy: 0.8001
    - Test  -> loss: 0.7426 - accuracy: 0.7512
- After each activation function with rate=0.2:
    - Train -> loss: 0.6349 - accuracy: 0.7785
    - Test  -> loss: 0.9254 - accuracy: 0.6802

These observations seem to confirm the tips provided in the resource, which warns from using dropout and batch normalization together. For these reasons, dropout layers were not added to the model.

### Tuning Parameters Analysis 2

Increased number of epochs to 12 to achieve 85% accuracy:
- Train -> loss: 0.4192 - accuracy: 0.8533
- Test  -> loss: 0.7761 - accuracy: 0.7414

Interestingly, although the training loss and accuracy improved, the testing loss and accuracy performed poorly. This is a sign of overfitting, so the number of epochs was reset to 10.

### Final Model

Train   -> loss: 0.4638 - accuracy: 0.8385\
Test    -> loss: 0.6747 - accuracy: 0.7770
