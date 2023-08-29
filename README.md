# COVID-19 Analysis

## Goal

This project is meant to demonstrate the data manipulation skills necessary for an entry-level Data Analyst position, using Python and its libraries for data manipulation, analysis, and visualization, such as NumPy, Pandas, Matplotlib, Plotly and Cufflinks.

The subject of this project is the COVID-19 virus, and it will be analyzed for its evolution around the world between January 2020 and May 2023.

***Note:** 
The visualizations for this project were performed using the Cufflinks Python library, which allows for the creation of **interactive charts**. To view them, you need to download [Jupyter Notebook file](https://github.com/diogosmferreira/COVID-19/blob/main/COVID-19%20Exploratory%20Data%20Analysis%20and%20Visualizations.ipynb) and run the code, or download the [HTML version](https://github.com/diogosmferreira/COVID-19/blob/main/COVID-19%20Exploratory%20Data%20Analysis%20and%20Visualizations.html).*


## Summary

1. [Scope](#Introduction)
    1. [Introduction](#subparagraph)
    2. [Assignment](#subparagraph2)
    
2. [About the data](#paragraph1)
    1. [Source](#subparagraph1)
    2. [Structure](#subparagraph2)
    
3. [Data preparation](#paragraph2)
    1. [Importing libraries and set-up](#subparagraph1)
    2. [Importing dataset](#subparagraph2)
    3. [Viewing/Inspecting data](#subparagraph3)
    
4. [Data manipulation and visualizations](#paragraph3)
    1. [Overview of COVID-19 in the World](#subparagraph1)
    2. [Overview of COVID-19 on the Continents](#subparagraph2)
    3. [Overview of COVID-19 in G7 Countries](#subparagraph3)   
    4. [Correlation between demographic factors and COVID-19](#subparagraph3)

## 1. Scope
### Introduction

COVID-19 is an infectious disease caused by the severe acute respiratory syndrome coronavirus 2 (SARS-CoV-2). SARS-CoV-2 was first identified in humans in December 2019 in the city of Wuhan, China, leading to a global pandemic declared by the World Health Organization (WHO) on March 11, 2020. 

When infectious spores are inhaled or come into contact with the eyes, nose, or mouth, COVID-19 can spread. The risk is greatest when people are close to one another, but tiny airborne particles containing the virus can linger in the atmosphere and spread further, especially indoors. Additionally, the virus can be transmitted when someone touches their eyes, nose, or mouth after contacting infected surfaces or items.

Although the signs and symptoms of COVID-19 might vary, they frequently include fever, coughing, headaches, exhaustion, breathing problems, loss of smell, and loss of taste.

### Assignment

Analyze historical data from the COVID-19 virus and examine the effect it has had on different groups of populations over time.

## 2. About the data
### Source

The complete COVID-19 dataset is available in [CSV](https://covid.ourworldindata.org/data/owid-covid-data.csv), [XLSX](https://covid.ourworldindata.org/data/owid-covid-data.xlsx), and [JSON](https://covid.ourworldindata.org/data/owid-covid-data.json) formats, and is a collection of the COVID-19 data maintained by [Our World in Data](https://ourworldindata.org/coronavirus), a scientific online publication that focuses on large global problems such as poverty, disease, hunger, climate change, war, existential risks, and inequality.

The data you will find in this dataset and the data sources are:

 - **Confirmed cases and deaths:** this data is collected from the [World Health Organization Coronavirus Dashboard](https://covid19.who.int/data). The cases & deaths dataset is updated daily.
 - **Hospitalizations and intensive care unit (ICU) admissions:** this data is collected from official sources and collated by Our World in Data. The complete list of country-by-country sources is available [here](https://github.com/owid/covid-19-data/blob/master/public/data/hospitalizations/locations.csv).
 - **Testing for COVID-19:** this data is collected by the Our World in Data team from official reports. **On 23 June 2022, Our World in Data stopped adding new datapoints to COVID-19 testing dataset.** You can read more [here](https://github.com/owid/covid-19-data/discussions/2667).
 - **Vaccinations against COVID-19:** this data is collected by the Our World in Data team from official reports.
 - **Other variables:** this data is collected from a variety of sources (United Nations, World Bank, Global Burden of Disease, Blavatnik School of Government, etc.)

### Structure

This complete COVID-19 dataset includes all historical data maintained by Our World in Data about the pandemic to date.

For the purpose of this analysis, the XLXS format was used, which is organized in a tabular structure, and follow a format of 1 row per location and date.
The variables represent all of main data related to confirmed cases, deaths, hospitalizations, and testing, as well as other variables of potential interest.

```python
import pandas as pd
data_structure = pd.read_csv("owid-covid-codebook.csv")
data_structure.head(21)
```
<img width="920" alt="image" src="https://github.com/diogosmferreira/COVID-19/assets/129385224/5a929f4e-311d-434c-91ec-b61011bbeae5">

You can find the **full codebook**, [here](https://github.com/owid/covid-19-data/blob/master/public/data/owid-covid-codebook.csv), with a description and source for each variable in the dataset. 

## 3. Data preparation

### Importing libraries and set-up

```
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
from plotly import __version__
import plotly.graph_objs as go
import plotly.express as px
import chart_studio.plotly as py
from plotly.offline import download_plotlyjs, init_notebook_mode, plot, iplot
import cufflinks as cf
init_notebook_mode(connected=True)
cf.go_offline()
```

### Importing the dataset

```
df = pd.read_excel("owid-covid-data.xlsx")
```
### View/Inspecting data

**Checking the data types**

Checked the data type of each column to see if any changes were needed.

```
df.info()
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/98ccdf2a-090e-4944-867d-bea8af7b6b20)

It appears that the data type for the **'Date'** column is an **object** and should be changed to **datetime**.

```
df["date"] = pd.to_datetime(df.date)
```

```
df["date"].info()
```
<img width="319" alt="image" src="https://github.com/diogosmferreira/COVID-19/assets/129385224/7ec42460-08eb-4a43-bb06-1e92f338a46f">

**Checking for missing values**

In order to understand how the dataset is structured in terms of missing values, a bar chart was created with the percentage of missing values in each column relative to the total number of rows.

```
total = df.isnull().sum().sort_values(ascending = False)
percent = ((df.isnull().sum()/df.isnull().count())*100).sort_values(ascending = False)
dfmv = pd.concat([total, percent], axis=1, keys=['Total', 'Percentage of Missing Values [%]'])
dfmv = dfmv.reset_index()
```

```
layout1 = cf.Layout(
    height=800,
    width=1200,
    title ='Percentage of Missing Values per Column',
    bargap= 0.3)
dfmv.iplot(kind='bar',x='index',y='Percentage of Missing Values [%]',theme = 'white', layout=layout1, color = 'blue')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/bf2437b4-4c62-408d-9207-d19b58b2a196)

It is possible to verify that certain columns have a high percentage of missing values relative to the total number of rows; however, this situation does not indicate that the dataset is missing information. 

For example, demographic data, such as diabetes prevalence, cardiovascular death rate, and handwashing facilities, are only present in dataset lines referring to countries, and not when these lines refer to continents or country organizations. The same goes for columns corresponding to economic factors such as GDP per Capita. 

With regard to the columns associated with vaccination, there is a large percentage of missing values; however, it is consistent with the fact that these data are also only associated with lines referring to countries, as well as only starting to appear from the date the vaccines began to be administered.

## 4. Data manipulation and Visualization

### Overview of COVID-19 in the World

**Aproach:**

Initially, the dataset was filtered to create a new dataframe with only the world data in order to create the following charts:

 - Cumulative confirmed COVID-19 cases.
 - World daily increases in confirmed cases (7-day MA).
 - Cumulative confirmed COVID-19 deaths.
 - World daily increases in confirmed deaths over (7-day MA).
 - World daily increases (7-day MA) - Confirmed cases VS Confirmed deaths.
 - Daily case fatality rate (7-day MA).
 
In order to make the charts of the daily increases in confirmed cases and deaths have a smoother presentation, the 7-day moving average was calculated and used.

Later, another dataframe was created with the data associated with the countries and specifically on the date of May 24, 2023, in order to create the following Choropleth maps:

 - Cumulative confirmed COVID-19 cases, May 24, 2023
 - Cumulative confirmed COVID-19 deaths, May 24, 2023

```
df_world=df[df['location'] == 'World']
```
```
df_world.iplot(kind='line',x='date',y='total_cases', theme = 'white', 
               title = 'Chart 4.1 - Cumulative confirmed COVID-19 cases', color ='Blue', fill = True)
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/e262c8bc-2c8e-41f2-9399-3443c6a8a9d9)

**Chart analysis 4.1**: 

It appears that in May 2023, the number of confirmed cases of COVID-19 in the world will be around 766 million. Over the period under study, there are two moments (Q1 2022 and December 2022) of greater acceleration in the number of confirmed cases.

```
df_world['New cases (7-MA)'] = df_world['new_cases'].rolling(7).mean()

df_world.iplot(kind='line',x='date',y='New cases (7-MA)', theme = 'white', color = 'Blue',
               title = 'Chart 4.2 - World daily increases in confirmed cases (7-day MA)', fill = True)
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/5277d048-10c7-40d4-8daf-f3735cc0feee)

**Chart analysis 4.2**:

In line with what was identified in the analysis of the previous chart, there are two periods in which there was a significant increase in the number of confirmed daily cases: Q1 2022 and December 2022.

```
df_country1 = df[df['continent'].notnull()]
df_country2 = df_country1[(df_country1['date'] == '2023-05-24')]

data = dict(
        type = 'choropleth',
        colorscale = 'Blues',
        reversescale = False,
        locations = df_country2['location'],
        locationmode = "country names",
        z = df_country2['total_cases'],
        text = df_country2['location'],
        colorbar = {'title' : '# of Cases'},
      ) 

layout = dict(title = 'Chart 4.3 - Cumulative confirmed COVID-19 cases, May 24, 2023',
                geo = dict(showframe = False,projection = {'type':'natural earth'})
             )

choromap = go.Figure(data = [data],layout = layout)
iplot(choromap,validate=False)
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/c0197f3d-10d7-4333-a921-5ae3df53c631)

**Chart analysis 4.3:** 

In this choropleth map, it is possible to gain a global understanding of the total number of confirmed cases by country worldwide. The United States is the country with the highest number of confirmed cases of COVID-19, around 103 million, followed by China with approximately 99 million confirmed cases. India is in third place with approximately 45 million confirmed cases.

```
df_world.iplot(kind='line',x='date',y='total_deaths', theme = 'white', color = 'Blue',
               title = 'Chart 4.4 - Cumulative confirmed COVID-19 deaths', fill = True)
````

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/7d5b80bc-44d8-4dab-ab09-dd1e7d8e4414)

**Chart analysis 4.4:** 

Since the appearance of the COVID-19 virus, there has only been a significant decrease in the number of confirmed deaths since March 2022.

```
df_world['New deaths (7-MA)'] = df_world['new_deaths'].rolling(7).mean()

df_world.iplot(kind='line',x='date',y='New deaths (7-MA)', theme = 'white', color = 'Blue', 
               title = 'Chart 4.5 - World daily increases in confirmed deaths (7-day MA)', fill = True)
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/d13187a2-f8bf-41fa-8d25-5c281cc0db43)

**Chart analysis 4.5:** 

The World Health Organization classified the COVID-19 virus as a pandemic on March 11, 2020. This date corresponds with the beginning of a large increase in the daily number of confirmed deaths, reaching its peak in mid-April 2020. 

In the last quarter of 2020, there was a new large increase in the daily number of confirmed deaths, reaching the daily historical maximum in January 2021. 

From January 2021, despite a negative trend, the number of confirmed daily deaths remained very high, and the situation only became more controlled in the second quarter of 2022. 

In January 2023, there was again a very significant increase in the number of deaths, a consequence of the high number of confirmed cases identified in December 2022.

```
data = dict(
        type = 'choropleth',
        colorscale = 'Blues',
        reversescale = False,
        locations = df_country2['location'],
        locationmode = "country names",
        z = df_country2['total_deaths'],
        text = df_country2['location'],
        colorbar = {'title' : '# of Deaths'},
      ) 

layout = dict(title = 'Chart 4.6 - Cumulative confirmed COVID-19 deaths, May 24, 2023',
                geo = dict(showframe = False,projection = {'type':'natural earth'})
             )

choromap = go.Figure(data = [data],layout = layout)
iplot(choromap,validate=False)
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/e49d97b0-fade-40d0-8698-6ceeae33bc8f)

**Chart analysis 4.6:** 

The United States is the country with the highest number of confirmed COVID-19 deaths, around 1.12 million, followed by Brazil with approximately 702 thousand confirmed deaths. India is in third place with approximately 531 thousand confirmed deaths.

```
annotations = dict(x = "2020-03-11",y = 0, text= 'WHO declares COVID-19 a global pandemic',showarrow=True, arrowsize=0.3, arrowhead = 1, 
                   arrowcolor ='grey', opacity = 1, ay = -160, size = 10, color = 'grey')


df_world.iplot(kind='line',x='date',y='New cases (7-MA)', secondary_y= 'New deaths (7-MA)', 
               yTitle = 'Confirmed cases', secondary_y_title = 'Confirmed deaths', theme = 'white',
               title = 'Chart 4.7 - World daily increases (7-day MA) - Confirmed cases VS Confirmed deaths',
               annotations=annotations, fill=True)
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/7484f756-44d3-415d-a072-f83fe687b3ec)

**Chart analysis 4.7:** 

Since the beginning of the pandemic, both the number of deaths and the number of confirmed cases of COVID-19 have skyrocketed. Over time, there has been a tendency for mortality to decrease for each confirmed case. To better understand this trend, the following chart shows the daily case fatality rate over time.

```
df_world['Daily case fatality rate'] = df_world['New deaths (7-MA)']/df_world['New cases (7-MA)']

annotations1 = dict(x = "2020-03-11",y = 0, text= 'Beginning of the COVID-19 pandemic',showarrow=True, arrowsize=0.3, arrowhead = 1, 
                   arrowcolor ='grey', opacity = 1, ay = -225, size = 10, color = 'grey'), dict(x = "2023-05-05",y = 0, text= 'End of the COVID-19 pandemic' , showarrow=True, arrowsize=0.3, arrowhead = 1, 
                   arrowcolor ='grey', opacity = 1, ay = -225, size = 10, color = 'grey')

df_world.iplot(kind='line',x='date',y='Daily case fatality rate', theme = 'white',
               title = 'Chart 4.8 - Daily case fatality rate [%]', color = 'blue',
               annotations=annotations1, fill = True)
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/e54fdce7-84b9-42f7-bd0f-cc3af280f66f)

**Chart analysis 4.8:** 

The daily case fatality rate (7-day MA) is the ratio between confirmed deaths (7-day MA) and confirmed cases (7-day MA). 

In this chart, it is possible to gain an understanding of how the lethality of the COVID-19 virus has evolved over time. The peak in the lethality of the virus occurred in February 2020, before the pandemic was declared by the World Health Organization, with a second peak in April 2020. Since then, a downward trend in the lethality of the virus has been observed.

## Overview of COVID-19 on the Continents

**Aproach:**

Initially, the dataset was filtered to create a new dataframe with only the Continents data in order to create the following charts:

 - Cumulative confirmed COVID-19 cases.
 - Cumulative confirmed COVID-19 deaths.
 
Then, two other dataframes were created: one with the total sum of confirmed cases grouped by continent, and another with the total sum of confirmed deaths grouped by continent. With these two dataframes, the following charts were created:

 - Share of COVID-19 cases.
 - Share of COVID-19 deaths.

Finally, the 7-day moving average of new cases and new deaths were calculated, and three new columns were created through calculations: 'Cumulative case fatality rate of COVID-19', 'Daily case fatality rate of COVID-19', and the 'Population fatality rate of COVID-19'. With the data from these new columns, the following charts were generated:

 - Cumulative case fatality rate of COVID-19 [%].
 - Daily case fatality rate of COVID-19 (7-day MA) [%].
 - Population fatality rate of COVID-19 [%].

```
df_continent1 = df[df['continent'].isnull()]
df_continent1 = df_continent1[(df_continent1['location'] == 'Africa') | (df_continent1['location'] == 'Asia') | (df_continent1['location'] == 'Europe') | (df_continent1['location'] == 'Oceania') | (df_continent1['location'] == 'North America') | (df_continent1['location'] == 'South America')]
```

```
df_continent1.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_cases', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.9: Cumulative confirmed COVID-19 cases')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/cbb02e6c-0fa1-4053-9046-c7f442f65ffb)

**Chart analysis 4.9:**

Asia is the continent with the highest number of cases, approximately 297 million confirmed cases. Europe follows with approximately 249 million, and North America is third with 124 million confirmed cases of COVID-19.

```
df_continent2 = df_continent1.groupby('location')['new_cases'].sum()
df_continent2 = df_continent2.reset_index()
df_continent2 = df_continent2.rename(columns={'location': 'Continent', 'new_cases': 'Total Cases'})
df_continent2.iplot(kind="pie",
                    labels="Continent",
                    values="Total Cases",
                    textinfo='percent+label', hole=0,theme = 'white', 
                    title = 'Chart 4.11: Share of COVID-19 cases',
                    pull=[0,0.07,0,0,0,0])
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/152ae12c-1ab2-419e-b491-916a4afbdd1a)

**Chart analysis 4.10:**

The number of cases in the Asian continent represents 38.8% of the total confirmed cases, followed by Europe with 32.5% and in third place North America with 16.2% of the total confirmed cases of COVID-19.

```
df_continent1.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_deaths', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.12: Cumulative confirmed COVID-19 deaths')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/91c34caf-6f90-4299-bcc5-56be2caa1706)

**Chart analysis 4.11:**

Europe is the continent with the highest number of deaths, approximately 2.06 million confirmed cdeaths. Asia follows with approximately 1.63 million, and North America is third with 1.60 million confirmed deaths of COVID-19. 

It should be noted that, despite Asia having the most confirmed cases, the virus was more lethal in Europe. North America, which has less than half the number of confirmed cases as Asia, has a very similar number of deaths, indicating a very high case fatality rate.

```
df_continent3 = df_continent1.groupby('location')['new_deaths'].sum()
df_continent3 = df_continent3.reset_index()
df_continent3 = df_continent3.rename(columns={'location': 'Continent', 'new_deaths': 'Total Deaths'})
df_continent3.iplot(kind="pie",
                    labels="Continent",
                    values="Total Deaths",
                    textinfo='percent+label', hole=0,theme = 'white', title = 'Chart 4.13: Share of COVID-19 deaths',
                    pull=[0,0,0.07,0,0,0])
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/61893170-f2bb-4d6d-ad25-0728e9374b64)

**Chart analysis 4.12**:

The number of deaths in the European continent represents 29.7% of the total confirmed cases, followed by Asia with 23.5% and in third place North America with 23.1% of the total confirmed deaths of COVID-19.

```
df_continent1['Cumulative case fatality rate'] = ((df_continent1['total_deaths']/df_continent1['total_cases'])*100)

df_continent1.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Cumulative case fatality rate', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.14: Cumulative case fatality rate of COVID-19 [%]')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/af499038-9b60-458b-9470-40982ec343d3)

**Chart analysis 4.13:** 

Cumulative case fatality rate is the ratio between cumulative deaths and confirmed cases.

It appears that the cumulative case fatality rate of COVID-19 reached its maximum value in the first half of 2020 in all continents, except for Oceania, which was in the second half of 2020. The trend after the peak in each continent has been decreasing.

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_continent1['New deaths (7-MA)'] = df_continent1['new_deaths'].rolling(7).mean()
df_continent1['New cases (7-MA)'] = df_continent1['new_cases'].rolling(7).mean()

df_continent1['Daily case fatality rate'] = df_continent1['New deaths (7-MA)']/df_continent1['New cases (7-MA)']

df_continent1.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Daily case fatality rate', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.15: Daily case fatality rate of COVID-19 (7-day MA) [%]')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/55c2065b-749e-4f07-90a1-f6acd0bab089)

**Chart analysis 4.14:** 

The daily case fatality rate (7-day MA) is the ratio between confirmed deaths (7-day MA) and confirmed cases (7-day MA).

There has been a negative trend in the ratio over time on all continents. Generally, the highest ratios occurred in 2020, except in Oceania, which occurred in 2021. In South America, there was a new peak higher than that seen in 2021 in 2022.

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_continent1['Death Rate_Population'] = ((df_continent1['total_deaths']/df_continent1['population'])*100)

df_continent1.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Death Rate_Population', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.16: Population fatality rate of COVID-19 [%]')
```
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/ba23bc37-0c56-42f9-89ee-63e4fb0d7f10)

**Chart analysis 4.15:**

The Population fatality rate is the ratio between confirmed deaths and the total population of the continent.

South America has the highest percentage of its population killed by COVID-19, approximately 0.309%, followed by Europe with 0.277%, and North America with 0.266%.

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).

## Overview of COVID-19 in G7 Countries

The **Group of Seven (G7)** is an intergovernmental political forum consisting of **Canada**, **France**, **Germany**, **Italy**, **Japan**, the **United Kingdom** and the **United States**. It is organized around shared values of pluralism, liberal democracy, and representative government.

In this chapter, the development of the COVID-19 virus in this group of countries was analyzed, specifically in terms of confirmed cases and deaths (both raw numbers and relative to the population), as well as the number of patients in hospitals and in intensive care relative to the population, case fatality rate and share of people who completed the initial COVID-19 vaccination protocol in this countries.

**Aproach:**

Initially, the dataset was filtered to create a new **dataframe with only the G7 Countries data** in order to create the following charts:

 - Cumulative confirmed COVID-19 cases in G7 Countries.
 - Cumulative confirmed COVID-19 cases per million people in G7 Countries.
 - Cumulative confirmed COVID-19 deaths in G7 Countries.
 - Cumulative confirmed COVID-19 deaths per million people in G7 Countries.
 - Number of COVID-19 patients in hospital per million people in G7 Countries.
 - Number of COVID-19 patients in intensive care (ICU) per million people in G7 Countries.

Then, new columns were created through calculations, namely **New cases per million (7-day MA)**, **New deaths per million (7-day MA)**, **Cumulative case fatality rate** and **Daily case fatality rate (7-day MA)**. With this new data, the following charts were created:

 - Daily new confirmed COVID-19 cases per million people in G7 Countries (7-day MA).
 - Daily new confirmed COVID-19 deaths per million people in G7 Countries (7-day MA).
 - Cumulative case fatality rate of COVID-19 in G7 Countries.
 - Daily case fatality rate of COVID-19 in G7 Countries.
 - Share of people who completed the initial COVID-19 vaccination protocol in G7 Countries [%].

```
df_country_G7 = df_country1[(df_country1['location'] == 'Germany') | (df_country1['location'] == 'United States') | 
                          (df_country1['location'] == 'Canada') | (df_country1['location'] == 'France') | 
                          (df_country1['location'] == 'Italy') | (df_country1['location'] == 'United Kingdom') |
                          (df_country1['location'] == 'Japan')]
  ```
  ```
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_cases', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.17: Cumulative confirmed COVID-19 cases in G7 Countries')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/f6c63163-bd06-4cc5-8999-745d30b8b18d)

**Chart analysis 4.16:** 

The United States is the country belonging to the G7 with the highest number of confirmed cases accumulated, with approximately 103.43 million. France is second with approximately 39 million cases, followed by Germany with approximately 38.4 million cases. Japan has 33.8 million, Italy has 25.84 million, the United Kingdom has 25.61 million, and Canada has 4.66 million confirmed cases. 

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_cases_per_million', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.18: Cumulative confirmed COVID-19 cases per million people in G7 Countries')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/1496d48f-8660-40ea-bd74-ba96b70ca4e5)

**Chart analysis 4.17:** 

Cumulative confirmed COVID-19 cases per million people allows for a comparison of the metric relative to the country's population, in order to have a better perception of the impact of COVID-19 on that population. 

France is the country with the highest number of cases per million people, with about 603.62 thousand, followed by Germany with 460.87 thousand cases, and Italy with 437.73 thousand cases. The United Kingdom is in fourth position with 364.56 thousand cases, and the United States, despite having the highest number of cases, is in fifth position when taking into account the country's population, with about 305.76 thousand cases per million people. Japan is sixth with 272.71 thousand cases, and Canada is last with 121.42 thousand cases per million people.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_deaths', 
                    theme = 'white',
                    colorscale ='plotly',
                    title = 'Chart 4.19: Cumulative confirmed COVID-19 deaths in G7 Countries')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/76c47898-01c9-4e6a-ad7e-5a3085f4f768)
                   
**Chart analysis 4.18:** 

The United States is clearly the country with the highest cumulative number of confirmed COVID-19 deaths, with 1.27 million confirmed deaths. It is then possible to group two sets of countries, one formed by the United Kingdom (225.85 thousand), Italy (190.24 thousand), Germany (174 thousand) and France (163.43 thousand), and another formed by Japan (74.69 thousand) and Canada (52.30 thousand) with an order of magnitude in terms of cumulative number of deaths confirmed by COVID-19 relatively similar.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='total_deaths_per_million', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.20: Cumulative confirmed COVID-19 deaths per million people in G7 Countries')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/7db009d7-cf58-4cee-b0d2-e4edea86778d)

**Chart analysis 4.19:**

When taking into account the population of each country, the United Kingdom (3345.51) appears in first place in the accumulated number of confirmed deaths from COVID-19, with the United States (3331.91) very close in second place. Italy (3222.39) is third, followed by France (2528.94) in fourth, Germany (2087.47) in fifth, Canada (1360.08) in sixth, and Japan (602.60) in seventh.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

```
df_country_G7['new_cases_per_million_MA7'] = df_country_G7['new_cases_per_million'].rolling(7).mean()

df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='new_cases_per_million_MA7', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.21: Daily new confirmed COVID-19 cases per million people in G7 Countries (7-day MA)')
```

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/fc5ccdd4-0864-4fa7-ba99-79f351863bf5)

**Chart analysis 4.20:**

France saw the highest peak of daily confirmed cases of COVID-19 per million people in the first quarter of 2022. The other countries of the G7, except Japan, also recorded their peaks in the first quarter of 2022. Japan's peak of daily confirmed cases of COVID-19 per million people was recorded in the third quarter of 2022.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

```                    
df_country_G7['new_deaths_per_million_MA7'] = df_country_G7['new_deaths_per_million'].rolling(7).mean()

df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='new_deaths_per_million_MA7', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.22: Daily new confirmed COVID-19 deaths per million people in G7 Countries (7-day MA)')
``` 

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/0c17a0fd-04e8-40a9-97d5-fbc14c56ff36)

**Chart analysis 4.21:**

In this chart, it is possible to identify three periods, across the various countries, where a higher number of confirmed deaths from COVID-19 per million people were recorded. The first was in the second quarter of 2020, shortly after the World Health Organization declared COVID-19 a pandemic, the second was in late 2020 - early 2021, and the third was in late 2021 - early 2022.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

``` 
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='hosp_patients_per_million', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.23: Number of COVID-19 patients in hospital per million people in G7 Countries')
``` 
              
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/dd3a8f7a-a556-4150-833d-f9ab52480757)

**Chart analysis 4.22:**

Similar to Chart 4.22, there are three periods across the various countries with a higher number of hospitalized COVID-19 patients per million people. The first was in the second quarter of 2020, shortly after the World Health Organization declared COVID-19 a pandemic, the second was in late 2020 - early 2021, and the third was in late 2021 - early 2022.

Germany and Japan are not shown in the graph because the dataset does not contain data on the number of hospitalized patients with COVID-19 per million people in these countries.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

``` 
df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='icu_patients_per_million', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.24: Number of COVID-19 patients in intensive care (ICU) per million people in G7 Countries')
``` 

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/6e73a9f4-0bbb-4dfd-afd6-2cc1a096bfec)

**Chart analysis 4.23:**

Over time, there is a downward trend across all countries.

**Canada/France:** There are three peaks in the number of patients with COVID-19 admitted to intensive care per million people, the first in April 2020, the second in April 2021, and the third in January 2022.

**Germany:** There are three peaks in the number of patients with COVID-19 admitted to intensive care, the first in April 2020, the second in January 2021, and the third in December 2022.

**Italy:** There are four peaks in the number of patients with COVID-19 admitted to intensive care, the first in November 2020, the second in April 2021, the third in April 2022, and the fourth in January 2022.

**United Kingdom:** There are two peaks in the number of patients with COVID-19 admitted to intensive care, the first in April 2021 and the second in January 2021.

**United States:** There are three peaks in the number of patients with COVID-19 admitted to intensive care, the first in January 2021, the second in September 2021, and the third in January 2022.

**Japan:** The dataset does not have data on the number of COVID-19 patients admitted to intensive care in Japan.

**Note:** For a better visualization, filter by country, clicking on the label (only available in Jupyter Notebook or HTML version).

``` 
df_country_G7['Cumulative case fatality rate'] = ((df_country_G7['total_deaths']/df_country_G7['total_cases'])*100)

df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Cumulative case fatality rate', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.25: Cumulative case fatality rate of COVID-19 in G7 Countries')
```   
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/c5321ade-e731-450d-9930-5083c32f2554)

 **Chart analysis 4.24:**

Cumulative case fatality rate is the ratio between cumulative deaths and confirmed cases.

It appears that the cumulative case fatality rate of COVID-19 reached its maximum value in the first half of 2020 in all G7 Countries. The trend after the peak in each country has been decreasing.

In the case of the United Kingdom, on February 3, 2020, the cumulative case fatality rate reached 100% as the first confirmed case of COVID-19 resulted in the first death in the country, before any more cases had been identified.

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).
 
``` 
df_country_G7['Daily case fatality rate'] = ((df_country_G7['new_deaths']/df_country_G7['new_cases'])*100)
df_country_G7['Daily case fatality rate_MA7'] = df_country_G7['Daily case fatality rate'].rolling(7).mean()

df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Daily case fatality rate_MA7', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.26: Daily case fatality rate of COVID-19 in G7 Countries (7-day MA)')
```                   
![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/0780ed98-8389-43fc-8c42-5b8925354678)

**Chart analysis 4.25:**

The daily case fatality rate (7-day MA) is the ratio between confirmed deaths (7-day MA) and confirmed cases (7-day MA).

There has been a negative trend in the ratio over time on all G7 Countries. Generally, the highest ratios for each country occurred in the first half of 2020.

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).

``` 
df_country_G7['Share people fully vaccinated'] = ((df_country_G7['people_fully_vaccinated']/df_country_G7['population'])*100)

df_country_G7.iplot(kind = 'line', 
                    mode ='lines', 
                    categories = 'location', 
                    x='date',y='Share people fully vaccinated', 
                    theme = 'white', 
                    colorscale ='plotly',
                    title = 'Chart 4.27: Share of people who completed the initial COVID-19 vaccination protocol in G7 Countries [%]')
``` 

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/0770325b-9578-441a-9b6d-83a5e0c29076)

**Chart analysis 4.26:**

The vaccination process against the COVID-19 virus began at the end of 2020/beginning of 2021. 

Within the G7 countries, the United States and the United Kingdom were the countries that managed, in the first six months, to complete the initial vaccination protocol to the highest percentage of the population, reaching 30 June 2021, 48.43% for the United States and 48.95% for the United Kingdom. 

In the subsequent six months, the vaccination process in other countries accelerated a lot, and at the end of 2021, Japan was the country with the highest percentage of the population fully vaccinated (80.44%), in second place Canada (76.55%), in third Italy (75.74%), fourth France (73.21%), and fifth Germany (70.95%). 

The United Kingdom and the United States, which in the first six months of vaccination had been the countries with the highest percentage of their population vaccinated, were at the end of 2021 at the tail of the G7, with the United Kingdom at 70.23% and the United States at 62.18%.

Currently, the percentage of the population that has completed the initial vaccination protocol against COVID-19 is as follows:

Japan (83.40%), Canada (82.59%), Italy (81.24%), France (78.43%), Germany (76.24%), United Kingdom (75.19%), United States (68.17%).

**Note:** For a better visualization, filter by continent, clicking on the label (only available in Jupyter Notebook or HTML version).

### Correlation between demographic factors and COVID-19

The objective of this section was to create a correlation matrix in order to verify the possible existence of a correlation between the total number of cases and deaths confirmed by COVID-19 per million people and various demographic factors in each country, such as population density, life expectancy, the Human Development Index, GDP per capita, and others.

**Approach:**

In order to create the correlation matrix, a pivot table was created, grouped by country, and containing the various variables to be compared. The matrix was then generated using the Python library Cufflinks.

``` 
df_country_pvt = df_country1.pivot_table(index = 'location', values = ['total_cases_per_million','total_deaths_per_million','life_expectancy','population_density','diabetes_prevalence','cardiovasc_death_rate','median_age','aged_70_older','gdp_per_capita','human_development_index','handwashing_facilities'],aggfunc=max)
df_country_pvt.corr().iplot(kind='heatmap',colorscale="Blues",title="Chart 4.28 - Correlation between demographic factors and COVID-19", theme='white')
``` 

![image](https://github.com/diogosmferreira/COVID-19/assets/129385224/ac3a1065-82e6-45ab-a534-4e8bb483d0b3)

**Chart analysis 4.27:**

A correlation is considered positive when one variable tends to increase as the other increases, and negative when one variable tends to decrease as the other increases.

Total cases per million people are positively correlated with:

 - Humam development index of the country (75.8%).
 - Median age (75%).
 - Age over 70 years (72.9%).
 - Life expectancy of the country (69%).
 - GDP per capita (64.5%).
 - Handwashing facilities in the country (58%).
 - Population density (18%).
 - Diabetes prevalence (12%).

Total deaths per million people are positively correlated with:

 - Median age (68.16%).
 - Age over 70 years (66.9%).
 - Humam development index of the country (59.7%).
 - Handwashing facilities in the country (57.8%).
 - Life expectancy of the country (51.05%).
 - GDP per capita (28.4%).

**Note:** Correlations below 10% were not considered in the above listing.
