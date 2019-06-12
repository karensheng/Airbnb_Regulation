## Impact of San Francisco's short-term rental regulation on the supply and pricing of Airbnb listings

### Link to the presentation:

https://drive.google.com/open?id=1kbgBahYtDttEf9qDHZhhtpx3_KRkE1Bf


### Motivation:

As an economist with many years' experience in litigation consulting, I'm naturally drawn to studying the dynamics of supply and demand as well as the impact of regulation on the marketplace. Despite its tremendous success, Airbnb has been involved with legal disputes with numerous cities with regard to the legitimacy of short-term rentals. It is particularly interesting in the sense that these regulations are highly localized as a result of Airbnb's efforts to reach agreements with individual cities where its operation has been challenged. 

Upon settling a lawsuit with the City of San Francisco in May 2017, Airbnb agreed to help enforce San Francisco's existing short-term rental laws on its platform. As the first phase of my capstone project, I intended to assess the reduction of listings (i.e. supplies) as a result of the newly enforced regulation and to assess whether there were any significant changes in the prices.

### Data Source:

The primary data source is the <a href="http://insideairbnb.com/get-the-data.html" target="_blank">InsideAirbnb</a> website. 

Due to the fact that the city-specific data has been scraped at roughly a constant time interval (once a month) since 2015, the combined data-set is essentially a panel data. This is extremely valuable for a wide range of topics, e.g. cohort study, churning and forecasting.

### Data Pipeline:

Raw data is organized as .gz files on the InsideAirbnb website. To make the data ingestion process accurate and scalable, I developed a BeautifulSoup script that scraped all of the hyperlinks on "http://insideairbnb.com/get-the-data.html". This includes links of files associated with other cities and countries. After that, I developed a function that imports and concatenates city-specific files associated with Listings, Reviews and Calendar, respectively. As of May 31 2019, there were 45 monthly scraped files for each set of data associated with San Francisco. Three months were scraped twice - November 2017, December 2017 and January 2018. These three months coincide with the period when Airbnb urged hosts to register with the city to meet the compliance requirements. 

Note that the concatenated "Calendar" data adds up to more than 100 million observations. The massive size caused crashes of RAM repeatedly. After several attempts, I decided to filter the data to dates with less than 180 days' lead time from the "current" scraped date before the data sets were concatenated. For instance, for the Calendar file scraped on January 1, 2017, only observations up to June 30, 2017 were included in the combined data. 

Overall the data pipeline could be pictured as below:


#### BeautifulSoup ----> Index of Hyperlinks ----> Import and Concatenate Raw Data (unzipping .gz files) 
#### ----> Pickle concatenated data ----> SQLITE3/Pandas

Sqlite3 was extensively used. Multiple data tables are stored in a master database called "airbnb.db". The advantage of using Sqlite3 is that the data tables could be queried directly across different Jupyter notebooks.



### Takeaways from EDA (Exploratory Data Analysis)

I initially analyzed the entire Listings data, but subsequently decided to filter the data to listings of which the minimum nights requirement is less than 30 days. This is consistent with how the SF city authority defines "short-term residential rental". All the discussion below is limited to short-term rentals ("STR" hereafter) unless explicitly clarified otherwise.

1. Supply side factors - number of listings:

The number of active listings on Airbnb peaked in 2017 and reached almost 9,000. However, as Airbnb reached an agreement with the city authority in SF and started to alert hosts of the pending new regulation, the number of active listings in SF progressively decreased from November 2017 to February 2018 ("the transition period" hereafter). It is noteworthy that the number of STR listings appeared to have plateaued around 4,000 since then. There could be multiple reasons for the reduction of supplies in the transition period. Some hosts may have voluntarily removed their listings because they wouldn't or couldn't meet the compliance requirements. Once the midnight of January 16, 2018 passed, Airbnb may have forcefully removed a large number of listings. One week prior to the compliance deadline, there were 5,837 STR listings. On the day immediately after the compliance deadline, the number dropped to 4,505. By February 2, there were only 3,961 listings. It is notable that around the same time Airbnb initiated an "Airbnb-Friendly Rental Building" program. Airbnb signed agreements with a few large REITs that own rental properties in SF. Short-term rentals are allowed in a portion of these buildings and in return the REITs got a share of the profits. This process may have encouraged some new hosts to sign up. It is unclear though how the new additions balanced out with the attrition of listings as a result of the new regulation.

![image](Images/number%20of%20listings.png)

![image](Images/attrition%20by%20month.png)


The proliferation of STR listings on Airbnb from 2015 to 2017 and the drastic drop in the "transition period" are both evident in the two heat maps below: (the 25th frame in each heat map is associated with November 1, 2017)

[Listings of Entire Home - Heat-map](https://karensheng.github.io/shortterm_entirehome_heatmap.html)

[Listings of Private Rooms - Heat-map](https://karensheng.github.io/shortterm_privateroom_heatmap.html)

When we put the screenshot of November 1, 2017 (start of the "transition period") and February 2, 2018 (end of the "transition period") side by side, the drastic drop in the density of listings geographically became very clear.

![image](Images/Nov1_2017_entire_home.PNG)

![image](Images/Feb2_2018_entirehome.PNG)

2. Room Type and Property Type:

Approximately 54.6% of the listings overall are entire home or apartments whereas 40.4% of the listings are for private rooms. Unsurprisingly, only 5% of the listings are for a shared room.

It's noteworthy that the categorization of property types (e.g. apartment, house, loft, condominium) is not standardized in the data. For instance,"apartment" "house" and "condominium" combined account for 89.72% of the listings of private home. However, there are in total 35 categories in property type (e.g. boat, bus).

![image](Images/pct%20by%20room%20type.png)

![image](Images/pct%20of%20property%20type%20entire%20home.PNG)

![image](Images/pct%20by%20property%20type%20private%20room.PNG)

![image](Images/mix%20of%20room%20type%20by%20prop%20type.PNG)


3. Longevity of a listing:

As a two-sided marketplace, retention of both hosts and guests are important to Airbnb. How long a given listing remains active on Airbnb is an indicator of the LTV (lifetime value) of a host. Notably, a host may have multiple listings at a given time or have different listings over time as she/he moves. At a glimpse, a host may not have an active listing long after her/his registration date on Airbnb's website. However, given that a review can only be left within 14 days after the check-out date of a guest, the first review date of a given listing could be used as a proxy of the start of the booking history. Hence, the longevity of a listing is defined as the difference between the first review date and the last "scrape date". (The distributions look roughly the same whether the listings with no reviews are excluded or not.)

![image](Images/longevity%20of%20a%20listing_all%20STR%20listings.png)


It is noteworthy that the majority of listings appear to remain active for 0-40 months. It might be an interesting project to study the common characteristics of those listings that have remained active for more than 40 months (e.g. number of reviews, location and amenities). For instance, the chart below shows positive correlation between the longevity of a listing and the number of reviews. Caution that no conclusions about causation could be drawn here. 

![image](Images/longevity%20of%20a%20listing%20vs%20number%20of%20reviews.png)

### Hypothesis Test

I intended to test whether the average price of listings in February 2018, controlling for room type, went up relative to that in February 2017, as a result of almost 50% reduction of listings. February 2017 was chosen as a baseline because I'd like to eliminate seasonality factors. Furthermore, in order to make the listings comparable, only the listings that were active in both February 2017 and February 2018 were included in the sample. To phrase the question differently - "Did the attrition or removal of almost half of the listings drive up the price of these listings that remained active after the compliance deadline?"

It is common practice in the travel/hospitality industry to set prices dynamically based on the lead time of the reservation. Ideally, such prices, as captured by the Calendar data sets, should be used to calculate the average price across listings. In other words, in addition to controlling for common listings, the amount of lead time should also be controlled for. However, due to time constraints, only the "sticker price" in the Listings data were used. "Sticker price" is the price that a guest sees when she/he visits the page of a particular listing. 

Even without controlling for common listings, it appears that even though there were almost twice as many listings in February 2017 as in February 2018, the distributions of prices in these two months are very similar.

![image](Images/distribution%20of%20price%20listings%20of%20entire%20home.png)

There are 1,348 common listings of entire home in February 2017 and February 2018. Apparently the distributions almost entirely overlap.

![image](Images/distribution%20of%20price%20listings%20of%20entire%20home_common%20listings.png)

Formally, I used Welch's t-test to test the null hypothesis that the mean difference of prices is 0 in these two months.

H0: price_Feb2017 - price_Feb2018 = 0
H1: price_Feb2017 - price_Feb2018 != 0

p_value is 0.3

I failed to reject the null hypothesis that the mean difference of prices is 0. 

![image](Images/t-test.png)

This conclusion is quite counter-intuitive. Several factors may explain the stability of the price. 

* February may be a conventionally slow season for vacation rental reservations and the expected price hike due to the reduction of supplies is not reflected in the "sticker price".

* There may have been a significant change in the prices but it's reflected in the Calendar data only.

* Hosts mostly assign the "sticker price" based on Airbnb's recommendations, and for some reason, Airbnb's recommended prices didn't reflect the change of supplies. 

* There may have been an oversupply on Airbnb prior to the "transition period". Attrition or removal of almost half of the listings may have brought the market to equilibrium. Notably, the number of listings has hovered around 4,000 since February 2018 and has never recovered the peak level in 2017. To test this conjecture, it's worthwhile studying the reservation and reviews history of those listings that were voluntarily or involuntarily removed in the "transition period". To further investigate this, again, the number of reviews may be used as a proxy to assess the demand prior to and after the Jan 2018 compliance deadline.

### Next Steps

I'd like to continue to look into the prices reflected in the Calendar data and to verify the conjectures I formulated above. Ideally, the price series should be charted year over year so that it's easy to spot seasonality and any drastic changes, if there were any. 

In addition, I'd like to perform more in-depth analysis of the demand side factors, which largely rely on the Reviews data. 

For the 2nd capstone,  I’d like to use multiple machine learning techniques to build a predictive model of prices that incorporates amenities, location, seasonality, lead time, etc.













