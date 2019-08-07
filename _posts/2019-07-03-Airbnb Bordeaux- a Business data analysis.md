Preface
Where does the name 'Airbnb' come from? : Founder Brian Chesky and his roommates rented an inflatable mattress in their living room when Brian had the idea to set up the service. Since its launch in 2008, and despite increased competition from a few projects financed by the same fund (Ycombinator), Airbnb has seen in large numbers ...

For this study, we will explore the data collected from Airbnb Bordeaux (France) in mid-June 2019. The country became Airbnb's first market after the United States. The hexagon has more than 300,000 homes to disposal and Paris (ahead of London, Rome and Barcelona) ranks as first European cities with 65,852 homes to rent.

Proportion of licensed goods by neighborhood.
For this exploration, I use the whole data (and define a case study) to answer several questions:

Which key variables determine the rental performance of the properties? (with the 'rental performance' corresponding to the actual demand for the properties)

What is the most popular property in Bordeaux ? I am are particularly interested in a property that is very successful and for which I calculate the amount of Return on Investment.

In what proportion can one finance the expenses of his anuity ?

Finally, I cleaned up the data in order to prepare it to predict the daily rental price. I explain the interest of such indicator for users and hosts. I finally use and evaluate one algorithm to predict it.


**Contents**

I. [Key facts of the study](#INS)

II.[Exploration and questionnings](#DE)

[Trends in demand for properties by neighbourhood - at 90, 60, 30 days](#DI)
[Main motivations underlying demand](#DEMAND)
[Pay its expenses thanks to Airbnb, in what proportion ?](#PAY)
[The best customer experience: who's the most solicited host ?](#REVIEWS)
a. [Return on investment of the property](#ROI)

III- [Data Cleaning](#ND)

[Loading libraries & data](#CHA)
[The nullity matrix](#VM)
[Maintained variables](#VS)
[Transformed variables](#VT)
[Detection and removal of weird variables](#VI)

IV- [Prediction

[Loading libraries](#IM)
[Which variables are best suited to linearly predict rental price ?](#NULL)
[Quartile-Quartile plot of target variable](#QQplot)
[Pipeline class definition for models and perfs measures](#PVA)
[Xgboost- Linear regression](#XGB0)
a. [Price ∈ (Dataset)](#XGB0)
b. [log(Price ∈ 0,200](#XGB2)


## <a name="INS" ></a> Key facts of the study

**73 €** is the average price of a rental for two people, located in the heart of downtown Bordeaux (example of the apartment in second cover photo)

*the type of accommodation, the number of reviews, the amount of rentals per host, the minimum number of nights required to rent, the price and the neighbourhood* (in order of importance) are the critical variables that determine the rental performance of the goods (the demand by property).

The tax declaration to the authority * averages 14%. We note that the number of licenses is decreasing as we move away from the center.

**16%** is the rental return on investment for the most popular property (counted in terms of reviews)

**36%**, this is the average realistic percentage of the expenses financed (rent + charges) by renting a room in Bordeaux on Airbnb.

**50%** of goods are correctly predicted with an error lower than 9.53 euros (absolute median) by using Xgboost regressor, for an average error of 14.55 euros .


## <a name="DE" ></a> Exploration and questionnings

## <a name="DI" ></a> 1.1 Trends in demand for properties by neighbourhood at 90, 60, 30 days

The dataset was collected by an independent scrapper on InsideAirbnb. I use the biggest dataset provided named 'listings_details'.

'''
from IPython.core.display import display, HTML, Javascript
import IPython.display
import pandas as pd
import numpy as np
from sklearn.preprocessing import *
listings = pd.read_csv('../input/listings_detail.csv')
listings_raw = pd.read_csv('../input/listings_detail.csv')
'''

'''
# output of the mean avaibility over 30 days of all the listings by neighourhood. It can be computed for avaibility_60, avaibility_90 in the same fashion.
le = LabelEncoder()
neigh_encoded = le.fit_transform(listings_raw['neighbourhood'].astype(str))
neigh_encoded  = pd.DataFrame(neigh_encoded)
neigh_encoded.columns = ['neigh_encoded']
listings_raw_enc = pd.merge(listings_raw, neigh_encoded, left_index=True, right_index=True)
for i in range(0,13,1): # I don't include index 13, these are NaNs encoded values
    avaib_mean = [None]*15
    avaibility = [None]*15
    avab = [None]*15
    avaibility[i] = listings_raw_enc[listings_raw_enc.neigh_encoded==i]
    avaib_mean[i] = avaibility[i].availability_30
    avab[i] = np.around(np.nanmean(avaib_mean[i]), 0)
    number = i
    labels_integers = {l: i for i, l in enumerate(le.classes_)}
    lab = list(labels_integers)
    print(avab[i],"days over the next 30 days are available for rent at", lab[i])
 '''
 
<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2030j).png" width="100%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2060j).png" width="100%">


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2090j).png" width="100%">

We note that the lowest average availability is in the Capucins-Victoire district ... Thus, the highest occupancy rate is Capucins-Victoire followed by Hôtel de Ville.

Hôtel de Ville ('City Hall') is the area with the highest daily rate. Would the rental prices of properties in the Capucins-Victoire district be underestimated? This complex issue will be the subject of a future study.

## <a name="DE" ></a> Main motivations underlying demand


```
feature_importance_dispo30

neighbourhood                                 0.037372
price                                         0.037857
minimum_minimum_nights                        0.038779
host_listings_count                           0.045887
number_of_reviews_ltm                         0.049543
room_type                                     0.115095
```

*Here we obtain the most important variables that determine the demand for goods. They were outputed by xgboost feature_importance after fitting the values. Thus, these variables represent (in %) how much importance the algorithm (Xgboost regression) accorded to each variable for splitting.
The type of housing comes first with an importance of 11.5% (0.115), the number of ratings comes next with 5% importance then the number of properties managed by host (4.5%).*

## <a name="PAY" ></a> Pay its expenses thanks to Airbnb, in what proportion 

*Imagine that you take a collocation with a friend. Unfortunately, the best friend metric is going to a local minima after two month. You realize that he screws up the apartment but he doesn't aggree much with the affirmation, you're overestimating. After much discussion, he takes the step through the door sill, you stay friends, but not too close to avoid underfitting. Now, how will you rentabilize the cost that you've to bear over the next few months ? Indeed, Airbnb may be a handy solution, we'll find how...*

I will try to answer in which proportions it is possible to pay the rent + charges for a two bedroom apartment when listing one of the rooms on Airbnb (after rent, utilities (water, gas, electricity) and Internet).

In order to make correct assumptions, I take the several data as basis :

**24.5 %** is the average share of expenditures (in renting proportion) to cover the housing cost (insurance, gas, electricity, internet).
The property has a *surface* of **65 m2 (700 sqm)**
The *average rental cost* of the city is **14€ / m2 (10.76sqm)**.
**30 %** is the *average occupancy rate* for this type of accommodation in Bordeaux (method below)

*These numbers lead to :*

**1130 €** as the *average monthly rent* cost for a 2 bedrooms accommodation **all charges included** (910 € (rent) + 220 € (charges))
It results in :

**4068 €** as the *annual amount of expense* yearly covered by the annuity on Airbnb
To answer the question : 36 % is the total renting amount one can finance by renting one bedroom in Bordeaux

**Method used to calculate the occupancy rate**

It is very complex to obtain accurate information on the occupation rate metric of Airbnb. Platforms such as Airdna and others **indicate very high rates (up to 80%) over the next 30 days, only !!**. The calculus often done is based on the average of "busy" days (not available) that they consider by default "Rented", over the next 30 days. For this matter, I use a different and more cautious method:

Example: 75% of the property is available for a maximum of 18 out of 30 days, which means that it is suspected to be rented for a maximum of 12 days. It is an optimistic asumption because a part of these 12 days (non available) could simply be intentional. This accounts for a max occupancy rate of 40.00% (12/30).
Let's see the calculation of max expected availability over the next 30, 60, 90, 365 days of all properties :

'''
a = listings_raw.availability_30.describe()# 75% of properties are rented for a minimum of 21 days in the next 30 >>> 70% occupancy
print(a.index[6],"of properties are  rented for a maximum of", 30-a[6],"days in the next 30. That makes an (optimistic) assumption of", np.round((30-a[6])/0.3, 1

'''
75% of properties are  rented for a maximum of 21.0 days in the next 30. That makes an (optimistic) assumption of 70.0 % occupancy

75% of properties are  rented for a maximum of 35.0 days in the next 60. That makes an (optimistic) assumption of 58.3 % occupancy

75% of properties are  rented for a maximum of 44.0 days in the next 90. That makes an (optimistic) assumption of 48.9 % occupancy

75% of properties are  rented for a maximum of 186.0 days in the next 365. That makes an (optimistic) assumption of 51.0 %

The closer the date is, the higher the ratio. It makes common sense that the closer the date, the highest is the likelihood that the property is rented. I take an average of the four rates which leads to 57 % in overall renting time for properties on the platform.

However, the data was collected mi-July : which explains a much higher demand at this period. One should take into account that some people will rent only a few months in a year and-or will profit from their property at a specific time once a year. Some hosts (Airbnb forums) also mention that 1-3 days can happen to fall between bookings. To obtain a more accurate rate, it would be needed to scrape constantly (daily) Airbnb and follow the trend of avaibility at overall. Indeed, in order to find a more realistic ratio for the purpose of this study, I apply a average 50% penalty out of 57%, which will be rounded to 30%.

The best customer experience: who's the most solicited host
