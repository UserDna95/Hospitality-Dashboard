# Hospitality-Dashboard

Power BI dashboard analyzing room bookings in different hotels across different cities in India.

Problem Statement: What is causing a drop in market share and revenue for AtliQ Grands across multiple hotels in India?

Step 1) 
Clean and handle null values in Excel 

Step 2)
Modeling in Power BI

![Screen Shot 2025-03-04 at 8 09 38 PM](https://github.com/UserDna95/Hospitality-Dashboard/blob/main/2025-03-04%20(6).png)

Step 3) 
Dashboard in Power BI & Insights

>Page 1, called "Overview" is created using multiple measures:

Revenue = SUM(fact_bookings[revenue_realized])

Total Bookings = COUNT(fact_bookings[booking_id])

Total Capacity = SUM(fact_aggregated_bookings[capacity])

Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])

Occupancy Rate means total successful bookings of total rooms available(capacity) 
= DIVIDE([Total Succesful Bookings],[Total Capacity],0)

Average Rating = AVERAGE(fact_bookings[ratings_given])

No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1

day type = Var wkd = WEEKDAY(dim_date[date],1) return IF(wkd>5,"Weekend","Weekday")

Total canceled bookings 
= CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")

Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])

Total Checked Out = CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")

Total no show bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")

No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])

Booking % by Platform = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(fact_bookings[booking_platform]) ))*100

Booking % by Room class = DIVIDE([Total Bookings], CALCULATE([Total Bookings], ALL(dim_rooms[room_class]) ))*100

Average Daily Rate (ADR) is the ratio of revenue to the total rooms booked/sold in a given period
= DIVIDE( [Revenue], [Total Bookings],0)

Realisation % (successfully "checked out" of room)
= 1 - ([Cancellation %]+[No Show rate %])

RevPAR(Revenue Per Available Room, whether or not they are occupied. RevPAR helps hotels measure their revenue-generating performance to accurately price rooms)
= DIVIDE([Revenue],[Total Capacity])

DBRN(Daily Booked Room Nights, on average, how many rooms are booked for a day considering aperiodd
= DIVIDE([Total Bookings], [No of days])

DSRN(Daily Sellable Room Nights, on average how many rooms are ready to sell for a day considering a period
= DIVIDE([Total Capacity], [No of days])

DURN(Daily Utilized Room Nights, on average how many rooms are successfully utilized by customers for a day considering aperiodd
= DIVIDE([Total Checked Out],[No of days])


>Page 2, called "Week over Week Change" was created using the following measure:

Revenue WoW change % is calculated to get the revenue change percentage week

Revenue WoW Change % = 
VAR CurrentWeek = MAX(dim_date[WeekNumber])
VAR RevenueCurrentWeek = CALCULATE([Total Revenue], dim_date[WeekNumber] = CurrentWeek)
VAR RevenuePreviousWeek = CALCULATE([Total Revenue], FILTER(ALL(dim_date), dim_date[WeekNumber] = CurrentWeek - 1))

VAR IsCurrentValueBlank = ISBLANK(RevenueCurrentWeek)
VAR IsPreviousValueBlank = ISBLANK(RevenuePreviousWeek)

RETURN
IF(
    IsCurrentValueBlank || IsPreviousValueBlank,
    0,
    DIVIDE(RevenueCurrentWeek - RevenuePreviousWeek, RevenuePreviousWeek, 0)
)

• The same measurement was used to create the WOW % change for other values like ADR, DBRN, DSRN, DURN, Occupancy %, Realization %, RevPAR 

![Screen Shot 2025-03-04 at 8 09 38 PM](https://github.com/UserDna95/Hospitality-Dashboard/blob/main/2025-03-04%20(4).png)
![Screen Shot 2025-03-04 at 8 09 38 PM](https://github.com/UserDna95/Hospitality-Dashboard/blob/main/2025-03-04%20(5).png)

This dashboard provides a comprehensive overview of various hotels across different cities, focusing on key metrics such as Occupancy %, Rating, booking platform usage, and total revenue generated from each location. It serves as a valuable tool for deeper analysis, enabling the identification of the most lucrative booking platforms, revenue-generating room types, and the impact of day-of-week trends. Additionally, it highlights rating figures to create better value for customers based on historical trends.
On the second page, the week-over-week changes are examined at a macro level. The most critical number to consider is the negative revenue value, indicating a lack of profitability, which supports the problem statement. Conversely, the daily utilized rooms (DURN) and daily booked rooms (DBRN) have shown positive increases, warranting further analysis to determine the factors behind these spikes despite the negative revenue. Other metrics, such as Occupancy % and RevPar %, remain unchanged, while Realization % has slightly increased, painting a dynamic picture of the potential causes for the drop in market share.

The next steps involve focusing on the factors that led to the increase in DURN, DBRN, and Realization %. Subsequently, it is essential to cross-check the Occupancy % figures to ensure alignment. A thorough analysis of the causes behind the negative revenue, including potential high cancellation rates or low ratings across various cities and hotels, will be crucial in addressing the issue.


