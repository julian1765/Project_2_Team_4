# Trading with Twitter Data, The Beta <br>

### Team Members: Julian Martinez, Kevin Walsh, Lawrence Cummings, Michael De Paula, Takeshi Nagai


## 1. Introduction

What came first? The market movement or the news spreading of the event that inevitably moved the market?

The good and bad news about a company can affect its stock price in mere seconds now that information travels so rapidly on the internet. Still, then begs the question: Is it possible to identify that correlation between news sentiment and stock price, quantify it, and use it to give better trading recommendations? 

This prevailing question drove us to investigate the fickle connection between a company’s stock price fluctuation and the online sentiment of that company at a given moment. We could agree that there was a correlation between the two, but the actual magnitude of that correlation was unclear. When a company boasts record earnings or rolls out a new product line, the stock price will, most likely, increase reflecting the market’s belief that the company’s actions have positively impacted the value of the firm. While the relationship between earnings and shareholder’s equity is well defined, and underpins much of fundamental analysis, the relationship between social media activity and the stock price is more ambiguous. We hypothesized that the overall sentiment of social media activity would hit a certain inflection point and functioned much in the same way as a company’s balance sheet, signaling to investors whether to buy, sell or hold. With that hypothesis in mind, we wanted to use this project as an opportunity to explore the feasibility of integrating social media sentiment into a stock price forecasting model and measure the effectiveness of that model.


To track this correlation, we needed a large number of data points to overlay with the stock prices throughout the day. Twitter has become almost synonymous with democratized news, and individual tweets have shown, in ad hoc scenarios, to carry enough weight to influence markets, industries and yo buoy, or sink, stock prices. We leveraged NLP analysis to quantify sentiment and incorporate its value as a feature into our models to enhance the forecasting and decision making ability of them. We chose Microsoft, Sony, Pfizer, Regeneron, and Moderna as subjects of our analysis. These selections gave us some variety across industries and comparables simultaneously. We turned to Twint, an advanced tweet scraping tool instead of a Tweet API, to gather tweets for each of the companies. In addition to Twint, we used Alpaca to gather stock information.


## 2. Initial Method
At first, we examined a straightforward method, where the only X feature is sentiment scores, and y values are classes such as 1 when the closing price moves up and 0 when it goes down. <br>
![data_shape3](https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape3.PNG)
We applied the following 5 classification models to the data, but as seen in the accuracy scores, none of them performed well.
Decision Tree Classifier	 53.72%
Logistic Regression		 52.72%
Random Forest Classifier	 54.33%
Support Vector Classifier	 53.52%
XGBoost Classifier		 53.92%


### Refining the Models:

In order to see if the models would perform better (1) with a different set of train and test data and (2) with their parameters optimized, we attempted two ways to refine the models: (1) Cross Validation and (2) Grid Search, both of which are libraries built within Scikitlearn.

#### (1) Cross Validation:

Cross validation is a way to reduce the possibility that performance of a model is affected by bias and randomness when splitting data into train and test data. Suppose we use the train_test_split library to divide a dataset into train data and test data. In this case, even if we apply a model and get a good accuracy score, that good performance might depend upon how the dataset was split, which is random. The model could perform much worse or much better if the dataset were split differently. To alleviate this randomness, cross validation repeats dividing a dataset into train data and test data and applying a model to them for a specified number of times such that the dataset is divided differently each time and each data point is allocated to test data only once. By calculating the average of scores of all iterations, we can have a more unbiased score of a model.

#### (2) Grid Search:

Using Grid Search, we aimed to finetune the parameters of our models in order to determine the best value for a given model. Grid search chooses the best values for parameters from a value list specified in advance. It is not necessarily limited to one type of model. When applying the grid search to the SVC model (Support Vector Classifier), we used the following parameters: (1) C which tells how much you want to avoid misclassifying each training set, and (2) Gamma which, if low, tells us that it does not influence the training data and, if high, tells us it closely influences the training data. We selected the SVC for the grid search algorithm from the five models because it had the best performance in the cross validation. 

We expected that it would have performed even better with optimized parameters, which, unfortunately was not the case. Even after attempting the cross validation and the grid search, accuracy scores of the five models were still not high enough to find a meaningful correlation between sentiment scores and stock price movements and make any type of stock price predictions based on the models.


## 3. Next Method: Comparing Performances of 1 Feature Model and 2 Feature Model

Using sentiment scores as the only independent value, we could not find a meaningful relationship between sentiment scores and stock price up and down movements. Then, we tried another method: comparing performances of a model with 1 feature (historical stock prices) and the one with 2 features (historical stock prices and sentiment scores). The background of the method is as follows. Stock prices are not determined only by sentiment scores. Many features such as seasonality and population come into play, among which stock prices in the past are probably one of the primary features affecting future stock prices most. If sentiment scores have some relationship with stock price movements in a predictable and learnable way, predictions of the 2-feature model would be better than those of the 1-feature model, regardless of regression analyses or classification analyses.

We deployed this methodology in several algorithms in the context of both regression or classification as explained below.

### (1) Regression Analysis

For regression analysis purposes, we used a 20-day rolling average of a stock’s closing price as the first feature and the 20-day rolling average of the sentiment score of the company as the second feature. The shapes of data used for the one-feature model and the two-feature model are below.<br>
![data_shape1](https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape1.PNG)
![data_shape2](https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape2.PNG)
<br>
We applied these two types of data to (1) Dense Neural Network and (2) Long Short-Term Memory (LSTM) model. To visualize which of the one-feature model or the two-feature model predicts future stock price movements better, we juxtapose (a) the historical stock price movements, (b) predictions by the one-feature model, and (c) predictions by the two-feature model as below for both of the Dense Neural Network and Long Short-Term Memory Model. As seen in the graphs for Microsoft, for both algorithms, the two-feature model demonstrated a better ability of tracking actual stock performance (i.e., y-test) over the single-feature model. This means adding sentiment scores as one of X features could improve the stock price predictions based on historical stock prices. For the other four companies, however, we could not find meaningful improvements in the two-feature model. <br>
![msft_dnn](https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_dnn.png)
<br>
![msft_lstm](https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_lstm.png)



### (2) Classification Analysis

In tandem to the regression analysis that we ran, we also encoded the stock closing prices into trading signals in order to use them in a classification framework.  The shapes of data used for the one-feature model and the two-feature model are below.<br>
![data_shape4](https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape4.PNG)
![data_shape5](https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape5.PNG)
<br>

We used the sigmoid curve as the activation function, binary cross-entropy as the loss function, and accuracy as our leading metric. In addition to the Dense Neural Network and the Long Short-Term Memory model, we applied the initial five models, with respect to which we tried all of the ordinary one-time application, the cross validation, and the parameter optimization (only for the SVC). In total, the following seven models are applied:
Dense Neural Network
Long Short-Term Memory Model
Decision Tree Classifier
Logistic Regression
Random Forest Classifier
Support Vector Classifier
XGBoost Classifier
<br>
Below is the results of accuracy scores of the five companies.
<br>
![classification_scores](https://github.com/julian1765/Project_2_Team_4/blob/main/images/classification_scores.PNG)
<br>
While we did see an improvement in each model’s accuracy scores in majority cases, in most cases, it fell within the ‘flip of a coin’ category. But, the ability of social media sentiment to improve the model was still well demonstrated in the classification framework. 

## Conclusions
