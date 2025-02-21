#Hospitality-Dashboard

Power BI dashboard analyzing room bookings in different hotels across different cities in India. 

Problem Statement:
What is leading the sales revenue for hotels?

Step 1) 
Run basic queries in SQL to understand the dataset 

Step 2)
Clean data & create relationships between tables in Power BI 

Step 3) 
Build Dashboard

> Page 1 called "Dashboard" is created using multiple measures:

- Revenue = SUM(fact_bookings[revenue_realized])

- Total Bookings = COUNT(fact_bookings[booking_id])

- Total Capacity = SUM(fact_aggregated_bookings[capacity])

- Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])

- Occupancy Rate means total successful bookings of total rooms available(capacity) 
Occupancy % = DIVIDE([Total Succesful Bookings],[Total Capacity],0)

- Average Rating = AVERAGE(fact_bookings[ratings_given])

- No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1

- day type = 
Var wkd = WEEKDAY(dim_date[date],1)
return
IF(
wkd>5,"Weekend","Weekday")

- Total cancelled bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")

- Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])

- Total Checked Out
CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")

- Total no show bookings
CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")

- No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])

- Booking % by Platform 
DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(fact_bookings[booking_platform])
  ))*100

- Booking % by Room class 
DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(dim_rooms[room_class])
  ))*100

- Average Daily rate (ADR) is the ratio of revenue to the total rooms booked/sold in a given time period	
ADR = DIVIDE( [Revenue], [Total Bookings],0)

- Realisation %	(succesfully "checked out" of room) 
Realisation % = 1- ([Cancellation %]+[No Show rate %])

- RevPAR(Revenue Per Available Room, whether or not they are occupied. RevPAR helps hotels measure their revenue generating performance to accurately price rooms)
RevPAR = DIVIDE([Revenue],[Total Capacity])

- DBRN(Daily Booked Room Nights, on average how many rooms are booked for a day considering a time period
DBRN = DIVIDE([Total Bookings], [No of days])

- DSRN(Daily Sellable Room Nights, on average how many rooms are ready to sell for a day considering a time period
DSRN = DIVIDE([Total Capacity], [No of days])

- DURN(Daily Utilized Room Nights, on average how many rooms are succesfully utilized by customers for a day considering a time period
DURN = DIVIDE([Total Checked Out],[No of days])

- Revenue WoW change % is calculated to get the revenue change percentage week 
Revenue WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Revenue],dim_date[wn]= selv)
var revpw =  CALCULATE([Revenue],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

- Occupancy WoW change % is calculated to get the occupancy change percentage week over week.
Occupancy WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Occupancy %],dim_date[wn]= selv)
var revpw =  CALCULATE([Occupancy %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

- ADR WoW change % is calculated to get the ADR(Average Daily rate) change percentage week over week.
ADR WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([ADR],dim_date[wn]= selv)
var revpw =  CALCULATE([ADR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

- Revpar WoW change %	is calculated to get the RevPar(Revenue Per Available Room) change percentage week over week.
Revpar WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([RevPAR],dim_date[wn]= selv)
var revpw =  CALCULATE([RevPAR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

- Realisation WoW change % is calculated to get the Realisation change percentage week over week.
Realisation WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Realisation %],dim_date[wn]= selv)
var revpw =  CALCULATE([Realisation %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

- DSRN WoW change % is calculated to get the DSRN(Daily Sellable Room Nights) change percentage week over week.
DSRN WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([DSRN],dim_date[wn]= selv)
var revpw =  CALCULATE([DSRN],FILTER(ALL(dim_date),dim_date[wn]= selv-1))
return
DIVIDE(revcw,revpw,0)-1

Overall, this dashboard is effectively set up with multiple slicers, including city, booking platform, month, week number, and booking status. It provides stakeholders with insights into revenue, occupancy rate, and average rating. Additionally, it offers the potential for deeper analysis to identify which booking platforms contribute the most to revenue.

> Page 2: "Other" This page examines the week-over-week changes and the most common room types booked in hotels. It confirms that more expensive rooms like "Elite" generate higher revenue due to their price points. However, the calculation for WoW % changes per category requires further investigation, as the majority of the values are negative, which contradicts the revenue values. This dashboard will be revised at a later date to address the negative values. Potential causes could include missing data, placeholders, issues in the data collection process, or errors in DAX calculations.

