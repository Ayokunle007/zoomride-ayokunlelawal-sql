# QUERIES

-- Q1:Total Number of Rows in Trips table
SELECT COUNT(*) AS Number_of_Rows FROM trips;

-- Q2:FIVE (5) Longest Completed Trips
SELECT 
    trip_id, 
    city, 
    distance_km, 
    fare 
FROM trips 
WHERE status = 'completed'
ORDER BY distance_km DESC
LIMIT 5;

-- Q3:Total Number of Trips in Each city
SELECT city, COUNT(*) AS total_trips FROM trips
GROUP BY city;
-- The query reveals inconsistent city representations, resulting in duplicate entries for Lagos, Kampala, and Port Harcourt.
-- There are naming and spelling inconsistencies, such as Port-Harcourt, PH, Kampla and Nairobbi.
-- There are also leading/trailing whitespace issues affecting cities such as Lagos and Accra.

-- Q4a: Identifying Duplicate Trips
SELECT 
    customer_id,
    driver_id,
    trip_date,
    fare,
    MIN(trip_id) AS first_trip_id,
    MAX(trip_id) AS later_trip_id
FROM trips
GROUP BY customer_id, driver_id, trip_date, fare
HAVING COUNT(*) > 1;

-- Q4b: Completed Trips with Missing fare
SELECT trip_id, status, fare
FROM trips
where status = 'Completed' and fare IS NULL;

-- Q5: Data Cleaning
UPDATE trips set city = 'Nairobi' where city = 'Nairobbi';
UPDATE trips set city = 'Kampala' where city = 'Kampla';
UPDATE trips set city = 'Port Harcourt' where city IN('PH','Port-Harcourt');
UPDATE trips set city = TRIM(city);
DELETE FROM trips WHERE trip_id IN (299, 300);

-- Verify the changes

-- Q3: Total Number of Trips in Each City After Cleanup
SELECT city, COUNT(*) AS total
FROM trips
GROUP BY city;

-- Q1: Total Number of Rows After Cleanup
SELECT COUNT(*) AS Number_of_Rows
FROM trips;

-- Q6: Revenue By City
SELECT 
    city, 
    COUNT(*) AS number_of_trips, 
    SUM(fare) AS revenue, 
    ROUND(AVG(fare), 2) AS average_fare
FROM trips
WHERE status = 'Completed'
GROUP BY city
ORDER BY revenue DESC;

-- Q7: Revenue By Month
SELECT 
    DATE_FORMAT(trip_date, '%Y-%m') AS month, 
    COUNT(*) AS number_of_trips, 
    SUM(fare) AS revenue
FROM trips
WHERE status = 'Completed'
GROUP BY month
ORDER BY month ASC;
-- December 2025 is the best month for ZoomRide with a total of 31 completed trips and a revenue of 66,980

-- Q8: Revenue By Vehicle Type
SELECT
    d.vehicle_type, 
    COUNT(*) AS number_of_trips,
    SUM(t.fare) AS revenue 
FROM trips t
INNER JOIN drivers d
ON t.driver_id = d.driver_id
WHERE t.status = 'Completed'
GROUP BY d.vehicle_type
ORDER BY revenue DESC;

-- Q9: Customers that have never booked trips
SELECT 
    a.customer_id,
    a.customer_name
FROM customers a
LEFT JOIN trips b
ON b.customer_id = a.customer_id
WHERE b.customer_id IS NULL;

-- Q10: Top 3 customers by total spend
SELECT 
  a.customer_name, 
  sum(b.fare) AS total_spend
FROM trips b
INNER JOIN customers a
ON b.customer_id = a.customer_id
GROUP BY a.customer_id, a.customer_name
ORDER BY total_spend DESC
LIMIT 3;

# ANSWER SHEET
| Question |                 Answer / Key Numbers                     |
| -------- | -------------------------------------------------------- |
| **Q1**   | 298 total trips after cleaning                           |

| **Q2**   | (trip_id=250, dist= 35.9km), (trip_id=225, dist= 33.4km),
             (trip_id=126, dist= 33.1km), (trip_id=98, dist= 32.8km),
             (trip_id=151, dist= 31.3km)
             
| **Q3**   | Lagos: 105, Accra: 44, Port Harcourt: 40, Abuja: 40,     |
             Nairobi: 38, Kampala: 31
             
| **Q4a**  | Duplicate trips identified: IDs 82/299 and 253/300       |

| **Q4b**  | Trip IDs: 29, 54, 102, 124, 130, 175, 213, 230 and 252   |

| **Q5**   | City names cleaned and duplicate trips 299 & 300 removed |

| **Q6**   | Lagos had the highest recorded revenue: ₦218,890,        |
             with total completed trips: 93
             
| **Q7**   | December 2025 was the best month with revenue: 66,980    |

| **Q8**   | Economy, 121 completed trips, revenue: 262,550           |
             Comfort, 76 completed trips, revenue:  239,050
             Bike, 51 completed trips, revenue: 66,870  
             
| **Q9**   | customer_id:  07    customer_name:  Bisi Ogunleye        |
             customer_id:  23    customer_name:  Wanjiru Kamau 
             customer_id:  29    customer_name:  Akinyi Ouma  
             customer_id:  36    customer_name:  Nakato Namutebi     
             
| **Q10** | customer_name: Chioma Nwosu  total_spend: 38,950          |
            customer_name: Tunde Bakare  total_spend: 37,610  
            customer_name: Zainab Garba  total_spend: 35,380  


