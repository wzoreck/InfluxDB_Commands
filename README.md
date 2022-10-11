# Useful commands for InfluxDB

These commands were taken from the [InfluxDB Essentials](https://university.influxdata.com/courses/influxdb-essentials-tutorial/) course.



## About InfluxDB Data Model

- Not a Relational Database

| RDBMS        | InfluxDB        |
| ------------ | --------------- |
| Databases    | Buckets         |
| Tables       | Measurements    |
| Columns      | Tags & Fields   |
| Rows         | Points          |
| Row-oriented | Column-oriented |



### Buckets

- Top level data structure
- Separates data in storage
- Has a retention policy (How long data stay in bucket before it's automatically delete, because you don't want to keep data for a long time in a time series database)
- Accessed with limited scope Tokens



### Points

- Measurements
  - Separate data by type
- Tags
  - Group data
  - Create indexes
  - Can only be strings
  - Optional
- Fields
  - Data types: integers, floats, strings or boolean
  - Not indexed
  - At least one required
  - Missing values don't take up space
- Timestamp
  - You can associate manually or InfluxDB will create automatically



### Implicit Schemas

- You don't have to define your columns and types ahead of time
- You don't have to provide values for all tags or fields
- You can add tags or fields at any time



### Output layout

- Flux queries return a stream of tables
- Column-oriented, rows have one field and one value
- The tables will be dynamically defined by the queried results
- Every table has a unique Group Key
  - Defaults to Measurement + Tag values + Field name
  - Can be redefined with the `group()` function
- Flux functions operate on a stream of tables
  - Aggregations are done per-Series
  - Can be converted to RDBMS-layout with the `pivot()` function







### Authentication with Stored Credentials

- Show active credentials
  - `influx config`

- Add your credentials
  - `influx config create -n "myauth" -u "host url" -o "org name" -t  "token"`
- List stored credentials
  - `influx config list`

- Switch active credentials
  - `influx config credential_name` example `influx config myauth`



### Listing resources

- Resource types:
  - Buckets: `influx bucket list`
  - Dashboards: `influx dashboards`
  - Tasks: `influx task list`
  - Telegraf configs: `influx telegrafs`
  - Stacks: `influx stacks`



## Writing Data

How to write data by terminal.

- Create a bucket
  - `influx bucket create -n "bucket_name"`
- From STDIN example
  - `echo "mydata,mytag=foo myfield=1" | influx write --bucket "bucket_name"`
- From a file
  - `echo "mydata,mytag=foo myfield=1" > file_name`
  - `influx write --bucket "bucket_name" --file file_name`
- Formats
  - Defaults to InfluxDB Line Protocol
  - CSV: `influx write --bucket "bucket_name" --file file_name --format csv`



## Querying InfluxDB

- Inline Flux
  - `influx query "buckets()"`
- From a file
  - `echo "buckets()" > file_name.flux`
  - `influx query --file file_name.flux`
- Output
  - Defaults to formatted table layout
  - Annotated CSV: `influx query --file file_name.flux --raw`



## Secret Management

- Secrets are write-only values you can use in Flux
- Can Safely store credentials to remote systems
- Useful for tasks and alerts that make remote API calls
- Useful for connecting to SQL databases with `sql.from()`
- List secret keys
  - `influx secret list`

- Add or update  a secret
  - influx secret update --key "key_name" --value "value"



## Backup & Restore

- Available only in InfluxDB 2.x OSS
- Create a backup
  - `influx backup --bucket "bucket_name" ./backup_folder`
- Restore a backup
  - `influx restore --bucket "bucket_name" -new-bucket "new_bucket_name" ./backup_folder`



