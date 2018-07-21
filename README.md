# Data_Destroyers
Project - Good Times to Go!!! (G2G)

1. Objective: The project, is developed in Python, focussed at recommending activities to people based on the current weather conditions for the given city,state. 

2. Xfactor: None of the applications that are currently present in the market considers the current weather condition in recommending the type of places or activities. Our project factors in the current weather and the time of the day to recommend the places and also provides the address and the ratings that are available on google.

3. PYTHON Modules used:
    a.openweathermapy.core
    b.pandas
    c.matplotlib
    d.numpy
    e.datetime
    f.json
    g.requests

4. APIs used:
    a. Openweather wrapper and the API key.
    b. Google APIs and the API key:
        i) https://maps.googleapis.com/maps/api/geocode/json?
        ii) https://maps.googleapis.com/maps/api/place/nearbysearch/json?
        iii) https://maps.googleapis.com/maps/api/place/findplacefromtext/json?
    c.Additional APIs that could be used:
        i)https://maps.googleapis.com/maps/api/place/details/json?
        ii)https://maps.googleapis.com/maps/api/distancematrix/json?

5. Source referred to get weather conditions and activity types are as below:
    a.Weather Conditions: https://openweathermap.org/weather-conditions
    b.Activity Types: https://developers.google.com/places/web-service/supported_types  

Note: Weather - Thunderstorm, Temperature, Windspeed, Humidity, Rain, Drizzle, Snow, Mist, Clear Sky are different factors to be considered under weather. The extreme weather like thunderstorms are considered as approriate for indoor activities whereas all other factors (excluding Temp, Windspped, Humidity) are evaluated based on further classification present in https://openweathermap.org/weather-conditions. For temperature & humidity, we took human sample of our family & fellow colleagues' experience to decide the appropriate range to go outdoor or remain indoor; For windspeed, we referred https://en.wikipedia.org/wiki/Beaufort_scale.

6. Limitations: 
    a. The places recommended are limited to what is present on the google data.
    b. The weather evaluation of is based on the human experience except for the extreme weather conditions &           windspeed.
    c. The project evaluates the current weather conditions and not the forecast.
    d. It does not factor in a person's past choices as the focus is on the current weather.
    e. It covers a hard coded radius not more than 50,000 meters.
    f. The project does not factor in age or personal choice and hence generic indoor & outdoor places are given.
    g. It does not calculate the distance to be covered or time to be taken to reach to the destination.
    h. It gives the open places and not the opening and closing time.
    i. Currently, the visualization is limited to the ratings comparison of various destinations of one kind.

 7. Future road to development:
    a. It would take the current location by default.
    b. The project would ask for the age and limit or recommend the kind of places.
    c. It would factor in the personal choices when type of places are chosen.
    d. It would give the distance to be covered and time to be taken to reach the destination, if a particular place        is chosen.
    e. It would give the opening & closing time. 
    f. The visualization for rating or distance or time taken to reach the destination would be displayed on the user's demand.



