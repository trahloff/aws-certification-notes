# Athena

## Security

- Rest
  - SSE-S3
  - SSE-KMS
  - CSE-KMS

## Anti-Patterns

- Visualization &rarr; QuickSight
- ETL &rarr; Glue



## Misc

- Supports Parquet + ORC
- Improve Performance/Cost: JSON to ORC/Parquet
- You will not get charged for failed queries
- You can use **workgroups** to isolate stuff for security or cost constraints
- Workgroups can trigger SNS
- Workgroups "per query data usage" automatically cancels queries
- **external tables** exist and aid partitioning
- **compressing** s3 objects helps