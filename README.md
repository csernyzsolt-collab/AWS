Data aquisition and processing project

Acquiring taxi usage and weather data data from public datasources, cleaning, transforming for further analizing.

Written in Python, uses the AWS cloud features, as Lambda functions, Glue, Athena, event triggers, S3 data lake.
[image]
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

2. List of the companies ordered by revenues

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

3. List of taxi companies with highest tariffs (specific averange tariff per distance)

SELECT 
  ROUND(AVG(fare)/AVG(trip_miles),1) AS specific_averange_fare_per_distance_per_company, 
  company
FROM 
  fact_taxi_trips 
INNER JOIN 
  dim_company 
ON fact_taxi_trips.company_id=dim_company.company_id 
GROUP BY 
  company
ORDER BY 
  specific_averange_fare_per_distance_per_company DESC
LIMIT 10

[image]

4. List of districts with the highest revenues 

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

5. Pickup and dropoff of the O'Hara district by companies.

SELECT 
    count(*) AS from_OHara_district_trip_number,
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
ORDER BY from_OHara_district_trip_number DESC
LIMIT 10

SELECT 
    count(*) AS to_OHara_district_trip_number,
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
ORDER BY to_OHara_district_trip_number DESC
LIMIT 10

[image]

