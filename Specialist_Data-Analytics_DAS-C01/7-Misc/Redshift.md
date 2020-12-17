# Redshift

https://panoply.io/data-warehouse-guide/redshift-architecture-and-capabilities/

## Stuff I got wrong in the practice exams

- Upload large (e.g., 100GB) data src from S3 to Redshift via `COPY` command
  - Split files so that number of files is a multiple of number of Redshift slices. Compgress files with Guip