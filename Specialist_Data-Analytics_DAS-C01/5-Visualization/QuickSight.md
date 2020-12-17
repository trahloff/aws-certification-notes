# QuickSight

- Data Sets are imported into SPICE (not free)
- Anti Patterns
  - ETL
  - Highly formatted reports

## Security

- MFA
- VPC connectivity (add QuickSight IP to DB SGs)
- Row level security 
- Private VPC access via ENI, Direct Connect, VPN, etc
- User Mgmt
  - User defined via IAM or email signup
  - AD Integration
- Encryption at rest only with enterprise edition



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

