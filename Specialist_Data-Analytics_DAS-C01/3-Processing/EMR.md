# EMR

https://tutorialsdojo.com/amazon-emr/
https://jayendrapatil.com/aws-emr-certification/

## Misc

- **S3DistCP** - Copy large amounts of data S3 <=> HDFS (x-acc, x-buckets)
- **Sqoop** - Same stuff as S3DistCP but for relational DBs
- Notebooks can only be opened after AWS login
- Deleting the EMR Cluster will delete the EBS volumes. Copy data to S3 to preserve it
- Pig doesn't support JDBC access
- Amazon EMR writes **step, bootstrap action and state logs** to **the master node**
- Apache Hadoop writes logs for jobs, tasks and task attempts
- EC2 types
  - **Master** not much processing
  - **Core** both processing & storage
  - **Task** only processing
- EMRFS allows you to define **retry rules** for **processing inconsistencies** 

