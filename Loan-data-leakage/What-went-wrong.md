## Data storage and architecture issues
The Problem The issue stems from a breach of input data integrity and vulnerabilities in the data pipeline architecture. Our analysis revealed that this was not a classic database hack, but rather a compromise of the data pipeline itself. The data contamination occurred due to three main architectural vulnerabilities:
    1. Lack of automated validation for data consistency and checksums of transmitted data packets.
    2. A complete lack of filtering and verification for data coming from external sources.
    3. An architectural loophole that allowed direct, unauthorized injection of manipulated records into both the training and scoring databases.
As a result, the model was trained and fed with data that did not reflect the real risk profile of actual customers.
## Data problems
To build the model, the company decided to use the following independent variables for prediction:

| Standard              | Problematic          |
|----------------------|---------------------|
| Income               | Gender              |
| Employment Type      | Marital Status      |
| Length of Employment | Place of Residence  |
| DTI ratio            | Transaction History |
| ...                  | ...                 |

The table above clearly shows that apart from the typical data needed for risk prediction, the model also included sensitive data. This increases the probability that customers who would normally get a loan without this data would stop receiving them, and vice versa. This introduces an element of discrimination.
While transaction history and location are standard in risk analysis, when combined with gender and marital status, they started acting as proxy variables, multiplying the discriminatory effect and replicating biases.
Example Scenario: A single mother with a child, living in a smaller town, with high expenses for medications due to a serious illness. The algorithm could automatically discriminate against such a person, even with other compensating factors—such as a well-paying remote job with promotion potential.
According to regulations like the AI Act, an approach that results in discrimination contradicts the principle of fairness and can lead to the system failing compliance requirements.
## Model Problems
The XGBoost model itself can be resistant to overfitting, but only under certain conditions. These include the amount of input data, its diversity, and proper fine-tuning of the model's hyperparameters (such as the depth of decision trees, the number of trees, and L1/L2 regularization).
As it turned out, the model's hyperparameters were chosen incorrectly—there was no proper regularization (L1/L2), and the decision trees were too deep. Because of this, instead of capturing general trends, the model uncritically adjusted to the manipulated training data (overfitting), losing its ability to generalize to new, real customers.
Furthermore, XGBoost is less interpretable than simpler models like logistic regression. While this is not a strict blocker for putting such a model into production, the company must be careful about compliance with European regulations (AI Act, GDPR) and US laws (Fair Credit Reporting Act, Equal Credit Opportunity Act, Consumer Financial Protection Bureau). This transparency can be achieved using the SHAP method, which explains the impact of individual features on the model's final decisions.
