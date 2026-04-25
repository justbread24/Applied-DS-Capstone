# ENTER TITLE HERE

### Business Problem 

### Project Overview 

### Data
For this project we utilized the Roosevelt National Forest Dataset which was obtained from Kaggle.

https://www.kaggle.com/datasets/uciml/forest-cover-type-dataset

Type: numerical 

Size: (581012, 55)

Key features: Elevation, aspect, and slope

### Data Preprocessing 
Upon first examination, using missingno, we saw that there weren't any missing or duplicate values. No cleaning or handling missing values was conducted. 

### Exploratory Data Analysis 
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

### Model Training 

### Results 

### Model Interpretation
The best model based on our results was SMOTE+Random Forest, so we will be interpreting how this model works.

<img width="1319" height="1720" alt="image" src="https://github.com/user-attachments/assets/13cb5d86-5474-4b69-99f6-6781c32332e6" />

Global Explanation (Bar Plot and Beeswarm):

Elevation is typically the most impactful feature — higher elevations strongly favor Spruce/Fir (Class 1) while lower elevations favor Ponderosa Pine (Class 3).
Horizontal_Distance_To_Roadways and Horizontal_Distance_To_Hydrology reveal how tree species distribution is tied to human infrastructure and water access.
Wilderness_Area dummy variables have high SHAP values — confirming that geographic zone is a strong predictor of which tree type dominates.

 
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
