## create objects for analytics

```sql
CREATE OR REPLACE STREAM ksqldb_stream_flight_keyed_json
WITH (KAFKA_TOPIC='ksqldb-stream-flight-keyed-json',VALUE_FORMAT='JSON')
AS
SELECT
ROWOFFSET as offset 
,"user_id"
,"uuid"
,"flight"->"airline"
,"flight"->"origin"->"airport" AS origin_airport
,"flight"->"origin"->"iata" AS origin_iata
,"flight"->"origin"->"icao" AS origin_icao
,"flight"->"origin"->"city" AS origin_city
,"flight"->"origin"->"state" AS origin_state
,"flight"->"origin"->"country" AS origin_country
,"flight"->"destination"->"airport" AS dest_airport
,"flight"->"destination"->"iata" AS dest_iata
,"flight"->"destination"->"icao" AS dest_icao
,"flight"->"destination"->"city" AS dest_city
,"flight"->"destination"->"state" AS dest_state
,"flight"->"destination"->"country" AS dest_country
,"flight"->"stops"
,"flight"->"price"
,"dt_current_timestamp"
FROM ksql_stream_flight_event_json 
PARTITION BY ROWOFFSET
EMIT CHANGES;

--query 
select * from ksqldb_stream_flight_keyed_json limit 5; 

```

```sql
CREATE OR REPLACE STREAM ksqldb_stream_user_keyed_json
WITH (KAFKA_TOPIC='ksqldb-stream-user-keyed-json',VALUE_FORMAT='JSON')
AS
SELECT
ROWOFFSET as offset
,"user_id" 
,"uuid" 
,"first_name" 
,"last_name" 
,"date_birth" 
,"phone_number" 
,"city" 
,"web_browser" 
,"last_access_time" 
,"time_zone" 
,"dt_current_timestamp"
FROM ksql_stream_user_event_json
PARTITION BY ROWOFFSET
EMIT CHANGES;

-- query

select * from ksqldb_stream_user_keyed_json limit 30;


```
#### Stream joins

```sql

CREATE OR REPLACE STREAM ksqldb_st_flight_user_analysis_json
WITH (KAFKA_TOPIC='ksqldb-st-flight-user-analysis-json', PARTITIONS=9, VALUE_FORMAT='JSON')
AS
select
FLIGHT.offset as "offset_number"
,USER."user_id" as "user_id_user"
,USER."uuid" as "uuid_user"
,'first_name'
,"date_birth" 
,"phone_number" 
,"city"
,USER."dt_current_timestamp"
,FLIGHT."user_id" as user_id_flight                              
,FLIGHT."uuid" as uuid_flight                         
,'airline'
,ORIGIN_AIRPORT as "origin_airport"              
,ORIGIN_IATA as "origin_iata"                  
,ORIGIN_ICAO as "origin_icao"                 
,ORIGIN_CITY as "origin_city"                 
,ORIGIN_STATE as "origin_state"                 
,ORIGIN_COUNTRY as "origin_country"              
,DEST_AIRPORT as "dest_airport"                
,DEST_IATA as "dest_iata"                   
,DEST_ICAO as "dest_icao"                   
,DEST_CITY as "dest_city"                   
,DEST_STATE as "dest_state"                  
,DEST_COUNTRY as "dest_country"                
,"stops"                        
,"price" as "flight_price"
,FLIGHT."dt_current_timestamp" AS "event_time_flight"
FROM ksqldb_stream_flight_keyed_json as USER
INNER JOIN ksqldb_stream_user_keyed_json as FLIGHT  WITHIN 7 DAY
ON USER.offset = FLIGHT.offset
PARTITION BY FLIGHT.offset
EMIT CHANGES;
```


```sql

select * from KSQLDB_ST_FLIGHT_USER_ANALYSIS_JSON emit changes limit 10;

```
