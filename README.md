# UFC Fight Predictor

The aim of this project was to create an interactive application that uses a machine learning model to make predictions on UFC fights using fighter statistics. This application would serve as an entertainment tool to increase traffic to a UFC website/blog. The application can be found at https://ufc-fight-predictor.herokuapp.com.

## File Descriptions

- superseded: Folder containing old notebooks and .py files.
- ufc_scrape:
  - ufc_scrape:
    - __init__.py, items.py, middlewares.py, pipelines.py, settings.py: Default Scrapy files.
    - spiders:
      - fighters_spider.py: spider to scrape data on the fights of each fighter.
      - fights_spider.py: spider to scrape data on each UFC event.
- data:
  - data_fighters.py: scraped data of each fighter's fight details.
  - data_fights.py: scraped data of each UFC event.
- Classes.py: contains grid search classes to be used in modelling.
- preprocessing.ipynb: notebook for initial exploration of scraped data and preprocessing for modelling.
- first_app.py: python file for creating the streamlit application.
- model.pkl: final model saved using pickle.
- scaler.pkl: StandardScaler transformation saved using pickle.
- Procfile: file for Heroku that configures services to be run in the dynpo.
- requirements.txt: file for Heroku that states the python library requirements for the application to be run.

## Methods Used

- Web scraping
- Data exploration, visualisation and cleaning
- Feature engineering
- Machine learning
- Cloud computing
- Front end development
- Deploying to web

## Technologies

- Python
- Scrapy
- Pandas
- Numpy
- Matplotlib
- Scikit-learn
- Keras
- Google Cloud Platform
- Streamlit
- Heroku

## Executive Summary

As stated at the beginning of this document, the aim of the project was to create a web application where users could choose two fighters and use a machine learning model to predict the outcome of the fight. It's purpose is primarly as an entertainment tool that could be placed on a UFC blog/website to increase traffic. Despite it being an entertainment tool, accuracy was still regarded as important because it was thought that making correct predictions would increase the likelihood of it being shared amongst social circles. It was also designed to be interactive and give the user as uch information as possible about the fighters.

### Web Scraping

The UFC maintains a [statistics website](www.ufcstats.com) that contains information on every fighter, fight and event that has occured in the organistation's history. To extract this data for modelling, two scrapy spiders were created:

- **fighter_spider** : Scraped the detailed breakdown of each fighter's bouts and their measurements such as height and reach. Examples of the pages scraped can be found [here](http://www.ufcstats.com/fight-details/b46f2f007b622bce) and [here](http://www.ufcstats.com/fighter-details/1338e2c7480bdf9e).
- **fight_spider**: Scraped the details of each ufc event. This was necessary because the page that the fighter_spider scrape had no information on the date of the bout, which was necessary to calculate the age of each fighter at the time of the fight. An example of the pages scraped can be found [here](http://www.ufcstats.com/event-details/53278852bcd91e11).

### Cleaning Data and Feature Engineering

A standard data cleaning procedure was followed where the data was formatted into the correct data types and then missing values were handled appropriately, usually filling in the value with a related column (e.g. height and reach) or filling in with the median of the weightclass group.

A major effort in the project was to prevent data leakage. It was important to only use data of a fighter that was available prior to the fight occuring. For example, a fighter's current career statistics should not be used to train the model on the outcome of a fight that occured 5 fights ago, because those statistics would have been different at the time of the fight. Therefore, for each past fight, the fighters' pre-fight statistics were calculated using the detailed breakdown of their previous bouts. Additionally to this, only the fighters' 5 previous fights were used to caluclate their pre-fight statistics. This decision was made to do this because over a fighter's career, their performance and style of fighting can change so only their recent performances should be considered.

The features fed into the model were as follows:

- Height (m)
- Weight (lbs)
- Reach (m)
- Age (years)
- Strikes Landed (per minute)
- Striking Accuracy (%)
- Strikes Absorbed (per minute)
- Striking Defence (%)
- Takedown Average (per 15 minutes)
- Takedown Accuracy (%)
- Takedown Defence (%)
- Submissions Average (per 15 mins)

### Modelling

The data was split into three sub datasets; training, validation and test datasets. Many classification models were created and their hyperparameters were tuned using GridSearchCV to crossvalidate their performance. The best performers from each model type are presented below (sorted by performance on the validation dataset):

<h5 align="center">Model Performances</h5>
<p align="center">
  <img src="https://github.com/ravimalde/ufc_fight_predictor/blob/master/images/model_evalutaion.png" width=500>
</p>

The stacking model comprised of a support vector machine, random forest and voting classifier (random forest + svm) model was the best performer (see diagram below for model architecture). This came as a surprise because stacking models often perform best when configured with very different models, so that a model's weak performance in one area can be picked up by another model's better performance in that region of the dataset. Nevertheless, the numbers don't lie, and the best performing model was made from models that had similarities. 

<h5 align="center">Stacking Model Architecture</h5>
<p align="center">
  <img src="https://github.com/ravimalde/ufc_fight_predictor/blob/master/images/stacking_architecture.png" width=500 align=middle>
</p>

An interesting insight from the model is the relative feature importances. These were produced using the random forest model as neither the overall stacking model nor the SVM and voting classifier models have the ability to produce feature importances. It is assumed that the feature importance of the stacking model is of a similar distribution to the random forest model. It appears age, win percentage, strikes landed per minute, takedown average and strikes absorbed per minute are the five best predictors of a fight.

<h5 align="center">Relative Feature Importance</h5>
<p align="center">
  <img src="https://github.com/ravimalde/ufc_fight_predictor/blob/master/images/feature_importance.png" width=850 align=middle>
</p>

**The model achieved an accuracy of 0.64 on the test dataset**. The nature of combat sports, particularly MMA, is that they are very unpredictable and upsets are frequent in (in fact this is in part why I believe the sport is gaining huge popularity), so although this acuracy isn't impressive on paper, I'm happy with the outcome and confident that the performance could be improved upon in the future as more data becomes available.

### Developing Application

To create the application I used a realtively new framework called Streamlit (www.streamlit.com). This tool allows for the creation of interactive machine learning applications in an extremely pythonic way. I designed the application to allow the user to choose from any two fighters in the organisation and predict who the winner will be while displaying what the confidence is in that outcome occuring. It also presents all of the fighter statistics in matplotlib plots so that the user can easily see a visual representation of how the fighter's compare.

### Deploying Application

Lastly, the application was deployed to the web using Heroku. This is a cloud platform as a service that allows developers to deploy and scale applicatons. The process was fairly straightforward; simply create an account with them, make a Procfile which is automatically detected by Heroku and serves to outline the commands that need to be run in order to launch the application, and then create a requirements.txt file to tell Heroku which python dependencies are necessary for the application to function. The final step is then to git push the entire github repository to the Heroku branch. Voila, the application is now live and can be accessed by anyone around the world.

### Limitations

### Future Work
