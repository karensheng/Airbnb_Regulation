
## Impact of SF's short-term rental regulation on the supply and pricing of Airbnb listing

### Link to the presentation: https://drive.google.com/open?id=1kbgBahYtDttEf9qDHZhhtpx3_KRkE1Bf

### Data Pipeline:

Raw data is organized as .gz files on the InsideAirbnb website. To make the data ingestion process accurate and scaleable, I developed a BeautifulSoup script that scraped all the hyperlinks on "http://insideairbnb.com/get-the-data.html". This includes links of files associated with other cities and countries. After that, I developed a function that imports and concatenates all the files associated with Listings, Reviews and Calendar, respectively. There are 45 monthly scraped files for each set of data. Three months were scraped twice - November 2017, December 2017 and January 2018. 

Note that the concatenanted "Calendar" data adds up to more than 100 million observations. The massive size caused crashes of RAM repeatedly. After several attempts, I decided to filter the data to dates with less than 180 days' lead time from the "current" scraped date before the data sets were concatenated. For instance, for the Calendar scraped on January 1, 2017, only observations within 180 days of June 30, 2017 were included in the combined data. 

Overall the data pipeline could be pictured as below:


#### BeautifulSoup ----> Index of Hyperlinks ----> Import and Concatenate Raw Data (unzipping .gz files) 

#### ----> Pickle concatenated data ----> SQLITE3/Pandas

Sqlite3 was extensively used. Multiple data tables are stored in a master database called "airbnb.db". The advantage of using Sqlite3 is that the data tables could be queried directly across different Jupyter notebooks.



### Takeaways from EDA of the Listings Data

I initially analyzed the entire listings data, and then filtered the data to listings of which the minimum nights requirement is less than 30 days. This is consistent with how the SF city authority defines "short-term residential rental". All the discussion below is limited to short-term rentals ("STR") unless explicitly clarified otherwise.

The number of active listings on Airbnb peaked in 2017 and reached amost 9000. However, as Airbnb reached an agreement with the city authority in SF and started to alert hosts of the pending new regulation, the number of active listings in SF progressively decreased from November 2017 to February 2018 ("the transition period" hereafter). Interesting, the number of STR listings has appeared be to plateaued around 4000 since then. There could be multiple reasons for the reduction of supplies in the transition period. Some hosts may have voluntarily removed their listings because they wouldn't or couldn't meet the compliance requirement. Once midnight of January 16, 2018 passed, Airbnb may have forcefully removed a large number of listings. One week prior to the compliance deadline, there were 5837 STR listings. On the day immediately after the compliance deadline, the number dropped to 4505. By February 2, there were only 3961 listings. It is notable that around the same time Airbnb initiated an "Airbnb-Friendly Rental Building" program. Airbnb signed agreements with a few large REITs that own rental properties in SF. Short-term rentals are allowed in a portion of these buildings and in return the REITs got a share of the profits. This process may have encouraged some new hosts to sign up. It is unclear though how the new additions balanced out with the attrition of listings as a result of the new regulation.

![image](Images/image 1.png)

![image](Images/attrition by month.html)



