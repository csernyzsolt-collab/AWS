Data aquisition and processing project

Acquiring taxi usage and weather data data from public datasources, cleaning, transforming for further analizing.

Written in Python, uses the AWS cloud features, as Lambda functions, Glue, Athena, event triggers, S3 data lake.
![AWS modules](../../blob/master/public/AWS-side.png)
The connection interface between the cloud elements and the local site is the AWS boto3 client.


Analizing the acquired data:

1. Revenue per payment type:

SELECT 
  ROUND(SUM(fare), 1) AS revenue_per_payment_type, 
  payment_type
FROM 
  fact_taxi_trips
INNER JOIN 
  dim_payment_type 
ON fact_taxi_trips.payment_type_id=dim_payment_type.payment_type_id
GROUP BY 
  payment_type
ORDER BY 
  revenue_per_payment_type DESC

[image]

2. List of the firt ten company ordered by revenues

SELECT 
  ROUND(SUM(fare),1) AS sumfare, 
  company
FROM 
  fact_taxi_trips 
INNER JOIN 
  dim_company 
ON fact_taxi_trips.company_id=dim_company.company_id 
GROUP BY 
  company
ORDER BY 
  sumfare DESC
LIMIT 10

[image]

3. Taxi trips by hours

SELECT COUNT(*) AS trip_by_hour, EXTRACT(HOUR FROM trip_start_timestamp) AS hours
FROM fact_taxi_trips 
GROUP BY EXTRACT(HOUR FROM trip_start_timestamp) 
ORDER by hours


[image]

4. Revenues per first ten area 

SELECT 
    dim_community_areas."community name",
    round(sum(fare),1) as revenue
FROM
    fact_taxi_trips
INNER JOIN
    dim_community_areas
ON dim_community_areas.area_code=fact_taxi_trips.pickup_community_area_id
GROUP BY
    dim_community_areas."community name"
ORDER BY revenue DESC
LIMIT 10

[image]

5. Pickup and dropoff of the O'Hare district by first ten company.

SELECT 
    count(*) AS from_OHare_district_trip_number,
    dim_company.company
FROM
    fact_taxi_trips
INNER JOIN
    dim_community_areas
ON dim_community_areas.area_code=fact_taxi_trips.pickup_community_area_id
INNER JOIN 
    dim_company
ON dim_company.company_id=fact_taxi_trips.company_id
GROUP BY
    dim_company.company, area_code
HAVING area_code=76
ORDER BY from_OHare_district_trip_number DESC
LIMIT 10

SELECT 
    count(*) AS to_OHare_district_trip_number,
    dim_company.company
FROM
    fact_taxi_trips
INNER JOIN
    dim_community_areas
ON dim_community_areas.area_code=fact_taxi_trips.dropoff_community_area_id
INNER JOIN 
    dim_company
ON dim_company.company_id=fact_taxi_trips.company_id
GROUP BY
    dim_company.company, area_code
HAVING area_code=76
ORDER BY to_OHare_district_trip_number DESC
LIMIT 10

[image]

