<!-- PROJECT LOGO -->
<br />
<div align="center">
  <a href="https://github.com/KyleleeSea/PowerliftML">
    <img src="https://i.imgur.com/kVQck0q.png" alt="Logo" width="80" height="80">
  </a>

<h1 align="center">PowerliftML</h1>

  <p align="center">
    <h4>
      A machine learning model predicting how you'd place at local level U.S. powerlifting competitions
    </h4>
    <br />
    <a href="https://powerliftml.herokuapp.com/">View Demo</a>
    ·
    <a href="https://powerliftml.herokuapp.com/documentation/">Documentation</a>
    ·
    <a href="https://github.com/KyleleeSea/Seraphina/issues">Report Bug</a>
  </p>
</div>

<!-- ABOUT THE PROJECT -->
## About The Project

[![PowerliftML Screen Shot][product-screenshot]](https://powerliftml.herokuapp.com/)

PowerliftML was designed for individuals to gauge how strong they are in comparison to competitive lifters. That goal is achieved through the creation and combination of two machine learning models: a linear regression model and a support vector regression model. The repository includes a mobile-responsive Python Flask application ready to be deployed.

Users input their gender, bodyweight, and compound lift maxes, and PowerliftML returns a range of their most statistically probable placing. 

Please view in-depth documentation and graphs <a href="https://powerliftml.herokuapp.com/documentation" target="_blank"> here.</a>

### Built With

* [![Flask][flask.com]][flask-url]
* [![Scikit-Learn][scikitlearn.org]][scikitlearn-url]
* [![Numpy][numpy.org]][numpy-url]
* [![Pandas][pandas.org]][pandas-url]
* [![Heroku][heroku.com]][heroku-url]

## Live Demo

Please view the full demo of PowerliftML here: <a href="https://powerliftml.herokuapp.com/">PowerliftML.herokuapp.com</a>
Deployed utilizing Heroku. 

## Local Copy
You may run this project locally by following these steps:

1. Clone the repo
   ```sh
   git clone https://github.com/KyleleeSea/PowerliftML
   ```
2. Install Python packages
   ```sh
   pip install -r requirements.txt
   ```
3. Start application
   ```sh
   python app.py
   ```
   
## Feature Engineering  
The initial dataset came from the openpowerlifting project and included 2,733,850 data points. The following steps were taken in feature engineering:

### Event Consistency
The initial dataset included data from competitions that were not squat, bench press, and deadlift. The most common being bench press only competitions. For consistency, I excluded non-SBD (squat, bench, deadlift) competition data points.

### Removal of Non-Numeric Placings
As later model creation and exploration will require comparison of what placing an individual received, the placing column should be type integer. However, the placing column's datatype in the initial dataset is of an object; upon exploring why, I found that there were string type placings for disqualifications, no shows, and "guests" who didn't receive an award. Therefore, I removed rows with non-numeric placings and converted the placing column to type integer.

### Strength Differences Between Countries
The initial dataset included data from a number of countries, with the most data coming from Russia, Ukraine, the United States, England, and Germany. Substantial differences were found between U.S. and international average Wilks scores (30-point difference in age class 24-34, 19-point difference in age class 20-23, and 15-point difference in class 35-39). Due to these differences, data was limited to only U.S. competitions.

### Removal of Championship & Invitational Competitions
As my model objective concerns placement at local powerlifting meets, I excluded data rows that include "championship" and "invitational" (and their respective case-sensitive counterparts) from the dataset. 

### Strength Differences Due to Equipment
Powerlifting competitions may differ in what type of equipment they allow. I grouped the dataset by equipment and found the mean kilograms lifted by competitors per equipment type. The results showed a substantial difference between Raw compared to Wraps (105.49 kg), Multi-ply (175.04 kg), an even more significant difference compared to Unlimited (226.04 kg). As few data rows were Wraps, Multi-Ply, or Unlimited competitions compared to the number of Single-ply and Raw competition results, data of the former three were dropped. Although a difference of 39.71 kg exists between Single-ply and Raw, removing one would lead to an overly substantial limit to the dataset. As such, a tradeoff was made, and the model was trained on both Single-ply and Raw competition results.

### Fixing Uneven Placement Distributions
Initial models heavily skewed toward a first place, so I returned to dataset exploration. I found an uneven distribution of placings. There were 203,884 first-place results, but only 118,273 second-place results, 87,386 third-place results, 67,694 fourth-place results, and the numbers kept decreasing. In fact, places lower than 29 contained less than 100 results.2 I found the cause to be that many competitions did not report all the placings of competitors. Many included only the first place competitors. To solve this, I created a dataframe grouping the dataset by MeetName (no null values) and finding the number of data points associated with each MeetName alongside the mean placing at each meet. Competitions where only the first place competitor was entered had a mean placing of 1. By creating a list of meets where there was a mean placing of at least 4, I was able to decrease how uneven the placement distribution was. I experimented with other cutoff values but found 4 to create the most accurate models, as a higher cutoff point helped to solve the placement distribution but sacrificed data.

## The Models
### Requirements & Scoring Criteria
The model must have a consistent negative relationship between Wilks score and placing. In other words, a higher Wilks score should always mean a lower placing number, which would mean a better placing. For example, if a model predicts 4th place for a Wilks score of 300, it should predict a placing better than 4th, 3rd, for example, for a Wilks score higher than 300. 

Metrics for evaluation included lowering mean absolute error, mean squared error, and root mean squared error when testing predictions in the X test set. Minimizing error would indicate a better-performing model. The final metric for evaluation was how close to 1st a model would predict when inputting a Wilks score of 384. 384 was the mean Wilks score first place finisher obtained, as found by data exploration.

### Training 
Over 50 models were created. The vast majority were regression models, as placing is a numeric category, but a few classification models were tried as well. Models tried included Linear Regression, Ridge Regression, Elastic Net Regression, Lasso Regression, Polynomial Regression, Decision Tree Regression, Random Forest Regression, K Nearest Neighbors, RANSAC, Support Vector Regression, Logistic Regression, and Gaussian Process Regression. In-depth spreadsheet documentation for every model and which dataset variation they were tried on can be found here. 

### Final Models 
Linear Regression and Support Vector Regression are the machine learning models that scored the best on the metrics described above. 

The Support Vector Regression model has an MAE of 3.07, MSE of 18.58, and RMSE of 4.311. The Linear Regression model has an MAE of 3.17, MSE of 18.1, and RMSE of 4.25. 

The Support Vector Regression predicted closer to first place for 384 Wilks than the Linear model. However, it displayed non-consistent positive behavior between Wilks score and placing before 150 and after 550 Wilks points. 

As the Linear Regression model predicts an exactly linear relationship, its shortcoming was a prediction of negative placings after 520 Wilks and predictions of "0" as placings as well. Thus, both machine learning models are utilized for the final website deployment.


<!-- CONTACT -->
## Contact

Twitter - [@KyleleeSea](https://twitter.com/KyleleeSea)

Project Link: [https://github.com/KyleleeSea/PowerliftML](https://github.com/KyleleeSea/PowerliftML/)

<!-- MARKDOWN LINKS & IMAGES -->
[product-screenshot]: https://i.imgur.com/lTp5EMK.png
[flask.com]: https://img.shields.io/badge/flask-000000?style=for-the-badge&logo=flask&logoColor=ffffff
[flask-url]: https://flask.palletsprojects.com/en/2.2.x/
[scikitlearn.org]: https://img.shields.io/badge/Scikit_learn-3499CD?style=for-the-badge&logo=scikitlearn&logoColor=F89939
[scikitlearn-url]: https://scikit-learn.org/
[numpy.org]: https://img.shields.io/badge/Numpy-013243?style=for-the-badge&logo=numpy&logoColor=4DABCF
[numpy-url]: https://numpy.org/
[pandas.org]: https://img.shields.io/badge/Pandas-130654?style=for-the-badge&logo=pandas&logoColor=fffff
[pandas-url]: https://pandas.pydata.org/
[heroku.com]: https://img.shields.io/badge/heroku-000000?style=for-the-badge&logo=heroku&logoColor=79589F
[heroku-url]: https://www.heroku.com/
