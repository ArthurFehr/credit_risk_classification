# Credit Risk Classification

## Objective

My main goal here is to build a classification model to predict the credit risk of loans (good or bad) and to derive meaningful insights from the data. 

## Dataset Description

The dataset contains the repayment performance of loans and the characteristics of loan applicants at the time they requested the loan. It encompasses a diverse range of both numerical and categorical features that significantly influence credit decisions. This data set can be found at: https://archive.ics.uci.edu/ml/datasets/statlog+(german+credit+data)

### Attributes:

- **Status of existing checking account**: Financial stability reflected by the applicant's existing checking account.
- **Duration in months**: The credit's duration, measured in months.
- **Credit history**: The applicant's credit history, revealing past credit behavior.
- **Purpose**: The intended use of the credit, such as for furniture, a car, or education.
- **Credit amount**: The requested credit amount.
- **Savings account/bonds**: Applicant's savings status, indicating financial resources.
- **Present employment since**: Duration of the applicant's current employment.
- **Installment rate in percentage of disposable income**: Portion of disposable income allocated to credit repayment.
- **Personal status and sex**: Personal status and gender of the applicant.
- **Other debtors / guarantors**: Presence of other debtors or guarantors associated with the credit.
- **Present residence since**: Duration of the applicant's current residence.
- **Property**: Type of property owned by the applicant.
- **Age in years**: Age of the applicant.
- **Other installment plans**: Presence of other installment plans.
- **Housing**: Type of housing the applicant resides in.
- **Number of existing credits at this bank**: Number of existing credits with this bank.
- **Number of Accounts**: Number of accounts held by the applicant.
- **Job**: The applicant's profession or job type.
- **Number of people being liable to provide maintenance for**: Number of dependents or individuals the applicant is responsible for.
- **Telephone**: Availability of a telephone for the applicant.
- **Foreign worker**: Indicates if the applicant is a foreign worker.
- **Credit risk**: Target variable indicating the loan's performance (1 = Good, 2 = Bad).

## Key findings from the EDA

- The dataset has 1,000 rows and 21 features, and there are 700 good loans and 300 bad loans. 

- There are two values for the number of accounts (1 and 2), and that all applicants with good loans have 1 account, while all applicants with bad loans have 2 accounts. I found that puzzling. From my experience, a person can only have a single account at a financial institution (considering it is a personal account and she is the only owner of it). But even if it was possible to have more than one, why there is no one with 3 or more accounts? But most important, why all applicants with bad loans would have 2 accounts, while applicants with good loans have just 1? If this is indeed correct, then we could use only this feature to perfectly classify the credit risk of loans. I have a felling however that there might be something going on here and that I should not use this variable in the classification. Maybe the loan is classified as bad and then the applicant make another account? In this scenario, the "Number of Accounts" is defined after the loan is classified as bad, so I defitinely should not use it in the prediction.

- Compared to good loans, bad loans have slighly higher durations (good: 19.2 vs bad: 24.9) and higher amounts (good: 2985 vs bad: 3938), and the median of installment rates in percentage of disposable income is higher (good: 3% vs bad: 4%). Holders of bad loans are also slightly younger than holders of good loans on average (good: 36 vs bad: 34).

- The more an applicant has on a savings or on a checking account, the more likely she is to have a good loan. Which makes sense, since people with more money are more likely to pay their loans. Surprisingly though, the great majority of applicants without a checking account or a savings account also have good loans. It might be the case that they do not have an account in this bank, but on another bank, so they actually have the money to pay for their loans.

- Surprisingly, credit history does not seem to be much correlated with the credit risk of the loan. There is a great number of applicants with a critical account that have a good loan, while the majority of applicants with all credits paid back have bad loans.

- The longer people have their jobs, the more likely they are to have good loans. This makes sense, since people with longer jobs are more likely to have more money to pay for their loans.

- The proportion of applicants with bad loans is lower among those who own a house relatively to those who rent or live for free. 

- Among applicants with other installment plans (banks + stores), the proportion of those with bad loans is higher than the proportion of bad loans among applicants with no other installment plan. This suggests that people might fail to pay their loans because they have other loans to pay.

- The majority of applicants are foreign workers, and the proportion of bad loans among them is higher than the proportion of bad loans among non-foreign workers.

## Main result

I have developed a logistic regression model to predict the type of credit risk (goor or bad) of credit loans. After carefully pre processing the data, fine tuning the hyperparameters and selecting the best decision threshold for this problem, I arrived at a model with following metrics:

| Precision | Recall | Accuracy | F1-Score | AUC |
| --------- | ------ | -------- | -------- | --- |
| 0.57      | 0.64   | 0.75     | 0.60     | 0.77|

And with the following confusion matrix:

| Credit risk | Predicted Good | Predicted Bad | Total |
| ----------- | -------------- | ------------- | ----- |
| Good        | 112            | 29            | 141   |
| Bad         | 21             | 38            | 59    |

Even thought the metrics are not high, I have tuned the model to obtain the best recall measure possible. I did so because I believe that it is important in this business to correctly classify bad loans as bad, even if it means that I will also classify some good loans as bad. I am assuming here that our goal is to maximize the returns on the loans, given for instance by the amount applicants are paying back to the bank (in the form of principal + interests) dividided by the loan amount. So it seems to me fair enough to try as hard as possible to avoid bad loans.

In order to illustrate the value of this model, let's assume that the bank makes 200 loans of $100 each, 141 of which will be good and 59 of which will be bad (following the target variable distribution in the test set). Assume also that bad loans have a zero return to the bank (applicants pay nothing back), and that good loans have a return of 1.5 (applicants pay 50% more than the loan amount). 

If the bank uses the model to classify the loans and then decides to give loans only to those classified as good loans, it will give loans to 133 applicants predicted to be good (112 good + 21 bad). So it will receive back 112 * 100 * 1.5 = $16,800 (from the good loans), while it will loan 133 * 100 = $13,300. So the bank will make a profit of $3,500.

If the bank does not use the model, it will give loans to every applicant. Then it will receive back 141 * 100 * 1.5 = $21,150, while it will concede 200 * 100 = $20,000. So the bank will make a profit of only $1,150.

This is a very simple example, highly dependent on the assumptions I made, but it shows in a simple manner that even a model far from perfect can be useful to the bank to increase its profits. 

## Next steps

To further develop this model to help the bank to predict the credit risk of its loans, I believe I could improve the model as follows:

1. Use other machine learning algorithms to see how they compare with the logistic regression model.

2. Use other values for the C hyperparameter to see if I can reduce the number of features used by the model, while maintaining a good recall value. Even though I optimized the C hyperparameter to have the better recall, I believe I could get a more explainable model at a small cost in terms of recall.

