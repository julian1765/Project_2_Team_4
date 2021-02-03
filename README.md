# Trading with Twitter Data, The Beta <br>

### Team Members: Julian Martinez, Kevin Walsh, Lawrence Cummings, Michael De Paula, Takeshi Nagai


## 1. Introduction

Financial statements, P/E ratios and revenue reports all have a positive, or potentially negative, impact on a company's stock price. That is because they are quite concrete and the relationship between them and the worth of the company is clear. But, is it possible to measure the relationship between a stock price and something more nebulous, say social media activity?

Good news and bad news about a company can affect its stock price in mere seconds now that information travels so rapidly on the internet. Still, then begs the question: Is it possible to quantify this correlation between news sentiment and stock price and use it in an applicable way to give better trading recommendations?

This prevailing question drove us to investigate the ambiguous connection between a company’s stock price fluctuation and the online sentiment of that company at a given moment. We hypothesized that the overall sentiment of social media activity would hit a certain inflection point and functioned much in the same way as a company’s balance sheet: signaling to investors whether to buy, sell or hold. With that hypothesis in mind, we wanted to use this project as an opportunity to explore the feasibility of integrating social media sentiment into a stock price forecasting model and measure the effectiveness of that model.


As to the data collected, we selected Microsoft, Sony, Pfizer, Regeneron, and Moderna as subjects of our analysis to have some variety across industries and comparables simultaneously. Using Twint, an advanced Tweet scraping tool, we gathered raw Twitter data for each company. Twitter has become almost synonymous with democratized news, and individual tweets have shown, in ad hoc scenarios, to carry enough weight to influence markets, industries, and stock prices. For us, we thought it more important to gauge the activity online through Twitter than to track the headlines in *The Wall Street Journal. 

For raw stock price data, we used the Alpaca API.


## 2. Initial Method
At first, we examined a straightforward method, where the only X feature is sentiment scores, and y values are classes such as 1 when the closing price moves up and 0 when it goes down. <br>
<img width="150" alt="data_shape3.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape3.PNG">
<br>
We applied the following 5 classification models to the data, but as seen in the accuracy scores, none of them performed well at all.<br>
Decision Tree Classifier    53.72%<br>
Logistic Regression         52.72%<br>
Random Forest Classifier    54.33%<br>
Support Vector Classifier   53.52%<br>
XGBoost Classifier          53.92%<br>


### Refining the Models:

To improve the models above we tried to (1) remove any potential bias in the separating of training and testing data and (2) optimize the set of hyperparameters in the models. To do this we used *cross validation* and *grid search*, both of which are libraries built within Scikitlearn.

#### (1) Cross Validation:

Cross validation is a way to reduce the possibility that performance of a model is biased by introducing randomness to the creation of training and testing data. Suppose we use the train_test_split library to divide a dataset into training data and testing data. In this case, even if we apply a model once and get a good accuracy score, that score is dependent upon how the dataset was split: The dataset might have been split preferably for the model by chance. The model might not perform well if we run the code again and get a different set of training data and testing data. To alleviate this potential bias, cross validation iterates through the dataset multiple times and applies a different split of the data each time so that each data point will fall into the test dataset only once. For instance, let’s say we have 100 data points numbered from 1 to 100 and conduct the cross validation with 4 iterations. How the cross validation works is as follows. In the first iteration, it (1) categorizes the data points from 1 to 25 as testing data and those from 26 to 100 as training data, (2) proceeds with fitting and predicting process, and (3) calculates an accuracy score. Then, in the second iteration, the cross validation categorizes the data points from 26 to 50 as testing data and those from 1 to 25 and 51 to 100 as training data, (2) proceeds with fitting and predicting process, and (3) calculates an accuracy score. Likewise, it repeats the same processes two more times, with testing data rotating to data points from 51 to 75 in the third iteration and from 76 to 100 in the last iteration. At the end of the last iteration, the cross validation will have produced four accuracy scores.<br>
By calculating the average of scores of all iterations, we can get a better picture of a model’s performance less dependent upon the bias and randomness in the course of splitting a dataset.<br>


#### (2) Grid Search:

Using Grid Search, we aimed to fine-tune the parameters of a model. Grid search chooses the best values for parameters from a list of values given in advance. It is not necessarily limited to one type of model. We applied the grid search to the Support Vector Classifier (SVC) Model and optimized the "C" and "gamma" parameters. We selected the SVC for the grid search algorithm from the five models because it had the best performance after applying cross-validation to the original set of models.

Even after applying cross validation and the grid search, accuracy scores of the five models were still not high enough to find a meaningful correlation between sentiment scores and stock price movements and we were not comfortable making stock price predictions based on the models.


## 3. Next Method: Comparing Performances of 1 Feature Model and 2 Feature Model

Given that we were unable to derive any useful information using sentiment scores as the only X feature, we decided to abandon the method. As the next method, we compared performances between the following two models and see if there was any meaningful improvement: (1) a model with one feature (historical stock prices) and (2) a model with two features (historical stock prices and sentiment scores).<br>

Our reasoning for adding the second feature is as follows. Stock prices are not determined only by sentiment scores. Many features come into play, among which historical stock prices are probably the primary feature. If sentiment scores have some relationship with stock price movements in a predictable and learnable way, predictions of the two-feature model would be better than those of the one-feature model, regardless of regression analyses or classification analyses.

We deployed this methodology in several algorithms in the context of both regression or classification as explained below.

### (1) Regression Analysis

For regression analysis purposes, we used a 20-day rolling average of a stock’s closing price as the first feature and the 20-day rolling average of the sentiment score of the company as the second feature. The shapes of data used for the one-feature model and the two-feature model are below.<br>
<img width="120" alt="data_shape1.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape1.PNG">
<img width="150" alt="data_shape2.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape2.PNG">

<br>
We applied these two types of data to a Dense Neural Network and to a Long Short-Term Memory (LSTM) model. To visualize which of the one-feature model or the two-feature model predicts stock price movements better, we juxtapose (a) the historical stock price movements, (b) predictions by the one-feature model, and (c) predictions by the two-feature model as below for both of the Dense Neural Network and LSTM Model.  <br>
<img width="600" alt="msft_dnn.png" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_dnn.png">
<img width="600" alt="msft_lstm.png" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_lstm.png">
<br>
As seen in the graphs for Microsoft, in both models the two-feature methodology demonstrated a better ability of tracking actual stock performance (i.e., y-test) over the single-feature model. This means adding sentiment scores as an additional feature to the historical stock price framework can improve the predictability of models. For the other four companies, however, we could not find meaningful improvements in the two-feature model, due in part to a lack of Twitter data. <br>

### (2) Classification Analysis

In tandem to the regression analysis that we ran, we also encoded the stock closing prices as well as the daily sentiment scores into trading signals in order to use them in a classification framework.  The shapes of data used for the one-feature model and the two-feature model are below.<br>
<img width="100" alt="data_shape4.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape4.PNG">
<img width="150" alt="data_shape5.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/data_shape5.PNG">
<img width="400" alt="msft_scatter.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/msft_scatter.PNG">
<br>

We used the sigmoid curve as the activation function, binary cross-entropy as the loss function, and accuracy as our leading metric. In addition to the Dense Neural Network and the Long Short-Term Memory model, we applied the initial five models, cross validation, and the parameter optimization (only for the SVC). In total, the following seven models were applied:<br>
1. Dense Neural Network<br>
2. Long Short-Term Memory Model<br>
3. Decision Tree Classifier<br>
4. Logistic Regression<br>
5. Random Forest Classifier<br>
6. Support Vector Classifier<br>
7. XGBoost Classifier
<br>
Below is the result of accuracy scores for the five companies.
<br>
<img width="800" alt="classification_scores.PNG" src="https://github.com/julian1765/Project_2_Team_4/blob/main/images/classification_scores.PNG">
<br>
While we did see an improvement in each model’s accuracy scores in a majority of cases, most stil fell within the ‘flip of a coin’ category. But, the ability of social media sentiment to improve the model was still well demonstrated in the classification framework. 

## Conclusions

Comparing the performances of the one-feature model and the two-feature model, in both regression analysis and classification analysis, reveals that sentiment scores could refine models to predict stock price movements of some companies. However, it was not the case for the others. On balance, the extent to which a company's stock price is affected by tweet sentiments depends upon each company. Therefore, we should be prudent to use sentiment scores in order to predict stock price movements. We include visualizations of the regression analysis and classification analysis in the Code folder in the form of a dashboard.
<br>
While incorporating sentiment analysis into predictive modeling is ill-advised in some cases, it would always be interesting for investors to read about and visualize what commentators, both professional and ametuer, are saying about their portfolio online. To that end, we created a workbook that takes an investor’s portfolio as a list of stocks, using Twint to scrape for raw Twitter data, performs some sentiment analysis, and deploys multiple visualizations of the Tweets into a Dashboard to make it easily consumable and communicable. The Sentiment_Dashboard folder, as well as the corresponding Python notebook, will give an investor an idea of the social media activity occurring in relation to the stocks that they own. In light of our regression and classification analysis, perhaps this is better used as an education tool rather than a trading one. 


