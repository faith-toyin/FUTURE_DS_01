## Amazon Customer Sentiment Analysis

### CONTENT:
  1. INTRODUCTION
2. DATA COLLECTION
3. DATA PREPROCESSING
4. EXPLORATORY DATA ANALYSIS
5. SENTIMENT ANALYSIS
6. DATA VISUALIZATION
7. SUMMARY

 
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



