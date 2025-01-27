# de-zoomcamp-homework-01
## Natalie Demyanenko
### Question 1
Ran:
```bash
docker run -it python:3.12.8 bash -c "pip --version"
```
Answer: 24.3.1

### Question 2
from `docker-compose.yaml`:
```yaml
services:
  db:
    container_name: postgres
    image: postgres:17-alpine
    environment:
      POSTGRES_USER: 'postgres'
      POSTGRES_PASSWORD: 'postgres'
      POSTGRES_DB: 'ny_taxi'
    ports:
      - '5433:5432'
    volumes:
      - vol-pgdata:/var/lib/postgresql/data

  pgadmin:
    container_name: pgadmin
    image: dpage/pgadmin4:latest
    environment:
      PGADMIN_DEFAULT_EMAIL: "pgadmin@pgadmin.com"
      PGADMIN_DEFAULT_PASSWORD: "pgadmin"
    ports:
      - "8080:80"
    volumes:
      - vol-pgadmin_data:/var/lib/pgadmin  

volumes:
  vol-pgdata:
    name: vol-pgdata
  vol-pgadmin_data:
    name: vol-pgadmin_data
```
Hostname for postgres == the service name, so "db", port to connect is specified as 5432, therefore

- db:5432

### Question 3
Ran:
```sql
WITH distance_grouped AS (
SELECT
	CASE
		WHEN trip_distance <= 1 THEN 'Less than 1 mile'
		WHEN trip_distance <= 3 THEN '1 to 3 miles'
		WHEN trip_distance <= 7 THEN '3 to 7 miles'
		WHEN trip_distance <= 10 THEN '7 to 10 miles'
		ELSE 'over 10 miles'
	END AS trip_length,
	lpep_dropoff_datetime
FROM green_taxi_trips)

SELECT
	trip_length,
	COUNT(*)
FROM distance_grouped
WHERE lpep_dropoff_datetime >= '2019-10-01'
  AND lpep_dropoff_datetime <  '2019-11-01'
GROUP BY trip_length;
```
Answer: 104,802; 198,924; 109,603; 27,678; 35,189

### Question 4
Ran:
```sql
SELECT MAX(trip_distance)
FROM green_taxi_trips
WHERE DATE_TRUNC('day', lpep_dropoff_datetime) = '2019-10-11';
```
for each of the listed dates. Found that 2019-10-11 had the longes trip at 95.78

### Question 5
Ran:
```sql
SELECT
	taxi_zones."Zone",
	COUNT(*) AS num_trips
FROM green_taxi_trips
INNER JOIN taxi_zones
  ON green_taxi_trips."PULocationID" = taxi_zones."LocationID"
WHERE DATE_TRUNC('day', lpep_pickup_datetime) = '2019-10-18'
GROUP BY taxi_zones."Zone"
HAVING SUM(green_taxi_trips.total_amount) > 13000
ORDER BY num_trips DESC;
```
Answer: East Harlem North (1236), East Harlem South (1101), Morningside Heights (764)

### Question 6
Ran:
```sql
SELECT
	tzp."Zone" AS PickupZone,
	tzd."Zone" AS DropoffZone,
	tip_amount
FROM green_taxi_trips
INNER JOIN taxi_zones AS tzp
	ON green_taxi_trips."PULocationID" = tzp."LocationID"
INNER JOIN taxi_zones AS tzd
	ON green_taxi_trips."DOLocationID" = tzd."LocationID"
WHERE (lpep_dropoff_datetime >= '2019-10-01'
	AND lpep_dropoff_datetime < '2019-11-01')
	AND tzp."Zone" = 'East Harlem North'
ORDER BY tip_amount DESC;
```
Answer: JFK Airport (87.3)
