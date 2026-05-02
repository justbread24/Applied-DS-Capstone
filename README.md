# FOREST TREE COVER TYPE PREDICTOR

## Business Problem 
This project predicts forest cover type from cartographic and environmental variables using machine learning. The dataset is highly imbalanced, so the project focuses on improving performance on minority classes rather than relying on accuracy alone.

To address this, I compared several models and imbalance-handling strategies, then selected the best-performing approach based on validation and test results. The final model was SMOTE + Random Forest, which gave the best balance of predictive performance, stability, and minority-class handling.

## Project Overview 
Forest cover classification is useful for environmental monitoring, land management, and ecological decision-making. However, the target variable, Cover_Type, is severely imbalanced, which makes the problem more difficult.

If the model is trained without addressing imbalance, it can become biased toward the majority classes and perform poorly on rare cover types. That is why this project emphasizes stratified sampling, class imbalance techniques, and evaluation metrics that reflect performance across all classes.

## Data
For this project we utilized the Roosevelt National Forest Dataset which was obtained from Kaggle.

https://www.kaggle.com/datasets/uciml/forest-cover-type-dataset

Type: tabular, multiclass classification  

Size: (581012, 55)

Key features: Elevation, Aspect, Slope, Horizontal/Vertical Distance to Hydrology, Wilderness Area, Soil Type

## Data Preprocessing 
Upon first examination, using missingno, we saw that there weren't any missing or duplicate values. No cleaning or handling missing values was conducted. 


The data preprocessing steps included:
- Checking class distribution to confirm imbalance.
- Splitting the dataset into train, validation, and test sets using stratified sampling so that each split preserved the original class proportions.
- Encoding the target variable for XGBoost, since XGBoost requires class labels to start at 0.
- Keeping the same observation indices across models so that all methods were evaluated on the same splits.


Because the dataset was already clean and structured, no heavy imputation was required.

## Exploratory Data Analysis 
The EDA focused on understanding class imbalance and the relationship between predictors and the target. The following visualizations were included:

- Bar chart of class distribution for Cover_Type
- Correlation heatmap of numeric features
- Feature distribution plots for important variables such as Elevation and Slope
- Class-wise comparisons to understand which variables separate the cover types

The EDA showed that the dataset is strongly imbalanced and that some features, especially Elevation, are highly informative for classification.

<img width="597" height="455" alt="image" src="https://github.com/user-attachments/assets/6b1c0f53-86fc-412d-b43e-43ef9ba74c29" />

The graph above is a histogram that shows the count numbers of our target variable Cover_type. This shows us that there is a severe class imbalance where classes 1 and 2 are the majority while classes 4 and 5 are the minority, however the other classes are not represented either. 


<img width="788" height="490" alt="image" src="https://github.com/user-attachments/assets/2777e73d-d530-4b6a-9dc3-1a52594ba543" />

This graph shows us the different classes living in the different wilderness areas. As you can see class 2 are able to live in any wilderness and are able to make up a significant amount of the cover types that live in those areas. Then, some classes such as class 3 and 6 seem to thrive in wilderness area 4. 


<img width="2210" height="2190" alt="image" src="https://github.com/user-attachments/assets/3b8207c7-66ad-473b-959f-44f698d2e2aa" />

The Heat map illustrates that the features in our dataset are not closely correlated except in a few instances such as soil type and wilderness type.




## Modeling Approach 
I tested several machine learning models to compare simple baselines with stronger tree-based methods.

**Models tested** 
- Logistic Regression
- Decision Tree
- Random Forest
- XGBoost

**Why these models**
- **Logistic Regression** served as a baseline.
- **Decision Tree** offered interpretability but could overfit.
- **Random Forest** improved stability by combining many trees.
- **XGBoost** was tested because it performs well on structured tabular data.

**Final selected model**
- **SMOTE + Random Forest**
This model performed best overall because it handled the imbalanced classes effectively while still producing strong predictive results. Random Forest was also a good fit for this dataset because it can model complex relationships between terrain features and forest cover type.

## Model Training 
The models were evaluated using:

- **Accuracy**
- **Balanced Accuracy**
- **Macro F1-score**

**Why these metrics** 
- **Accuracy** shows overall correctness.
- **Balanced Accuracy** accounts for imbalance by averaging recall across classes.
- **Macro F1-score** is especially important because it gives equal weight to all seven cover types.

**Training process**
- Hyperparameter tuning was performed using **RandomizedSearchCV**.
- Cross-validation was **stratified** to preserve class proportions in each fold.
- The final model was selected based on both performance and stability, not just the highest score.

## Fine Tuning

After selecting Random Forest as the best model family, I used **RandomizedSearchCV** to improve performance.

**Best model settings**
- SMOTE with `k_neighbors = 3`
- Random Forest with 300 trees
- `max_features = sqrt`
- No maximum tree depth

**Validation performance**
- **Accuracy:** 0.9507
- **Balanced Accuracy:** 0.9362
- **Macro F1-score:** 0.9256

**Stability check**
I also tested the model with five different random seeds. The average Macro F1-score was **0.9253**, with a standard deviation of **0.0013**. This showed that the model was stable and not dependent on one random split.



## Results 
The final model achieved strong and consistent performance on the test set.

### Test performance
- **Accuracy:** 0.9517
- **Balanced Accuracy:** 0.9341
- **Macro F1-score:** 0.9267

These results were very close to the validation scores, which suggests that the model generalized well and did not overfit.

### Key observations
- The most common confusion was between Class 1 and Class 2.
- Some confusion also occurred between Class 3 and Class 6.
- These classes appear in similar environmental conditions, which makes them harder to separate.

### Visual outputs
- Validation and test confusion matrices
- Model comparison plots
- Macro F1-score bar chart


## Model Interpretation
For this section we will be using the best model, which is SMOTE+Random Forest, and interpreting it. To understand what the model was learning, I used both feature importance and SHAP.

### Most important features
<img width="990" height="690" alt="image" src="https://github.com/user-attachments/assets/14616ea4-6041-41b4-8aaa-77caea34a008" />

- Elevation
- Horizontal distance to roadways
- Horizontal distance to fire points
- Horizontal distance to hydrology
- Wilderness area
- Vertical distance to hydrology
- Hillshade
- Soil type
- Aspect

### Why this matters
The most important predictors were meaningful environmental variables, which increased confidence in the model. Elevation being the top feature makes sense because forest cover types are strongly tied to climate and terrain conditions.

### SHAP insights
SHAP confirmed that the model relied on realistic environmental patterns rather than random noise. It also helped explain why some classes were harder to distinguish, since certain forest cover types naturally overlap in environmental space.

The best model based on our results was SMOTE+Random Forest, so we will be interpreting how this model works.

<img width="1319" height="1720" alt="image" src="https://github.com/user-attachments/assets/13cb5d86-5474-4b69-99f6-6781c32332e6" />

The SHAP results supported the same general story as permutation importance: elevation and the distance-based features played a major role, and their influence changed direction depending on the observation. That is exactly why SHAP was useful - it did not only rank features, but it also showed that the effect of a feature could vary by case instead of acting as one fixed simple rule. 



<img width="989" height="490" alt="image" src="https://github.com/user-attachments/assets/14a4eac1-22be-4beb-a803-01fd35afea91" />

The Error Analysis showed that the commonly confused pairs tend to be:

- Class 1 (Spruce/Fir) and Class 2 (Lodgepole Pine), likely because the two are the dominant classes and share overlapping elevation ranges and soil types, making them hard to separate even for tree experts.
- Class 3 (Ponderosa Pine) and Class 6 (Douglas Fir), they both occupy lower elevation zones with similar slope characteristics in Wilderness Area 2.
- Class 4 (Cottonwood/Willow) and Class 5 (Aspen), these minority classes have very few training samples, leading to high misclassification rates despite SMOTE.

It's possible that these classes commonly confused because the species that share similar elevation, soil, and hydrological conditions are naturally harder to distinguish from remotely sensed data alone. In this case it was classes 1 and 2. Another possible reason is that even after SMOTE and resampling, synthetic minority samples may not fully capture real-world feature variability. Finally, the soil types are-hot encoded collapses nuanced soil chemistry into 40 binary columns, which could potentially lose fine-grained distinctions.

## Key Interpretation
- SMOTE helped the model learn minority classes more effectively.
- Random Forest provided a strong balance of performance and stability.
- Stratified splitting was necessary for fair evaluation.
- Macro F1-score gave a clearer picture of model quality than accuracy alone.
- Elevation was the strongest predictor of forest cover type.

The final model is useful as a decision-support tool, but it should not replace expert judgment.

## Conclusion
This project built a complete machine learning workflow for forest cover type prediction. The workflow included data understanding, preprocessing, class imbalance handling, model comparison, tuning, explainability, testing, and deployment.

The final model, **SMOTE + Random Forest**, performed strongly on both validation and test data. The model handled the imbalanced multiclass problem well, and the explainability analysis showed that the predictions were driven by meaningful environmental features.

Overall, the project demonstrates that machine learning can be a useful decision-support tool for forest cover classification and environmental analysis.

## Future Work 
Possible improvements include:
- Adding remote sensing or climate-related features
- Testing the model on other forest regions
- Adding explainability directly into the Streamlit app
- Improving input validation and user guidance
- Exploring additional imbalance-handling methods such as Balanced Random Forest or cost-sensitive learning

## How to run 
### 1. Install dependencies
```bash
pip install -r requirements.txt
```

### 2. Run the project
1. Load the dataset into the `data/` folder.
2. Run the preprocessing notebook or script.
3. Train the models.
4. Evaluate validation and test results.
5. Review the saved figures and metrics.
6. Run the Streamlit app for interactive predictions.

### 3. Launch the app
```bash
streamlit run app.py
```

  
## Repository Structure
Folder descriptions
- data/: Raw and cleaned datasets.
- notebooks/: EDA, preprocessing, and model development notebooks.
- models/: Saved trained models.
- results/: Metrics tables and predictions.
- images/: Charts, confusion matrices, and interpretation figures.

## Requirements 
- pandas
- numpy
- scikit-learn
- imbalanced-learn
- xgboost
- matplotlib
- seaborn
- shap
- jupyter
