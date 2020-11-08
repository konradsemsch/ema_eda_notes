# Introduction

# Baseline workflow

This section serves as a synthesis of all notes and presents the most basic approach and set of concrete steps that can be used for validation of any model:

1) Correlation analysis (numerical vs. other feature **types**)
2) ...

## Three laws of model explanation

1) **Prediction’s validation:** for every prediction of a model, one should be able to verify how strong is the evidence that supports the prediction.
   
2) **Prediction’s justification:** for every prediction of a model, one should be able to understand which variables affect the prediction and to what extent.
   
3) **Prediction’s speculation:** for every prediction of a model, one should be able to understand how the prediction would change if the values of the variables included in the model changed.

<br/>

Let's illustrate the these laws with this practical tree model example:

<img src="img/tree_model.png" alt="tree_model" class="center" width="400"/>

* regarding _prediction’s validation_, we see how many patients fall in a given category in each node
* with respect to _prediction’s justification_, we know which explanatory variables are used in every decision path
* regarding _prediction’s speculation_, we can trace how will changes in particular variables affect the model’s prediction

## Black-box vs. Glass-box (White-box) models

1) **Black-box:** is used for models with a complex structure that is hard to understand by humans. This usually refers to a large number of model coefficients or complex mathematical transformations. As people vary in their capacity to understand complex models, there is no strict threshold for the number of coefficients that makes a model a black-box. In practice, for most people, this threshold is probably closer to 10 than to 100.

2) **Glass-box:**  is used for models easy to understand (though maybe not by every person). It has a simple structure and a limited number of coefficients.

Examples of glass-box models are:
* linear regression
* logistic regression
* GLM, GAM and more
* decision tree
* decision rules
* RuleFit algorithm
* Naive Bayes
* K-Nearest Neighbors

## Model-specific vs. Model-agnostic methods

"(...) this variety of model-specific approaches does lead to issues, though. For instance, one cannot easily compare explanations for two models with different structures. Also, every time a new architecture or a new ensemble of models is proposed, one needs to look for new methods of model exploration. Finally, no tools for model explanation or diagnostics may be immediately available for brand-new models.

"(...) for these reasons, we prefer not to assume anything about the model structure, as we may be dealing with a black-box model with an unspecified structure. Note that often we do not have access to model coefficients, but only to a specified Application Programming Interface (API) that allows querying remote models as, for example, in Microsoft Cognitive Services (Azure 2019). In that case, the only operation that we may be able to perform is the evaluation of a model on a specified set of data."
 
## Model development

* purpose of the model: inferential/ statistical modelling vs. predictive modelling - depending on the purpose there are important consequences for the methods used in the model development process

* the traditional CRISP-DM model building process (MBP):
  * business understanding
  * data understanding
  * data preparation
  * modelling
  * evaluation
  * deployment 

* the new MBP process proposed by Biecek: it recognizes that fact that consecutive iterations are not identical because the knowledge increases during the process and consecutive iterations are performed with different goals in mind

  * the process is split into five different phases (rows)
  * the process is split into four stages (indicated at the top of the diagram)
  * horizontal axis presents the time from the problem formulation to putting the model into practice (decommissioning)
  * for a particular phase, resources can be used in different amounts depending on the current stage of the process, as indicated by the height of the bars
  * there may be several iterations of different phases within each stage, as indicated at the bottom of the diagram

</br>

<img src="img/mdp_biecek.png" alt="tree_model" class="center" width="700"/>

# EMA (Explanatory Model Analysis)

## Quantitative methods

### Global (model-based)

Help to understand how do the model predictions perform overall, for an entire set of observations. The following examples illustrate situations in which dataset-level explainers may be useful:

* we may want to learn which variables are “important” in the model. For instance, we may be interested in predicting the risk of heart attack by using explanatory variables that are derived from the results of medical examinations. If some of the variables do not influence predictions, we could simplify the model by removing the variables

* we may want to understand how does a selected variable influence the model’s predictions? For instance, we may be interested in predicting prices of apartments. Apartment’s location is an important factor, but we may want to know which locations lead to higher prices?

* we may want to discover whether there are any observations, for which the model yields wrong predictions. For instance, for a model predicting the probability of survival after a risky treatment, we might want to know whether there are patients for whom the model’s predictions are extremely wrong. Identifying such a group of patients might point to, for instance, an incorrect form of an explanatory variable, or even a missed variable

* we may be interested in an overall “performance” of the model. For instance, we may want to compare two models in terms of the average accuracy of their predictions

___

**Model performance metrics**

There were many individual metrics presented for different types of target variables. I won't note them down in details, and just made some general notes from the chapter.

* goodness-of-fit (GoF) - mainly used for explanatory models (how well do the model’s predictions explain (fit) dependent-variable values of the observations used for developing the model)

* goodness-of-prediction(GoP) - mainly applied for predictive models (how well does the model predict the value of the dependent variable for a new observation)

"(...) it is worth mentioning that there are two important aspects of prediction: **calibration and discrimination**. Calibration refers to the extent of bias in predicted values, i.e., the mean difference between the predicted and true values. Discrimination refers to the ability of the predictions to distinguish between individual true values. For instance, consider a model to be used for weather forecasts in a region where, on average, it rains half a year. A simple model that predicts that every other day is rainy is well-calibrated because, on average, the resulting predicted risk of a rainy day in a year is 50%, which agrees with the actual situation. However, the model is not very much discriminative (for each calendar day, the probability of a correct prediction is 50%, the same as for a fair-coin toss) and, hence, not very useful. Thus, in addition to overall measures of GoP, **we may need separate measures for calibration and discrimination of a model**. Note that, for the latter, we may want to weigh differently the situation when the prediction is, for instance, larger than the true value, as compared to the case when it is smaller. Depending on the decision on how to weigh different types of disagreement, we may need different measures"

___

**Feature importance**

The proposed approach for measuring model-agnostic variable importance (applicable for any model and that can be used for making between-model comparisons) is called **permutation importance**.

**Idea:** to measure how much does a model’s performance change if the effect of a selected explanatory variable, or of a group of variables, is removed? To remove the effect, we use perturbations, like resampling from an empirical distribution or permutation of the values of the variable. If a variable is important, then we expect that, after permuting the values of the variable, the model’s performance will worsen. The larger the change in the performance, the more important is the variable.

> NOTE: the use of resampling or permuting data involves randomness. Thus, the results of the procedure may depend on the obtained configuration of resampled/permuted values. Hence, it is advisable to repeat the procedure several (many) times. In this way, the uncertainty associated with the calculated variable-importance values can be assessed.

1) Pros:

* model-agnostic
* works for every variable type
* easy to understand
* measures can be compared between models
* can be used to measure the importance of a single explanatory variable or a group of variables (e.g. groups of variables that are complementary to each other or are related to a similar concept)

> NOTE: if variables are correlated, then models like random forest are expected to spread importance across many variables, while in regularized-regression models the effect of one variable may dominate the effect of other correlated variables

2) Cons:

* its dependence on the random nature of the permutations (different results for different permutations)
* the value of the measure depends on the choice of the loss function, thus, there is no single, “absolute” measure 

___

**Partial dependence profiles (PD plots)**

**Idea:** to show how does the expected value of model prediction behave as a function of a selected explanatory variable? For a single model, one can construct an overall PD profile by using all observations from a dataset, or several profiles for sub-groups of the observations. Comparison of sub-group-specific profiles may provide important insight into, for instance, the stability of the model’s predictions. To show how does the expected value of model prediction behave as a function of a selected explanatory variable, the average of a set of individual ceteris-paribus (CP) profiles can be used. Recall that a CP profile shows the dependence of an instance-level prediction on an explanatory variable. A PD profile is estimated by the mean of the CP profiles for all instances (observations) from a dataset.

PD profiles are also useful for comparisons of different models:

* _Agreement between profiles for different models is reassuring_: some models are more flexible than others. If PD profiles for models, which differ with respect to flexibility, are similar, we can treat it as a piece of evidence that the more flexible model is not overfitting and that the models capture the same relationship.
  
* _Disagreement between profiles may suggest a way to improve a model_: if a PD profile of a simpler, more interpretable model disagrees with a profile of a flexible model, this may suggest a variable transformation that can be used to improve the interpretable model. For example, if a random forest model indicates a non-linear relationship between the dependent variable and an explanatory variable, then a suitable transformation of the explanatory variable may improve the fit or performance of a linear-regression model.

* _Evaluation of model performance at boundaries_: models are known to have different behavior at the boundaries of the possible range of a dependent variable, i.e., for the largest or the lowest values. For instance, random forest models are known to shrink predictions towards the average, whereas support-vector machines are known for a larger variance at edges. Comparison of PD profiles may help to understand the differences in models’ behavior at boundaries.

**PD variations:**

* _Clustered partial-dependence profiles:_ the mean of CP profiles is a good summary if the profiles are parallel. If they are not parallel, the average may not adequately represent the shape of a subset of profiles. To deal with this issue, one can consider clustering the profiles and calculating the mean separately for each cluster. To cluster the CP profiles, one may use standard methods like K-means or hierarchical clustering. The similarities between observations can be calculated based on the Euclidean distance between CP profiles.

* _Grouped partial-dependence profiles:_  it may happen that we can identify an explanatory variable that influences the shape of CP profiles for the explanatory variable of interest. The most obvious situation is when a model includes an interaction between the variable and another one. In that case, a natural approach is to investigate the PD profiles for the variable of interest within the groups of observations defined by the variable involved in the interaction.

* _Contrastive partial-dependence profiles:_ comparison of clustered or grouped PD profiles for a single model may provide important insight into, for instance, the stability of the model’s predictions. PD profiles can also be compared between different models.

1) Pros:

* simple way to summarize the effect of a particular explanatory variable on the dependent variable
* easy to explain and intuitive
* can be obtained for sub-groups
* can be compared across different models

2) Cons:

* may offer a crude and potentially misleading summarization when variables are strongly correlated (culustered and grouped PD plots are helpful in identifying those underlying relationships)

___

**Local dependance and accumulated-local profiles**

The main problem with PD plots that this section is trying to address is the following scenario:

"(...) CP/ PD profiles may be misleading if, for instance, explanatory variables are correlated. In many applications, this is the case. For example, in the apartment-prices dataset, one can expect that variables surface and number of rooms may be positively correlated, because apartments with a larger number of rooms usually also have a larger surface. Thus, in ceteris-paribus profiles, it is not realistic to consider, for instance, an apartment with five rooms and a surface of 20 square meters."

**Idea:** essentially, LD plots and AL profiles are also calculated over CP profiles (just as PD plots), but the final outcome differs in the way these individual observations are summarized together.

>TODO: get the explanation/ idea form the other book

**Summary**

* when explanatory variables are independent and there are no interactions in the model, the CP profiles are parallel and their mean, e.g., the PD profile adequately summarizes them
  
* when the model is additive, but an explanatory variable is correlated with some other variables, neither PD nor LD profiles will properly capture the effect of the explanatory variable on the model’s predictions. However, the AL profile will provide a correct summary of the effect.

* when there are interactions in the model, none of the profiles will provide a correct assessment of the effect of any explanatory variable involved in the interaction(s). This is because the profiles for the variable will also include the effect of other variables. Comparison of PD, LD, and AL profiles may help in identifying whether there are any interactions in the model and/or whether explanatory variables are correlated. When there are interactions, they may be explored by using a generalization of the PD profiles for two or more dependent variables (Apley and Zhu 2020).

___

**Residual-diagnostic plots**

>TODO: list some further, desirable residual properties

Analyzing residuals may be of interest in a number of cases:

* residuals can be used to identify potentially problematic instances. The single-instance explainers can then be used in the problematic cases to understand, for instance, which factors contribute most to the errors in prediction.

* for most models, residuals should express a random behavior with certain properties (like, e.g., being concentrated around 0). If we find any systematic deviations from the expected behavior, they may signal an issue with a model (for instance, an omitted explanatory variable or a wrong functional form of a variable included in the model).

* sometimes, however, we may be more interested in cases with the largest prediction errors, which can be identified with the help of residuals.

**Idea:** for a “good” model, residuals should deviate from zero randomly, i.e., not systematically. Thus, their distribution should be symmetric around zero, implying that their mean (or median) value should be zero. Also, residuals should be close to zero themselves, i.e., they should show low variability.

1) Pros:

* very useful in model exploration
* allows identifying different types of issues with model fit or prediction (e.g.: distributional assumptions or with the assumed structure of the model)
* helps in detecting groups of observations for which a model’s predictions are biased and, hence, require inspection

2) Cons:

* they rely on graphical displays - one may need to review a large number of graphs
* interpretation is not straightforward and is not always conclusive (it may not be immediately obvious which element of the model may have to be changed to remove the potential issue with the model fit or predictions)

### Summary

* _Exploration on training/testing data:_ In the case of model-performance assessment, it is natural **evaluate it using an independent testing dataset to minimize the risk of overfitting**. However, PD profiles or accumulated-local (AL) profiles can be constructed for both the training and testing datasets. **If the model is generalizable, its behavior for the two datasets should be similar**. If we notice significant differences in the results for the two datasets, the source of the differences should be examined, as it may be a sign of shifts in variable distributions. Such shifts are called data-drift in the machine learning world.

* _Correlated explanatory variables:_ all of the techniques presented in this chapter allow for analyzing the relation of individual variables in isolation, as well as detecting their join impact.

* _Comparison of models (champion-challenger analysis):_ it often appears that different models may offer a similar performance, while basing their predictions on different relations extracted from the same data (this phenomenon is called "Rashomon effect"). By comparing models, we can gain important insights that can lead to improvement of one or several of the models. For instance, when comparing a more flexible model with a more rigid one, we can check if they discover similar relationships between the dependent variable and explanatory variables. If they do, this can reassure us that both models have discovered genuine aspects of the data. Sometimes, however, the rigid model may miss a relationship that might have been found by the more flexible one. This could provide, for instance, a suggestion for an improvement of the former. 
___

### Local (prediction-based)

Help to understand how does a model yield a prediction for a single observation (instance). The following examples illustrate situations in which instance-level explainers may be useful:

* we may want to evaluate effects of explanatory variables on the model’s predictions. For instance, we may be interested in predicting the risk of heart attack based on a person’s age, sex, and smoking habits. A model may be used to construct a score (for instance, a linear combination of the explanatory variables representing age, sex, and smoking habits) that could be used for the purposes of prediction. For a particular patient, we may want to learn how much do the different variables contribute to the score?

* we may want to understand how would the model’s predictions change if values of some of the explanatory variables changed? For instance, what would be the predicted risk of heart attack if the patient cut the number of cigarettes smoked per day by half?

* we may discover that the model is providing incorrect predictions and we may want to find the reason. For instance, a patient with a very low risk-score experienced a heart attack. What has driven the wrong prediction?

Approaches presented below can be grouped into three main classes:

* one approach is to analyze how does the model’s prediction for a particular instance **differ from the average prediction** and how can the difference be distributed among explanatory variables? This method is often called the “variable attributions” approach

* another approach uses the interpretation of the model as a function and **investigates the local behavior of this function around the point (observation) of interest**. In particular, we analyze the curvature of the model response (prediction) surface around y_pred. In case of a black-box model, we may approximate it with a simpler glass-box model around y_pred

* yet another approach is to **investigate how does the model’s prediction change if the value of a single explanatory variable changes**? The approach is useful in the so-called “What-if” analyses. In particular, we can construct plots presenting the change in model-based predictions induced by a change of a single explanatory variable. Such plots are usually called ceteris-paribus (CP) profiles

**Break-down plots - additive attributions**

BD plots can be used to present “variable attributions,” i.e., the decomposition of the model’s prediction into contributions that can be attributed to different explanatory variables.

**Idea:** we assume that prediction is an approximation of the expected value of the dependent variable given values of explanatory variables. The underlying idea of BD plots is to capture the contribution of an explanatory variable to the model’s prediction by computing the shift in the expected value, while fixing the values of other variables.

**Calculation:**

1. Calculate the overall, average model prediction across all records
2. For an individual record:
   1. For each variable, calculate the overall, average model prediction having the value of that variable set to constant (to the value of the instance). This establishes the importance ordering of all variables for that instance
   2. For each subsequent variable according to that ordering, calculate the overall, average model prediction having next variables set to constant (to the value of the instance). This shows the cumulative, development of predictions for subsequent, most important variables
3. Calculate subsequent prediction differences to determine if a given variable has a positive or negative impact on the prediction

> NOTE: it is also worth mentioning that, for models that include interactions, the part of the prediction attributed to a variable depends on the order, in which one sets the values of the explanatory variables.

1) Pros:

* can be applied to any model and is easy to understand
* this approach reduces to an intuitive interpretation for linear models

2) Cons:

* interpretation may be misleading for models with interactions (due to the additive nature of the calculation), thus the choice of ordering is important for the interpretation
* may provide complex interpretations for models with many variables

**Break-down plots - interactions**

The following implementation of BD plots addresses the issue of interactions and different interpretation of variables depending on their ordering. In particular, the algorithm identifies interactions between pairs of variables and takes them into account when constructing break-down (BD) plots, but also could be extended to a larger number of variables.

**Idea:** interaction (deviation from additivity) means that the effect of an explanatory variable depends on the value(s) of other variables (s). This BP plots implementation differs from the basic one by also calculating the join impact of every variable pair on the prediction, and incorporating that information in the importance ordering process.

**Calculation:**

1. As before
2. For an individual record:
   1. As before
   2. Same as in point 2.1, but for each variable pair
   3. Calculate the net effect of the interaction by subtracting effects calculated in step 2.1 from 2.2. This gives an indication of the deviation from additivity and strength of interaction
   4. Performing ordering of variables to determine the cumulative impact on the prediction. Ordering is done according to the highest, absolute impact of individual variables, as well as variable pairs. If a variable in an interaction has a larger effect than the same variable treated individually, then the individual variable is no longer listed (its effect has been accounted for)
3. Calculate subsequent prediction differences to determine if a given variable/ variable pair has a positive or negative impact on the prediction

Most pros and cons of this implementation are same as in the basic example. Adding below only some additional points.

1) Pros:

* gives more correct interpretations for models with interactions

2) Cons:

* higher computational complexity than in the basic implementation, especially for models with a higher number of explanatory variables
* this logic is not based on any statistical tests, which especially for small samples, might lead to false-positive findings

**SHAP for Average Contributions**

The following approach builds up on the ones presented before to address the ordering issue of variables and their interactions. It is based on the idea of averaging the value of a variable’s attribution over all (or a large number of) possible orderings.

**Idea:** to remove the influence of the ordering of the variables, we can compute the mean value of the attributions over a high number of orderings. In that way we obtain the average impact of the variable on the prediction, as well as its impact distribution.

**Calculation:** the calculation of BP plots is the same, vut repeated for a given number of permutations e.g. 25 times.

> NOTE: it is worth noting that, for an additive model, the BD plots based approaches and in the current one lead to the same attributions. The reason is that, for additive models, different orderings lead to the same contributions. Since Shapley values can be seen as the mean across all orderings, it is essentially an average of identical values, i.e., it also assumes the same value.

1) Pros:

* Shapley values provide a uniform approach to decompose a model’s predictions into contributions that can be attributed additively to different explanatory variables

2) Cons:

* an important drawback of Shapley values is that they provide additive contributions (attributions) of explanatory variables. If the model is not additive, then the Shapley values may be misleading

* might be time consuming for larger models, but sub-sampling can speed things up

**LIME**

> NOTE: BD plots and Shapley values are most suitable for models with a small or moderate number of explanatory variables. None of those approaches is well-suited for models with a very large number of explanatory variables, because they usually determine non-zero attributions for all variables in the model. However, in domains like, for instance, genomics or image recognition, models with hundreds of thousands, or even millions, of explanatory (input) variables are not uncommon. In such cases, sparse explanations with a small number of variables offer a useful alternative. The most popular example of such sparse explainers is the Local Interpretable Model-agnostic Explanations (LIME) method and its modifications.

**Idea:** locally approximate a black-box model by a simpler glass-box model, which is easier to interpret.

**Calculation:**

1. For each prediction to explain, permute the observation n times.
2. Let the complex model predict the outcome of all permuted observations.
3. Calculate the distance from all permutations to the original observation.
4. Convert the distance to a similarity score.
5. Select m features best describing the complex model outcome from the permuted data.
6. Fit a simple model to the permuted data, explaining the complex model outcome with the m features from the permuted data weighted by its similarity to the original observation.
7. Extract the feature weights from the simple model and use these as explanations for the complex models local behavior.

Practical implementation of this idea involves three important steps, which are discussed in the subsequent subsections:

1. **Interpretable data representation** - the white-box model explainer does not operate on the same X-variable space as the original black-box model. For example, in image recognition the dimension is reduced by combining pixels into superpixels, in NLP applications groups of words are created, and in tabular data examples continuous data is often discretized
2. **Sampling around the instance of interest** - to develop a local-approximation glass-box model, we need new data points in the low-dimensional interpretable data space around the instance of interest. The data for the development of the glass-box model is often created by using perturbations of the instance of interest, e.g.: adding Gaussian noise to continuous variables/ discretizing them and shuffling their values, or turning of superpixels in the original image in image recognition applications
3. **Fitting the glass-box model** - a common choice are regularized linear models, such as: LASSO, that produces a sparse model. 

> NOTE: most practical examples of the LIME method are related to the text or image data.

1) Pros:

* is model-agnostic, as it does not imply any assumptions about the black-box model structure
* offers an interpretable representation, because the original data space is transformed into a more interpretable, lower-dimension space
* provides local fidelity, i.e., the explanations are locally well-fitted to the black-box model
* the underlying intuition for the method is easy to understand: a simpler model is used to approximate a more complex one

1) Cons:

* there are many different implementations of LIME for tabular data, which may lead to different interpretations
* another important point is that, because the glass-box model is selected to approximate the black-box model, and not the data themselves, the method does not control the quality of the local fit of the glass-box model to the data
* Fin high-dimensional data, data points are sparse. Defining a “local neighborhood” of the instance of interest may not be straightforward. Sometimes even slight changes in the neighborhood strongly affect the obtained explanations

To summarize, the **most useful applications of LIME are** limited to high-dimensional data for which one can define a low-dimensional interpretable data representation, **image analysis, text analysis, or genomics**.

**CP profiles**

**Idea:** ceteris-paribus (CP) profiles show how a model’s prediction would change if the value of a single exploratory variable changed. In essence, a CP profile shows the dependence of the conditional expectation of the dependent variable (response) on the values of the particular explanatory variable.

**Calculation:** CP profiles are the underlying calculation component of PD plots - they are calculated on an individual observation level.

1) Pros:

* easy to calculate, visualize and understand
* very useful tool for model sensitivity analysis

2) Cons:

* interpretation problems when there are correlated/ interaction variables in the model (extrapolating predictions to unlikely variable combinations)
* visualizing CP plots is problematic for models with hundreds of variables or for categorical variables with multiple levels

**CP oscillations**

Visual examination of ceteris-paribus (CP) profiles is insightful, however, in case of a model with a large number of explanatory variables, we may end up with a large number of plots that may be overwhelming. In such a situation, it might be useful to select the most interesting or important profiles. CP-oscillations is directly linked to CP profiles and can be seen as an instance-level variable-importance measure alternative to those presented above.

**Idea:** it is worth noting that the larger influence of an explanatory variable on prediction for a particular instance, the larger the fluctuations of the corresponding CP profile. For a variable that exercises little or no influence on a model’s prediction, the profile will be flat or will barely change. In other words, the values of the CP profile should be close to the value of the model’s prediction for a particular instance. Consequently, the sum of differences between the profile and the value of the prediction, taken across all possible values of the explanatory variable, should be close to zero. The sum can be graphically depicted by the area between the profile and the horizontal line representing the value of the single-instance prediction. On the other hand, for an explanatory variable with a large influence on the prediction, the area should be large.

**Calculation:** for a given observation, CP profiles are calculated for every single variable. Then CP oscillations importance measures are calculated to determine the importance, and hence ordering from most to least influential variables. On a high level, the importance is defined a sum of absolute deviation of the CP profile from a horizontal line (for continuous variables). For categorical variables, we would speak about the the sum of absolute deviations of each categorical level from the vertical line.

1) Pros:

* oscillations of CP profiles are easy to interpret and understand
* by using the average of oscillations, it is possible to select the most important variables for an instance prediction
* this method can easily be extended to many variables

2) Cons:

* same problems may arise when there are correlated/ interaction variables
* the CP-oscillations do not fulfil the local accuracy condition, as the predictions do not sum up to the instance prediction value (like BD-plots)

**Local-diagnostics plots**

It is sometimes worthwhile to check how does the model behave locally for observations similar to the instance of interest.

This part presents two local-diagnostics techniques that can be used for such purposes. The first one are **local-fidelity plots** that evaluate the local predictive performance of the model around the observation of interest. The second one are **local-stability plots** that assess the (local) stability of predictions around the observation of interest.

**Idea:** assume that, for the observation of interest, we have identified a set of observations from the training data with similar characteristics. We will call these similar observations “neighbors”. The basic idea behind local-fidelity plots is to compare the distribution of residuals for the neighbors with the distribution of residuals for the entire training dataset. The idea behind local-stability plots is to check whether small changes in the explanatory variables, as represented by the changes within the set of neighbors, have got much influence on the predictions. This needs to be done for every single variable or for selected, most important variables.

1) Pros:

* Local-stability plots may be very helpful to check if the model is locally additive, as for such models the CP profiles should be parallel. The plots can allow assessment whether the model is locally stable, as in that case, the CP profiles should be close to each other
* Local-fidelity plots are useful in checking whether the model-fit for the instance of interest is unbiased, as in that case the residuals should be small and their distribution should be symmetric around 0

1) Cons:

* the disadvantage of both types of plots is that they are quite complex and lack objective measures of the quality of the model-fit. Thus, they are mainly suitable for exploratory analysis.

### Summary

**Number of explanatory variables in the model**

One of the most important criteria for selection of the exploration and explanation methods is the number of explanatory variables in the model:

1) **Low to medium number of explanatory variables** - a low number of variables usually implies that the particular variables have a very concrete meaning and interpretation. In such a situation, the most detailed information about the influence of the variables on a model’s predictions is provided by the CP profiles. In particular, the variables that are most influential for the model’s predictions are selected by considering CP-profile oscillations and then illustrated graphically with the help of individual-variable CP profiles.
  
2) **Medium to a large number of explanatory variables** - in models with a medium or large number of variables, it is still possible that most (or all) of them are interpretable. An example of such a model is a car-insurance pricing model in which we want to estimate the value of an insurance based on behavioral data that includes 100+ variables about characteristics of the driver and characteristics of the car. When the number of explanatory variables increases, it becomes harder to show the CP profile for each individual variable. In such situation, the most common approach is to use BD plots or plots of Shapley values. They allow a quick evaluation whether a particular variable has got a positive or negative effect on a model’s prediction; we can also assess the size of the effect. If necessary, it is possible to limit the plots only to the variables with the largest effects.

3) **Very large number of explanatory variables** - when the number of explanatory variables is very large, it may be difficult to interpret the role of each single variable. An example of such situation are models for processing of images or texts. In that case, explanatory variables may be individual pixels in image processing or individual characters in text analysis. As such, their individual interpretation is limited. Due to additional issues with computational complexity, it is not feasible to use CP profiles, BD plots, nor Shapley values to evaluate influence of individual values on a model’s predictions. Instead, the most common approach is to use LIME, which works on the context-relevant groups of variables.

**Correlated explanatory variables**

When deriving properties for the methods, we often assumed that explanatory variables are independent. Obviously, this is not always the case. For instance, in the case of the data on apartment prices, the number of rooms and the surface of an apartment will most likely be positively associated. A similar conclusion can be drawn for the travel class and ticket fare for the Titanic data.

Of course, technically speaking, all the presented methods can be applied also when explanatory variables are correlated. However, in such a case the results may be misleading or unrealistic.

To address the issue, one could consider creating new variables that would be independent. This is sometimes possible using the application-domain knowledge or by using suitable statistical techniques like principal-components analysis. An alternative is to construct two-dimensional CP plots or permute variables in blocks to preserve the correlation structure of variables when computing Shapley values or BD plots.

**Models with interactions**

In models with interactions, the effect of one explanatory variable may depend on values of other variables. For example, the probability of survival for Titanic passengers may decrease with age, but the effect may be different for different travel-classes.

In such a case, to explore and explain a model’s predictions, we have got to consider not individual variables, but sets of variables included in interactions. To identify interactions, we can use iBD plots. To show effects of interaction, we may use a set of CP profiles. In particular, for the Titanic example, we may use the CP profiles for the age variable for instances that differ only in gender. The less parallel are such profiles, the larger the effect of interaction.

**Sparse explanations**

Predictive models may use hundreds of explanatory variables to yield a prediction for a particular instance. However, for a meaningful interpretation and illustration, most of the human beings can handle only a very limited number of variables. Thus, sparse explanations are of interest. The most common method that is used to construct such explanations is LIME. However, constructing a sparse explanation for a complex model is not trivial and may be misleading. Hence, care is needed when applying LIME to very complex models.

**Additional uses of model exploration and explanation**

All presented methods can also be used for other purposes:

* _Model improvement/debugging:_ if a model’s prediction is particularly bad for a selected observation, then the investigation of the reasons for such a bad performance may provide hints about how to improve the model.
  
* _Additional domain-specific validation:_ understanding which factors are important for a model’s predictions helps in evaluation of the plausibility of the model. If the effects of some explanatory variables on the predictions are observed to be inconsistent with the domain knowledge, this may provide a ground for criticising the model and, eventually, replacing it by another one. On the other hand, if the influence of the variables on the model’s predictions is consistent with prior expectations, the user may become more confident with the model. Such confidence is fundamental when the model’s predictions are used as a support for taking decisions that may lead to serious consequences, like in the case of, for example, predictive models in medicine.
  
* _Model selection:_ in the case of multiple candidate models, one may use results of the model explanation techniques to select one of the candidates. It is possible that, even if two models are similar in terms of overall performance, one of them may perform much better locally.
  
* _New knowledge extraction:_ machine-learning models are mainly built for the effectiveness of predictions. It is a different style than the modelling based on the understanding of the phenomena that generated observed values of interests. However, model explanations may sometimes help to extract new and useful knowledge in the field, especially in areas where there is not much of domain knowledge yet.

* _Champion-challenger analysis_

## Qualitative methods

### Error analysis

# References

1) (Interpretable Machine Learning: A Guide for Making Black Box Models Explainable)[https://christophm.github.io/interpretable-ml-book/]
2) 1) (Explanatory Model Analysis: Explore, Explain, and Examine Predictive Models. With examples in R and Python)[https://pbiecek.github.io/ema/preface.html]
