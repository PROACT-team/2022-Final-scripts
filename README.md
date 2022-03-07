# Goals
- To develop individualized prediction models for gastrostomy in ALS patients

**Target Variables**     
- Time to loss of autonomy in swallowing function (ALSFRS-R_Q3_Swallowing score dropping to 1.0)

**Python scripts**  
https://github.com/PROACT-team/2022-Final-scripts

**data files**      
https://drive.google.com/drive/folders/1O_nh1urnSdugGl2qJao7_Irw2CwIrHfO?usp=sharing

# Preparations  

## Data source 

PRO-ACT database (raw data)   
https://nctu.partners.org/proact  

Preprocessed csv files   
https://www.dropbox.com/sh/3lmi5ii3sgyi7o3/AACeVLTGtSDXSKIaX6P7Hxj2a?dl=0  
(local) /Users/hong/Dropbox/ALSmaster/PROACT  

## Scripts   
**1_Preprocessing_0227.ipynb**   
Extracting features and target variables from PRO-ACT dataset & Data imputation

**1_Preprocessing_snuh_0227.ipynb**   
Extracting features and target variables from tertiary ALS clinic & Data imputation

**2_feature_selection_0227.ipynb**    
Lasso & permutation importance based selection
     
**3_Fitting_and_evaluation_Q3_1_0227.ipynb**      
Building prediction model & testing model performance

**4_SNUH_Registry_External_validation_0227.ipynb**   
Testing model by external validation using SNU hospital ALS registry data

**Proact_vs_Snuh_0227.ipynb**
Investigating data heterogeneity between PRO-ACT and SNUH registry data

# Gastrostomy prediction

**Goal** : 
build a model to predict the time to loss of autonomy in swallowing function

**Select features according to previous studies** :    
- Age(ordered categorical data by 5yrs), Gender, onset_site(Bulbar/Non-bulbar), onset_delta(months), diag_delta(months), diag_minus_onset(months) 
- ALSFRS (total score, domain scores; bulbar, motor, respiratory), FVC, Creatinine, Weight (cf. Q2 score was excluded from bulbar score because salivation can be easily affected by drug usage)
- References https://www.tandfonline.com/doi/abs/10.3109/17482960802566824?journalCode=iafd19, https://www.nature.com/articles/nbt.3051
- calculate mean values over the first 3 months for ALSFRS, FVC, Creatinine     
- calculate slope values over the first 3 months for the time-resolved features (ALSFRS/FVC/Creatinine/Weight, per month)            
- exclude cases with missing values in onset_delta because of a large proportion of missing values (same cases with missing values in diag_delta, diag_minus_onset, 99.9% similar cases in onset_site)
- exclude cases with missing values in mean_ALSFRS_q beacause all 10 sub scores had same large proportion of missing values 

**Imputation** :
- Missing data nullity matrix
<img src="https://user-images.githubusercontent.com/79128639/135747347-39fef080-14d1-43df-9a00-a46ef2b6401d.PNG" width="60%">

- Missing data nullity matrix after excluding cases with missing values in onset_delta, mean_ALSFRS_Q
<img src="https://user-images.githubusercontent.com/79128639/135713545-8b813052-d727-4eec-8f13-c73ee36f45b9.png" width="60%">

- Missing data proportion circle graph
<img src="https://user-images.githubusercontent.com/79128639/135709837-3121e213-d58c-4f83-a766-ac52259289cc.png" width="60%">

- Imputation using iterativeImputer in scikit-learn

**Algorithm** :          
- Accelerated failure time (parametric)
- Cox proportional hazard model (semi-parametric)
- Random survival forests (machine-learning)

**Feature selection - Wrapper method applied to each algorithm** : 
- Lasso regression(AFT, COX) & permutation importance based selection(RSF)
- selected features
![feature selection](https://user-images.githubusercontent.com/78291206/156963956-44dbbcbb-7d7c-4544-a616-cff3938d6fde.png)


**Results** :         
- Evaluating model performance; C-index in Repeated 5-fold cross validation
![C-index](https://user-images.githubusercontent.com/79128639/135747430-93d02c66-7ffe-4feb-8def-ffd85ec411fe.PNG)
models show C-index around 0.84


- Group Stratification
![group stratification](https://user-images.githubusercontent.com/79128639/135747846-858af067-a9e7-4fb8-89a1-06ed7d239582.PNG)
Prediction matches well with Kaplan Meier in Rapid and Intermediate group but not much in slow group

![group stratification2](https://user-images.githubusercontent.com/79128639/135747505-73614925-48de-4fbe-a23f-c1ee73470925.PNG)
Thick colored lines are interquartile range between 25 to 75 % probability time to event. And the dots in the middle are the 50% probability times    
