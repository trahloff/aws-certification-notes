# Redshift

## Architecture

- Leader Node (JDBC/ODBC Interface)
- Compute Nodes
  - Node Slices
  - Types:  (XL, 8XL)
    - Dense Storage (DS)
    - Dense Compute (DC)

### **Distribution** (to Node Slices)

>  [pg_class_info_view, svv_class_info]

- AUTO - based on size, Redshift picks one of the below
- EVEN - round robin (no JOINs, clustering not important)
- KEY - basically like DynamoDB partitioning (JOINs based on key, e.g., weather station with many sensors, weather station ID is key, analysis that joins all sensor data)
- ALL - entire table is copied to node (only for slow moving tables)



### Sort Keys

- Basically like an index
- Results in fast range queries
- Examples
  - Queries on recency: Timestamp Sort Key
  - Queries on equality (win === true): Win Sort Key
  - Joins: Join Column as Sort _and_ Distribution Key (&rarr; Sort Merge Join instead of slower Hash Join
- **Types**
  - Single 
  - Compound 
    - Order is important. First Colum == Primary Sort Key
    - Performance can decrease when query only uses secondary sort key
    - Default Type
    - improves compression
  - Interleaved
    - Gives the same weight to each column (like compound but without primary/secondary problem)
    - Disadvantages: increased load + vacuum times, DO NOT USE WITH increasing types (dates, timestamps, IDs)



## Spectrum

- Table from unstructured data  (s3)
- => Run queries against that stuff, join with tables
- Limitless concurrency
- Horizontal Scaling
- Supports Gzip + Snappy



## Import/Export

- Enhanced VPC routing is a thing. No internet, direct COPY/UNLOAD commands (VPCe, NAT-Gs, IGWs, ...)

### Import

> `COPY` command

- Parallel, efficient
- For _external_ data
- For data that is already in Redshift use `INSERT INTO ... SELECT` or `CREATE TABLE AS`
- Sources
  - S3, EMR, DynamoDB, remote hosts (ssh)
  - S3 needs manifest file + iam role
  - Always needs some kind of auth (role, ssh, etc)
- Can decrypt on the fly from S3
- Gzip, lzop, bzip2 compression
- Automatic compression
- **Edge case: Narrow Table** (many rows, few columns)
  - try to load with one single `COPY`
  - Because: otherwise metadata consumes too much space each COPY creates hidden metadata
- Data Pipeline
- Database Migration Service

### Export

> `UNLOAD` command

- Dumps it to S3



## Durability

- Replication within Cluster
- Backup to S3
- Also async s3 x-region sync 
- Automated cluster snapshots. Default retention: 1d, can be extended to 35d (0d turns of Snapshots)
- **Single Node Clusters do not support replication**. Failure &rarr; restore from Snapshot
- **Redshift is single AZ** 

## Scalability

1. A new cluster is created while old one remains available for READs
2. CNAME is flipped to new cluster (couple of minutes downtime)
3. Data is moved in parallel to new compute nodes

- **Concurrency Scaling** 
  - automatically adds READ cappa
  - You can manage via WLM which queries can use concurrency scaling



## Resizing

### Elastic

> **Nodes of same type**

- Cluster down for a few minutes
- Tries to keep connections open (queries might not even drop)
- Limitation: For some node types you can only double or halve 

### Classic

> **Change Node Type and/or number**

- Cluster down for hours/days
- Read-only for the entire time



### Snapshot, Restore, Resize

- Hackaround to make classic resize without days long downtime





## Misc

- Block Size 1mb
- Uses Column Compression => No Index or Materialized Views
- COPY Command applies compression
- Compression can not be altered after table is created
- `$ analyze compression` command 
- **X-Region Snapshot Copies w/ KMS**
  - In Destination Region
    1. Create KMS key
    2. Specify uniquely named **snapshot copy grant**
    3. Specify key id for copy grant
  - In Source Region
    1. Enable copying of snapshot to copy grant
- **DBLINK** connects Redshift to Postgres (copy/sync data)



### VACUUM

> Recovers space from deleted rows

- **FULL** &rarr; Default, resort all rows, reclaims space
- **DELETE ONLY** &rarr; reclaim space from deleted rows, do not sort
- **SORT ONLY** &rarr; do not reclaim space from deleted rows, do sort
- **REINDEX** &rarr; ONLY FOR INTERLEAVED re-analyze distributions of sort keys, performs full VACUUM afterwards



### Workload Management (**WLM**)

> Prioritize short, fast queries vs long ones

#### Automatic WLM

- Up to 8 queues, default 5 queues with even mem alloc
- Large queries &rarr; lower concurrency
- Smaller queries &rarr; higher concurrency
- **Config**
  - Prio (Just assign number)
  - Concurrency scaling mode 
  - User/Query groups
  - Query monitoring rules (Define metric based performance boundaries. For example: rule to kick queries from short query queue when they run longer than 60s if something goes wrong)

#### Manual WLM

- One default queue with concurrency level 5 
- Superuser queue with concurrency level 1
- define up to 8 queue, up to concurrency level 50



#### Short Query Acceleration (SQA)

- Instead of WLM
- Works with
  - `CREATE TABLE AS`
  - `SELECT`
- Short queries run in dedicated space, do not need to wait in queue
- You can config what is "short" (seconds)





### Anti-Patterns

- Small Data Sets
- OLTP
- Unstructured Data (but Spectrum)
- BLOB DATA

