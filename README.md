## Amazon Customer Sentiment Analysis

### CONTENT:
  1. [INTRODUCTION](#introduction)
2. [DATA COLLECTION](#data-collection)
3. [DATA PREPROCESSING](#data-preprocessing)
4. [EXPLORATORY DATA ANALYSIS](#exploratory-data-analysis)
5. [SENTIMENT ANALYSIS](#sentiment-analysis)
6. [DATA VISUALIZATION](#data-visualization)
7. [SUMMARY](#summary)

 
 ### INTRODUCTION
 The purpose of this analysis is to understand the sentiment of Amazon customer reviews. By analyzing the sentiment, we aim to gain insights into customer satisfaction, identify common issues, and uncover trends in customer feedback. This information can be used to improve products, enhance customer service, and inform business strategies.
 The dataset consists of Amazon customer reviews, which include various attributes such as:
 * Review Text- The text of the customer review.
* Rating- The star rating given by the customer (1 to 5 stars).
* Review Date- The date when the review was posted.
* Reviewer Name- the names of the reviewers who posted the reviews. 
* Review Time- it indicates the date when the review was posted. 
* Day Difference (day_diff)- This represents the difference in days between the review date and a reference date.
* Score Positive-Negative Difference (score_pos_neg_diff)- This calculates the difference between the number of helpful votes (positive) and unhelpful votes (negative) for a review. 
* Score Average Rating (score_average_rating)-This represents the average rating given by the reviewer. 
* Wilson Lower Bound (wilson_lower_bound)- This calculates the lower bound of the Wilson score interval for the helpfulness of the review.

### DATA COLLECTION
The dataset was downloaded from Kaggle and the specific dataset used is the "Amazon Product Reviews" dataset, which contains customer reviews for various products available on Amazon.The dataset is publicly available and can be accessed [Here](https://www.kaggle.com/datasets/arhamrumi/amazon-product-reviews).

### DATA PREPROCESSING
It involves cleaning and transforming the data to ensure it is in a suitable format for further analysis.
#### Data Cleaning
Handling Missing Values: To ensure the dataset is complete and ready for analysis, we addressed missing values using "isnull" method in pandas and removed the rows with missing values in critical columns like "reviewText". This ensuresthat only complet reviews are included.
```def missing_values_analysis(df):
    na_columns_ = [col for col in df.columns if df[col].isnull().sum() > 0]
    n_miss = df[na_columns_].isnull().sum().sort_values(ascending=True)
    ratio_ = (df[na_columns_].isnull().sum() / df.shape[0]* 100).sort_values(ascending=True)
    missing_df = pd.concat([n_miss, np.round(ratio_, 2)], axis =1, keys=["Missing Values", "Ratio"])
    missing_df = pd.DataFrame(missing_df)
    df.dropna(subset=['reviewText'], inplace=True)
    return missing_df
```

Duplicates: We used the "duplicated()" method in pandas to identify duplicate rows in the dataset. This method checks for duplicate entries based on the specified columns. In this case, we checked for duplicates in the "reviewText" column to ensure each review text is unique.
```df.drop_duplicates(subset=['reviewText'], inplace=True)
```
![image](https://github.com/user-attachments/assets/a6ee5cd7-bc46-47fc-b2a5-33d6f9f9e990)

#### Text Preprocessing
This is a step in preparing the review text for sentiment analysis. this steps involved: lowercasing, removing special characters,tokenization, etc.
```rt = lambda x: re.sub('[^a-zA-Z]', '', str(x))
df["reviewText"] = df["reviewText"].map(rt)
df["reviewText"] = df["reviewText"].str.lower()
df["reviewText"] = df["reviewText"].apply(lambda x: x.split())
df.head()
```
![image](https://github.com/user-attachments/assets/7979c935-056a-4486-abb3-bfed061cf2d5)
![image](https://github.com/user-attachments/assets/de43af26-c6f9-4bb1-97a7-f5cfc7a3f5e5)

### EXPLORATORY DATA ANALYSIS
To visualize the distribution of categorical variables, we used the following function to create count plots and pie charts:
```def categorical_variable_summary(df, column_name):
    fig = make_subplots(rows=1, cols=2,
                        subplot_titles=('Countplot', 'Percentage'),
                        specs=[[{'type': 'xy'}, {'type': 'domain'}]])

    fig.add_trace(go.Bar(y=df[column_name].value_counts().values.tolist(),
                         x=[str(i) for i in df[column_name].value_counts().index],
                         text=df[column_name].value_counts().values.tolist(),
                         textposition='auto',
                         name=column_name,
                         marker=dict(color='brown'))),  
    fig.add_trace(go.Pie(labels=df[column_name].value_counts().keys(),
                         values=df[column_name].value_counts().values,
                         textfont=dict(size=18),
                         name=column_name,
                         marker=dict(colors=['sandybrown', 'chocolate', 'Tan', 'lightbrown', 'darkbrown'])),  
                  row=1, col=2) 

    fig.update_layout(title_text=f'{column_name} Summary',
                      annotations=[dict(text='Countplot', x=0.2, y=0.5, font_size=12, showarrow=False),
                                   dict(text='Percentage', x=0.8, y=0.5, font_size=12, showarrow=False)])
    fig.show()

 
data = { 'overall': [1, 2, 2, 3, 3, 3, 4, 4, 4, 4, 5, 5, 5, 5, 5] } 
df = pd.DataFrame(data)
```
The above function creates a count plot and a pie chart to display the distribution and percentage of each category in the specified column. The visualizations help us understand the distribution of ratings in the dataset.
![image](https://github.com/user-attachments/assets/125393e9-84e9-4d10-94f2-2509e8cf1cf7)

### SENTIMENT ANALYSIS
This helps us determine the sentiment or emotion expressed in a piece of text. It helps in understanding customer feedback by classifying reviews as positive, negative, or neutral. This analysis provides valuable insights into customer opinions and experiences, allowing businesses to identify strengths and areas for improvement.
```from vaderSentiment.vaderSentiment import SentimentIntensityAnalyzer
from textblob import TextBlob

analyzer = SentimentIntensityAnalyzer()
df[['polarity', 'subjectivity']] = df['reviewText'].apply(lambda Text:pd.Series(TextBlob(Text).sentiment))

for index, row in df['reviewText'].items():

    score = SentimentIntensityAnalyzer().polarity_scores(row)

    neg = score['neg']
    neu = score['neu']
    pos = score['pos']
    if neg>pos:
        df.loc[index, 'sentiment'] = "Negative"
    elif pos > neg:
        df.loc[index, 'sentiment'] = "Positive"
    else:
        df.loc[index, 'sentiment'] = "Neutral"
```
![image](https://github.com/user-attachments/assets/ccd350cd-86c4-491f-bb7f-6c2a52cc0a13)


### DATA VISUALIZATION
![image](https://github.com/user-attachments/assets/c39d7aa5-687c-4e4d-bbd6-2cdf894d3987)

### SUMMARY
The results of the sentiment analysis indicate that the majority of customer reviews are positive, with 81.6% of the reviews expressing positive sentiment. This suggests that most customers are satisfied with their purchases and have had good experiences.
The negative sentiment accounts for 12.8% of the reviews, which is much lower than the positive sentiment. This indicates that while there are some dissatisfied customers, they are in the minority.
The neutral sentiment makes up 5.6% of the reviews, showing that a small portion of customers have mixed or indifferent feelings about their purchases.
Overall, the results suggest that the product or service being reviewed is generally well-received by customers, with a high level of satisfaction. However, it is important to address the concerns of the minority of customers who have expressed negative sentiment as well as the neutral customers to further improve customer satisfaction and loyalty.
