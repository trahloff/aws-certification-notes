# DynamoDB

## Indexes

### Local Secondary Index (LSI)

- Used to make queries against one additional column
- Creates alternate range key
- Limitations
  - Must be created at table creation time
  - Max 5 LSIs per table
  - Sort Key consists of exactly one scalar attribute
  - attribute chosen must be a scalar String, Number or Binary

### Global Secondary Index (GSI)

- Used to speed up queries on non-key attributes
- Creates new table with projected attributes
  - KEYS_ONLY
  - INCLUDE
  - ALL
- => Must define RCU/WCU
- => Can create GSI after table creation



## Partitions

- Limits per Partition
  - Max 3000 RCU / 1000 WCU
  - Max of 10GB
- Calculating number of Partitions
  1. CAPACITY = ($TOTAL_RCU / 3000) + ($TOTAL_WCU / 1000)
  2. SIZE = $TOTAL_SIZE / 10
  3. NUM_PARTITIONS = CEILING( MAX( $CAPACITY, $SIZE ) )
- Distribution via hash of partition key
- RCU & WCU will be spread evenly between partitions => each partition will get the same RCU & WCU

## Throughput 

### Read - RCU

> 1 RCU = 1 **strongly consistent** R/s for an item up to 4kb

> 1 RCU = 2 **eventually consistent** R/s for an item up to 4kb



#### Examples

1. 10 **strongly** consistent writes per seconds, each 4kb => 10 * 4kb/4kb = 10WCU
2. 16 **eventually** consistent writes per seconds, each 12kb => (16/2) * (12kb/4kb) = 8 * 3 = 24WCU
3. 10 **strongly** consistent writes per seconds, each 6kb => 10 * 6kb/4kb = 10 * 8kb/4kb = 10 * 2 = 20WCU

### Write - WCU

> 1 WCU = 1 W/s for an item up to 1kb

#### Examples

1. 10 objects per seconds, each 2kb => 10 * 2kb = 20WCU
2. 6 objects per second, each 4.5kb => 6 * 5kb = 30WCU
3. 120 objects per minute, each 2kb => 120/60 * 2kb = 2WCU



### Throttling

- `ProvisionedThroughputExceededException`
- Solutions
  - Exponential Backoff
  - Distribute Partition Key as much as possible
  - Kill it with money => DAX
  - (Serverless is also a thing)



## Misc

- Max row size: 400kb

## Got Wrong in Tests

- "Maximum number of fields that can make a primary key?"

  -  2 (partition + sort key)





