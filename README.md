# Twitter-Sentiment-Analysis-using-Python
 
# Twitter Sentiment Analysis using Python

## Table of Contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Twitter API Access](#twitter-api-access)
- [Project Overview](#project-overview)
- [Code Implementation](#code-implementation)
- [Running the Project](#running-the-project)
- [Results](#results)
- [Further Enhancements](#further-enhancements)
- [Conclusion](#conclusion)

## Introduction
This project demonstrates how to perform Twitter Sentiment Analysis using Python. We'll use the Twitter API to fetch tweets related to a specific topic and analyze their sentiment, categorizing them as positive, negative, or neutral.

## Prerequisites
Before running the project, make sure you have the following prerequisites:
- Python installed on your system.
- Required libraries (Tweepy, TextBlob). You can install them using pip:
  ```
  pip install tweepy textblob
  ```
  
## Twitter API Access
To access the Twitter API, follow these steps:
1. Create a Twitter Developer account at [Twitter Developer Platform](https://developer.twitter.com/).
2. Create a Twitter App to obtain your API keys (consumer_key, consumer_secret) and access tokens (access_token, access_token_secret).

## Project Overview
- `TwitterSentimentAnalysis.py`: Python script for sentiment analysis.
- `YOUR_CONSUMER_KEY`, `YOUR_CONSUMER_SECRET`, `YOUR_ACCESS_TOKEN`, `YOUR_ACCESS_TOKEN_SECRET`: Replace with your Twitter API keys and tokens.
- Specify the search query and the number of tweets to analyze.

## Code Implementation
1. Initialize the Twitter API client.
2. Clean tweets by removing special characters.
3. Analyze tweet sentiment using TextBlob.
4. Fetch tweets using the specified search query.
5. Calculate the percentage of positive and negative tweets.
6. Print positive and negative tweets.
What is sentiment analysis? 
Sentiment Analysis is the process of ‘computationally’ determining whether a piece of writing is positive, negative or neutral. It’s also known as opinion mining, deriving the opinion or attitude of a speaker. 

Why sentiment analysis?

Business: In marketing field companies use it to develop their strategies, to understand customers’ feelings towards products or brand, how people respond to their campaigns or product launches and why consumers don’t buy some products.
Politics: In political field, it is used to keep track of political view, to detect consistency and inconsistency between statements and actions at the government level. It can be used to predict election results as well!
Public Actions: Sentiment analysis also is used to monitor and analyse social phenomena, for the spotting of potentially dangerous situations and determining the general mood of the blogosphere.
Installation:


Tweepy: tweepy is the python client for the official Twitter API. Install it using following pip command:
pip install tweepy
TextBlob: textblob is the python library for processing textual data. Install it using following pip command:
pip install textblob
Also, we need to install some NLTK corpora using following command:
python -m textblob.download_corpora
(Corpora is nothing but a large and structured set of texts.)
Authentication: In order to fetch tweets through Twitter API, one needs to register an App through their twitter account. Follow these steps for the same:

Open this link and click the button: ‘Create New App’
Fill the application details. You can leave the callback url field empty.
Once the app is created, you will be redirected to the app page.
Open the ‘Keys and Access Tokens’ tab.
Copy ‘Consumer Key’, ‘Consumer Secret’, ‘Access token’ and ‘Access Token Secret’.
Implementation: 

```Python

import re
import tweepy
from tweepy import OAuthHandler
from textblob import TextBlob
 
class TwitterClient(object):
    '''
    Generic Twitter Class for sentiment analysis.
    '''
    def __init__(self):
        '''
        Class constructor or initialization method.
        '''
        # keys and tokens from the Twitter Dev Console
        consumer_key = 'XXXXXXXXXXXXXXXXXXXXXXXX'
        consumer_secret = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXX'
        access_token = 'XXXXXXXXXXXXXXXXXXXXXXXXXXXX'
        access_token_secret = 'XXXXXXXXXXXXXXXXXXXXXXXXX'
 
        # attempt authentication
        try:
            # create OAuthHandler object
            self.auth = OAuthHandler(consumer_key, consumer_secret)
            # set access token and secret
            self.auth.set_access_token(access_token, access_token_secret)
            # create tweepy API object to fetch tweets
            self.api = tweepy.API(self.auth)
        except:
            print("Error: Authentication Failed")
 
    def clean_tweet(self, tweet):
        '''
        Utility function to clean tweet text by removing links, special characters
        using simple regex statements.
        '''
        return ' '.join(re.sub("(@[A-Za-z0-9]+)|([^0-9A-Za-z \t])
                                    |(\w+:\/\/\S+)", " ", tweet).split())
 
    def get_tweet_sentiment(self, tweet):
        '''
        Utility function to classify sentiment of passed tweet
        using textblob's sentiment method
        '''
        # create TextBlob object of passed tweet text
        analysis = TextBlob(self.clean_tweet(tweet))
        # set sentiment
        if analysis.sentiment.polarity > 0:
            return 'positive'
        elif analysis.sentiment.polarity == 0:
            return 'neutral'
        else:
            return 'negative'
 
    def get_tweets(self, query, count = 10):
        '''
        Main function to fetch tweets and parse them.
        '''
        # empty list to store parsed tweets
        tweets = []
 
        try:
            # call twitter api to fetch tweets
            fetched_tweets = self.api.search(q = query, count = count)
 
            # parsing tweets one by one
            for tweet in fetched_tweets:
                # empty dictionary to store required params of a tweet
                parsed_tweet = {}
 
                # saving text of tweet
                parsed_tweet['text'] = tweet.text
                # saving sentiment of tweet
                parsed_tweet['sentiment'] = self.get_tweet_sentiment(tweet.text)
 
                # appending parsed tweet to tweets list
                if tweet.retweet_count > 0:
                    # if tweet has retweets, ensure that it is appended only once
                    if parsed_tweet not in tweets:
                        tweets.append(parsed_tweet)
                else:
                    tweets.append(parsed_tweet)
 
            # return parsed tweets
            return tweets
 
        except tweepy.TweepError as e:
            # print error (if any)
            print("Error : " + str(e))
 
def main():
    # creating object of TwitterClient Class
    api = TwitterClient()
    # calling function to get tweets
    tweets = api.get_tweets(query = 'Donald Trump', count = 200)
 
    # picking positive tweets from tweets
    ptweets = [tweet for tweet in tweets if tweet['sentiment'] == 'positive']
    # percentage of positive tweets
    print("Positive tweets percentage: {} %".format(100*len(ptweets)/len(tweets)))
    # picking negative tweets from tweets
    ntweets = [tweet for tweet in tweets if tweet['sentiment'] == 'negative']
    # percentage of negative tweets
    print("Negative tweets percentage: {} %".format(100*len(ntweets)/len(tweets)))
    # percentage of neutral tweets
    print("Neutral tweets percentage: {} % \
        ".format(100*(len(tweets) -(len( ntweets )+len( ptweets)))/len(tweets)))
 
    # printing first 5 positive tweets
    print("\n\nPositive tweets:")
    for tweet in ptweets[:10]:
        print(tweet['text'])
 
    # printing first 5 negative tweets
    print("\n\nNegative tweets:")
    for tweet in ntweets[:10]:
        print(tweet['text'])
 
if __name__ == "__main__":
    # calling main function
    main()
Here is how a sample output looks like when above program is run:

Positive tweets percentage: 22 %
Negative tweets percentage: 15 %


Positive tweets:
RT @JohnGGalt: Amazing—after years of attacking Donald Trump the media managed
to turn #InaugurationDay into all about themselves.
#MakeAme…
RT @vooda1: CNN Declines to Air White House Press Conference Live YES! 
THANK YOU @CNN FOR NOT LEGITIMI…
RT @Muheeb_Shawwa: Donald J. Trump's speech sounded eerily familiar...
POTUS plans new deal for UK as Theresa May to be first foreign leader to meet new 
president since inauguration 
.@realdonaldtrump #Syria #Mexico #Russia & now #Afghanistan. 
Another #DearDonaldTrump Letter worth a read @AJEnglish 


Negative tweets:
RT @Slate: Donald Trump’s administration: “Government by the worst men.” 
RT @RVAwonk: Trump, Sean Spicer, etc. all lie for a reason. 
Their lies are not just lies. Their lies are authoritarian propaganda.  
RT @KomptonMusic: Me: I hate corn 
Donald Trump: I hate corn too
Me: https://t.co/GPgy8R8HB5
It's ridiculous that people are more annoyed at this than Donald Trump's sexism.
RT @tony_broach: Chris Wallace on Fox news right now talking crap 
about Donald Trump news conference it seems he can't face the truth either…
RT @fravel: With False Claims, Donald Trump Attacks Media on Crowd Turnout 
Aziz Ansari Just Hit Donald Trump Hard In An Epic Saturday Night Live Monologue
We follow these 3 major steps in our program:

Authorize twitter API client.
Make a GET request to Twitter API to fetch tweets for a particular query.
Parse the tweets. Classify each tweet as positive, negative or neutral.
Now, let us try to understand the above piece of code:

First of all, we create a TwitterClient class. This class contains all the methods to interact with Twitter API and parsing tweets. We use __init__ function to handle the authentication of API client.
In get_tweets function, we use:
fetched_tweets = self.api.search(q = query, count = count)
to call the Twitter API to fetch tweets.
In get_tweet_sentiment we use textblob module.
analysis = TextBlob(self.clean_tweet(tweet))
TextBlob is actually a high level library built over top of NLTK library. First we call clean_tweet method to remove links, special characters, etc. from the tweet using some simple regex. Then, as we pass tweet to create a TextBlob object, following processing is done over text by textblob library:
Tokenize the tweet ,i.e split words from body of text.
Remove stopwords from the tokens.(stopwords are the commonly used words which are irrelevant in text analysis like I, am, you, are, etc.)
Do POS( part of speech) tagging of the tokens and select only significant features/tokens like adjectives, adverbs, etc.
Pass the tokens to a sentiment classifier which classifies the tweet sentiment as positive, negative or neutral by assigning it a polarity between -1.0 to 1.0 .
TextBlob uses a Movies Reviews dataset in which reviews have already been labelled as positive or negative.
Positive and negative features are extracted from each positive and negative review respectively.
Training data now consists of labelled positive and negative features. This data is trained on a Naive Bayes Classifier.
Finally, parsed tweets are returned. Then, we can do various type of statistical analysis on the tweets. For example, in above program, we tried to find the percentage of positive, negative and neutral tweets about a query.
Full Code Explanation:

The code first creates a RandomForestRegressor object.
This object is used to train a model that predicts air quality index (AQI) values.
Next, the code separates the class label (train) and other attributes (target).
The train data set contains information about air quality index values for different classes, while the target data set contains only AQI values.
The code then fits the RandomForestRegressor object on the train data set and targets the predicted AQI value in the target data set.
The code creates a model using the RandomForestRegressor algorithm.
The model is fit to data consisting of air quality index values from training data and target values for air quality index.
The code in this section is used to train a Random Forest Regressor.
A Random Forest Regressor is a machine learning algorithm that uses a collection of trees (or forests) to make predictions.
The first thing the code does is set some parameters.
The most important parameter is the bootstrap parameter, which determines how often the training data should be randomly sampled from.
The default value is True, which means that the training data will be randomly sampled every time it’s needed.
Another important parameter is ccp_alpha, which controls how much weight should be given to features when making predictions.
By default, ccp_alpha is set to 0.0, which means that all features are equally important when making predictions.
However, if you want more weight to be given to certain features over others, you can set ccp_alpha to a value between 0 and 1 .
If you set ccp_alpha too low (i.e., less than 0), then the feature with the lowest importance will have the most weight in predicting outcomes; if you set ccp_alpha too high (i.e., greater than 1), then the feature with the highest importance will have the most weight in predicting outcomes.
The next thing
The code will create a Random Forest Regressor to predict sales.
The Random Forest Regressor will use bootstrap sampling to generate samples, and will have a criterion of mse.
The Random Forest Regressor will also have a max_depth and max_features parameter.
The max_depth parameter controls the maximum number of layers in the Random Forest Regressor, while the max_features parameter controls the number of features that are used in the model.
Finally, the code specifies that the Random Forest Regressor should have a min_impurity_decrease and min_impurity_split parameter.
These parameters control how aggressively the model should try to reduce impurity (i.e., variance).
Lastly, the code specifies that the Random
The code begins by importing the necessary modules.
The AdaBoostRegressor module is used to create and fit the model.
The learning_rate, loss, and n_estimators parameters are all optional; they can be left at their default values (1.0, ‘linear’, and 50, respectively).
Next, the base_estimator parameter is set to None.
This means that the model will be fitted using a random forest algorithm instead of a simple linear regression model.
The next step is to define the model parameters.
The learning_rate parameter sets how often the algorithm should learn from data; it should be greater than 1 but less than or equal to 2 (in this case, 1.0).
The loss parameter specifies how much weight each prediction should have in determining the final score; it should be ‘linear’ in this case (meaning that predictions with lower scores will have less impact on the final score).
Finally, n_estimators defines how many trees will be used in the random forest algorithm; 50 is used here.
After defining these parameters, it’s time to fit the model!
First, train1 and target are passed into the fit() method as input data.
Next, m2
The code first imports the AdaBoostRegressor module.
This module allows you to train a model using a gradient descent algorithm.
Next, the code defines the model using the AdaBoostRegressor() function.
The parameters that are defined include the base estimator (which is None in this case), learning rate (1.0), and loss (linear).
Finally, the code sets up 50 training iterations and passes in the target value as an input.
After fitting the model, the predicted values for each sample are returned.
## Running the Project
1. Save the Python script to a .py file.
2. Replace 'YOUR_CONSUMER_KEY', 'YOUR_CONSUMER_SECRET', 'YOUR_ACCESS_TOKEN', and 'YOUR_ACCESS_TOKEN_SECRET' with your Twitter API keys and tokens.
3. Run the script.
```
## Results
The project will display the percentage of positive and negative tweets related to your search query. Additionally, it will print the first few positive and negative tweets found.

## Further Enhancements
This is a basic sentiment analysis project. You can enhance it by:
- Implementing data visualization (e.g., pie chart of sentiment distribution).
- Storing and analyzing more tweets.
- Building machine learning models for more accurate sentiment analysis.

## Conclusion
This project demonstrates how to perform Twitter Sentiment Analysis using Python and the Twitter API. You can use this as a starting point to build more advanced sentiment analysis applications.
 
