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
 *	Utilize Azure Event Hubs to ingest JSON files with minimum latency and store them into Azure Blob/ADLS. After that, we ingest the JSON data using Azure Data Bricks for refinement and aggregation until we have data that is easily query-able (silver tables). We can then persist the silver table into our data lake / delta lake for direct analysis or generating gold tables for specialized usage scenarios. 
 *	With the silver and gold tables we have, machine learning models can be built to provide appropriate recommendations to customers. 
 	*	Since potential customers include both registered and unregistered guests, we don’t always have enough data for user profiling. Thus, a recommendation system using Item-Item Collaborative Filtering is preferred since we will only rely on the similarities between flights and trips.
 	*	To maximize revenue, we can prioritize the recommendation of flights/trips that are more profitable.

**How does the cost in miles affect the guests’ willingness to purchase?**  
We can use Power BI or Tableau to build various charts/graphs/dashboards to illustrate the difference between the rate of regular payment and the rate of miles payment. Based on that, we can examine whether the cost in miles affect the guests’ willingness to purchase.

3.
**Option 1: List 6 or 8 most popular flight at that time on the Flight Search interface with lowest price.**
* Pros: If the customer happens to choose this listed popular flight, this approach would compress the hesitation time before purchasing.
* Cons: If the customer is from a small airport with limited options, this approach would not help to generate revenue.

**Option 2: Based on the previous flighting records or search, a registered customer could see some recommended flights on the Flight Search interface.** 
* Pros: Customers would feel convenient while using our Flight Search interface, and stick into our company. Besides, this approach will reduce the thinking time.
* Cons: It will be challenging to generate reliable recommendations for customers who are not logged in, not registered or newly registered. The recommendations might confuse customers.

4. 
* We will use JSON as the source format to capture the search information. There are two general directions we can go:
  * Include all/most of the information we need in the JSON documents. For example, for each flight/route, we store not only the UUID but also additional information about the flight/route.
  * Include only essential information we need in the JSON documents. For example, for each flight/route, we only store the UUID and retrieve other related information only when we need it for analysis.
  * Approach 1 should be the preferred method since most of the information we are storing are already used to power the website UI, so the website backend can simply prepare another version of the data in JSON format and send it to our Blob/ADLS storage.
    * Pros: we will need less joins during our future analysis and thus reduce unnecessary reads from databases.
    * Cons: this approach might put additional load on the application servers and will also require more storage space.

* We will use Databricks to perform the transformations and analysis needed, and use Delta Lake to store our refined data. 
  * Pros: 
    * Databricks is a unified platform – the data refinement, transformation and advanced analytics (ML, recommendation) can all happen within a single space, which can reduce unnecessary data movement.
    * Databricks is built on top of Spark, which we can utilize to handle massive parallel processing – this will be important if we want our recommendation results to be based on the most recent data and if we want the recommendations to be generated quickly.
    * Delta Lake combines the benefits of data warehouse and data lake. We will have a massive amount of storage at a relatively low cost, but also the ability to support transactions, different isolation levels and data versioning.
    * Delta file, the open-source format used to enable Delta Lake, is built on top of Parquet. Both are efficient columnar stores that are easy to compress, and open-source format means low data gravity.
    * We can perform some visualization right within Databricks through the built-in Visualization functionality. We can also easily power tools like Tableau or Power BI for more advanced visualizations using data prepared by Databricks.
 
   * Cons:
     * We need to carefully examine our data and our usage scenarios to come up with partitioning strategies since proper partitioning is crucial to Databricks and Spark solutions.
     * Even though the underlying technologies are all open-source projects, many of the optimizations and Databricks itself is proprietary. This might cause our solutions to tightly couple with Databricks, Delta Lake and Azure/AWS platform.








