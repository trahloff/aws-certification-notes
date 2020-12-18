# Kinesis (Analytics)

## Input

- **Sources:** 
  - Kinesis Data Streams
  - Kinesis Firehose
- **Output:**
  - Kinesis Data Streams
  - Kinesis Firehose

## Config

- Measured in "KPU"
- **1 KPU == 4GB** Mem + CPU + Networking
- Default Capacity: 32GB (8KPU)

## Trouble Shooting

- **Record arrives late** &rarr; Record is written to Error Stream
- Destination is configured correctly, **no data shows up in destination**
  - IAM Role Issue
  - Mismatched name for output stream
  - Destination service unavailable (down)







## Misc

- Up to **1mb**