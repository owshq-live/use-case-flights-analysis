# KSQLDB ~ SQL Streaming for Apache Kafka

## install ksqldb
```sh


BROKER_IP=167.99.20.155:9094
kcat -C -b $BROKER_IP -t src-app-flight-events-json -J | jq


# get info
kubens processing
kgpw
```

### working with ksqldb
```sh
# access ksqldb server
KSQLDB=ksqldb-server-9597d9d88-jlvnv
k exec $KSQLDB -n processing -i -t -- bash ksql

# set latest offset read
SET 'auto.offset.reset' = 'earliest';
SET 'auto.offset.reset' = 'latest';

# show info
SHOW TOPICS;
SHOW STREAMS;
SHOW TABLES;
SHOW QUERIES;
```


### housekeeping
```sh
# drop enriched stream
SHOW QUERIES;
TERMINATE QUERY_ID;

PRINT 'src-app-user-events-json' FROM BEGINNING;
PRINT 'src-app-flight-events-json' FROM BEGINNING;
```

# create objects

```sql
-- create streaming user table

CREATE OR REPLACE STREAM ksql_stream_user_event_json
(   
    "user_id" INT
    ,"uuid" VARCHAR
    ,"first_name" VARCHAR
    ,"last_name" VARCHAR
    ,"date_birth" VARCHAR
    ,"phone_number" VARCHAR
    ,"city" VARCHAR
    ,"web_browser" VARCHAR
    ,"last_access_time" VARCHAR
    ,"time_zone" VARCHAR
    ,"dt_current_timestamp" VARCHAR
)
WITH (KAFKA_TOPIC='src-app-user-events-json', VALUE_FORMAT='JSON');


-- create streaming flight table
CREATE OR REPLACE STREAM ksql_stream_flight_event_json
(
"user_id" INT
,"uuid" VARCHAR
,"flight" STRUCT<"airline" VARCHAR,
                 "origin" STRUCT<"airport" VARCHAR,
                                 "iata" VARCHAR,
                                 "icao" VARCHAR,
                                 "city" VARCHAR,
                                 "state" VARCHAR,
                                 "country" VARCHAR>
                ,"destination" STRUCT<"airport" VARCHAR,
                                  "iata" VARCHAR,
                                  "icao" VARCHAR,
                                  "city" VARCHAR,
                                  "state" VARCHAR,
                                  "country" VARCHAR>
               ,"stops" VARCHAR
               ,"price" VARCHAR>
,"dt_current_timestamp" VARCHAR
)
WITH (KAFKA_TOPIC='src-app-flight-events-json', VALUE_FORMAT='JSON');
```

```sql
-- query flight stream
SELECT
ROWOFFSET  
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
,"dt_current_timestamp
FROM ksql_stream_flight_event_json 
EMIT CHANGES LIMIT 10;
```

```sql
-- query user stream
SELECT
ROWOFFSET
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
EMIT CHANGES LIMIT 10;

```


```sh

# drop streams
DROP STREAM KSQL_STREAM_FLIGHT_EVENT_JSON;                               
DROP STREAM KSQL_STREAM_USER_EVENT_JSON;                                
```


