# bees_on_dahlia
This reprository goes along with the post (https://towardsdatascience.com/how-to-use-deep-learning-to-quantify-pollinator-behavior-i-5fc8000a9bde).


How to use deep-learning to quantify pollinator behavior I

In the last years there has been quite some fuzz in the news about two seemingly unrelated topics: the decrease of bees and other pollinators and, simultaneously, the increase of artificial intelligence and data science. Reading up a bit on pollination and how research on pollination works I realized that although a lot is known, there is, as in all sciences, way more unknown. This triggered my curiosity, wouldn’t it be possible to use AI for the study of pollinators? In this post I explain a simple way to do so. I wrote some code, which can be found om my github. (https://github.com/jnnkl/bees_on_dahlia). But as a teaser: I wanted to quantify the number of visitations to the flowers in this movie. 

https://youtu.be/1V2n5X6-I_Y


Getting the  data

The first thing to do was to collect data. My goal was to make something cheap so I bought the cheapest web-cam I could find in the local electronics store. Last September I brought it to a park and collected about 1,5 hour one picture a second time-lapse series of a bunch of Dahlia flowers. This resulted in a dataset of about 4500 pictures. My goal was to use deep-learning methods to count the number of pollinators visiting each flowers. I decided to go for supervised learning because that is the easiest. To do so I needed a labeled dataset and I used the first 1500 images for this. That is, I did cut out squares on the middle of the main flowers with and without insects visitors. I did make the squares different sized and did cut out each bee several times. In this way I collected 635 images of a flower with a bee, and because flowers are more often not-visited then visited, way more images without an insect. It turned out that flowers are more often not-visited then visited. To make the dataset balanced I used a similar amount of pictures of empty flowers.

Building the network

I wanted to build a classifier containing two classes 1) there is an insect present and 2) there is no insect present. I used a convolutional neural network for this, because these are impressively useful for image recognition. As the amount of data was not overwhelming I went for transfer-learning. As VGG16 was the smallest network available in the keras distribution installed on my computer I used that. I used the techniques discussed in this (https://blog.keras.io/building-powerful-image-classification-models-using-very-little-data.html) blogpost from keras. That is on top of the VGG16 network I did put a small convolutional neural network and trained that smaller network and saved that. Next, I took the whole network, made the first 15 layer untrainable and then trained the rest with a very small training rate to prevent over-fitting. I quickly got an accuracy of over 92%. I was lucky to have access to a GPU cluster to do the training, otherwise it would have taken quite some time.

Making the movie

Now that I could classify square images having a bee or not it was time to use this to check the number of insect visitors of the two main flowers in my movie. Luckily, I made my time lapse record on a non-windy day, so the images were quite stable. So I simply determined the locations of the two flowers and took those parts of each picture, ran it through the neural network and got a table with for each picture for both flowers if there was an insect or not. As the accuracy was high but not too high I decided only to count those instances of insect if there where at least two subsequent instances of an insect, and similarly if it was it was gone for shorter then a one second  frame then I reclassified it as with. I drew boxes where I looked, red if there was no insect and green if there was an insect. That lead to the following movie:

https://www.youtube.com/watch?v=s1woB_7bF6E&t=87s

Quantifying the results and some discussion
So now I got a nice visualization, that is not really quantifying let’s look at some plots. In this plots flower 1 is the flower in the left upper corner, and flower 2 is the large flower that is only half visible.

(plots on https://towardsdatascience.com/how-to-use-deep-learning-to-quantify-pollinator-behavior-i-5fc8000a9bde)

They show that the larger flower gets more visits and the intervals between the visits seems to be smaller. Of course this short measurement does not support any serious claims about pollinator behavior but that is not what I was after. The goal of this experiment was to show that deep learning techniques can help to study pollination behavior, also in a quantitative way. Of course this could also have been done in a classical way of image analysis. Probably, one could relatively easily come up with an algorithm that would recognize insect visitation on these dahlia flowers automatically. However, they do not generalize to other flowers. I checked if this network worked for time-lapses of other flowers as well. I would have been lucky if that was the case, and indeed I had to conclude that this network works for this flowers only. Other flowers are often way more diverse in there structure then this particular dahlia, and to get a network detecting insects on all kind of flowers is actually quite tricky. So how to get a good insect detection network for other flowers and more backgrounds will be a topic of later post.
