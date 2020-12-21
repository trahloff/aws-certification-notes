# Kinesis

## Exam Keys

- **"Async Data" &rArr; KPL Producer**


## Streams

- You can **replay**/reprocess data (not like SQS, in Kinesis data is **only deleted after retention time**, not after read) -> multiple apps can read the same stream
- Append only stream -> once data is written to it, you cannot change it


- Record
  - **Data Blob (up to 1mb)** (>1mb &rarr; MSK Kafka)
  - Record Key (like S3, sharding partitioning based on this one) -> highly distributed to avoid "hot key" problem
  - Sequence number - added by Kinesis after Ingestions, not added by producer

    
# Producers
- Types
	- SDK
	  - "PutRecord(s)" &rarr; always SDK. PutRecords uses Batching (less HTTP requests). If one record fails, only this one will be in retry, batch succeeds
	  - Use-cases: Low Throughput, Higher Latency, Simple Setup, AWS Lambda
	- KPL (more advanced)
	  - c++/java lib
	  - High performance, long running producers
	  - Built-in retry mechanism (ProvisionedThroughputException)
	  - Synchronous or Asynchronous API 
	  - Submits Producer metrics to CloudWatch
	  - **Batching enabled by default**
	    - Collect - Record & Write to multiple shards in same PutRecords API call
	    - Aggregate - Store multiple records in one record -> increase payload size
	    - **RecordMaxBufferedTime** = **100ms** (waits this time window for records to batch)
	  - **Compression** needs to be **implemented by user**
	  - Can **only be read by KCL** or helper library
	- Kinesis Agent (runs on Linux server)
	  - java based
	  - built on top of KPL
	  - log files
	  - write from multiple directories & write to multiple streams
	  - routing based on directory / log file
	  - pre-process data (csv to json)
	- 3rd Party (Kafka Connect, etc)
- Managed AWS Sources for Data Streams
  - CloudWatch Logs
  - AWS IoT
  - Kinesis Data Analytics

# Consumers

- **"Checkpointing" exists (Uses Dynamo)**
- Classic
  - SDK (Pull - GetRecords), Problem: More Consumers, less throughput per consumer
  - KCL
    - Checkpoints via DynamoDB (make sure to provision enough WCU/RCU, otherwise throttle)
    - **THEREFORE NEEDS ACCESS TO DynamoDB** 
  - Connector Library
    - Old Stuff, must run on EC2
    - leverages KCL
    - only used to write from Kinesis to S3, Dynamo, Redshift, Elasticsearch
  - 3rd party (Spark, ...)
  - Firehose/Lambda
  - when to use
    - low number of applications 
    - can tolerate 200ms latency
    - min cost
- **Enhanced Fan Out**
  - KCL 2.0 + Lambda
  - **Uses push (consumer don't need to poll/pull)**
  - uses http2
  - reduced latency (70ms)
  - **Each consumer get's 2mb/s of throughput**
  - default limit of 5 consumers with fan-out per shard



# Scaling

- "Shard Splitting"
  - Old Shard is closed and will be deleted once its data is expired
  - new shards live in the same space
- Shard Merging
  - Group two shards with low traffic
  - Old shards are closed and will be deleted once data is expired
- Limitations
  - **Resharding cannot be done in parallel**
  - &rarr; Only one operation at a time and it takes a few seconds
  - Example: 1000 Shards -> 2000 Shards = 8.3 hours
- Autoscaling
  - not native
  - lambda hacking
- Records could be out of order after resharding when reading child shard before parent shard

# Limits

- **Producers**
	- **1 MB/s or 1000messages/**s write PER SHARD
	- Breach this limit: "ProvisionedThroughputException". Solution Approaches
	  - Retries with Backoff
	  - Increase shards (scaling)
	  - Optimize partition key
- Consumer **Classic**
	- **2 MB/s read or 5 API-calls/s PER SHARD**
- Consumer **Enhanced Fan-Out**
	- **2 MB/s read PER SHARD, PER ENHANCED CONSUMER**
	- **No API Calls (push model)**
- Data **Retention** 
	- default: 24h 
	- max: **7 days**
- Per Shard: 1mb/s write + 2mb/s read + 1000 records per second



# Security

- Auth via IAM policies
- Encr@Trans TLS
- Encr@Rest KMS
- Client-side encr only manually
- There are VPC endpoints for Kinesis



## Firehose

- Not Realtime ("Near-Realtime")
- Compression
  - GZIP, ZIP, SNAPPY S3
  - GZIP Redshift
- auto scaling
- supports KMS encryption of steams
- only pay for data
- **Spark/KCL cannot read from KDF**
- sources
  - kpl
  - kinesis agent
  - kinesis data streams
  - cw logs/events
  - iot rules action
- transformation via lambda
- targets
  - s3
  - redshift
  - es
  - splunk
- source records + Transformation/Delivery failures into different bucket
- **buffer**
  - size (exp 32mb)
  - time (2min)
  - firehose can automatically increae buffer size
- streams vs firehose
  - Custom Code (Producer/consumer) vs fully mgmt/serverless
  - real time vs near real time
  - must manage scaling (shard splitting) vs automated scaling
  - data storage for up to 7 d vs no storage





## Stuff I got wrong in practice exams

- Resolve slow ingestion
  - random partition keys => distribute hash key evenly
  - `UpdateShardCount` API (Combination of PlitShard + MergeShards)
- Mitigate ReadProvisionedThroughputExceede
  - Exponential Backoff