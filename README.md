![banner](https://github.com/user-attachments/assets/3763e642-e93d-47ac-b0a4-69901ee0c31a)

# Airlines Data Analysis ✈️🧑‍✈️

## Business Problem 💡

Akash (an airline company) has been providing high-quality air transportation service to their clients for several years, ensuring a safe, comfortable, and convenient journey for their 
passengers. They operate a diverse fleet of aircraft, ranging from small business jets to medium-sized machines. However, they are currently facing challenges due to various 
factors such as stricter environmental regulations, higher flight taxes, increased interest rates, rising fuel prices, and a tight labor market leading to higher labor 
costs. These challenges are putting pressure on the company's profitability, and they are actively seeking solutions to address this issue. In order to tackle this 
challenge, the company is planning to analyze their database and identify opportunities to increase the occupancy rate, thereby boosting the average profit earned 
per seat.

## Dataset
Database URL : https://www.kaggle.com/datasets/shahnbej007/travel-sqlite/data

## Data Analysis using Python and SQL

-  How many seats are there in each plane?

        pd.read_sql_query("""select aircraft_code, count(seat_no) as seats
                         from seats group by aircraft_code""", connection)

- How many planes have more than 100 seats?

      pd.read_sql_query("""select aircraft_code, count(seat_no) as seats 
                    from seats group by aircraft_code having seats>100""", connection)

- How the number of tickets booked and total amount earned changed with the time?

      tickets = pd.read_sql_query("""select * from bookings b inner join tickets t on b.book_ref=t.book_ref""",connection)
      tickets['book_date'] = pd.to_datetime(tickets['book_date'])
      tickets['date']=tickets['book_date'].dt.date
      x = tickets.groupby('date')[['ticket_no']].count()
      plt.figure(figsize=(15,5))
      plt.plot(x.index,x['ticket_no'],marker='o')
      plt.xlabel('Date',fontsize=15)
      plt.ylabel('No. of Tickets',fontsize=15)
      plt.grid('b')
      plt.show()

      bookings = pd.read_sql_query("""select * from bookings""",connection)
      bookings['book_date'] = pd.to_datetime(bookings['book_date'])
      bookings['date']=bookings['book_date'].dt.date
      x = bookings.groupby('date')[['total_amount']].sum()
      plt.figure(figsize=(15,5))
      plt.plot(x.index,x['total_amount'],marker='o')
      plt.xlabel('Date',fontsize=15)
      plt.ylabel('Total Amount',fontsize=15)
      plt.grid('b')
      plt.show()

- Calculate the average charges for each aircraft with different fare conditions.

      df = pd.read_sql_query("""select aircraft_code,fare_conditions,round(avg(amount),2) as avg_amount from ticket_flights tf inner join flights f on tf.flight_id=f.flight_id group by aircraft_code,           fare_conditions""",connection)
      sns.barplot(data=df, x='aircraft_code',y='avg_amount',hue='fare_conditions')

- For each aircraft, calculate the total revenue per year and the average revenue per ticket.

      pd.read_sql_query("""select aircraft_code, sum(amount) as total_revenue, (sum(amount)/count(ticket_no)) as avg_revenue_per_ticket
      from flights f inner join ticket_flights tf on f.flight_id=tf.flight_id group by aircraft_code""",connection)

- Calculate the average occupancy per aircraft.

      occupancy_rate = pd.read_sql_query("""select a.aircraft_code, round((avg(booked_seats)/seat_count)*100,2) as occupancy_rate
      from (select s.aircraft_code, count(seat_no) as seat_count
      from seats s group by s.aircraft_code) as a inner join 
      (select f.aircraft_code, f.flight_id, count(bp.seat_no) as booked_seats
      from boarding_passes bp inner join flights f 
      on bp.flight_id=f.flight_id group by f.aircraft_code,f.flight_id) as b
      on a.aircraft_code=b.aircraft_code group by a.aircraft_code""",connection)
      occupancy_rate

- Calculate by how much the total annual turnover could increase by giving all aircraft a 10% higher occupancy rate.

      occupancy_rate['Inc_occupancy_rate'] = occupancy_rate['occupancy_rate']+occupancy_rate['occupancy_rate']*0.1
      total_revenue = pd.read_sql_query("""select aircraft_code, sum(amount) as total_revenue
      from ticket_flights tf inner join flights f on tf.flight_id=f.flight_id group by aircraft_code""",connection)
      occupancy_rate['Inc_in_total_annual_turnover']=((total_revenue['total_revenue']/occupancy_rate['occupancy_rate'])*occupancy_rate['Inc_occupancy_rate'])-total_revenue['total_revenue']
      occupancy_rate

### Conclusion :

- To summarize, analyzing revenue data such as total revenue per year, average revenue per ticket, and average occupancy per aircraft is critical for airlines seeking to maximize profitability.
- Airlines can find areas for improvement and modify their pricing strategies and route plans to increase their profitability.
- A greater occupancy rate is one important feature that can enhance profitability since it allows airlines to maximize revenue while minimizing costs associated with vacant seats.
- The airline should revise the price for each aircraft as very low price and very high price is also a factor that people are not purchasing tickets for some aircrafts. They shold decide a reasonable price according to the condition and facility the aircraft is providing so that it should not be very cheap or very costly.
- Also, profit should not come at the price of consumer satisfaction and safety. So, airlines must strike a balance b/w the necessity for profit and the significance of delivering high-quality service and upholding safety regulations. Balancing these factors is key to long term success in the competitive airline industry.
- Adopting a data-driven approach to revenue analysis and optimization can lead to sustainable growth and success.

    







