---
title:  "Etude du marché Airbnb à Bordeaux"
date:   2019-7-14
layout: single
author_profile: true
comments: true
---

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Place_de_la_Bourse_Bordeaux2comp.png" width="100%">

**Préface**

D'où vient le nom 'Airbnb' ? : Le fondateur Brian Chesky et ses colocataires louaient un matelas gonflable dans leur salon lorsque Brian a eu l'idée de monter le service. Depuis son lancement en 2008, et malgré la concurrence accrue de quelques projets financés par le même fonds (Ycombinator), Airbnb a vu en grand ...

 Airbnb recense aujourd'hui - **3** millions de logements habitables répartis dans
                            - **65 000** villes à travers plus de 190 pays, pour un total de
                            - **150 millions d'utilisateurs**. 
                
                                      
La **France** est devenue le **premier marché d'Airbnb après les Etats-Unis**. L'hexagone compte plus de 300 000 logements et Paris se situe au rang de **numéro 1** des villes européennes avec 65 852 logements, devant Londres, Rome et Barcelone. 


Pour cette étude, je m’intéresse et j'explore les données recueillies d'Airbnb Bordeaux à la mi-Juin 2019  : 
- La répartition des biens et leurs caractéristiques (*types, tailles, prix, disponibilité)
- Les proportions de biens sous licence par quartier.

A partir de cette exploration, je réponds à plusieurs questionnements : 
- Quelles variables clés déterminent la performance locative des biens ? (la 'performance locative' correspond à la demande effective des biens)
- Quels sont les biens les plus populaires à Bordeaux ? Je m'intéresse en particulier à un bien qui remporte un grand succès  et pour lequel je calcule le montant du Retour sur Investissement. 
- Dans quelle proportion peut-on financer les charges de son logement ? (*Cas d'étude)

Enfin, j'ai nettoyé les données dans le but de les préparer pour prédire le prix journalier des biens. J'explique l'intérêt de cet indicateur pour les utilisateurs et hôtes. J'utilise enfin deux algorithmes afin de prédire le prix de location et compare l'effet de deux méthodes d'imputation sur la prédiction ("moyenne" vs. "plus proches voisins"). 


**Contenus**

1 - [Enseignements clés de l'enquête](#INS)

I- [Exploration des données](#DE)
  1. [Types de logements](#TP)
  2. [Types de propriétés](#TPP)
  3. [Répartition des biens par quartier](#NE)
  4. [Prix moyen d'une location par quartier](#PM)
  5. [Capacité totale des biens par nombre d'accommodés](#CP)
  6. [Evolution de la demande des biens par quartiers - à 90, 60, 30 jours](#DI)
  7. [Quelles variables sont les plus corrélées ?](#HM)
  8. [Déterminants de la Demande](#DEMAND)
  9. [Des données supplémentaires à capturer](#GAPM)
  
II- [Questionnements](#SK)
  1. [Combien les hôtes possèdent-ils de biens en location ?](#HL) 
  2. [Les hôtes déclarent-ils leurs activités ? ](#NL) 
  3. [Cas d'Etude : Payer ses charges grâce à Airbnb, en quelle proportion ?](#PAY)
  4. [La meilleure expérience client: qui est l'Hôte le-la plus plesbicité ?](#REVIEWS)
  * a.[Retour sur investissement de ce logement](#ROI)
  
  
III- [Nettoyage des données](#ND) 
  1. [Chargement des bibliothèques et données](#CHA)
  2. [Variables maintenues](#VM)
  2. [Variables supprimées](#VS)
  3. [Variables transformées](#VT)
  4. [Variables imputées](#VI)
  5. [Variables encodées](#VE)
  
IV- [Prédiction](#PR) 
  1. [Import des bibliothèques utiles](#IM)
  2. [The nullity matrix](#NULL)
  3. [Quelles variables sont meilleures pour prédire linéairement le prix de location ?](#PV)
  4. [Quartile-Quartile plot de la variable cible](#QQplot)
  5. [Définition d'une Class pipeline pour les modèles et mesures de performances](#PVA)
  6. [Fonction Plot de distribution des residuals, Ypred vs Y](#PRES)
  7. [Régression linéaire](#REL)
  8. [Xgboost- Régression linéaire](#XGB0)
  * a. [Prix ∈ (Dataset)](#XGB0)
  * b. [Prix ∈ [0,200]](#XGB1)
  * c.[log(Prix ∈ [0,200]](#XGB2)
      * [Imputation de 23% de données manquantes par la moyenne -> Effets sur le modèle](#OPT)
        * d. [Xgboost- Prix ∈ (Dataset) + 23% des Reviews imputés par la moyenne](#XGB3)
        * e. [Xgboost- log(Prix ∈ 0,200) + 23% des Reviews imputés par la moyenne](#XGB4)
      * [Imputation de 23% de données manquantes par Knn -> Effets sur le modèle](#OPT2)
        * f. [Xgboost- log(Prix ∈ [0,200]) + 23% des Reviews imputés par 'k-Nearest Neighbors (kNN- 1)'](#XGB5)
        * g. [Xgboost- log(Prix ∈ [0,200]) + 23% des Reviews imputés par 'k-Nearest Neighbors (kNN- 3)'](#XGB6)
        * h. [Xgboost- log(Prix ∈ [0,200]) + 23% des Reviews imputés par 'k-Nearest Neighbors (kNN- 5)'](#XGB7)
   
  8. [Résultats - comparaison des modèles](#RES)

V - [Conclusions et ouverture](#CC)
  1. [Pour aller plus loin](#PAL)  
    
VI-[Références](#REF)
    
   
   

 
## <a name="INS" ></a> Enseignements clés de l'enquête


**73 €** correspond au prix moyen d'une location pour deux personnes, située au coeur du centre-ville de Bordeaux (exemple de l'appartement en deuxième photo de couverture)

*le type de logement, le nombre de reviews, la quantité de biens en locations par hôte, le minimum de nuits nécessaire pour louer, le prix puis le quartier* (**Par ordre d'importance**) sont les variables critiques qui déterminent la performance locative des biens (la demande des biens). 

La *déclaration auprès de l'administration fiscale* s'élève en moyenne à **14 %**. On remarque que le nombre de licences est décroissant à mesure que l'on s'éloigne du centre-ville. 

**16%** est le taux de rentabilité locative du bien le plus populaire (compté en termes de reviews)

**36 %**, c'est le pourcentage réaliste moyen des frais financés (loyer+charges) grâce à la [location d'une chambre à Bordeaux sur  Airbnb](#PAY).

**104 %** est la rentabilité locative en proportion de l'investissement total, pour un appartement entier sur Bordeaux ( selon cette [configuration](#PAY)).

50% des biens sont correctement prédits avec une erreur inférieure à 9.53 euros grâce à l'utilisation d'Xgboost (algorithme basé sur des arbres de prédictions), pour une moyenne de 14.55 euros d'erreur.

L'algorithme est le plus performant pour prédire les biens qui ont ces caractéristiques particulières: ...

On prédit correctement les prix des locations de moins de 200 euros avec une erreur médiane de 9.53 euros.

## <a name="DE" ></a> Exploration des données 

L'ensemble de données obtenus ont été récoltées par un srapper indépendant sur [InsideAirbnb](http://insideairbnb.com/). Celui-ci indique  "listings_details" (détails des biens) comme étant « bon pour les visualisations », je vais donc utiliser ce fichier. 
Au total, il y avait 9 699 biens inscris à Airbnb à Bordeaux le 16 juin 2019.


### <a name="TP" ></a> Type de logements

```
freq = listings['room_type'].value_counts().sort_values(ascending=True)
freq.plot.barh(figsize=(15, 3), width=1, color = ["g","b","r"])
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Typesdelogement.png" width="35%">


### <a name="TPP" ></a> Type de propriétés

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
### <a name="NE" ></a> Répartition des biens par quartier

Hôtel de Ville arrive en tête avec plus de 1396 biens en location. Les 7 quartiers les moins répertoriés représentent à eux seul le même nombre de biens que le deuxième et troisème quartiers majeur réunis (Chartron et Capucins-Victoire).

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

### <a name="PM" ></a> Prix moyen d'une location par quartier pour deux personnes

Hotel de Ville est en moyenne 57 % plus cher que l'ensemble des quartiers alentours ! La ville s'étend pourtant sur 1.5 km de rayon (en prenant le centre du quartier Hotel de Ville). Nous verrons un peu plus tard dans cette analyse une piste pour expliquer une partie de cette différence ([Les hôtes déclarent-ils leurs activités ?](#NL)).

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

### <a name="CP" ></a> Capacité totale des biens par nombre d'accommodés	


**4112 biens pour 2 personnes** sont disponibles, ce qui fait une disponibilité totale de ces biens pour 8224 personnes.

**2469 biens pour 4 personnes** sont disponibles, ce qui en font une disponibilité totale - supérieure de 1652 personnes.

Ce qui signifie, en cas d'overbooking, que les familles seront les plus nombreuses à se promener en Centre-Ville.

Légende : Le nombre de biens en verticale, le nombre total d'accomodés en légende au-dessus de la barre et le nombre d'accomodés	en X.

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

### <a name="DI" ></a> Disponibilité moyenne des biens par quartier - évolution sur 90, 60, 30 jours

On remarque que la disponibilité moyenne la plus faible se trouve au quartier Capucins-Victoire… Ainsi, le taux d'occupation le plus élevé se trouve Capucins-Victoire suivie par Hôtel de Ville. 

Hôtel de Ville est le quartier au tarif journalier le plus élevé. Les prix de location des biens du quartier Capucins-Victoire seraient-ils sous-estimés ? Cette question complexe fera l'objet d'une prochaine étude.



<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2030j).png" width="100%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2060j).png" width="100%">


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Disponibilite%20moyenne%20des%20Biens%20par%20Quartier%20(moy%2090j).png" width="100%">


### <a name="HM" ></a> Matrice heatmap des corrélations


Tracer une matrice de corrélations ici avec 47 variables reviendrait à 2 (k) parmi 47 (n)= 1081 combinaisons/comparaisons possibles.  

Pour cet exemple, je prends un extrait des variables avec les corrélations significatives définies au seuil de >0.35 (valeurs absolues).

```
corr_cols = ['accommodates', 'bedrooms', 'bathrooms', 'price', 'property_type', 'cancellation_policy', 'neighbourhood']

listings_tocorr = listings.loc[:, corr_cols] .corr()

list_corr_filter = listings_tocorr[(listings_tocorr>0.35) | (listings_tocorr< -0.35)]

sns.heatmap(list_corr_filter, linewidths=0.1, vmax = 1.0, square = True, cmap = colormap, linecolor = 'white')
```
On remarque que le prix est fortement corrélé avec le nombre de chambres (0.46). Ce qui montre que le prix suit linéairement la taille du bien 'au sens utile'. Par contraste, on pourrait observer, pour une plateforme positionnée sur les biens de luxe, un prix moins corrélé à l'équipement de la maison mais variant sur d’autres critères plus subjectifs tel que la surface totale en m2, l’agencement des pièces ou la décoration.

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/corrheatmap.png" width="75%">


### <a name="GAPM" ></a> Des données supplémentaires à capturer 

L'algorithme de scoring d'Airbnb, à la manière de Google effectue le référencement des biens en fonction d'une multitude de variables, dont le taux de note attribuées. Airbnb affiche des messages d'information destinés à faciliter la conversion lorsque l'on choisit une date. Ici, on s'intéresse aux biens dont le nombre d'appréciations est supérieur à 13 commentaires par mois: 3 biens sont concernés.

Cependant, une information pertinente est donnée sur la visibilité du logement: le nombre total de fois ou le bien a été visualisé.
Ces données sont récupérables dans le but de prédire le poids des variables liées aux scoring des annonces.


Les biens suivants ont été notés plus de 13 fois par mois :

[n 1](https://www.airbnb.com/rooms/29958146?source_impression_id=p3_1563230927_euTvpaIQmeQo5abG)

"This place is getting a lot of attention.
It’s been viewed 111 times in the past week."

[n 2](https://www.airbnb.com/rooms/29958146?source_impression_id=p3_1563230927_euTvpaIQmeQo5abG)

"This is a rare find.
Alexandre's place is usually booked."


```
rev_ehigh = listings_raw[listings_raw.reviews_per_month>13]
```

### <a name="DE" ></a> Déterminants de la Demande 

```
feature_importance_dispo30

neighbourhood                                 0.037372
price                                         0.037857
minimum_minimum_nights                        0.038779
host_listings_count                           0.045887
number_of_reviews_ltm                         0.049543
room_type                                     0.115095
```
On obtient ici les variables les plus importantes qui permettent de déterminer la demande des biens.

Le type de logement arrive en premier avec une importance de 11,5% (0.115), le nombre de notes vient ensuite avec 5% d'importance puis le nombre de biens gérés par hôte (4.5%). Ce qui nous amène aux questionnements suivants : Quels types de logements (room_type) sont les mieux loués ? Quel est le prix moyen des logements les plus demandés ? 

### Disponibilité par type de logement

 ```
dispo30jours_moy_byroomtype = = listings5.groupby('room_type').availability_30.describe()

                  count      mean        std  min  25%  50%   75%   max
room_type                                                              
Entire home/apt  7326.0  4.738466   7.731085  0.0  0.0  0.0   7.0  30.0
Private room     2306.0  9.298786  10.910107  0.0  0.0  3.0  18.0  30.0
Shared room        67.0  9.791045  12.506407  0.0  0.0  0.0  24.5  30.0
```

On remarque que les logements entiers sont les plus occupés en moyenne sur la plateforme.
A Bordeaux, pour 3/4 des données, les logements entiers sont 2 fois et demi plus occupés (en moyenne) que les logements privés chez l'habitant.


### Disponibilité par nombre de reviews 

 ```
dispo_parnbreviews = listings5.groupby("number_of_reviews").availability_30.describe()
 ```
 
### Expérience

Expérience : On prend les biens répondants à l'ensemble des critères correspondants à une Demande élevée. Comment se situe la Demande de ces biens ? Les critères sont-ils justifiés/suffisants pour déterminer les biens les plus demandés ? Quels biens 'très demandés' ont échappé à ce filtrage ? Autrement dit, peut-on attribuer une relation causale de l'ensemble de ces variables sur la Demande ?


## <a name="SK" ></a> Questionnements 


### <a name="HL" ></a> Combien les hôtes possèdent-ils de biens en location ? 

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

Voici maintenant les descriptions des plus "grands hôtes" en termes de quantité de biens gérés; qui ne manquent parfois pas de féerie :

```
freq = listings.groupby(['host_id', 'host_name', 'host_about']).size().reset_index(name='num_host_listings')

freq = freq.sort_values(by=['num_host_listings'], ascending=False)
freq = freq[freq['num_host_listings'] >= 20]
freq
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/FreqHostabout.PNG" width="100%">


### <a name="NL" ></a> Les hôtes déclarent-ils leurs activités ? - Nombre de licences par quartier


**Enregistrement obligatoire en mairie**

Depuis le 1er mars 2019, les Bordelais qui souhaitent louer une pièce ou toute leur résidence principale via Airbnb ont pour obligation d'enregistrer leur bien auprès de la mairie qui souhaite éviter que les immeubles de ses quartiers historiques du centre ne soient intégralement consacrés à ce type d’activités.

On remarque que les hôtes qui louent des biens au centre sont les plus respectueux des règles. De ce fait, le prix moyen plus élevé peuvent s'expliquer du fait de cette déclaration qui implique le paiement des impôts (de 0% à 45% en fonction de la tranche de revenus en comptant un abattement de 50%). Au contraire, une quantité très faible de biens sont enregistrés en périphérie.


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Licences par Quartier.png" width="100%">



### <a name="PAY" ></a> Payer ses charges grâce à Airbnb, en quelle proportion ?


On souhaite savoir s’il est possible de payer le loyer + charges d’un logement de deux chambres en répertoriant l’une des chambres sur Airbnb. L'objectif est de calculer les bénéfices nets attendus (après le loyer moyen, les services publics (eau , gaz, électricité) et Internet).

- **24,5 %**, c’est la part des dépenses moyenne en proportion du loyer que l'on prend en référence pour couvrir les frais du logement (assurances, gaz, électricité, internet).

Je prends la référence suivante: un logement possède une surface de 65 m2, pour un loyer de 14e/m2 (moyenne à Bordeaux) hors-charges [Source](https://blog.locservice.fr/bordeaux-les-chiffres-cles-du-marche-locatif-prive-en-2013-1469.html).

## Calcul:
- 910 € de loyer + charges (220€) = 1130 € /mois 
  - **1130 €**, c'est le coût moyen de rente mensuelle pour un logement de 2 chambres toutes charges comprises à Bordeaux
  
- **29.7 %** : taux d'occupation moyen de ce type de logement à Bordeaux (méthode ci-dessous)

**Résultats**:

 - **4068 €**, c'est le montant annuel des frais couverts grâce à la rente sur Airbnb,

 - **36 %** : pourcentage des frais mensuels financés

---

**Méthode de calcul du taux d'occupation

Il est très complexe d'obtenir une information exacte sur l'occupation des biens d'Airbnb. 
Les plateformes tel que Airdna et d'autres indiquent des taux très élevés (jusqu'à 80%). Ils se basent sur le calcul moyen des jours "occupés" (non disponibles) qu'ils considèrent par défaut en tant que 'loué', de plus, uniquement sur les 30 prochains jours. Pour ce calcul, j'utilise une méthode différente et plus prudente :

- Exemple : 75% des biens sont disponibles (au maximum) 8 jours sur les 30 prochains. La plupart des biens sont donc disponibles moins de 8 jours. Ainsi, si l'on soupçonne qu'ils sont loués sur les autres 22 jours, ce taux d'occupation 'optimiste'  s'élève à 73.33 %. 

J'effectue le calcul sur 30, 60, 90, 365 jours et je prends la moyenne: 

```
# listings = pd.read_csv(r"/Users/jeyrm/Documents/Bordeaux/listings_detail.csv", low_memory = False)

araw.availability_30.mean()
Out[]: 9.298785776235906                   # En moyenne, le bien est loué au maximum 21 jours sur les 30 prochains.

araw.availability_30.describe() # Disponibilité dans les 30 jours 
Out[]: 
count    2306.000000       
mean        9.298786
std        10.910107
min         0.000000
25%         0.000000
50%         3.000000
75%        18.000000                        # 75% des biens sont loués au maximum 12 jours (30-18) dans les 30 prochains jours >>> 40 % max        30.000000                         d'occupation
Name: availability_30, dtype: float64


araw.availability_60.describe()
...
75%        43.000000                         # 28.3 % d'occupation

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

Par mesure de prudence, on prends la moyenne des 4 : 29,70 % en tant que taux d'occupation de référence.



### <a name="REVIEWS" ></a> La meilleure expérience client, l'Hôte la plus appréciée

La notation des hôtes s'étend de 0 à 15 par mois, avec plus des 3/4 des annonces qui possèdent au minimum une petite attention post-location. On remarque cependant (index 'max') ci-dessous qu'il existe des hôtes qui sont notés jusqu'à 15 fois durant le mois ! Visiblement, il y a une hôte qui remplit cette condition exceptionnelle :


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Selection_006.png" width="100%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/ubuntuShutter.png" width="100%">


[*lien*](https://www.airbnb.com/rooms/32541282?source_impression_id=p3_1563223901_s1cD%2BxPIaIiI2HbY&check_in=2019-07-18&guests=1&adults=1&sl_alternate_dates_exclusion=true&check_out=2019-07-19)

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
- 3 jours prochainement ne sont pas réservés (date de cette étude: mi-Juillet 2019). Également, 6 nuits sont disponibles le mois prochain puis 15 au mois suivant. Le taux d'occupation du bien est ainsi supérieur à la moyenne.




### <a name="ROI" ></a>  Retour sur investissement de ce logement 

On prend en compte le prix au  m² de l'apartement à Mérignac (2.531 €) et la configuration de ce studio de luxe et on tente maintenant
de connaître le retour sur investissement à l'année de ce type de bien.

On prend les données suivantes en considération : 
- **200 nuitées**, ou le taux d'occupation de **55%** appliqué sur 1 an (10 mois).
- Pour une location au coût de **69 € la nuitée**
        
Ce qui permet une rentabilité locative de **16%** pour ce studio.
    


## <a name="ND" ></a> Nettoyage des données 


### <a name="CHA" ></a> Chargement des bibliothèques et données

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

Pour rappel, il y avait au total 9 699 inscriptions à Airbnb à Bordeaux le 16 juin 2019. Nous allons maintenant nous 'attaquer au gâteau' tout en étant prudent car la qualité des futures prédictions dépendent de la qualité des données brutes puis de la manière dont elles seront traitées actuellement.


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

(9699, 106)


listings.price.head()
0     $24.00
1     $71.00
2     $75.00
3    $100.00
4    $155.00
Name: price, dtype: object
```


Je définis dans un premier temps une fonction qui permet de vérifier les valeurs non attribuées pour l’ensemble des variables (en pourcentages).


```
def isnull(X):
    listings_na = pd.isnull(X)
    listings_na = listings_na*1
    listings_na = (listings_na==1).sum()/listings_na.shape[0]*100
    listings_na = pd.DataFrame(listings_na.T.sort_index(ascending=True))
    listings_na = listings_na[listings_na>0.0001] # je garde uniquement les colonnes dont le % de NaNs (non-attribuées) est défini 
    listings_na = listings_na.dropna()
    return listings_na

isnull(listings)

Out[183]: 

access                        50.994948
bathrooms                      0.051552
bedrooms                       0.051552
beds                           0.134034
cleaning_fee                  35.807815
description                    1.505310
first_review                  20.744407
host_about                    60.542324
host_acceptance_rate         100.000000
host_location                  0.690793
host_neighbourhood            46.561501
host_response_rate            38.117332
host_response_time            38.117332
house_rules                   53.407568
interaction                   50.438190
jurisdiction_names             0.969172
last_review                   20.744407
license                       84.843798
market                         0.226828
medium_url                   100.000000
monthly_price                 93.896278
name                           0.010310
neighborhood_overview         39.344262
neighbourhood                 22.589958
notes                         65.212909
review_scores_accuracy        22.548716
review_scores_checkin         22.579647
review_scores_cleanliness     22.507475
review_scores_communication   22.538406
review_scores_location        22.559027
review_scores_rating          22.445613
review_scores_value           22.559027
reviews_per_month             20.744407
security_deposit              35.849057
space                         36.519229
square_feet                   99.133931
state                          2.278585
summary                        2.876585
thumbnail_url                100.000000
transit                       36.014022
weekly_price                  89.194762
xl_picture_url               100.000000
zipcode                        1.237241
```

### <a name="NULL" ></a> Matrice des nullités 

```
import missingno as msno

cols = isnull(listings).reset_index()
cols = cole['index']
msno.heatmap(listings[cols], figsize=(20,20), cmap= colormap)
```

La matrice de nullité donne une idée rapide de la répartition des données manquantes dans le dataset. 

Cette heatmap calcule la relation de nullité entre les différentes variables grâce à un coefficient de correlation R (-1 ≤ R ≤ 1). 

- Lorsque la première variable et la deuxième variable ont les **valeurs manquantes correspondantes**, la corrélation de nullité est positive parfaite **(R = 1)**.  

- Une corrélation de nullité négative parfaite (**R = -1**) signifie qu’**une des variables est manquante et que la seconde est présente**.

- Si la corrélation de nullité est très proche de zéro (-0,05 <R <0,05), aucune valeur n'est affichée. 


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/missingnoNullmatrix.png" width="100%">

On remarque que 'reviews per month' n'est pas défini dès l'instant ou 'first_review' n'est pas défini. Il n'y a pas de 'first review' donc obligatoirement, il n'y aura pas de calcul de 'reviews per month'. L'ensemble de ces valeurs non-définies sont donc égales à 0.

### <a name="VM" ></a> Variables maintenues

On garde l'ensemble des données numériques (47 colonnes) ainsi que les variables catégoriques 'room_type', 'host_since', 'property_type' et 'cancellation_policy'. 

### <a name="VS" ></a> Variables supprimées 

Je supprime toutes lignes avec 'bathrooms', 'bedrooms', 'beds' non attribuées (environ 0,5 a 1% des données. 
On supprime également toute entrée "étranges" comme les listings avec valeur = 0 pour ‘bedrooms’, ‘beds’ ou ‘price’. 

```
listings = listings.dropna(axis=0, subset = ['bathrooms'])
listings = listings.dropna(axis=0, subset = ['bedrooms'])
listings = listings.dropna(axis=0, subset = ['beds'])
listings = listings[listings['beds'] != 0] 
listings = listings[listings['bedrooms'] != 0]
listings = listings[listings['price'] != 0] #Pas de bien gratuits à Bordeaux; même le logeur du 13 Novembre n'aurait pas trouvé cela louche
listings = listings.dropna(axis=0, subset = ['bathrooms'])
listings = listings.dropna(axis=0, subset = ['bedrooms'])
listings = listings.dropna(axis=0, subset = ['beds'])
```


On se sépare temporairement des différentes notes non attribuées (environ 22% des données). C'est une partie importante de données manquantes dont je me sépare, leur traitement sera réservé au moment de la prédiction pour tester différentes techniques d'imputation:

```
listings = listings.dropna(axis=0, subset = ['review_scores_location'])
listings = listings.dropna(axis=0, subset = ['review_scores_accuracy'])
listings = listings.dropna(axis=0, subset = ['review_scores_checkin'])
listings = listings.dropna(axis=0, subset = ['review_scores_communication'])
listings = listings.dropna(axis=0, subset = ['review_scores_value'])
listings = listings.dropna(axis=0, subset = ['review_scores_cleanliness'])
listings = listings.dropna(axis=0, subset = ['review_scores_rating'])
listings = listings.reset_index()
del listings['index', 'thumbnail_url', 'xl_picture_url', 'host_acceptance_rate', 'medium_url', 'scrape_id', 'square_feet']
# Plus de 95% des surfaces en m2 sont manquantes. Après vérification, certains hôtes mentionnent la surface du bien dans les descriptions (variables str) : celles-ci peuvent en partie être déterminées.  

```

### <a name="VT" ></a> Variables transformées

```
listings.price.head()
0     $24.00
1     $71.00
2     $75.00
3    $100.00
4    $155.00
Name: price, dtype: object
```

Le type de variable prix est ‘str’, de la forme ‘$50,00’, afin de les convertir en float, on supprime le  ‘$’ et remplace la ‘,’ par ‘.’  puis on convertit la variable en euros (*0.88). On effectue la même transformation pour le prix par semaine, par mois, les frais de ménage, le prix d'une personne supplémentaire, du déposit de sécurité.

```

a = []
for i in listings.price:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:]
        b = b.replace(',','')
        b = float(b)
        c = b*0.88 
        a.append(c)
listings.price = 0,88*a

a = []
for i in listings.weekly_price:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:] #removes the first character '$'
        b = b.replace(',','') # replaces ',' value as 135,00 to 'empty' in order to be able to convert it then 
        b = float(b)
        c = b*0.88
        a.append(b)
listings.weekly_price = a

a = []

for i in listings.monthly_price:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:]
        b = b.replace(',','')
        b = float(b)
        a.append(b)
        c = b*0.88
listings.monthly_price = a

a = []
for i in listings.cleaning_fee:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:]
        b = b.replace(',','')
        b = float(b)
        c = b*0.88
        a.append(b)
        
listings.cleaning_fee = a

a = []
for i in listings.extra_people:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:]
        b = b.replace(',','')
        b = float(b)
        c = b*0.88
        a.append(b)

listings.extra_people = a

a = []
for i in listings.security_deposit:
    if pd.isnull(i):
        a.append(i)
    else:
        b = str(i)[1:]
        b = b.replace(',','')
        b = float(b)
        c = b*0.88
        a.append(b)
listings.security_deposit = a

```

### <a name="VI" ></a> Variables imputées

J’entre temporairement les données de cleaning_fee et security_deposit par leur moyenne respectives. Dans le cas où ces variables sont importantes pour la prédiction, il peut est nécessaire d’entrer des valeurs plus proches de la réalité. Pour aller plus loin, on pourra inférer ces valeurs grâce à un algorithme des plus proches voisins (nearest neighbors)). 

```
listings = listings.fillna(listings.mean())

```

Pour la variable 'reviews_per_month’ : une large partie de valeurs sont NaNs, on peut les supprimer. Cependant, après un rapide coup d'œil (matrice des Nullités précédente), on remarque que 'reviews_per_month’ est non défini dès l’instant ou ‘first_review’ est non défini également. L'ensemble de ces valeurs non-définies sont donc égales à 0.

Après une revue rapide des biens concernées sur Airbnb, on s’aperçoit que les biens ne sont en fait pas notés (Airbnb indique « No reviews (yet) »). 

On entre ‘0’ pour l’ensemble des valeurs concernées.

```
listings.reviews_per_month  = listings.reviews_per_month.fillna(0)
```

### <a name="VE" ></a> Variables encodées 

Le regresseur de scikit-learn accepte uniquement les variables numériques.
J'utilise des arbres de régression dans la partie prédiction. Splitter la variable en plusieurs classes binaires (OneHot) risque d'affecter considérablement la performance de ce type d'algorithme.
On va donc maintenant encoder des variables catégoriques ‘intéressantes’ en valeurs numériques (le type de chambre, de propriétes, de quartier) par LabelEncoder().  

```
categorical_cols = ['room_type', 'host_since', 'property_type','cancellation_policy'] 

le = LabelEncoder()

listings_encoded = listings[categorical_cols].apply(lambda col: le.fit_transform(col))
neigh_encoded = le.fit_transform(listings['neighbourhood'].astype(str))
neigh_encoded  = pd.DataFrame(neigh_encoded)
neigh_encoded.columns = ['neighbourhood']

listings_num = listings.select_dtypes(include = ['float64', 'int64']) 

listings = pd.merge(listings_encoded, listings_num, left_index=True, right_index=True) 
listings = pd.merge(listings, neigh_encoded, left_index=True, right_index=True)

```


## <a name="PR" ></a> Objectif de la prédiction

Une fois que les hôtes publient leurs annonces, le prix reste fixe tout au long du temps. Cependant, la demande varie de manière saisonnière et plus encore à l'échelle d'une semaine (évènements). L’estimation du prix des biens peut être une nouvelle fonctionnalité à caractère informatif destinée aux hôtes d'Airbnb. Elle peut permettre de mieux gérer le taux d’occupation, de rentabilité et d’offrir des offres intéressantes pour les clients à certaines périodes.

L'objectif final est de prédire le prix de location des biens inférieurs à 200 € la nuit avec une erreur médiane de 10.50 €. Ce qui signifie : 50% des biens devront être prédits avec une erreur inférieure à 10.50 € sur les données de test.   
 

### <a name="IM" ></a> Import des bibliothèques utiles 

```

%matplotlib inline
import os
import numpy as np
import pandas as pd
from scipy import stats
import matplotlib.pyplot as plt
from scipy.stats import norm, skew
from sklearn.feature_selection import f_regression
from sklearn import preprocessing
from sklearn.preprocessing import StandardScaler
from sklearn.model_selection import train_test_split
from sklearn.model_selection import GridSearchCV
from sklearn.linear_model import LinearRegression as Lin_Reg
from sklearn import metrics

import warnings
def ignore_warn(*args, **kwargs):
    pass
```

Dans un premier temps on supprime les valeurs extrêmes qui pourront déstabiliser le modèle (prix de location supérieur à 800 €). Également on supprime les variables inutiles (id, host_id...). Les variables super colinéaires ont été supprimées précedemment : 'monthly price' et 'weekly-price'. 


```
listings  = listings.drop(columns = ['longitude', 'latitude','id', 'host_id', 'monthly_price'])
listings.index[listings.price>800].tolist()
listings = listings.drop(index = [64, 743, 5842])
listings = listings.reset_index(drop=True)
Y = listings.price
del(listings["price"])
del(listings['weekly_price']) # suppression de la variable fuitée de 'price' : On ne la possèderait normalement pas en situation réelle
del(listings['monthly_price']) # suppression de la variable fuitée de 'price' : On ne la possèderait normalement pas en situation réelle
```

### <a name="PV" ></a> Visualisation des p-values

La p-value est la probabilité que deux variables soient dépendantes si le coefficient de corrélation entre ces deux variables est égal à zéro (hypothèse nulle confirmée). Si la p-value est plus petite que 0.05 (5%) alors on peut affirmer que la variable est statistiquement significative. 

Grace à la function f_regression, on obtient les F-scores (index [0]) converties en p_values (index [1]) comprises entre 0 et 1.

Les variables beds et cleaning_fee ont le plus de capacité pour prédire le prix. 

```
columns = listings.columns.tolist()

p_values = f_regression(listings, Y)[1]
p_valuesdf = pd.DataFrame(p_values, index = listings.columns)

p_valuesdf.sort_values(by = 0, ascending=True)

Out[272]: 
                                                          
beds                                            0.000000e+00
cleaning_fee                                0.
security_deposit                           0.
price                                              0.
bedrooms                                      0.
bathrooms                                     0
accommodates                              0.
room_type                                     1.897830e-196
guests_included                               4.133347e-136
cancellation_policy                           6.704422e-114
property_type                                  7.284759e-81
calculated_host_listings_count_private_rooms   2.289285e-71
calculated_host_listings_count_entire_homes    3.214125e-43
calculated_host_listings_count                 2.328240e-34
reviews_per_month                              3.494274e-34
host_listings_count                            2.474280e-30
host_total_listings_count                      2.474280e-30
availability_365                               3.235064e-22
number_of_reviews_ltm                          5.117113e-18
extra_people                                   7.345291e-15
                                                   ...
availability_90                                6.698582e-02
availability_60                                1.178657e-01
review_scores_location                         1.259715e-01
review_scores_communication                    5.022401e-01
review_scores_accuracy                         5.126517e-01
review_scores_checkin                          5.418442e-01
```

### <a name="QQplot" ></a> Graphique Quartile-Quartile de la distribution de la variable Prix

Le graphique Quartile-Quartile est une comparaison entre une distribution (ensemble de valeurs) et une autre distribution (une variable, une loi de probabilité). 

Ici, on s'intéresse à savoir si les données sont distribuées de manière normale (de paramètres mu (moyenne) et sigma (écart-type)).

Si les données suivent la distribution normale, les points seront situés sur la droite diagonale. 
On remarque que les quantiles de la distribution (valeurs qui divisent les données en intervalles contenant le même nombre de données). du prix sont à peu près égaux aux quartiles de la distribution normale. [Pour bien saisir le QQ-plot](https://www.youtube.com/watch?v=okjYjClSjOg).


```
sns.kdeplot(listings['price'] , clip= (0.0, 800))

fig = plt.figure()
res = stats.probplot(listin['price'], plot=plt)
plt.show()
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/Pricedistribution.png" width="80%">

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/QQplotprice.png" width="80%">

Les valeurs sont anormalement élevées en fin de distribution pour parler parfaitement de distribution normale. On remarque une dissymétrie dans la partie droite (prix de location élevés). En gardant ces valeurs, le risque est de déstabiliser le modèle linéaire futur.

### <a name="PVA" ></a> Class pipeline pour les modèles

J'utilise une classe avec les fonctions principales d’évaluation des modèles. Pour évaluer la performance de l'algorithme, on calcule sur n  itérations  le score moyen à chaque convergence afin de pouvoir effectuer des comparaisons. On prend 70 % des biens en entraînement, le reste pour la validation.  

Pour évaluer le modèle, j'utilise le score R2 qui sert à mesurer la précision donnée par le modèle de régression linéaire et l'erreur absolue moyenne (sur train et test set). 


```
class model:

    def __init__(self, model):
        self.model = model
        self.x_train = None
        self.y_train = None
        self.x_test = None
        self.y_test = None
        self.y_pred_train = None
        self.y_pred_test = None
        self.train_score = None
        self.test_score = None
        self.train_score_log = None
        self.test_score_log = None
        self.train_score_mae = None
        self.test_score_mae = None
        self.train_score_mae1 = None
        self.test_score_mae1 = None
        self.train_score_m_ae_unlog = None 
        self.test_score_m_ae_unlog = None
        self.train_score_mae_unlog = None
        self.test_score_mae_unlog = None

    def data_split(self, x, y, test_size):
        self.x_train, self.x_test, self.y_train, self.y_test = train_test_split(x, y, test_size=test_size)

    def score_reg(self):
        return self.train_score, self.test_score
        
    def score_mean_abs_err(self):
        self.train_score_mae = metrics.mean_absolute_error(self.y_pred_train, self.y_train)
        self.test_score_mae = metrics.mean_absolute_error(self.y_test, self.y_pred_test)
        return self.train_score_mae, self.test_score_mae

    def score_median_abs_err(self):
      self.train_score_mae1 = metrics.median_absolute_error(self.y_pred_train, self.y_train)  
      self.test_score_mae1 = metrics.median_absolute_error(self.y_test, self.y_pred_test)
      return self.train_score_mae1, self.test_score_mae1

    def score_log(self):
        self.train_score_log = metrics.r2_score(np.exp(self.y_train), np.exp(self.y_pred_train))
        self.test_score_log = metrics.r2_score(np.exp(self.y_test), np.exp(self.y_pred_test))
        return self.train_score_log, self.test_score_log

    def score_mean_log(self):
       self.train_score_m_ae_unlog = metrics.mean_absolute_error(np.exp(self.y_pred_train), np.exp(self.y_train))
       self.test_score_m_ae_unlog = metrics.mean_absolute_error(np.exp(self.y_test), np.exp(self.y_pred_test))
       return self.train_score_m_ae_unlog, self.test_score_m_ae_unlog

    def score_median_log(self):
        self.train_score_mae_unlog = metrics.median_absolute_error(np.exp(self.y_pred_train), np.exp(self.y_train))
        self.test_score_mae_unlog = metrics.median_absolute_error(np.exp(self.y_test), np.exp(self.y_pred_test))
        return self.train_score_mae_unlog, self.test_score_mae_unlog

    def data_frame_convert(self):
        df_train = pd.DataFrame({'y_pred': self.y_pred_train, 'y_real': self.y_train})
        df_test = pd.DataFrame({'y_pred_test': self.y_pred_test, 'y_real_test': self.y_test})
        return self.train_score, self.test_score, df_train, df_test

    def data_frame_convert_log(self):
        df_train = pd.DataFrame({'y_pred': np.exp(self.y_pred_train), 'y_real': np.exp(self.y_train)})
        df_test = pd.DataFrame({'y_pred_test': np.exp(self.y_pred_test), 'y_real_test': np.exp(self.y_test)})
        return self.train_score_log, self.test_score_log, df_train, df_test

    def fit_model(self, x, y, test_size):
        self.data_split(x, y, test_size)
        self.model = self.model.fit(self.x_train, self.y_train)
        self.train_score = self.model.score(self.x_train, self.y_train)
        self.test_score = self.model.score(self.x_test, self.y_test)
        self.y_pred_train = self.model.predict(self.x_train)
        self.y_pred_test = self.model.predict(self.x_test)

def model_iterations(n, x, y, model_arg, log_bool=False):
    training_scores = [None]*n
    testing_scores = [None]*n
    training_scores_mean_ae = [None]*n
    testing_scores_mean_ae = [None]*n
    training_scores_mae = [None]*n
    testing_scores_mae = [None]*n


    for i in range(n):
        new_model = model(model_arg)
        new_model.fit_model(x, y, 0.5)
        training_scores[i], testing_scores[i] = new_model.score_reg() if not log_bool else new_model.score_log()
        training_scores_mean_ae[i], testing_scores_mean_ae[i]  = new_model.score_mean_abs_err() if not log_bool else new_model.score_mean_log()
        training_scores_mae[i], testing_scores_mae[i] = new_model.score_median_abs_err() if not log_bool else new_model.score_median_log()

    print('-Best R2 training', np.max(training_scores))
    print('-Best R2 testing', np.max(testing_scores))
    print('-Avg R2 training', np.mean(training_scores))
    print ('-Avg R2 testing',np.mean(testing_scores))
    print ('Training mean score (_mean absolute error)', np.mean(training_scores_mean_ae))
    print ('Testing mean score (_mean absolute error)', np.mean(testing_scores_mean_ae))

    print ('Training best score (_mean absolute error)', np.min(training_scores_mean_ae))
    print ('Training best score (median absolute error)', np.min(training_scores_mae))

    print ('Testing best score (_mean absolute error)', np.min(testing_scores_mean_ae))
    print ('Testing best score (median absolute error)', np.min(testing_scores_mae))
    print ('std -ecarts moyens des perfs testing', np.std(testing_scores_mae))
    print ('std des perfs training', np.std(training_scores_mae))

    return new_model
 ```


### <a name="PRES" ></a> Fonction graphique de distribution des residuals, Ypred vs. Y et leur distribution

Je définis une fonction pour afficher valeurs prédites vs valeurs réels, les résidus (écarts prédit - réel) ainsi que leur distributions à partir de la classe définie précédemment.

```
def plot_residuals(ax1, ax2, ax3, y_pred, y_real, line_label, title):
    ax1.scatter(y_pred,
                y_real,
                color='blue',
                alpha=0.6,
                label=line_label)
    ax1.set_xlabel('Predicted Y')
    ax1.set_ylabel('Real Y')
    ax1.legend(loc='best')
    ax1.set_title(title)

    ax2.scatter(y_pred,
                y_real - y_pred,
                color='green',
                marker='x',
                alpha=0.6,
                label='Residual')
    ax2.set_xlabel('Y Prédit')
    ax2.set_ylabel('Residual')

    ax2.axhline(y=0, color='black', linewidth=2.0, alpha=0.7, label='y=0')

    ax2.legend(loc='best')
    ax2.set_title('Residual Graph')

    ax3.hist(y_real - y_pred, bins=30, color='green', alpha=0.7)
    ax3.set_title('Histogram of residual values')

    return ax1, ax2, ax3

def plots(model):
    fig, axes = plt.subplots(2, 3, figsize=(18, 10))
    data_vals = model.data_frame_convert()
    plot_residual(axes[0][0], axes[0][1], axes[0][2], data_vals[2]['y_pred'], data_vals[2]['y_real'], 'model: {}'.format(data_vals[0]), 'Scatter Plot: Y_Predit vs. Y')
    plot_residual(axes[1][0], axes[1][1], axes[1][2], data_vals[3]['y_pred_test'], data_vals[3]['y_real_test'], 'model: {}'.format(data_vals[1]), 'Residual Plot for Test Data')
plt.show()
```


### <a name="REL" ></a> Régression linéaire

```
Lin_Reg_Model = model_iterations(1000, X_train, y_train, Lin_Reg(fit_intercept=True), log_bool=False)
# R2 = 0.675 sur le test set

ypredtest = Lin_Reg_Model.y_pred_test
ytest = Lin_Reg_Model.y_test
metrics.mean_absolute_error(ypredtest, ytest)
```
> On observe une erreur médiane de 23.00 euros sur le test set. 

On remarque que les résidus sont à peu près normalement distribués (sans prendre en compte les valeurs résiduels élevées), ce qui indique que la fonction linéaire est appropriée.

```
Plots(Lin_Reg_Model)
```

<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/plot_Linreg.png" width="100%">



### <a name="XGB0" ></a> Xgboost- Régression linéaire

On tente maintenant un modèle d'arbres de décision boostés sur l'ensemble des données.

```
import xgboost as xgb
```
J'ai effectué une grid search auparavant avec les 4  paramètres (4^4: 256) qui a duré plus de 2h30 (mon prochain objectif sera de passer à un entraînement sur GPU (il y a RapidsAI qui a lancé ces librairies récemment), ce qui sera plus écologique et moins long). J'utilise ainsi les meilleurs paramètres obtenus:

```
mod = xgb.XGBRegressor(
    gamma=1,                 
    learning_rate=0.01,
    max_depth=3,
    n_estimators=10000,                                                                    
    random_state=34
)

Out []: 
train score 0.8906280063056778   #R2
test score 0.6984784216019209    #R2
train score (mean absolute error) 9.018808187769666
test score (mean absolute error) 14.690016550400438
train score (median absolute error) 6.665931701660156
test score (median absolute error) 10.828716278076172
```
### <a name="XGB1" ></a> Xgboost- Régression linéaire - Prix € [0,200]


Le but de l'exercice est de prédire le prix des locations des biens de moins de 200 €. 

```
xgb_Linreg = model_iterations(100, listings, Y, mod)

-Best R2 training 0.9230592356400042
-Best R2 testing 0.7082738465464786
-Avg R2 training 0.9161908380970193
-Avg R2 testing 0.6831100307904913
Training mean score (_mean absolute error) 7.908379433267141
Testing mean score (_mean absolute error) 15.052304869742533

Training best score (_mean absolute error) 7.509695185767145
Training best score (median absolute error) 5.50396728515625

Testing best score (_mean absolute error) 14.55569930134527
Testing best score (median absolute error) 10.3055419921875

std -ecarts moyens des perfs testing 0.21937221812857302
std des perfs training 0.14406245166564824

```
```
Plots(xgb_Linreg)
```
<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/plot_xgb_linreg.png" width="100%">

```
Plots(Lin_Reg_Model)
```
<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/plot_Linreg.png" width="100%">

On remarque que la  prédiction avec  les arbres de régressions Xgb boost crée une fonction beaucoup plus précise (moins de biais) et moins variables que la régression linéaire de base.

On remarque également que la généralisation est meilleure avec 20.49 € contre 23 € d'erreur moyenne absolue pour la régression multi classique. 

### <a name="XGB2" ></a> Xgboost- Régression linéaire - log(Prix) € [0,200]

xgb_result_log = model_iterations(20, listings, Y_log, mod, log_bool=True)

```
-Best R2 training 0.7277868062756596
-Best R2 testing 0.6769044040026038
-Avg R2 training 0.6995340595468243
-Avg R2 testing 0.6564364110363676
Training mean score (_mean absolute error) 14.226231455246326
Testing mean score (_mean absolute error) 15.166779256598721
Training best score (_mean absolute error) 13.872281664051414
Training best score (median absolute error) 9.368274688720703
Testing best score (_mean absolute error) 14.768355142125083
Testing best score (median absolute error) 9.890993118286115
std moyens des perfs testing 0.22604745566400672
std des perfs training 0.17883106317140526
```

### <a name="OPT" ></a> Imputation de 23% de données manquantes -> Effets sur le modèle

On garde toujours ces deux variables imputées par la moyenne pour observer seuls les effets sur l'imputation des reviews:

```
listings['cleaning_fee'] = listings.fillna(listings.mean())
listings['security_deposit'] = listings.fillna(listings.mean())
```
Les variables qui sont à inférer le plus précisément (en %):

```
isnull(listings)
Out[233]: 
                                     0
review_scores_accuracy       22.685078
review_scores_checkin        22.708285
review_scores_cleanliness    22.638663
review_scores_communication  22.673474
review_scores_location       22.685078
review_scores_rating         22.569042
review_scores_value          22.685078
```
On commence par la technique d'imputation par la moyenne. L'inconvénient est qu'elle ne prend pas en compte les corrélations entre les variables. Cependant, la méthode est facile et rapide.

### <a name="XGB3" ></a> Xgboost- Régression linéaire - Prix € [Dataset] +  23% of Reviews imputed from 'mean'

--- Imputation 'moyenne', log(Prix), Full data

Je garde les mêmes paramètres de Xgboost. Je prédis le log en base 3.6 du prix cible sur l'ensemble des données. 

```
model_iterations(100, listings_imputmean, Y_log, mod, log_bool=True)

Training mean score (_mean absolute error) 24.14273094512183
Testing mean score (_mean absolute error) 26.12519652301791
Training best score (_mean absolute error) 21.179671511565964
Training best score (median absolute error) 11.186231613159201
Testing best score (_mean absolute error) 23.337639623851338
Testing best score (median absolute error) 12.05378723144532
std -ecarts moyens des perfs testing 0.2100861726799187
std des perfs training 0.21093080646688295
```
Aïe Aïe Aïe, l'erreur médiane est plus élevée que lors de la même prédiction sans les données manquantes : 12.05 (baseline) contre 10.82.  

La méthode de la moyenne est un échec. Cependant je me recentre sur l'objectif des prix de location inférieurs à 200 € : 

### <a name="XGB4" ></a> Xgboost- Régression linéaire - log(Prix € [0,200]) +  23% of Reviews imputed from 'mean'


```
-Best R2 training 0.7274884933366395
-Best R2 testing 0.68643360881938
-Avg R2 training 0.7055151176660036
-Avg R2 testing 0.6594467249865594
Training mean score (_mean absolute error) 13.997939824560863
Testing mean score (_mean absolute error) 15.106062169818927
Training best score (_mean absolute error) 13.536121259835111
Training best score (median absolute error) 9.161304473876936
Testing best score (_mean absolute error) 14.484055507518447
Testing best score (median absolute error) 9.539064407348647

```

Voilà 3.5% d'amélioration par rapport à la baseline : 9.53 d'erreur médiane absolue contre 9.89 ! L'imputation par la moyenne a tendance à donner des estimations erronnées pour les prix élevés... 

Ceci s'explique par le choix d'imputer cleaning_fee et security_deposit par la moyenne également : cleaning_fee : 29.98 €, security_deposit : 500 €). Les variables des biens dont les frais de ménages et le déposit de sécurité se trouvent à bas coût (relatif) sont ainsi mieux imputés car ils correspondent à la plus grande partie des données. 

## <a name="OPT2" ></a> Imputation de 23% de données manquantes par Knn -> Effets sur le modèle

### <a name="XGB5" ></a> Xgboost- log(Prix € [0,200]) + 23% of Reviews imputed from 'k-Nearest Neighbors (kNN- 1)'

Qu'en est-il de l'imputation par les plus proches voisins ? 

L'algorithme permet de trouver par la mesure de la distance euclidienne, les trois bien en location les plus proches de celui d'intérêt (qui possède des valeurs manquantes). Ainsi, on s'inspire de biens très proches pour récupérer les valeurs des variables équivalentes.


On va maintenant utiliser le kNN (k nearest neighbors) sur respectivement **k = 1 3 et 5 voisins**.  Pour les valeurs 3 et 5, l'algorithme  effectue la moyenne des variables recherchées. Le risque de s'inspirer du plus proche voisin (k=1) est que celui-ci possède une variable extrême qui serait éloigné de la réalité et que l'on impute ainsi une valeur extrme. Le calcul sur 3 puis 5 voisins permet alors de mitiger ce risque. 

[k-Nearest Neighbors (kNN) Imputation](https://pypi.org/project/missingpy/)

```
pip install missingpy
```

```
from missingpy import KNNImputer

imputer = KNNImputer(n_neighbors=1, weights="uniform")

listings_rev_knn1_thresh = imputer.fit_transform(listings[listings.price<200])

listings_rev_knn1_thresh = pd.DataFrame(listings_rev_knn1_thresh, columns = listings.columns)

Y_imp = listings_imp.price
del(listings_imp['price'])
Y_knn1_thresh_log = np.log(Y_imp)

xgb_result_revknn3 = model_iterations(100, listings_rev_knn1_thresh, Y_knn1_thresh_log, mod, log_bool=True)

-Best R2 training 0.7251967986869021
-Best R2 testing 0.6857986463037695
-Avg R2 training 0.7067672282455653
-Avg R2 testing 0.6573788407962098
Training mean score (_mean absolute error) 14.024657182092668
Testing mean score (_mean absolute error) 15.103161246398525
Training best score (_mean absolute error) 13.551736232073193
Training best score (median absolute error) 9.098037719726562
Testing best score (_mean absolute error) 14.505256760760647
Testing best score (median absolute error) 9.860536575317395
```
Et voila, ce qui devait arriver est probablement : des variables extrêmes (k=1 plus proche voisin) se sont infiltrées à l'entraînement. On remarque cependant le prix médian de 9.86 comparable à la baseline (9.89).

### <a name="XGB6" ></a> Xgboost - log(Prix € [0,200]) + 23% of Reviews imputed from 'k-Nearest Neighbors (kNN-3)' 

```
-Best R2 training 0.777614798001613
-Best R2 testing 0.7177540879026125
-Avg R2 training 0.44401981747064057
-Avg R2 testing 0.4672465667234366
Training mean score (_mean absolute error) 19.470312127565386
Testing mean score (_mean absolute error) 21.076772482791466
Training best score (_mean absolute error) 17.60448572246527
Training best score (median absolute error) 9.698270797729492
Testing best score (_mean absolute error) 19.179957136149014
Testing best score (median absolute error) 10.379804611206058
```

### <a name="XGB7" ></a> Xgboost - log(Prix € [0,200]) + 23% of Reviews imputed from 'k-Nearest Neighbors (kNN-5)' 

```
-Best R2 training 0.7274868546422395
-Best R2 testing 0.6777300641759778
-Avg R2 training 0.706506253465841
-Avg R2 testing 0.6546066103841278
Training mean score (_mean absolute error) 14.000391540338258
Testing mean score (_mean absolute error) 15.181730796577375
Training best score (_mean absolute error) 13.660578161373206
Training best score (median absolute error) 9.086807250976534
Testing best score (_mean absolute error) 14.44077185657628
Testing best score (median absolute error) 9.898965835571271
```

### <a name="RES" ></a> Résultats - Comparaison des modèles


<img src="https://raw.githubusercontent.com/JeremieDec/home/master/pics/Bordeaux%20Post/models.png" width="100%">

Le log(Prix) (imputation par la moyenne) donne le meilleur score selon l'évaluation du prix médian, sur l'ensemble des données dont les prix sont inférieurs à 200 €.

### <a name="CC" ></a> Conclusion et ouverture 

Plusieurs pistes sont à exploiter afin de gagner en informations  : 

- La prédiction peut certainement être précisée en utilisant d'autres modèles, voir ensemble. Les biens au dessus de 200 € sont sous-représentés, il peuvent être sur-samplés dans l'objectif de prédire l'ensemble des données. Je suis limité par la puissance de calcul de ma machine (i7 7700HQ, 4 logiques, 8 threads). Un modèle prends à peu près 45 secondes pour être calculé. D'après l'étude effectuée des performances sur la comparaison GPUphys/CPU, l'entraînement sur gtx1080 est 8x plus rapide par rapport au i7 7700HQ, j'envisage d'y passer prochainement afin d'effectuer plus d'hypothèses [xgboost GPU performance on low-end GPU vs high-end CPU](https://medium.com/data-design/xgboost-gpu-performance-on-low-end-gpu-vs-high-end-cpu-a7bc5fcd425b). 

- La stratégie de validation que j'utiliserai dorénavant sera une validation-croisée et non un split seed fixe car celle-ci permet de comparer plus raisonnablement les différences de performances des modèles en assurant que le modèle est bien régularisé (c'est la méthode utilisée en compétition sur Kaggle par l'ensemble des meilleurs prédictions). Cela permet de créer des modèles plus robustes et comparables. 

- La variables "surface en m2" des biens est manquante à plus de 95%. Il y a possibilité de la retrouver en partie grâce aux descriptions et titres. 

- Le “feature engineering” qui signifie  ‘la combinaison de variables ‘  nous permet de créer de nouvelles variables.  Chaque domaine d’expertise possède des variables propres et leur combinaison peut s’avérer déterminante dans la prédiction. Egalement, on peut tenter l'utilisation de la bibliothèque [Featuretools](https://towardsdatascience.com/why-automated-feature-engineering-will-change-the-way-you-do-machine-learning-5c15bf188b96) qui permet d'automatiser la création de nouvelles variables à partir de l'instant ou deux variables dépendantes sont renseignées. 

-  LabelEncoder a été utilisé ici pour optimiser la vitesse d'entraînement; il se trouve qu'il est également adapté à l'utilisation d'Xgboost du fait de la structure en arbre de décision. D'autres méthodes d'imputation seront testées, en particulier dans cet article qui résume des [méthodes courantes](https://towardsdatascience.com/6-different-ways-to-compensate-for-missing-values-data-imputation-with-examples-6022d9ca0779?gi=b922bafa325b)

- Pour aller plus loin, peut obtenir des informations supplémentaires en exploitant les photos des biens (utilisation du deep learning). Par exemple, un ensemble de caractéristiques peuvent influencer le prix : la hauteur de plafond, le niveau de luminosité, le type de décoration tel que dans ce papier qui cite un modèle de classification du 'niveau de luxe' afin d'améliorer l'estimation des biens -Décembre 2019 : [Inside 50,000 living rooms: an assessment of global residential ornamentation using transfer learning]  (https://link.springer.com/article/10.1140/epjds/s13688-019-0182-z). Il cite aussi (hors-sujet) une étude similaire qui a utilisé un modèle apprenant sur une collection de photos d'intérieurs de restaurant (uploadé par Yelp) afin de prédire le succès de celui-ci.

- Les descriptions des biens, les commentaires sont également d'autres sources d'informations possibles.

- Autre solution, on peut ‘binner’ la variable Prix ( Le prix de 20 à 40 euros, prix de 40 à 60…)  afin De résoudre un problème de classification: Ce qui permet de rendre le modèle  plus robuste et d'éviter l’overfitting (Fonction de prédiction très proche des données d’entraînement:  entraîne une mauvaise généralisation) mais cela a un coût pour la performance. Dès l'instant où on stocke de l'information, on en perds et on régularise davantage.

## <a name="PAL" ></a> Pour aller plus loin

- Dans le but d'**Aider les hôtes à maximiser leur réussite sur la plateforme** : Dans une prochaine étude, Il serait intéressant de récupérer l'ensemble de ces commentaires pré-conversion ainsi que le classement (ranking) des biens sur Bordeaux (effectué par Airbnb). Ces deux variables additionnées à celles déjà acquises, ouvrent la possibilité de connaître d'avantage le poids de chaque variables dans l'optimisation de la visibilité des annonces.

## <a name="REF" ></a> Références

**Sources :** 

[EFI](https://www.efl.fr/actualites/immobilier/details.html?ref=r-be87d41a-753e-4bec-802a-abbf7927231c)

[Airdna](https://www.airdna.co/vacation-rental-data/app/fr/new-aquitaine/bordeaux/overview)

[Blog locservice](https://blog.locservice.fr/bordeaux-les-chiffres-cles-du-marche-locatif-prive-en-2013-1469.html) 

[Taille d'un logement T3](https://www.fonction-publique.gouv.fr/archives/home20020121/bases/logement_index.htm)

[Automated feature engineering ](https://towardsdatascience.com/why-automated-feature-engineering-will-change-the-way-you-do-machine-learning-5c15bf188b96)

[xgboost GPU performance on low-end GPU vs high-end CPU](https://medium.com/data-design/xgboost-gpu-performance-on-low-end-gpu-vs-high-end-cpu-a7bc5fcd425b).

[Comparaison de la distribution, Quartile-Quartile graph](https://www.youtube.com/watch?v=okjYjClSjOg)

[État des techniques d'imputations](https://medium.com/ibm-data-science-experience/missing-data-conundrum-exploration-and-imputation-techniques-9f40abe0fd87)



