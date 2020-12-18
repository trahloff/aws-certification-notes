### RDS

- Must manage User permissions within database itself (no IAM roles)
- Transparent Data Encryption on top of KMS for Oracle 6 MySQL Server
- IAM auth for **Aurora** Postgres & MySQL





### Glue

- basically everything can be encrypted (yes, even bookmarks)
- can be configured to only use JDBC over TLS
- data catalog resource based policy



### EMR

- EC2 Key Pair SSH 
- EC2 Security Groups
  - One for master node
  - a different one for cluster node (core or task)
- IAM Roles
  - s3 access EMRFS
  - DynamoDB scans w/ Hive
- Auth via AD: Kerberos
- RBAC via Ranger on external EC2 (cannot be setup directly on EMR)
- You can block public access on account level in console. this prevents users from creating public EMR clusters
- EMR Security configuration mainly used for encryption
- you need to re-create cluster to encrypt unencrypted EMR cluster
- There are IAM roles for EMRFS 
  - EMRFS will fallback to EMR svc role => svc role no S3 access, specific bucket level permission in roles for EMRFS that can be assumed by authed users
  - ![image-20201217104923535](C:\Users\trahloff\AppData\Roaming\Typora\typora-user-images\image-20201217104923535.png)

#### Encryption

- EMRFS at Rest
  - SSE-S3, SSE-KMS, CSE, (**NO SSE-C**)
- Local Disks at Rests
  - HDFS encryption
  - EC2 instance store: NVMe or LUKS
  - **EBS: KMS (works with root), LUKS (does not work with root)**
- In Transit
  - Node to node communication
  - EMRFS between S3 and cluster nodes
- per bucket CMK overrides exist within security configuration



### Elasticsearch

- IAM or Cognito auth



### Redshift

- @Rest KMS or HSM (latter needs connection)
- Supports SSE-S3
- external Security Module possible
- LUKS not supported
- Column level grants are a thing



### Misc

- Custom Identity Broker Application &rarr; only if identity provider is not SAML2.0 compatible, does stuff, requests stuff from STS with sudo
- Encrypt big chunk of data: envelope encryption
- CloudHSM not for RDS

