# ENTER TITLE HERE

### Business Problem 
Forest cover type classification is important for environmental monitoring, land management, and ecological planning. The goal of this project is to predict the forest cover type from cartographic variables such as elevation, slope, aspect, and wilderness area.

This problem is especially challenging because the target variable, Cover_Type, is highly imbalanced. Some classes appear far more often than others, which can cause standard machine learning models to favor the majority classes and perform poorly on rare cover types. Because of this, the project focuses on comparing imbalance-handling strategies to improve minority-class performance.

### Project Overview 
This project compares multiple machine learning approaches for multi-class classification on an imbalanced forest cover dataset. I evaluated a baseline Decision Tree and Random Forest, then compared two class imbalance methods: Random Undersampling and Balanced Bagging. I also included XGBoost as a stronger boosted-tree model.

The main goal was to determine which method produced the best balance between overall performance and minority-class detection, using metrics such as macro F1-score, classification reports, and confusion matrices.

### Data
For this project we utilized the Roosevelt National Forest Dataset which was obtained from Kaggle.

https://www.kaggle.com/datasets/uciml/forest-cover-type-dataset

Type: tabular, multiclass classification  

Size: (581012, 55)

Key features: Elevation, Aspect, Slope, Horizontal/Vertical Distance to Hydrology, Wilderness Area, Soil Type

### Data Preprocessing 
Upon first examination, using missingno, we saw that there weren't any missing or duplicate values. No cleaning or handling missing values was conducted. 
The data preprocessing steps included:

Checking class distribution to confirm imbalance.

Splitting the dataset into train, validation, and test sets using stratified sampling so that each split preserved the original class proportions.

Encoding the target variable for XGBoost, since XGBoost requires class labels to start at 0.

Keeping the same observation indices across models so that all methods were evaluated on the same splits.

Because the dataset was already clean and structured, no heavy imputation was required.

### Exploratory Data Analysis 
The EDA focused on understanding class imbalance and the relationship between predictors and the target. The following visualizations were included:

Bar chart of class distribution for Cover_Type

Correlation heatmap of numeric features

Feature distribution plots for important variables such as Elevation and Slope

Class-wise comparisons to understand which variables separate the cover types

The EDA showed that the dataset is strongly imbalanced and that some features, especially Elevation, are highly informative for classification.

<img width="597" height="455" alt="image" src="https://github.com/user-attachments/assets/6b1c0f53-86fc-412d-b43e-43ef9ba74c29" />

The graph above is a histogram that shows the count numbers of our target variable Cover_type. 

Cover_Type	

2:	283301

1:	211840

3:	35754

7:	20510

6:	17367

5:	9493

4:	2747


<img width="788" height="490" alt="image" src="https://github.com/user-attachments/assets/2777e73d-d530-4b6a-9dc3-1a52594ba543" />


### Modeling Approach 
I used both baseline and advanced models to compare performance under imbalance handling.

Baseline models
- Decision Tree

- Random Forest

These were included because they are easy to interpret and provide a strong baseline for tabular classification problems.

Advanced model
XGBoost

XGBoost was selected because it is a powerful gradient boosting model that often performs well on structured data.

Imbalance techniques compared
- Random Undersampling
- SMOTE 
- Balanced Bagging
- Cost-sensitive


Random Undersampling was used as a simple method to reduce majority class dominance. Balanced Bagging was used as a more advanced ensemble-based technique that trains each model on a balanced bootstrap sample.

### Model Training 
All models were trained using a 60/20/20 stratified split:

60% training

20% validation

20% test

Hyperparameter tuning was performed using RandomizedSearchCV with Stratified K-Fold cross-validation. This ensured that each fold preserved the class distribution and that tuning was done only on the training data.

Tools used
- Python
- Pandas
- NumPy
- scikit-learn
- imbalanced-learn
- XGBoost
- Matplotlib / Seaborn

Example hyperparameters tuned
- Decision Tree: max_depth, min_samples_split, min_samples_leaf
- Random Forest: n_estimators, max_depth, min_samples_split, min_samples_leaf
- XGBoost: n_estimators, learning_rate, max_depth, subsample, colsample_bytree



### Results 
The primary metric used for comparison was macro F1-score because the dataset is highly imbalanced. Macro F1 gives equal weight to every class, which is more informative than accuracy in this setting.

Why macro F1?
Accuracy can look good even when the model performs poorly on minority classes. Macro F1 shows whether the model is actually learning all classes, not just the majority ones.

Results summary
A model comparison table and validation plot were used to compare:

RUS + Decision Tree

RUS + Random Forest

RUS + XGBoost

Balanced Bagging + Decision Tree

Balanced Bagging + Random Forest

Balanced Bagging + XGBoost

In addition to macro F1, I included:

Classification reports

Confusion matrices

These helped show which classes were being predicted well and which were still difficult.


### Model Interpretation
The best model based on our results was SMOTE+Random Forest, so we will be interpreting how this model works.

<img width="1319" height="1720" alt="image" src="https://github.com/user-attachments/assets/13cb5d86-5474-4b69-99f6-6781c32332e6" />

The SHAP results supported the same general story as permutation importance: elevation and the distance-based features played a major role, and their influence changed direction depending on the observation. That is exactly why SHAP was useful - it did not only rank features, but it also showed that the effect of a feature could vary by case instead of acting as one fixed simple rule. 

 
<img width="990" height="690" alt="image" src="https://github.com/user-attachments/assets/14616ea4-6041-41b4-8aaa-77caea34a008" />

The permutation importance reveals which features, when randomly shuffled, cause the greatest drop in macro-F1. This means which features the model relied on them most for correct predictions. Typically for the Forest Cover Type dataset:

Elevation tends to be the most discriminative feature, as different tree species thrive at different altitudes.
Horizontal_Distance_To_Roadways and Horizontal_Distance_To_Hydrology capture proximity effects that influence soil moisture and human disturbance.
Wilderness_Area dummy variables reflect the distinct ecological zones with different dominant species.
Features with near-zero or negative importance can be considered candidates for removal to simplify the model.

<img width="989" height="490" alt="image" src="https://github.com/user-attachments/assets/14a4eac1-22be-4beb-a803-01fd35afea91" />

The Error Analysis showed that the commonly confused pairs tend to be:

- Class 1 (Spruce/Fir) and Class 2 (Lodgepole Pine), likely because the two are the dominant classes and share overlapping elevation ranges and soil types, making them hard to separate even for tree experts.
- Class 3 (Ponderosa Pine) and Class 6 (Douglas Fir), they both occupy lower elevation zones with similar slope characteristics in Wilderness Area 2.
- Class 4 (Cottonwood/Willow) and Class 5 (Aspen), these minority classes have very few training samples, leading to high misclassification rates despite SMOTE.

It's possible that these classes commonly confused because the species that share similar elevation, soil, and hydrological conditions are naturally harder to distinguish from remotely sensed data alone. In this case it was classes 1 and 2. Another possible reason is that even after SMOTE and resampling, synthetic minority samples may not fully capture real-world feature variability. Finally, the soil types are-hot encoded collapses nuanced soil chemistry into 40 binary columns, which could potentially lose fine-grained distinctions.

### Key Interpretation

### Conclusion

### Future Work 

### How to run 

### Repository Structure

### Requirements 
