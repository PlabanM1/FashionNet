# FashionNet
Fashion recommender system using deep learning 

# Objectives :
In FashioNet we aim to build a fashion recommendation system capable of learning a person’s clothing style and preferences by extracting the a variety of attributes from his/her clothing images. These attributes are then fed to a similarity model to retrieve most closest similar images as recommendations.

# Approach:
We planned to train a convolutional deep net, capable of performing multi-class, multi-label classification. So each clothing article can have one or more clothing attributes attached to it, with each attribute itself having classes. We decided to go for attributes like Pattern [checkered, striped, floral, graphic, solid], Fabric [cotton, chiffon, lace, wool, silk] , Style [casual, formal, party, winter, summer], Shape [full-sleeve, half-sleeve, sleeveless] . For each clothing image the network would output, what we’d like to call, a Fashion Vector. A Fashion Vector is basically a vector with attributes as the dimensions and the attribute class as the value. It’d look like [attribute1_class, attribute2_class, attribute3_class, ...] [checkered, cotton, formal, ...]. These fashion vectors will be used to create a similarity model to provide recommendations.

# Dataset :
The final dataset that we used was comprised of about 50000 images that we collected from the internet by scraping the web and various clothing retail sites. The dataset collected was highly specific to train our models . We listed the specific features required to form our feature vector and extracted images specific to the classes of this features. We invested heavily on data preprocessing so that the network would not have any data specific issues. We did a automated curating and cleaning of data to eliminate duplicates and non relevant images. We then did a manual check to eliminate images that were not specific to the class they were meant to belong to for training. We ran the data sets on simple neural networks to check if they performed well enough to be used in the main model. Once we were satisfied with the performance , we used the images as the training and validation dataset and separated out a well represented section for test. (7:1.5:1.5)

# Implementation
To implement the proposed model , we needed to build models to initially classify the categories of features . Using these models, we form the fashion vector i.e a vector with all categories of features classified for each image . With these vectors ,w e build a style profile for a user based on the input images . We match this profile with fashion vectors of images in repository to form the best suited recommendations

a. Network architecture
We use Keras wrapper for tensorflow and VGG16 model as the base of the network. The base of our network is VGG16. On top of the network , we add a customized dense layer of 512 neurons with RELU activation. For the final classification, we use a softmax layer of n output neurons where n is the no of subclasses for each category of feature We use pre-trained VGG16 model on ImageNet as the starting weights / parameters .

Now, we build m such networks , each to train one category of feature (where m is the total no of categories of features , 5 in our case) and use a stacked version of these networks .

With this stacked CNNs , each input image entered is classified into one of the classes for each category , these classifications are then combined to form a Fashion Vector for that image

b. Training
As we are performing domain transfer, we freeze the bottom layers of the network i.e the VGG16 layers and train the model on the dataset for 5 epochs .This trains the outer layers to learn to classify the images. We then unfreeze the lower layers and train the model for 5-7 epochs until the validation accuracy stabilizes. We store the best possible weights (best on validation accuracy) and use it for the recommendation model.

c. Repository Creation
To get recommendations ,we needed to build a repository of images . This repository would be application specific .If the recommendation needs to be done for shopping , then the repository would contain images from online retail stores like Amazon,Macy’s etc. We used images scraped from these stores and added scraped images from the web to test our recommendation engine. We then cleaned the data , and removed the images which were not relevant /noise. The images were then passed through the network and fashion images were generated from each image .All these vectors were combined to form the repository.

d. Recommendation Model
For the recommendation ,we first need to construct the user style profile . This is done by taking one or more images of the user’s preferred clothing items as input and creating their fashion vector . These vectors are then combined to form the style profile matrix of a person .
Now we use a similarity algorithm to compare the fashion vector of each image in the repository with the style profile matrix . This gives us a score based on the number of feature matches - the higher the score the closer an image is to the person’s style profile.
We then rank the images in order of their scores and display the top most images as recommendations
