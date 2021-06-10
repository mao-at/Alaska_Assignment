# Question of Interest
1. What is the conversion rate for an individual flight search result?  
2. How does the cost in miles affect the guests’ willingness to purchase? For this question, assume that the conversion space is already tagged successfully, and this data is already accessible.  
3. How many times does a guest attempt to search for a destination that we do not serve for the given airport or date? And provide model for recommendation to guest on what itinerary.   

# Assumption

1. Potential customers include both registered and unregistered Guests.  
2. There exists an Aviation Database which contains all the information of scheduled flights, airplanes, airports, etc.  
3. There exists a User Database which contains all the information of registered users.  

# Tasks

1.
*	Our primary goal is to develop a structured dataset and use it to build a recommendation model for generating revenue. Therefore, we need to retain as much information as possible from individual searches to power our further analysis.
*	After each individual customer session, we need to capture:
  * **User information**: User information from our registered user database or information that can be used to identify unregistered users.
  * **Search conditions**: Flight Type (Round-trip, One-way, Multi-city), Departing and Destination Airports, Departing Date, Cost-in-Miles (Boolean), etc.
  * **List of Flight information from the Aviation Database**: Departure/Arrival Time, WIFI (Boolean), Flight Duration, Plane Model, Charging Outlet Availability, etc. We will use NULL to indicate empty search result.
    * The **rate of empty searches** can be calculated with (Number of NULLs) / (Total number of searches) 
  * **Converted**: value indicating whether the session ended with a purchase. For example, we can use 0 to indicate no conversion, 1 to indicate regular purchase, 2 to indicate miles purchase.
    * The **conversion rate** of searches can be easily calculated with this formula: (Number of type 1 + type 2 conversions)/ (total number of searches)
    * The **rate of miles payment** can be calculated with: (Number of type 2 conversions) / (Number of type 1 + type 2 conversions)

2.
Utilize Azure Event Hub to develop a stream analytics for ingesting JSON files without latency, and then store them into Azure Blob storage. After that, with Azure Data Bricks, we read the JSON files as dataframe, and transform the data into the query-able type. In the end, through Azure Data Factory, we write the data into delta format and store into the Azure Data Lake for further Machine Learning modeling.   

With the attributes in our data, such as Flight Fare, Number of Search Result, Departing Time and Flight Type, the machine learning model can be built for provide appropriate recommendations to customer. According to the assumption 1 that potential customers include both registered and unregistered guests, guests don’t have enough personal demographics to classify and model. Thus, the Content Based Recommendation algorithm is preferred. In this step, we can use Azure Data Bricks for model, and Power BI or Tableau for data visualization.   

**How does the cost in miles affect the guests’ willingness to purchase?**   
Assume that the conversion space is already tagged successfully, and this data is already accessible. 
With Power BI or Tableau to build various charts or graphs, compared with the regular payment method, 




