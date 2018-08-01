## Project - Good Times to Go!!! (G2G)

**Objective**: The project, is developed in Python, focussed at recommending activities to people based on the current weather conditions for the given city,state.

**Xfactor**: None of the applications that are currently present in the market considers the current weather condition in recommending the type of places or activities. Our project factors in the current weather and the time of the day to recommend the places and also provides the address and the ratings that are available on google.

### PYTHON Modules used:
 a.openweathermapy.core b.pandas c.matplotlib d.numpy e.datetime f.json g.requests

### APIs used: 
* Openweather wrapper and the API key.
* Google APIs and the API key: 
  * https://maps.googleapis.com/maps/api/geocode/json
  * https://maps.googleapis.com/maps/api/place/nearbysearch/json? 
  * https://maps.googleapis.com/maps/api/place/findplacefromtext/json?
* Additional APIs that could be used: 
  * https://maps.googleapis.com/maps/api/place/details/json? 
  * https://maps.googleapis.com/maps/api/distancematrix/json?

### Source referred to get weather conditions and activity types are as below: 
* Weather Conditions: https://openweathermap.org/weather-conditions 
* Activity Types: https://developers.google.com/places/web-service/supported_types

**Note**: Weather - Thunderstorm, Temperature, Windspeed, Humidity, Rain, Drizzle, Snow, Mist, Clear Sky are different factors to be considered under weather. The extreme weather like thunderstorms are considered as approriate for indoor activities whereas all other factors (excluding Temp, Windspped, Humidity) are evaluated based on further classification present in https://openweathermap.org/weather-conditions. For temperature & humidity, we took human sample of our family & fellow colleagues' experience to decide the appropriate range to go outdoor or remain indoor; For windspeed, we referred https://en.wikipedia.org/wiki/Beaufort_scale.

### Limitations: 
* The places recommended are limited to what is present on the google data.
* The weather evaluation of is based on the human experience except for the extreme weather conditions & windspeed. 
* The project evaluates the current weather conditions and not the forecast.
* It does not factor in a person's past choices as the focus is on the current weather. 
* It covers a hard coded radius not more than 50,000 meters. 
* The project does not factor in age or personal choice and hence generic indoor & outdoor places are given. 
* It does not calculate the distance to be covered or time to be taken to reach to the destination. 
* It gives the open places and not the opening and closing time. 
* Currently, the visualization is limited to the ratings comparison of various destinations of one kind.

### Future road to development: 
* It would take the current location by default. 
* The project would ask for the age and limit or recommend the kind of places. 
* It would factor in the personal choices when type of places are chosen. 
* It would give the distance to be covered and time to be taken to reach the destination, if a particular place is chosen. 
* It would give the opening & closing time. 
* The visualization for rating or distance or time taken to reach the destination would be displayed on the user's demand.

Code & output sample import requests import json import datetime

```python
###Modules used
#openweathermapy.core
#pandas
#matplotlib
#numpy
#datetime
#json
#requests

###API's used (API keys used for google as well as openweather)
# "https://maps.googleapis.com/maps/api/geocode/json"
# "https://maps.googleapis.com/maps/api/place/nearbysearch/json?"
# "https://maps.googleapis.com/maps/api/place/findplacefromtext/json?"
#Instead of openweathermapy API, wrapper is 

#Additional APIs that could be used
#https://maps.googleapis.com/maps/api/place/details/json?
#https://maps.googleapis.com/maps/api/distancematrix/json?
```


```python
import requests
import json
import datetime

import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
import openweathermapy.core as owm

from config import api_key
from config import gkey
```


```python
#Read all the Weather csv files 
main = "Resources/Indoor_Outdoor_Weather - main.csv"
main_df = pd.read_csv(main, encoding="ISO-8859-1")
Atmosphere = "Resources/Indoor_Outdoor_Weather - Atmosphere.csv"
atmosphere_df = pd.read_csv(Atmosphere, encoding="ISO-8859-1")
Drizzle= "Resources/Indoor_Outdoor_Weather - Drizzle.csv"
drizzle_df = pd.read_csv(Drizzle, encoding="ISO-8859-1")
Rain = "Resources/Indoor_Outdoor_Weather - Rain.csv"
rain_df = pd.read_csv(Rain, encoding="ISO-8859-1")
Snow ="Resources/Indoor_Outdoor_Weather - Snow.csv"
snow_df = pd.read_csv(Snow, encoding="ISO-8859-1")

#https://developers.google.com/places/web-service/supported_types
#Read all the activities files
Acty_In = pd.read_excel("Resources/Indoor.xlsx")
Acty_Out = pd.read_excel("Resources/Outdoor.xlsx")
Acty_EveIn = pd.read_excel("Resources/EveIndoor.xlsx")
Acty_EveInOut = pd.read_excel("Resources/EveInOut.xlsx")
Acty_NightIn = pd.read_excel("Resources/NightIndoor.xlsx")
Acty_NightInOut = pd.read_excel("Resources/NightInOut.xlsx")
```


```python
#Converting the actvity dataframes into list
Acty_Inlist = Acty_In["Indoor"].tolist()
Acty_Outlist = Acty_Out["Outdoor"].tolist()
Acty_EveInlist = Acty_EveIn["EveIndoor"].tolist()
Acty_EveInOutlist = Acty_EveInOut["EveInOut"].tolist()
Acty_NightInlist = Acty_NightIn["NightIndoor"].tolist()
Acty_NightInOutlist = Acty_NightInOut["NightInOut"].tolist()
```


```python
#Input the target city and state
Target_City = input("Enter city & state in the format 'city,state: ")
```

    Enter city & state in the format 'city,state: Sayreville,NJ
    


```python
#Split the input to get the city
City = Target_City.split(",")[0]

#Create settings dictionary for units & appid
settings = {"units": "metric", "appid": api_key}
```


```python
#Get current weather
current_weather = owm.get_current(City, **settings)

summary = ["name", 'weather', "main", "wind", 'clouds']
data = current_weather(*summary)

currentmain = data[1][0]["main"]
temp = data[2]['temp']
humidity = data[2]['humidity']
windspeed = data[3]["speed"]
currentdesc = data[1][0]['description']
```


```python
Finalresult = ""
Words_of_advice = " "
 
# loop through main weather types to determine if outdoor or indoor activities
if (currentmain=="Thunderstorms") or (currentmain=="Clouds") or (currentmain=="Clear") or (currentmain=="Mist"):
    if (currentmain=="Thunderstorms"):
        Words_of_advice = "Staysafe!!! "+ currentmain + " in the " + City
        Finalresult="indoor"
        print(f'{Words_of_advice}: Be {Finalresult}')
    else:
        Finalresult="outdoor"
        print(f'Lets evaluate temp, windspeed and humidity further to confirm if we should be {Finalresult}s')
else:
    if currentmain=="Rain":
    #read rain.df to find further details
        for index, desctype in rain_df.iterrows():
            if currentdesc == desctype["Description"]:
                Finalresult=rain_df["Rating"][index]
                print(f'{currentmain}:{currentdesc}: You can be {Finalresult}')
    elif currentmain=="Snow" :
        #read snow.df to find further details
        for index, desctype in snow_df.iterrows():
            if currentdesc == desctype["Description"]:
                Finalresult=desctype["Rating"][index]
                print(f'{currentmain}:{currentdesc}: You can be {Finalresult}')
    elif currentmain=="Drizzle" :
        #read drizzledf to find further details
        for index, desctype in drizzle_df.iterrrows():
            if currentdesc == desctype["Description"]:
                Finalresult=drizzle_df["Rating"][index]
                print(f'{currentmain}:{currentdesc}: You can be {Finalresult}')
    elif currentmain=="Atmosphere" :
        #read atmospheredf to find further details
        for index, desctype in atmosphere_df.iterrows():
            if currentdesc== desctype["Description"]:
                Finalresult=atmosphere_df["Rating"][index]
                print(f'{currentmain}:{currentdesc}: You can be {Finalresult}')
if Finalresult == "outdoor" or " ":
    if  (temp<7 or temp>35):
        Words_of_advice = "Better to be safe inside as it is extreme temperature" + str(temp) + " outside in " + City
        Finalresult="indoor"
        print(f'{Words_of_advice}: Advisable to be {Finalresult}')
    elif(windspeed>38):
        #https://en.wikipedia.org/wiki/Beaufort_scale
        Words_of_advice = "Better to be safe inside as it is windy " + str(windspeed) + " outside in " + City
        Finalresult="indoor"
        print(f'{Words_of_advice}: Advisable to be {Finalresult}')
    elif(humidity>60): 
        Words_of_advice  = "Better to be inside as it is quite humid " + str(humidity) + " outside in " + City
        Finalresult="indoor" 
        print(f'{Words_of_advice}: Advisable to be {Finalresult}')
    else:
        Words_of_advice = "It is safe & pleasant!!! Enjoy outside in the current weather of "+ currentmain +" in "+ City
        Finalresult="outdoor"
        print(f'{Words_of_advice}: Be {Finalresult}s & have fun!!!')
```

    Lets evaluate temp, windspeed and humidity further to confirm if we should be outdoors
    It is safe & pleasant!!! Enjoy outside in the current weather of Clear in Sayreville: Be outdoors & have fun!!!
    


```python
now = datetime.datetime.now()
print(now)
At12= now.replace(hour=12, minute=0, second=0, microsecond=0)
At15 = now.replace(hour=15, minute=0, second=0, microsecond=0)
At18 = now.replace(hour=18, minute=0, second=0, microsecond=0)
At21 = now.replace(hour=21, minute=0, second=0, microsecond=0)
```

    2018-07-06 21:12:31.505586
    


```python
Acty = Finalresult
if (Acty == "outdoor"):
    if(now < At12):
        print(Words_of_advice)
        print("It's morning, plan outdoor activities for the entire day")
        Acty = Acty_Outlist
    elif((now >= At12 and now < At15)):
        print(Words_of_advice)
        print("It's afternoon, plan outdoor activities for the day")
        Acty = Acty_Outlist
    elif((now >= At15 and now < At18)):
        print(Words_of_advice)
        print("It's evening, though you can go outdoor,however, we would suggest indoor activities for the evening as parks,zoos might be closed. We would add stadiums though, as there might be matches and campgrounds if you are adventurous!!!")
        Acty = Acty_EveInOutlist
    elif((now >= At18)):
        print(Words_of_advice)
        print("It's lovely night, though you can go outdoor,however, we would recommend indoor activities for the night,as parks,zoos are closed. We would add stadiums though, as there might be matches and campgrounds if you are adventurous!!!")
        Acty = Acty_NightInOutlist 
elif (Acty == "indoor"):        
    if(now < At12):
        print(Words_of_advice)
        print("It's morning, plan indoor activities for the entire day or stay at home & relax.")
        Acty = Acty_Inlist
    elif((now >= At12 and now < At15)):
        print(Words_of_advice)
        print("It's afternoon, plan indoor activities for the day or stay at home & relax.")
        Acty = Acty_Inlist
    elif((now >= At15 and now < At18)):
        print(Words_of_advice)
        print("It's evening, plan indoor activities for the evening or stay at home & relax.")
        Acty = Acty_EveInlist 
    elif((now >= At18)):
        print(Words_of_advice)
        print("It's night, plan indoor activities for the night or stay at home & relax.")
        Acty = Acty_NightInlist 
```

    It is safe & pleasant!!! Enjoy outside in the current weather of Clear in Sayreville
    It's lovely night, though you can go outdoor,however, we would recommend indoor activities for the night,as parks,zoos are closed. We would add stadiums though, as there might be matches and campgrounds if you are adventurous!!!
    


```python
#Google Maps Geocode API
base_url_geocode = "https://maps.googleapis.com/maps/api/geocode/json"

#Creating the params dict with address and key
params_geocode = {"address": Target_City, "key": gkey}
```


```python
# Run request (Build URL using the base_url & params)
response = requests.get(base_url_geocode, params=params_geocode)

# Convert the response to JSON
city_geo = response.json()

# Extract Lat/Lng
lat = city_geo["results"][0]["geometry"]["location"]["lat"]
lng = city_geo["results"][0]["geometry"]["location"]["lng"]

# Print results
print("%s: %s, %s" % (Target_City, lat, lng))

#Converting the lat & lng to string
lat = str(lat)
lng = str(lng)
```

    Sayreville,NJ: 40.45940210000001, -74.360846
    


```python
#Google maps nearby places search API
base_url_nearby = "https://maps.googleapis.com/maps/api/place/nearbysearch/json?"
#Google maps places API
base_url_findplace = "https://maps.googleapis.com/maps/api/place/findplacefromtext/json?"
```


```python
#Find the names and addresses of nearbyplaces in given city,state entered by the
#user.
type_of_place = []
place_names = []
addresses = []
rating = []
openorclose = []
for i in range(len(Acty)):
    
    target_type = Acty[i]
    radius = 50000

# Build URL using the Google Maps API and other fields
    target_url_nearby = base_url_nearby+"key="+gkey+"&location="+lat+","+lng+"&radius="+str(radius)+"&type="+target_type
    #target_url_nearby = base_url_nearby+"key="+gkey+"&location="+lat+","+lng+"&rankby=distance"+"&type="+target_type

# Run request
    response = requests.get(target_url_nearby)
    city_placestogo = response.json()
   
    if city_placestogo["status"] == "ZERO_RESULTS":
        print(f'{Acty[i].upper()} : No {Acty[i]} near your place of choice.')
        type_of_place.append(target_type)
        place_names.append("")
        addresses.append("")
        rating.append("")
        openorclose.append("")
        print("\n")
    else:
        print(f'{Acty[i].upper()} : Bingo!!! We have found good {Acty[i]}s near your place of choice.')
        count = 0
        for item in city_placestogo["results"]:
            myplace = item["name"]
            myplace = myplace.replace(" ", "%20")
            target_url_findplace = base_url_findplace+"key="+gkey+"&input="+myplace+"&inputtype=textquery"+"&fields=formatted_address,name,rating,opening_hours"
            response = requests.get(target_url_findplace)
            data = response.json()
            type_of_place.append(target_type)
            try:
                place_names.append(data["candidates"][0]["name"])
            except:
                place_names.append(item["name"])
            try:    
                addresses.append(data["candidates"][0]["formatted_address"])
            except:
                addresses.append(item["vicinity"])
            try:    
                rating.append(data["candidates"][0]["rating"])
            except:   
                rating.append(np.nan)
            try:  
                if(data["candidates"][0]["opening_hours"]["open_now"]):
                    openorclose_status = "Open"
                else:
                    openorclose_status = "Closed"
                openorclose.append(openorclose_status)
            except:
                openorclose.append("Check the opening hours on the website")
            count = count + 1
        print(f'Total {count} {Acty[i]}s are found near {City}')
        print("\n")    
```

    BAR : Bingo!!! We have found good bars near your place of choice.
    Total 20 bars are found near Sayreville
    
    
    CAMPGROUND : Bingo!!! We have found good campgrounds near your place of choice.
    Total 20 campgrounds are found near Sayreville
    
    
    CASINO : Bingo!!! We have found good casinos near your place of choice.
    Total 19 casinos are found near Sayreville
    
    
    LODGING : Bingo!!! We have found good lodgings near your place of choice.
    Total 20 lodgings are found near Sayreville
    
    
    MOVIE_RENTAL : Bingo!!! We have found good movie_rentals near your place of choice.
    Total 20 movie_rentals are found near Sayreville
    
    
    MOVIE_THEATER : Bingo!!! We have found good movie_theaters near your place of choice.
    Total 20 movie_theaters are found near Sayreville
    
    
    NIGHT_CLUB : Bingo!!! We have found good night_clubs near your place of choice.
    Total 20 night_clubs are found near Sayreville
    
    
    RESTAURANT : Bingo!!! We have found good restaurants near your place of choice.
    Total 20 restaurants are found near Sayreville
    
    
    STADIUM : Bingo!!! We have found good stadiums near your place of choice.
    Total 20 stadiums are found near Sayreville
    
    
    


```python
Df = pd.DataFrame({"Type":type_of_place,"Place":place_names,"Address":addresses, 
                   "Rating":rating,
                  "OpenorClose":openorclose})
Df.fillna(0, inplace=True)
Df[["Type","Place","Address","Rating","OpenorClose"]]
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Type</th>
      <th>Place</th>
      <th>Address</th>
      <th>Rating</th>
      <th>OpenorClose</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>bar</td>
      <td>The Madison Hotel Morristown, NJ</td>
      <td>1 Convent Rd, Morristown, NJ 07960, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bar</td>
      <td>The Peacock Inn</td>
      <td>20 Bayard Ln, Princeton, NJ 08540, USA</td>
      <td>4.6</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>2</th>
      <td>bar</td>
      <td>Tick Tock Diner NY</td>
      <td>481 Eighth Ave, New York, NY 10001, USA</td>
      <td>3.6</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>3</th>
      <td>bar</td>
      <td>The Court - A St Giles Hotel</td>
      <td>130 E 39th St, New York, NY 10016, USA</td>
      <td>3.7</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>4</th>
      <td>bar</td>
      <td>Sofitel New York</td>
      <td>45 W 44th St, New York, NY 10036, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>5</th>
      <td>bar</td>
      <td>Roger Smith Hotel</td>
      <td>501 Lexington Ave, New York, NY 10017, USA</td>
      <td>4.2</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>6</th>
      <td>bar</td>
      <td>ABC Kitchen</td>
      <td>35 E 18th St, New York, NY 10003, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>7</th>
      <td>bar</td>
      <td>Hudson</td>
      <td>New York 12534, USA</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>8</th>
      <td>bar</td>
      <td>Extra Virgin</td>
      <td>259 West 4th Street, New York, NY 10014, USA</td>
      <td>4.2</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>9</th>
      <td>bar</td>
      <td>The Empire Hotel</td>
      <td>44 W 63rd St, New York, NY 10023, USA</td>
      <td>3.9</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>10</th>
      <td>bar</td>
      <td>Flatiron Lounge</td>
      <td>4200, 37 W 19th St, New York, NY 10011, United...</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>11</th>
      <td>bar</td>
      <td>Loews Regency New York</td>
      <td>540 Park Ave, New York, NY 10065, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>12</th>
      <td>bar</td>
      <td>The Lowell Hotel</td>
      <td>28 East 63rd Street, at Madison Ave, New York,...</td>
      <td>4.5</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>13</th>
      <td>bar</td>
      <td>The Mermaid Inn</td>
      <td>96 2nd Ave, New York</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>14</th>
      <td>bar</td>
      <td>Battery Gardens</td>
      <td>1 Battery Pl, New York, NY 10004, USA</td>
      <td>3.9</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>15</th>
      <td>bar</td>
      <td>Spot Dessert Bar</td>
      <td>13 St Marks Pl, New York, NY 10003, USA</td>
      <td>4.5</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>16</th>
      <td>bar</td>
      <td>Manzo</td>
      <td>200 5th Avenue, New York</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>17</th>
      <td>bar</td>
      <td>Texas Roadhouse</td>
      <td>1000 U.S. 9, Parlin</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>18</th>
      <td>bar</td>
      <td>Casa Mezcal</td>
      <td>86 Orchard St, New York, NY 10002, USA</td>
      <td>4.3</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>19</th>
      <td>bar</td>
      <td>Penthouse808 Rooftop</td>
      <td>8-08 Queens Plaza S, Long Island City, NY 1110...</td>
      <td>3.9</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>20</th>
      <td>campground</td>
      <td>Liberty Harbor RV Park</td>
      <td>11 Marin Blvd, Jersey City, NJ 07302, USA</td>
      <td>3.6</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>21</th>
      <td>campground</td>
      <td>Turkey Swamp Park</td>
      <td>200 Georgia Rd, Freehold</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>22</th>
      <td>campground</td>
      <td>William H Pouch Boy Scout Camp</td>
      <td>1465 Manor Rd, Staten Island, NY 10314, USA</td>
      <td>4.6</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>23</th>
      <td>campground</td>
      <td>Black Bear Lake Day Camp</td>
      <td>457 Stagecoach Rd, Clarksburg, NJ 08510, USA</td>
      <td>4.5</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>24</th>
      <td>campground</td>
      <td>TIP TAM CAMPING RESORT LLC</td>
      <td>301 Brewers Bridge Rd, Jackson, NJ 08527, USA</td>
      <td>4.2</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>25</th>
      <td>campground</td>
      <td>Indian Rock RV Park &amp; Campground</td>
      <td>920 W Veterans Hwy, Jackson, NJ 08527, USA</td>
      <td>4.2</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>26</th>
      <td>campground</td>
      <td>Butterfly Camping Resort</td>
      <td>360 Butterfly Rd, Jackson, NJ 08527, USA</td>
      <td>3.8</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>27</th>
      <td>campground</td>
      <td>Timberland Lake Campground</td>
      <td>1335 Reed Rd, Jackson, NJ 08527, USA</td>
      <td>4.0</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>28</th>
      <td>campground</td>
      <td>Pine Cone Resort</td>
      <td>601 US-50, Zephyr Cove, NV 89448, USA</td>
      <td>3.9</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>29</th>
      <td>campground</td>
      <td>Camp Kinderland</td>
      <td>1543 Colebrook River Rd, Tolland, MA 01034, USA</td>
      <td>5.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>149</th>
      <td>restaurant</td>
      <td>Maialino</td>
      <td>2 Lexington Ave, New York, NY 10010, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>150</th>
      <td>restaurant</td>
      <td>Loews Regency New York</td>
      <td>540 Park Ave, New York, NY 10065, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>151</th>
      <td>restaurant</td>
      <td>The Lowell Hotel</td>
      <td>28 East 63rd Street, at Madison Ave, New York,...</td>
      <td>4.5</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>152</th>
      <td>restaurant</td>
      <td>The Mermaid Inn</td>
      <td>96 2nd Ave, New York</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>153</th>
      <td>restaurant</td>
      <td>Battery Gardens</td>
      <td>1 Battery Pl, New York, NY 10004, USA</td>
      <td>3.9</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>154</th>
      <td>restaurant</td>
      <td>Manzo</td>
      <td>200 5th Avenue, New York</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>155</th>
      <td>restaurant</td>
      <td>The Mark Hotel</td>
      <td>25 E 77th St, New York, NY 10075, USA</td>
      <td>4.5</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>156</th>
      <td>restaurant</td>
      <td>P.F. Chang's</td>
      <td>3545 US Highway 1, Princeton</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>157</th>
      <td>restaurant</td>
      <td>Texas Roadhouse</td>
      <td>1000 U.S. 9, Parlin</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>158</th>
      <td>restaurant</td>
      <td>Miss Lily's</td>
      <td>132 W Houston St, New York, NY 10012, USA</td>
      <td>4.2</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>159</th>
      <td>stadium</td>
      <td>MetLife Stadium</td>
      <td>1 MetLife Stadium Dr, East Rutherford, NJ 0707...</td>
      <td>4.4</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>160</th>
      <td>stadium</td>
      <td>Red Bull Arena</td>
      <td>600 Cape May St, Harrison, NJ 07029, USA</td>
      <td>4.3</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>161</th>
      <td>stadium</td>
      <td>HighPoint Solutions Stadium</td>
      <td>1 Scarlet Knight Way, Piscataway Township, NJ ...</td>
      <td>4.3</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>162</th>
      <td>stadium</td>
      <td>Madison Square Garden</td>
      <td>4 Pennsylvania Plaza, New York, NY 10001, USA</td>
      <td>4.6</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>163</th>
      <td>stadium</td>
      <td>CURE Insurance Arena</td>
      <td>81 Hamilton Ave, Trenton, NJ 08611, USA</td>
      <td>4.0</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>164</th>
      <td>stadium</td>
      <td>Msg Networks GmbH</td>
      <td>Bundesallee 233, 42103 Wuppertal, Germany</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>165</th>
      <td>stadium</td>
      <td>Newark Schools Stadium</td>
      <td>541 Roseville Ave, Newark, NJ 07107, USA</td>
      <td>4.5</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>166</th>
      <td>stadium</td>
      <td>Rutgers Athletic Center</td>
      <td>83 Rockafeller Rd, Piscataway Township, NJ 088...</td>
      <td>4.4</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>167</th>
      <td>stadium</td>
      <td>Powers Field @ Princeton Stadium</td>
      <td>Princeton, NJ 08540, USA</td>
      <td>4.5</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>168</th>
      <td>stadium</td>
      <td>Meadowlands Sports Complex</td>
      <td>50 NJ-120, East Rutherford, NJ 07073, USA</td>
      <td>4.4</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>169</th>
      <td>stadium</td>
      <td>Wall Stadium Speedway</td>
      <td>1803 NJ-34, Wall Township, NJ 07719, USA</td>
      <td>4.7</td>
      <td>Closed</td>
    </tr>
    <tr>
      <th>170</th>
      <td>stadium</td>
      <td>Centercourt Athletic Club of Chatham</td>
      <td>222 N Passaic Ave, Chatham, NJ 07928, USA</td>
      <td>4.4</td>
      <td>Open</td>
    </tr>
    <tr>
      <th>171</th>
      <td>stadium</td>
      <td>Stadium Entertainment Holdings</td>
      <td>888 7th Avenue #35, New York</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>172</th>
      <td>stadium</td>
      <td>Somerset Patriots Baseball</td>
      <td>860 East Main Street, Bridgewater</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>173</th>
      <td>stadium</td>
      <td>Arm &amp; Hammer Park</td>
      <td>1 Thunder Road, Trenton</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>174</th>
      <td>stadium</td>
      <td>Weaver Stadium</td>
      <td>Waterlode, Nantwich CW5 5BS, UK</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>175</th>
      <td>stadium</td>
      <td>Ford Field</td>
      <td>2000 Brush St, Detroit, MI 48226, USA</td>
      <td>4.6</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>176</th>
      <td>stadium</td>
      <td>EVENTS MASTER</td>
      <td>Al Asayel St - Dubai - United Arab Emirates</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>177</th>
      <td>stadium</td>
      <td>Sports Consultants</td>
      <td>50 New Jersey 120, East Rutherford</td>
      <td>0.0</td>
      <td>Check the opening hours on the website</td>
    </tr>
    <tr>
      <th>178</th>
      <td>stadium</td>
      <td>Thomas M Gerrity Athletic Complex</td>
      <td>440 South, Jersey City, NJ 07305, USA</td>
      <td>4.6</td>
      <td>Check the opening hours on the website</td>
    </tr>
  </tbody>
</table>
<p>179 rows × 5 columns</p>
</div>




```python
for acty in Acty:
    print(acty.upper())
    Df_A = Df[Df["Type"] == acty]
    if (Df_A["Place"].any() == ""):
            print(f'No {acty} near your place of choice.')
    else:    
        Df_A = Df_A.sort_values(by=["Rating"],ascending=False)
        DF_A = Df_A[Df_A["OpenorClose"]=="Open"]
        Df_A = Df_A[:10]
        x_axis = np.arange(len(Df_A["Place"]))
        plt.figure(figsize = (10,5))
        plt.bar(x_axis, Df_A["Rating"],align="center",color="blue",alpha = 0.5)
        plt.xlim(-1,10)
        plt.ylim(0,5.5)
        plt.xlabel(f'{acty.upper()}s')
        plt.ylabel("Ratings".upper())
        plt.title(f"Top {acty.capitalize()}s' in {City} with ratings(Max 5) for G2G")
        plt.xticks(x_axis, Df_A["Place"],rotation=45,horizontalalignment="right")
        for a,b in zip(x_axis, Df_A["Rating"]):
            plt.text(a, b+.1, str(b))    
        plt.savefig(f'Results/Top_{acty.capitalize()}s_of_{City}_with_Ratings_G2G_Radius_Open.jpg',bbox_inches="tight")
        plt.show()    
```

    BAR
    


![png](Results/output_15_1.png)


    CAMPGROUND
    


![png](Results/output_15_3.png)


    CASINO
    


![png](Results/output_15_5.png)


    LODGING
    


![png](Results/output_15_7.png)


    MOVIE_RENTAL
    


![png](Results/output_15_9.png)


    MOVIE_THEATER
    


![png](Results/output_15_11.png)


    NIGHT_CLUB
    


![png](Results/output_15_13.png)


    RESTAURANT
    


![png](Results/output_15_15.png)


    STADIUM
    


![png](Results/output_15_17.png)

