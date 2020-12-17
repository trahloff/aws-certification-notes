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
- Edge case: Narrow Table (many rows, few columns)

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

## Misc

- Block Size 1mb
- Uses Column Compression => No Index or Materialized Views
- COPY Command applies compression
- Compression can not be altered after table is created
- `$ analyze compression` command 

