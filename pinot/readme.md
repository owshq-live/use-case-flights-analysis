# Apache Pinot

## acessing portal

```sh
http://localhost:9000/
http://localhost:9000/help
````

## adding schema

```curl
# http://localhost:9000/help#/Schema/addSchema
# curl -X GET "http://localhost:9000/schemas" -H "accept: application/json" | jq
# curl -X GET "http://localhost:9000/schemas/sch_output_ksqldb_st_flight_user_analysis_json" -H "accept: application/json" | jq
curl -X POST "http://localhost:9000/schemas?override=true" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"schemaName\": \"sch_output_ksqldb_st_flight_user_analysis_json_428d3463\", \"dimensionFieldSpecs\": [ { \"name\": \"offset_number\", \"dataType\": \"LONG\" },{ \"name\": \"user_id_user\", \"dataType\": \"LONG\" }, { \"name\": \"uuid_user\", \"dataType\": \"STRING\" }, { \"name\": \"first_name\", \"dataType\": \"STRING\" }, { \"name\": \"date_birth\", \"dataType\": \"STRING\" }, { \"name\": \"phone_number\", \"dataType\": \"STRING\" }, { \"name\": \"city\", \"dataType\": \"STRING\" }, { \"name\": \"USER_dt_current_timestamp\", \"dataType\": \"STRING\" }, { \"name\": \"USER_ID_FLIGHT\", \"dataType\": \"LONG\" }, { \"name\": \"UUID_FLIGHT\", \"dataType\": \"STRING\" }, { \"name\": \"airline_company\", \"dataType\": \"STRING\" }, { \"name\": \"origin_airport\", \"dataType\": \"STRING\" }, { \"name\": \"origin_iata\", \"dataType\": \"STRING\" }, { \"name\": \"origin_icao\", \"dataType\": \"STRING\" }, { \"name\": \"origin_city\", \"dataType\": \"STRING\" }, { \"name\": \"origin_state\", \"dataType\": \"STRING\" }, { \"name\": \"origin_country\", \"dataType\": \"STRING\" }, { \"name\": \"dest_airport\", \"dataType\": \"STRING\" }, { \"name\": \"dest_iata\", \"dataType\": \"STRING\" }, { \"name\": \"dest_icao\", \"dataType\": \"STRING\" }, { \"name\": \"dest_city\", \"dataType\": \"STRING\" }, { \"name\": \"dest_state\", \"dataType\": \"STRING\" }, { \"name\": \"dest_country\", \"dataType\": \"STRING\" }, { \"name\": \"stops\", \"dataType\": \"STRING\" }, { \"name\": \"flight_price\", \"dataType\": \"STRING\" } ], \"dateTimeFieldSpecs\": [{ \"name\": \"event_time_flight\", \"dataType\": \"STRING\", \"format\": \"1:MILLISECONDS:SIMPLE_DATE_FORMAT:yyyy-MM-dd HH:mm:ss.SSSSSS\", \"granularity\": \"1:DAYS\" }]}"

# http://localhost:9000/help#/Table/addTable
# curl -X GET "http://localhost:9000/tables?sortAsc=true" -H "accept: application/json" | jq
# curl -X GET "http://localhost:9000/tables/realtime_output_faust_enriched_rides_events/status?type=realtime" -H "accept: application/json" | jq
# curl -X GET http://localhost:9000/tables/realtime_output_faust_enriched_rides_events/idealstate?tableType=realtime | jq
curl -X POST "http://localhost:9000/tables" -H "accept: application/json" -H "Content-Type: application/json" -d "{ \"tableName\": \"realtime_output_ksqldb_st_flight_user_analysis_json\", \"tableType\": \"REALTIME\", \"segmentsConfig\": { \"timeColumnName\": \"event_time_flight\", \"timeType\": \"DAYS\", \"retentionTimeUnit\": \"DAYS\", \"retentionTimeValue\": \"60\", \"schemaName\": \"sch_output_ksqldb_st_flight_user_analysis_json_428d3463\", \"replication\": \"1\", \"replicasPerPartition\": \"1\" }, \"tenants\": {}, \"tableIndexConfig\": { \"loadMode\": \"MMAP\", \"invertedIndexColumns\": [ \"offset_number\" ], \"streamConfigs\": { \"streamType\": \"kafka\", \"stream.kafka.consumer.type\": \"lowlevel\", \"stream.kafka.topic.name\": \"output-ksqldb-st-flight-user-analysis-json\", \"stream.kafka.decoder.class.name\": \"org.apache.pinot.plugin.stream.kafka.KafkaJSONMessageDecoder\", \"stream.kafka.consumer.factory.class.name\": \"org.apache.pinot.plugin.stream.kafka20.KafkaConsumerFactory\", \"stream.kafka.broker.list\": \"edh-kafka-brokers.ingestion.svc.Cluster.local:9092\", \"realtime.segment.flush.threshold.time\": \"3600000\", \"realtime.segment.flush.threshold.size\": \"50000\", \"stream.kafka.consumer.prop.auto.offset.reset\": \"smallest\" } }, \"metadata\": { \"customConfigs\": {} }}"
```

## housekeeping

```sh
# table
curl -X DELETE "http://localhost:9000/tables/realtime_output_faust_enriched_rides_events?type=realtime" -H "accept: application/json"

# schema
curl -X DELETE "http://localhost:9000/schemas/sch_output_faust_enriched_rides" -H "accept: application/json"
```
