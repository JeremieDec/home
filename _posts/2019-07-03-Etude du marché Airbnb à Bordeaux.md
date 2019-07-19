---
title:  "Etude du marché Airbnb à Bordeaux"
date:   2019-7-14
layout: single
author_profile: true
comments: true
---

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Place_de_la_Bourse_Bordeaux2comp.png" width="100%">


**Préface**

D'ou viens le nom 'Airbnb' ? : Le fondateur Brian Chesky et ses colocataires louaient un matelas gonflable dans leur salon lorsque Brian eu l'idée de monter le service. Depuis son lancement en 2008, et malgré la concurrence accrue de quelques projets financés par le même fond (Ycombinator), Airbnb eu l'inspiration de devenir bien plus qu'une plateforme de réservation de matelas gonflables... Regardons plus près:

 Airbnb recense  - **3** millions de logements habitables répartis dans 
                 - **65 000** villes à travers plus de 190 pays, pour un total de 
                 - **150 millions d'utilisateurs**. 

Il n'est pas facile de mettre en location son logement, en témoignent la différence d’utilisateurs e de logements habitables. Néanmoins, le nombre de biens actifs est en croissance continue depuis 11 ans.

La **France** est devenue le **premier marché d'Airbnb après les Etats-Unis**. Dans l'hexagone, la plateforme compte plus de 300 000 logements et Paris se situe au rang de numéro 1 des villes européennes avec 65 852 logements, devant Londres, Rome et Barcelone. 


Seul point de discorde : le bras de fer engagé contre plusieurs villes dans le monde, qui tentent toutes initiatives (jusqu'à interdire la plateforme) afin d'enrayer le mécontentement des acteurs de l'hôtellerie ainsi que la hausse des prix de l'immobilier. A New York, par exemple, la législation locale interdit le séjours de courte-durée (inférieurs à 30 jours) sauf si le propriétaire habite en même temps que les voyageurs. En France, la limite est établie à [120 nuits] (https://www.airbnb.fr/help/article/2108/limitation-du-nombre-de-nuit%C3%A9es-en-france%C2%A0-foire-aux-questions) par année dans les plus grandes villes

Pour cette étude, on s’intéressera aux données Airbnb de la ville de Bordeaux, nous verrons : 
- la répartition des biens et leurs charactéristiques
- les annonces des hôtes qui réussissent le mieux sur la plateforme, 
-  En quelles mesures il est possible de rentabiliser un bon investissement locatif, en prenant en compte le taux d’occupation
- Combien de biens sont sous licences et comment ceux-ci se répartissent géographiquement

Ensuite, en deuxième, je nettoyerai les données afin de les préparer pour prédire le prix journalier des biens. En troisième partie, je testerai et configurerai deux algorithme afin de prédire ce prix journalier et nous verrons quelles variables sont les plus importantes.


**Contenus**

I - [Enseignements clés de l'enquête](#INS)

I- [Exploration des données](#DE)
  1. [Répartition des biens par quartier](#NE)
  2. [Types de logements](#TP)
  3. [Payer ses charges grâce à Airbnb, en quelle mesures ?](#PAY)
  3. [Types de propriétés](#TPP)
  4. [Capacité totale des biens par nombre d'accommodés](#CP)
  5. [Prix moyen d'une location par quartier](#PM)
  6. [La meilleur expérience client, qui est l'Hôte le-la plus plesbicité au quotidien ?](#REVIEWS)
  7. [Les hôtes peuvent payer 81%  de leur rente en listant un logement 2 pièces](#FIN)
  8. [Quels sont les quartiers les plus-moins disponibles ? - évolution moyenne sur 90, 60, 30 jours](#DI)
  9. [Les hôtes déclarent-ils leurs activités ?](#NL)
  10. [Combien les hôtes possèdent-ils de biens ?](#HL)  
  11. [Quelles variables sont les plus corrélées ?](#HM)
 
  
II- [Nettoyage des données](#ND) 
  1. [Chargement des bibliothèques et données](#CHA)
  2. [Variables maintenues](#VM)
  2. [Variables supprimées](#VS)
  3. [Variables transformées](#VT)
  4. [Variables imputées](#VI)
  5. [Variables encodées](#VE)
  
III- [Prédiction](#PR) 
  1. [Import des bibliothèques utiles](#IM)
  2. [The nullity matrix](#NULL)
  2. [Quelles variables sont meilleures pour prédire linéairement le prix de location ?](#PV)
  3. [Quartile-Quartile plot de la variable cible](#QQplot)
  4. [Définition d'une Class pipeline pour les modèles et mesures de performances](#PVA)
  5. [Fonction Plot de distribution des residuals, Ypred vs Y](#PRES)
  6. [Régression linéaire](#REL)
  7. [Xgboost- Régression linéaire](#XGB)
  8. [Imputation de 25% des données manquantes, effets sur le modèle] (#OPT)
IV - [Conclusions et ouverture](#CC)
    1. [Pour aller plus loin](#PAL)  
    
V-[Références](#REF) 
 




## <a name="INS" ></a> Enseignements clés de l'enquête

**36 %**, c'est le pourcentage réaliste moyen des frais financés (loyer+charges) grâce à la [location d'une chambre à Bordeaux sur  Airbnb](#PAY).

**104 %** est la rentabilité en proportion de l'investissement total en louant un appartement entier sur Bordeaux, selon cette [configuration](#PAY).

**73 €** correspond le prix moyen d'une location pour deux personnes, située au coeur du Centre-Ville de Bordeaux (exemple d'un appartement en deuxième photo de couverture)


**

## <a name="DE" ></a> Exploration des données 

L'ensemble de données que j'ai obtenu contient 7 fichiers au total.  [InsideAirbnb](http://insideairbnb.com/) indique  "listings_details" (détails des biens) comme étant « bon pour les visualisations », je vais donc utiliser celui-ci. 
Au total, il y avait 9 699 biens inscris à Airbnb à Bordeaux le 16 juin 2019.

## <a name="NE" ></a> Répartition des biens par quartier

Hôtel de Ville arrive en tête avec plus de 1396 biens en location. Les 7 quartiers les moins répertoriés représentent à eux seul le même nombre de biens que le deuxièmes et troisème quartiers majeur réunis (Chartron et Capucins-Victoire).

```
cols = pd.DataFrame(listings.columns)
```
```
a = listings['neighbourhood'].value_counts().sort_values(ascending=True)

a.shape

(13,)

a = pd.DataFrame(a)

a = a[a["neighbourhood"] > 50]

a.plot.barh(figsize=(10, 8), color='b', width=2, title = 'Répartition des biens par quartier par quartiers')

alternative pour calculer n éléments distincts d’une variable: 

(Counter(listings5.neighbourhood)
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/hmapListesparquartier.png" width="100%">

## <a name="TP" ></a> Type de logements

```
freq = listings['room_type'].value_counts().sort_values(ascending=True)
freq.plot.barh(figsize=(15, 3), width=1, color = ["g","b","r"])
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Typesdelogement.png" width="35%">


## <a name="TPP" ></a> Type de propriétés

Les appartements arrivent en tête, également avec une proportion de logement entier à plus de 85%


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Types de Propriétés.png" width="100%">

```
prop = listings.groupby(['property_type','room_type']).room_type.count()
prop = prop.unstack()
prop['total'] = prop.iloc[:,0:3].sum(axis = 1)
prop = prop.sort_values(by=['total'])
prop = prop[prop['total']>=100]
prop = prop.drop(columns=['total'])
prop.plot(kind='barh',stacked=True, color = ["r","b","g"],
              linewidth = 1, grid=True, figsize=(15,8), width=1)
plt.title('Types de propriétés à Bordeaux', fontsize=18)
plt.xlabel('Nombre de biens', fontsize=14)
plt.ylabel("")
plt.legend(loc = 4,prop = {"size" : 13})
plt.rc('ytick', labelsize=13)
plt.show()
```

## <a name="PAY" ></a> Payer ses charges grâce à Airbnb, en quelle proportion ?


On souhaite savoir s’il est possible de payer le loyer + charges d’un logement de deux chambres en répertoriant l’une des chambres sur Airbnb. L'objectif est de calculer les bénéfices nets attendus (après le loyer moyen, les services publics (eau , gaz, électricité) et Internet).

- **24,5 %**, c’est la part des dépenses moyenne en proportion du loyer que l'on prends en référence pour couvrir les frais du logement (assurances, gaz, électricité, internet).

Je prends la référence suivante: un logement possède une surface de 65 m2, pour un loyer de 14e/m2 (moyenne à Bordeaux) hors-charges [Source](https://blog.locservice.fr/bordeaux-les-chiffres-cles-du-marche-locatif-prive-en-2013-1469.html).

- 910 € de loyer + charges (220€) = 1130 € /mois 

- **1130 €**, c'est le coût moyen de rente mensuelle pour un logement de 2 chambres toutes charges comprises à Bordeaux

**Enseignements clés de cette étude**:

 - **4068 €**, c'est le montant annuel des frais couverts grâce à la rente sur Airbnb,

 - **29.7 %** est le taux d'occupation moyen de ce type de logement à Bordeaux,

 - **36 %**, c'est le pourcentage des frais mensuels financés
 
**Méthode de calcul du taux d'occupation

Il est très complexe d'obtenir une information exacte sur l'occupation des biens d'Airbnb. 
Les plateformes tel que Airdna et d'autres indiquent des taux très élevés (jusqu'à 80%). Ils se basent sur le calcul moyen des jours "occupés" (non disponibles) qu'ils considèrent par défault 
"loués". Pour ce calcul, j'utilise une méthode différente et plus prudente :

- Exemple : 75% des biens sont disponibles au maximum 18 jours sur les 30, ce qui signifie, qu'ils sont soupçonnés loués au minimum 12 jours (des biens sont loués 30 jours, d'autres 15...). 
Ce qui comptabilise un taux d'occupation plancher de 40.00 % (12/30). 

J'effectue le calcul sur 30, 60, 90, 365 jours: 

```
# listings = pd.read_csv(r"/Users/jeyrm/Documents/Bordeaux/listings_detail.csv", low_memory = False)

araw.availability_30.mean()
Out[]: 9.298785776235906                   # En moyenne, la location est réalisée 21 jours sur les 30 prochains.

araw.availability_30.describe() # Disponibilité dans les 30 jours 
Out[]: 
count    2306.000000       
mean        9.298786
std        10.910107
min         0.000000
25%         0.000000
50%         3.000000
75%        18.000000                        # 75% des biens sont loués au minimum 12 jours dans les 30 prochains >>> 40 % d'occupation
max        30.000000
Name: availability_30, dtype: float64


araw.availability_60.describe()
...
75%        43.000000                         # 28.3 % 

araw.availability_90.describe()
...
75%        69.750000                         # 22.2 % 
 

araw.availability_365.describe()
...
25%         0.000000
50%        74.000000
75%       262.000000                          # 28.2 % 
max       365.000000
```

Plus la date est proche, plus le taux augmente, ce qui est caractéristique du comportement des consommateurs : la location a tendence à être prévue à des dates proches de la date choisie.   
Par mesure de prudence, on prends la moyenne des 4 : 29,67% en tant que taux d'occupation de référence.

## <a name="CP" ></a> Capacité totale des biens par nombre d'accommodés	


**4112 biens pour 2 personnes** sont disponibles, ce qui fait une disponibilité totale de ces biens pour 8224 personnes.

**2469 biens pour 4 personnes** sont disponibles, ce qui en font une disponibilité totale - supérieure de 1652 personnes.

Ce qui signifie, en cas d'overbooking, que les familles seront les plus nombreuses à se promener en Centre-Ville.

Légende : Le nombre de biens en verticale, le nombre total d'accomodés en légende au dessus de la barre et le nombre d'accomodés	en X.

Biens disponibles par nombre d'accomodés.png
<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Biens disponibles par nombre d'accomodés.png" width="100%">

```
ac = listings['accommodates'].value_counts().sort_index()
ac.plot.bar(figsize=(10, 8), color='b', width=1, rot=0)
plt.title("Nombre d'accomodés", fontsize=20)
plt.ylabel('Biens', fontsize=12)
plt.xlabel('Personnes', fontsize=12)
plt.show()

```

## <a name="PM" ></a> Prix moyen d'une location par quartier pour deux personnes

Hotel de Ville est en moyenne 57 % plus cher que l'ensemble des quartiers alentours ! La ville s'étend pourtant sur 1.5 km de rayon (en prenant le centre du quartier Hotel de Ville). Nous verons un peu plus tard dans cette analyse une piste pour expliquer une partie de cette différence ([Les hôtes déclarent-ils leurs activités ?](#NL)).

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Prix moyen d'une location par Quartier pour 2 personnes.png" width="100%">

```
grp = listings[listings['accommodates']==2]
grp = grp.groupby('neighbourhood')['price'].mean().sort_values(ascending=True)
grp.plot.barh(figsize=(10, 15), color='b', width=1)
plt.title("Prix moyen pour une accomodation pour 2 personnes", fontsize=20)
plt.xlabel('Prix moyen (Euro)', fontsize=12)
plt.ylabel("")
plt.show()
listings.reviews_per_month.describe()
Out[78]: 
count    8603.000000
mean        0.903068
std         1.377258
min         0.000000
25%         0.050000
50%         0.360000
75%         1.120000
max        15.000000
```
## <a name="REVIEWS" ></a> La meilleure expérience client, l'Hôte le plus plesbicité au quotidien

La notation des hôtes s'étends de 0 à 15 par mois, avec plus des 3/4 des annonces qui possèdent au minimum une petite attention post-location. On remarque cependant (index 'max') ci-dessous qu'il existe des hôtes qui sont notés jusqu'à 15 fois durant le mois ! Visiblement, il y a une hôte qui remplit cette condition exceptionelle, voyons..



["l'annonce de la Patronne"](https://www.airbnb.com/rooms/32541282?source_impression_id=p3_1563223901_s1cD%2BxPIaIiI2HbY&check_in=2019-07-18&guests=1&adults=1&sl_alternate_dates_exclusion=true&check_out=2019-07-19)
```
listings.reviews_per_month.describe()
Out[78]: 
count    8603.000000
mean        0.903068
std         1.377258
min         0.000000
25%         0.050000
50%         0.360000
75%         1.120000
max        15.000000

rev_excell = listings_raw[listings_raw.reviews_per_month==15]
rev_excell.shape
(1, 106)
```
- L'appartement est localisé dans la périphérie de Bordeaux (Mérignac), bien que la surface soit réduite (20m2), la décoration et l'agencement en font une place de premier choix: - le taux de reviews s'élève à 15 par mois, ce qui signifie au minimum 15 hôtes différents, pour une durée minimale de 15 nuits.  
- 3 jours prochainement ne sont pas réservés (date de cette étude: mi-Juillet 2019). Egalement, 6 nuits sont disponibles le mois prochain puis 15 au mois suivant. Le taux d'occupation du bien est ainsi probablement supérieur à la moyenne.


**Quel est le taux de rentabilité de ce logement ?**

On prends en compte le prix au  m² de l'apartement à Mérignac (2.531 €) et la configuration de ce studio de luxe et on tente maintenant
de connaître 

    - **200 nuitées**, ou le taux d'occupation de **55%** appliqué sur 1 an.
        - Pour une location à l'allègement de **69 € la nuitée**, sans en faire part à quelque autorité qui soit permet:
      - **15%** de rentabilité locative pour ce très beau studio.
     
     
```
amerig = listings_raw[listings_raw.neighbourhood_group_cleansed=='Merignac']

```


## <a name="PM" ></a> Conseils décoration aux futurs hôtes

L'algorithme de scoring d'Airbnb, à la manière de Google effectue le référencement des biens en fonction d'une multitude de variables, dont le taux de note attribuées. Airbnb affiche des messages d'information destinés à faciliter la conversion lorsque l'on choisit une date. Ici, on s'intéresse aux biens dont le nombre d'appréciations est supérieur à 13 commentaires par mois: 3 biens concernés. Cependant, une information pertinente est donnée sur la visibilité du logement: le nombre total de fois ou le bien a été visualisé.
Ces données sont récupérables dans le but de prédire le poids des variables liées aux scoring des annonces. L'exposition de ces biens peut également inspirer les futurs hôtes lorsqu'ils mettront leur bien sur la plateforme, ces logements sont notés très régulièrement. A moins que les voyageurs soient quotidien sous la menace d'un révolver de l'hôte, le taux constant de commentaires positifs reste un bon indicateur de la qualité de ces biens. 

- [Pour aller plus loin](#PAL)

Les biens suivants ont été notés plus de 13 fois par mois
[n 1](https://www.airbnb.com/rooms/29958146?source_impression_id=p3_1563230927_euTvpaIQmeQo5abG)

"This place is getting a lot of attention.
It’s been viewed 111 times in the past week."

[n 2](https://www.airbnb.com/rooms/29958146?source_impression_id=p3_1563230927_euTvpaIQmeQo5abG)

"This is a rare find.
Alexandre's place is usually booked."


```
rev_ehigh = listings_raw[listings_raw.reviews_per_month>13]
```

## <a name="DI" ></a> Disponibilité moyenne des biens par quartier - évolution sur 90, 60, 30 jours

On remarque que la disponibilité moyenne la plus faible se trouve au quartier Capucins-Victoire… Ainsi, le taux d'occupation le plus élevé se trouve Capucins-Victoire suivie par Hôtel de Ville. 

Hôtel de Ville est le quartier au tarif journalier le plus élevé. Les prix de location des biens du quartier Capucins-Victoire seraient-ils sous-estimés ? Cette question complexe fera l'objet d'une prochaine étude.


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite moyenne des Biens par Quartier (moy 30j).png
" width="100%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite moyenne des Biens par Quartier (moy 60j).png
" width="100%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite moyenne des Biens par Quartier (moy 90j).png
" width="100%">


## <a name="NL" ></a> Nombre de licences par quartier

**Enregistrement obligatoire en mairie**

Depuis le 1er mars 2019, les Bordelais qui souhaitent louer une pièce ou toute leur résidence principale via Airbnb ont pour obligation d enregistrer leur bien auprès de la mairie qui souhaite éviter que les immeubles de ses quartiers historiques du centre ne soient intégralement consacrés à ce type d’activités.

On remarque que les hôtes qui louent des biens au centre sont les plus respectueux des règles. De ce fait, le prix moyen plus élevé peuvent s'expliquer du fait de cette déclaration qui implique le paiement des impôts (de 0% à 45% en fonction de la tranche de revenus en comptant un abattement de 50%). Au contraire, une quantité très faible de biens sont enregistrés en périphérie.


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Licences par Quartier.png" width="100%">


## <a name="HL" ></a> Combien les hôtes possèdent-ils de biens en location ? 

75 % des hôtes proposent un bien unique en location.

```
freq = listings.groupby(['host_id']).size().reset_index(name='num_host_listings')
host_prop = freq.groupby(['num_host_listings']).size().reset_index(name='count').transpose()
host_prop.columns = host_prop.iloc[0]
host_prop = host_prop.drop(host_prop.index[0])
host_prop

Out[]: 
Quantité de biens loués     1    2   3   4   5   6   7  ...  17  21  25  34  61  72  75
count                     7318  605  98  46  24  11   7 ...   2   1   1   1   1   1   1

[1 rows x 19 columns]

```
**Graphique** : 

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Nombre d'ID uniques possédant n listes .png" width="100%">


On remarque que l'ensemble de ces hôtes possédant +20 biens sont classés 'Superhost' et possèdent le 'Government ID': ils déclarent l'activité professionnelle. 

Voici maintenant les descriptions des plus "grands hôtes" en termes de quantité de biens en gestion; qui ne manquent parfois pas de féerie :

```
freq = listings.groupby(['host_id', 'host_name', 'host_about']).size().reset_index(name='num_host_listings')

freq = freq.sort_values(by=['num_host_listings'], ascending=False)
freq = freq[freq['num_host_listings'] >= 20]
freq
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/FreqHostabout.PNG" width="100%">


## <a name="HM" ></a> Matrice heatmap des corrélations


Tracer une matrice de corrélations ici avec 47 variables reviendrait à (47*47 - 47)/2 = 1081 comparaisons.  

Pour cet exemple, je prends un extrait des variables avec les corrélations significatives définies au seuil de >0.35 (valeurs absolues).

```
corr_cols = ['accommodates', 'bedrooms', 'bathrooms', 'price', 'property_type', 'cancellation_policy', 'neighbourhood']

listings_tocorr = listings.loc[:, corr_cols] .corr()

list_corr_filter = listings_tocorr[(listings_tocorr>0.35) | (listings_tocorr< -0.35)]

sns.heatmap(list_corr_filter, linewidths=0.1, vmax = 1.0, square = True, cmap = colormap, linecolor = 'white')
```
On remarque que le prix est fortement corrélé avec le nombre de chambres (0.46). Ce qui montre que le prix suit linéairement la taille du bien 'au sens utile'. Par contraste, on pourrait observer, pour une plateforme positionnée sur les biens de luxe, un prix moins corrélé à l'équipement de la maison mais variant sur d’autres critères plus subjectifs tel que la surface totale en m2, l’agencement des pièces ou la décoration.

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/corrheatmap.png" width="100%">

## <a name="ND" ></a> Nettoyage des données 


## <a name="CHA" ></a> Chargement des bibliothèques et données

```
%matplotlib inline
import os
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.preprocessing import LabelEncoder
from sklearn.preprocessing import StandardScaler
```

Pour rappel, il y avait au total 9 699 inscriptions à Airbnb à Bordeaux le 16 juin 2019. Nous allons mainteant nous 'attaquer au gâteau' tout en étant prudent car la qualité des futurs prédictions dépendent de la qualité des données brutes puis de la manière dont elles seront traitées actuellement.


```
listings = pd.read_csv(r"/Users/Jeremie/Desktop/listings_detail.csv", low_memory = False)

listings.columns


Index(['id', 'listing_url', 'scrape_id', 'last_scraped', 'name', 'summary',
       'space', 'description', 'experiences_offered', 'neighborhood_overview',
       ...
       'instant_bookable', 'is_business_travel_ready', 'cancellation_policy',
       'require_guest_profile_picture', 'require_guest_phone_verification',
       'calculated_host_listings_count',
       'calculated_host_listings_count_entire_homes',
       'calculated_host_listings_count_private_rooms',
       'calculated_host_listings_count_shared_rooms', 'reviews_per_month'],
      dtype='object', length=106)

print(listings.shape)

(9
