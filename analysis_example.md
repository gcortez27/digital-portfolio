---
jupytext:
  formats: ipynb,md:myst
  text_representation:
    extension: .md
    format_name: myst
    format_version: 0.13
    jupytext_version: 1.16.3
kernelspec:
  display_name: Python 3 (ipykernel)
  language: python
  name: python3
---

+++ {"editable": true, "slideshow": {"slide_type": ""}}

# Titanic Dataset: Exploratory Data Analysis

+++

```{figure} img/titanic.jpg
---
height: 500px
name: titanic-image
---
Titanic Image downloaded from [wikimedia](https://commons.wikimedia.org/wiki/File:RMS_Titanic_4.jpg).
```

+++ {"editable": true, "slideshow": {"slide_type": ""}}

This is an EDA of the famous Titanic {numref}`titanic-image` dataset from Kaggle. Through EDA, I will  try to achive the following goals:

1. Investigate the demographic characteristics of the Titanic's passengers, such as their age, gender, and social class, to gain a better understanding of the composition of the passenger list.

2. Explore the origins of the Titanic's passengers, including their countries of origin, to gain a better understanding of the cultural diversity of the passenger list.

3. Determine which passengers traveled with their families and which ones traveled alone to gain insights into the social dynamics of the passengers.

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
```

```{code-cell} ipython3
# load the dataset.
titanic_df = pd.read_csv('data/titanic_train.csv')
# preview your dataset
titanic_df.head() 
```

```{code-cell} ipython3
# information about the dataset. 
titanic_df.info()
```

## Demographic characteristics of the Titanic's passengers

Let's look at some demographical information about the passengers.

Let's take a look at socio-economic class of a person (Pclass column)

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
sns.catplot(x='Pclass',data=titanic_df,hue='Sex',kind='count')
```

Let's explore passangers' gender:

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
# number of male and female passengers
sns.catplot(x='Sex',data=titanic_df,kind='count')
```

Figure summary: there were considerably more male passengers onboard. 

__*Note: It is important to acknowledge that gender is not binary and that there are many individuals who do not identify as strictly male or female. However, the dataset available for the Titanic passengers only includes information on male and female passengers. While this limitation may not fully capture the diversity of gender identities among the passengers, it is still valuable in providing insights into the demographics and social dynamics of the Titanic's passengers.*__

+++

Let's now figure out how many kids were on the Titanic:

```{code-cell} ipython3
# function to create category child
def child(df):
    sex=df['Sex']
    age=df['Age']
    
    return 'child' if age < 16 else sex

# create a new column "Individuals"
titanic_df['Individuals'] = titanic_df.apply(child,axis=1)
# see counts
print(titanic_df['Individuals'].value_counts())
```

Now, let's check teh distribution of men, women and children belonging to different socio-economic classes.

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
sns.catplot(x='Pclass',data=titanic_df,hue='Individuals',kind='count')
```

Figure summary: There are way more male passengers than woman or childeren in the 3rd class than in any other class.

Let's check out how the age distribution varies for different classes

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
age_class = sns.FacetGrid(titanic_df,hue='Pclass',aspect=2)
age_class.map(sns.kdeplot,'Age',fill=True)
oldest = titanic_df['Age'].max()
age_class.set(xlim=(0,oldest))
age_class.add_legend()
```

Figure summary: first class passengers were older that passagenrs of other class

+++

## Explore the origins of the Titanic's passengers

+++ {"editable": true, "slideshow": {"slide_type": ""}}

The __Embarked__ columns are abbreviation of the following port of embarkation:

- C = Cherbourg
- Q = Queenstown
- S = Southampton

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
sns.catplot(x='Embarked',data=titanic_df,hue='Pclass',kind='count')
```

Figure summary: Most of the 3rd class passengers embarked from Southampton

+++

## Determine which passengers traveled with their families

+++

we need 2 columns:

- __SibsSP__ : number of siblings or spouses that a passenger had aboard.
- __Parch__ : number of parents or children someone had on the ship.

We can combine teh column to determine whetehr the passanger was alone or with someone and record in in __ModeofTravel__ column:

```{code-cell} ipython3
#Adding the number of family a passenger had onboard
titanic_df['ModeofTravel'] = titanic_df.SibSp + titanic_df.Parch
titanic_df['ModeofTravel'] = titanic_df['ModeofTravel'].apply(lambda x: 'With Someone' if x > 0 else 'Alone')
```

```{code-cell} ipython3
---
editable: true
slideshow:
  slide_type: ''
tags: [hide-input]
---
sns.catplot(x='ModeofTravel',data=titanic_df,kind='count')
```

Figure summary: The majority of people on the ship travled with someone.
