# QuickSight

- Data Sets are imported into SPICE (not free)
- Anti Patterns
  - ETL
  - Highly formatted reports
- Connection to Redshift: Add Quicksight IP to Redshift SGs
- You can use scheduled lambda instead of kinesis firehose to pipe data into ElasticSearch

## Security

- MFA
- VPC connectivity (add QuickSight IP to DB SGs)
- Private VPC access via ENI, Direct Connect, VPN, etc
- User Mgmt
  - User defined via IAM or email signup
  - AD Integration
- Encryption at rest only with enterprise edition
- Authorize Quicksight so use S3 bucket via Athena Table: Authorize in Quicksight console access to bucket

#### Row Level Security (!!!!)

1. The permissions dataset can’t contain duplicate values. **Duplicates are ignored** when evaluating how to apply the rules.

2. If you use the rules to **grant access**, the following rules apply:

   - Each user or group specified can **see only the rows** that **match the field** values in the dataset rules.

   - If you **add a rule** for a user or group and **leave all other columns with no value (NULL)**, you **grant them access to all the data**.

   - If you **don’t add a rule** for a user or group, that user or group **can’t see any of the data**.

3. If you use the rules to **deny access,** the following rules apply:

   - Each user or group specified can **see only the rows** that **don’t match the field** values in the dataset rules.

   - If you **add a rule** for a user or group and **leave all other columns with no value (NULL)**, you **deny them access to all the data**.

   - If you **don’t add a rule** for a user or group, they are denied nothing—in other words, they **can see all the data**.

4. The full set of rule records that are applied per user must not exceed 999. This applies to the total number of rules that are directly assigned to a user name plus any rules that are assigned to the user through group names.



## Visual Types

- AutoGraph (Quicksight will figure it out)
- Bar - Comparison/Distribution (Histograms)
- Line - Changes over time
- Scatter/Heat - Correlation
- Pie/Tree - Aggregation
- Pivot - Tabular



## Machine Learning Insights

- Anomaly Detection (RANDOM CUT FORREST)
- Forecasting (also RANDOM CUT FORREST)
  - excludes outliers
  - Detects trends
- Autonarratives (writes plain english from data)
- Sugested Inishgts

