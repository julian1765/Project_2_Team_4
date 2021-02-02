# Trading with Twitter Data, The Beta <br>

### Team Members: Julian Martinez, Kevin Walsh, Lawrence Cummings, Michael De Paula, Takeshi Nagai


## 1. Introduction

What came first? The market movement or the news spreading of the event that inevitably moved the market?

The good and bad news about a company can affect its stock price in mere seconds now that information travels so rapidly on the internet. Still, then begs the question: Is it possible to identify that correlation between news sentiment and stock price, quantify it, and use it to give better trading recommendations?

This prevailing question drove us to investigate the fickle connection between a company’s stock price fluctuation and the online sentiment of that company at a given moment. We hypothesized that the overall sentiment of social media activity would hit a certain inflection point and functioned much in the same way as a company’s balance sheet, signaling to investors whether to buy, sell or hold. With that hypothesis in mind, we wanted to use this project as an opportunity to explore the feasibility of integrating social media sentiment into a stock price forecasting model and measure the effectiveness of that model.


As to the data collection, We selected Microsoft, Sony, Pfizer, Regeneron, and Moderna as subjects of our analysis to have some variety across industries and comparables simultaneously. Using Twint, an advanced tweet scraping tool, we gathered each company's tweets on Twitter because Twitter has become almost synonymous with democratized news, and individual tweets have shown, in ad hoc scenarios, to carry enough weight to influence markets, industries, and stock prices. As to the stock information, we used Alpaca API.


## 2. Initial Method
At first, we examined a straightforward method, where the only X feature is sentiment scores, and y values are classes such as 1 when the closing price moves up and 0 when it goes down. <br>
<img width="150" alt="data_shape3.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape3.PNG">
<br>
We applied the following 5 classification models to the data, but as seen in the accuracy scores, none of them performed well.<br>
1. Decision Tree Classifier    53.72%<br>
2. Logistic Regression         52.72%<br>
3. Random Forest Classifier    54.33%<br>
4. Support Vector Classifier   53.52%<br>
5. XGBoost Classifier          53.92%<br>


### Refining the Models:

In order to see if the models would perform better (1) with a different set of train and test data and (2) with their parameters optimized, we attempted two ways to refine the models: (1) Cross Validation and (2) Grid Search, both of which are libraries built within Scikitlearn.

#### (1) Cross Validation:

Cross validation is a way to reduce the possibility that performance of a model is affected by bias and randomness when splitting data. Suppose we use the train_test_split library to divide a dataset into train data and test data. In this case, even if we apply a model and get a good accuracy score, that good performance might depend upon how the dataset was split, which is random. To alleviate this randomness, cross validation repeats dividing a dataset and applying a model for a specified number of times such that the dataset is divided differently each time and each data point is allocated to test data only once. By calculating the average of scores of all iterations of cross validation, we can have a more unbiased score of a model.

#### (2) Grid Search:

Using Grid Search, we aimed to fine-tune the parameters of a model. Grid search chooses the best values for parameters from a list of values given in advance. It is not necessarily limited to one type of model. We applied the grid search to the SVC model (Support Vector Classifier) and optimized the "C" and "gamma" parameters. We selected the SVC for the grid search algorithm from the five models because it had the best performance in the cross validation.

Even after attempting the cross validation and the grid search, accuracy scores of the five models were still not high enough to find a meaningful correlation between sentiment scores and stock price movements and to make a reliable stock price prediction based on the models.


## 3. Next Method: Comparing Performances of 1 Feature Model and 2 Feature Model

Finding no meaningful relationship by the initial method, we tried another method: comparing performances of a model with 1 feature (historical stock prices) and the one with 2 features (historical stock prices and sentiment scores). <br>
The background of the method is as follows. Stock prices are not determined only by sentiment scores. Many features come into play, among which historical stock prices are probably the primary feature. If sentiment scores have some relationship with stock price movements in a predictable and learnable way, predictions of the 2-feature model would be better than those of the 1-feature model, regardless of regression analyses or classification analyses.

We deployed this methodology in several algorithms in the context of both regression or classification as explained below.

### (1) Regression Analysis

For regression analysis purposes, we used a 20-day rolling average of a stock’s closing price as the first feature and the 20-day rolling average of the sentiment score of the company as the second feature. The shapes of data used for the one-feature model and the two-feature model are below.<br>
<img width="120" alt="data_shape1.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape1.PNG">
<img width="150" alt="data_shape2.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape2.PNG">

<br>
We applied these two types of data to (1) Dense Neural Network and (2) Long Short-Term Memory (LSTM) model. To visualize which of the one-feature model or the two-feature model predicts future stock price movements better, we juxtapose (a) the historical stock price movements, (b) predictions by the one-feature model, and (c) predictions by the two-feature model as below for both of the Dense Neural Network and Long Short-Term Memory Model.  <br>
<img width="600" alt="msft_dnn.png" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_dnn.png">
<img width="600" alt="msft_lstm.png" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_lstm.png">
<br>
As seen in the graphs for Microsoft, for both algorithms, the two-feature model demonstrated a better ability of tracking actual stock performance (i.e., y-test) over the single-feature model. This means adding sentiment scores as one of X features could improve the stock price predictions based on historical stock prices. For the other four companies, however, we could not find meaningful improvements in the two-feature model. <br>

### (2) Classification Analysis

In tandem to the regression analysis that we ran, we also encoded the stock closing prices into trading signals in order to use them in a classification framework.  The shapes of data used for the one-feature model and the two-feature model are below.<br>
<img width="100" alt="data_shape4.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape4.PNG">
<img width="150" alt="data_shape5.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape5.PNG">
<img width="400" alt="msft_scatter.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_scatter.PNG">
<br>

We used the sigmoid curve as the activation function, binary cross-entropy as the loss function, and accuracy as our leading metric. In addition to the Dense Neural Network and the Long Short-Term Memory model, we applied the initial five models, with respect to which we tried all of the ordinary one-time application, the cross validation, and the parameter optimization (only for the SVC). In total, the following seven models are applied:<br>
1. Dense Neural Network<br>
2. Long Short-Term Memory Model<br>
3. Decision Tree Classifier<br>
4. Logistic Regression<br>
5. Random Forest Classifier<br>
6. Support Vector Classifier<br>
7. XGBoost Classifier
<br>
Below is the results of accuracy scores of the five companies.
<br>
<img width="800" alt="classification_scores.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/classification_scores.PNG">
<br>
While we did see an improvement in each model’s accuracy scores in majority cases, in most cases, it fell within the ‘flip of a coin’ category. But, the ability of social media sentiment to improve the model was still well demonstrated in the classification framework. 

## Conclusions
Comparing the performances of the one-feature model and the two-feature model, in both the regression analysis and classification analysis, reveals that sentiment scores could refine models to predict stock price movements of some companies. However, it was not the case for the others. On balance, the extent to which a company's stock price is affected by tweet sentiments depends upon each company.<br>
Therefore, we should be prudent to use sentiment scores in order to predict stock price movements.


