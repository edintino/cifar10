# Cifar 10

## Aims

Use a subset (1000 images) of the cifar10 dataset to obtain an as accurate and robust model as possible with transfer learning.

## Enviroment set up

```console
edintino@docler:~$ conda env create -f edintino_cifar10.yml
edintino@docler:~$ conda activate edintino_cifar10
```

## Pretrained model selection

As far as I know the deeper a CNN model the more abstract features we can abstract with the convolutional layers. Furthemore as I am not going to train the body of the CNN ideally I would like to have as many extracted features as possible and see wheter among those there are more important ones also. Thus I will look for a pretrained body with high feature output, acceptable run time on my machine and performance on a simple head.

I am going to compare multiple pretrained bodies and change the head into a simple linear layer with as many outputs as many classes there are in the CIFAR10. The head will be trained only for one epoch just to see how they perform in order of magnitude relative to each other. The bodies I compared are

- vgg16,
- vgg13_bn,
- alexnet,
- densenet,
- vgg19,
- vgg19_bn.

Among which I decided to go with **vgg16** as it has the highest number of outputs, decent base accuracy it achieved, shorter run time among the longer ones. Alexnet would have been also interesting as we would have to give up on a bit of accuracy, but it runs 3 times faster. In addition densenet would have been also a nice choice as the train and test accuracies are really close, thus it is reassuring that we are not overfitting on the train set on this model.

## Subset selection

First I had the idea to look for images of different classes where the output vector after the convolution layers are as close as possible in L2 norm and as far as possible within the same classes and optimise the subset with this idea. However it was outperformed by random sampling, thus I did not carry it forward.

Later I decided to go with standard undersampling technique, a bit varied for images using [tomek links](https://www.scirp.org/journal/paperinformation.aspx?paperid=60996). This method uses knn approach and looks for nearest neighbours with different classes. However unfortunately I did not have enough memory in my GPU to load in all the training dataset and it makes sense if we load the most data in and get the closest links. Note that the links would have been considered for the output of the convolutional layers. I would have looked up let us say the three nearest neighbours and looked for places where knn tells me that it beongs to the category 1 however in realty we do know the label and it is not in there, than it would have been selected for the optimal training subset.

In the end I decided to go with a **random sample for 750 images** and use **active learning with margin sampling** in batches of 50 fed into the dataset at each iteration and evaluated after each batch of 50 whether we need to change the fed in classes. With margin sampling we look for the classes between which the model is the least confident, i.e. the distance between the first and second classes is the smallest and we try to imrpove it with feeding more data into the dataset of these classes.

## Models

### Baseline model

I created a baseline model for comparison, it has a random sample to learn from, with a basic head. I evaluated its performance using **train-test accuracy** and robustness with **fgsm "accuracy"**.

### Robust model

In this scenario I used the active learning described above, I did change the head slightly also. Furthermore the training dataset was augmented with colorjitter, rotation and horizontal flip next to projected gradient descent training.

## Results



## Deployment

