# Week 1 Homework

## Daily trip counts

Q: Total number of trips that started (i.e. were picked up) on January 15, 2021?

A: 53024

```sql
SELECT
	day,
	count
FROM
	(SELECT
		CAST(tpep_pickup_datetime AS DATE) as "day",
		COUNT(1)
	FROM
		yellow_taxi_trips t
	GROUP BY
		CAST(tpep_pickup_datetime AS DATE)
	) daily_count_table
WHERE day = '2021-01-15';

```

## Largest tip daily

Q: Which day (based on pickup time) had the largest tip in January?

A: 2021-01-20 ($1140.44!! 0.O)

```sql
SELECT
	CAST(tpep_pickup_datetime AS DATE) as "day",
	max(tip_amount) as max_tip
FROM
	yellow_taxi_trips t
GROUP BY day
ORDER BY max_tip DESC
LIMIT 1;
```

## Most popular destination

Q: Most popular destination for people picked up from central park on January 14?

A: Upper East Side South (388)

```sql
SELECT
	CAST(t.tpep_pickup_datetime AS DATE) as "day",
	zdo."Zone" as "dropoff_loc",
	count(3)
FROM
	yellow_taxi_trips t
	JOIN zones zpu
		ON t."PULocationID" = zpu."LocationID"
	JOIN zones zdo
		ON t."DOLocationID" = zdo."LocationID"
WHERE zpu."Zone" = 'Central Park' AND
	CAST(t.tpep_pickup_datetime AS DATE) = '2021-01-14'
GROUP BY
	day,
	zdo."Zone"
ORDER BY count DESC
LIMIT 1;
```

## Most expensive locations

Q: Pickup-dropoff pair with the largest average price for a ride (based on the total amount)?

A: Alphabet City / Unknown ($2292.40!! 0.O)

```sql
SELECT
	trip,
	AVG(total_amount) as avg_trip_cost
FROM
	(SELECT
 		t.index as index,
		CONCAT(zpu."Zone", ' / ', zdo."Zone") as "trip",
		t.total_amount as "total_amount"
	FROM
		yellow_taxi_trips t,
		zones zpu,
		zones zdo
	WHERE
		t."PULocationID" = zpu."LocationID" AND
		t."DOLocationID" = zdo."LocationID"
	GROUP BY
		t.index,
		"trip",
		"total_amount"
	 ) trip_pairing_table
GROUP BY
	trip
ORDER BY avg_trip_cost DESC
LIMIT 1;
```

