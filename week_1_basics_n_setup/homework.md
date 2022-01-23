## Week 1 Homework

In this homework we'll prepare the environment 
and practice with terraform and SQL

## Question 1. Google Cloud SDK

Install Google Cloud SDK. What's the version you have? 

To get the version, run `gcloud --version`

## Google Cloud account 

Create an account in Google Cloud and create a project.


## Question 2. Terraform 

Now install terraform and go to the terraform directory (`week_1_basics_n_setup/1_terraform_gcp/terraform`)

After that, run

* `terraform init`
* `terraform plan`
* `terraform apply` 

Apply the plan and copy the output (after running `apply`) to the form.

It should be the entire output - from the moment you typed `terraform init` to the very end.

## Prepare Postgres 

Run Postgres and load data as shown in the videos

We'll use the yellow taxi trips from January 2021:

```bash
wget https://s3.amazonaws.com/nyc-tlc/trip+data/yellow_tripdata_2021-01.csv
```

You will also need the dataset with zones:

```bash 
wget https://s3.amazonaws.com/nyc-tlc/misc/taxi+_zone_lookup.csv
```

Download this data and put it to Postgres

## Question 3. Count records 

How many taxi trips were there on January 15?

Consider only trips that started on January 15.

```sql
select COUNT(*) from yellow_taxi_trips where date(tpep_pickup_datetime) ='2021-01-15';
+---------+
| count   |
|---------|
| 53024   |
+---------+
```

## Question 4. Largest tip for each day

Find the largest tip for each day. 
On which day it was the largest tip in January?

Use the pick up time for your calculations.

(note: it's not a typo, it's "tip", not "trip")

```sql
select date(tpep_dropoff_datetime), max(tip_amount) max_tip from yellow_taxi_trips group by date(tpep_dropoff_datetime) order by max_tip desc;
+------------+-----------+
| date       | max_tip   |
|------------+-----------|
| 2021-01-20 | 1140.44   |
| 2021-01-04 | 696.48    |
| 2021-01-03 | 369.4     |
| 2021-01-26 | 250.0     |
| 2021-01-09 | 230.0     |
| 2021-01-19 | 200.8     |
| 2021-01-30 | 199.12    |
| 2021-01-12 | 192.61    |
| 2021-01-21 | 166.0     |
| 2021-01-01 | 158.0     |
| 2021-01-05 | 151.0     |
| 2021-01-11 | 145.0     |
| 2021-01-24 | 122.0     |
| 2021-01-02 | 109.15    |
| 2021-01-31 | 108.5     |
| 2021-01-25 | 100.16    |
| 2021-01-08 | 100.0     |
| 2021-01-23 | 100.0     |
| 2021-01-16 | 100.0     |
| 2021-01-13 | 100.0     |
| 2021-01-27 | 100.0     |
| 2021-01-06 | 100.0     |
| 2021-02-01 | 100.0     |
| 2021-01-15 | 99.0      |
| 2021-01-14 | 95.0      |
| 2021-01-07 | 95.0      |
| 2021-01-22 | 92.55     |
| 2021-01-10 | 91.0      |
| 2021-01-18 | 90.0      |
| 2021-01-28 | 77.14     |
| 2021-01-29 | 75.0      |
| 2021-01-17 | 65.0      |
| 2020-12-31 | 4.08      |
| 2021-02-22 | 1.76      |
| 2020-10-13 | 1.0       |
| 2008-12-31 | 0.0       |
| 2009-01-01 | 0.0       |
+------------+-----------+
```


## Question 5. Most popular destination

What was the most popular destination for passengers picked up 
in central park on January 14?

Use the pick up time for your calculations.

Enter the zone name (not id). If the zone name is unknown (missing), write "Unknown" 

```sql
WITH tmp AS (
     SELECT locationid AS centralparkid
     FROM ny_zones
     WHERE zone = 'Central Park'
 )
 SELECT dolocationid DestinationID, COUNT(dolocationid) Visitors
 FROM yellow_taxi_trips,tmp
 WHERE pulocationid = tmp.centralparkid AND date(tpep_pickup_datetime) = '2021-01-14'
 GROUP BY DestinationID
 ORDER BY visitors desc;

+-----------------+------------+
| destinationid   | visitors   |
|-----------------+------------|
| 237             | 97         |
| 236             | 94         |
| 142             | 83         |

```

The final answer becomes Upper East Side South:

```sql
select zone, borough from ny_zones where locationid = 237
+-----------------------+-----------+
| zone                  | borough   |
|-----------------------+-----------|
| Upper East Side South | Manhattan |
+-----------------------+-----------+

```


## Question 6. Most expensive locations

What's the pickup-dropoff pair with the largest 
average price for a ride (calculated based on `total_amount`)?

Enter two zone names separated by a slash

For example:

"Jamaica Bay / Clinton East"

If any of the zone names are unknown (missing), write "Unknown". For example, "Unknown / Clinton East". 

```sql
 select pulocationid pickup, dolocationid dropoff, avg(total_amount) avg_total_amount
         from yellow_taxi_trips group by pulocationid, dolocationid
         order by avg_total_amount desc limit 1
+----------+-----------+--------------------+
| pickup   | dropoff   | avg_total_amount   |
|----------+-----------+--------------------|
| 4        | 265       | 2292.4             |
+----------+-----------+--------------------+
SELECT 1

```
The final answer becomes Alphabet City / Unknown:

```sql
select locationid, zone, borough from ny_zones where locationid = 4 or locationid = 265                                                                                                    
+--------------+---------------+-----------+
| locationid   | zone          | borough   |
|--------------+---------------+-----------|
| 4            | Alphabet City | Manhattan |
| 265          | <null>        | Unknown   |
+--------------+---------------+-----------+

```
## Submitting the solutions

* Form for submitting: https://forms.gle/yGQrkgRdVbiFs8Vd7
* You can submit your homework multiple times. In this case, only the last submission will be used. 

Deadline: 24 January, 17:00 CET

