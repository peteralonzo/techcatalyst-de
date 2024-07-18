# Peter Alonzo: Week 5 ETL Project

### **ETL Process Diagram**
![ETL Process Diagram](images/ETL_Diagram.PNG)

### **Data Cleaning**
My solution architecture involved initially extracting all the raw data located in S3 and moving it to Databricks as Spark dataframes. In Databricks, I was able to transform the song/log data into a star schema:

Fact Table: Song Plays \
Dimension Tables: Songs, Users, Times, Artists

This process involved selecting certain columns, transforming data types, and creating new columns to add more information to dimension tables. These five Spark dataframes were then loaded to S3 as partitioned/non-partitioned parquet files.

To write, I needed to use the following cell with my AWS credentials (has been removed from dbc file):

```spark
spark.conf.set("fs.s3a.access.key", "<my_access_key>")
spark.conf.set("fs.s3a.secret.key", "<my_secret_key>")
```

### **Data Validation**
To verify the accuracy of my data transformations, AWS Glue and AWS Athena can be used to read the partitioned data. Although this process didn't work due to Databricks exportation issues, this is a great way to verify results.

### **SQL Querying**
Snowflake was used to query the result data and verify its accuracy. Here are the DDL scripts for the tables I created:

#### Songs
```sql
CREATE OR REPLACE TRANSIENT TABLE TECHCATALYST_DE.PALONZO.SONGS_DIM_TEMP (
    SONG_ID STRING,
    TITLE STRING,
    YEAR STRING,
    ARTIST_ID STRING,
    DURATION FLOAT,
    PARTITION_YEAR STRING,
    PARTITION_ARTIST_ID STRING
);
```
#### Users
```sql
CREATE OR REPLACE TRANSIENT TABLE TECHCATALYST_DE.PALONZO.USER_DIM_TEMP (
    USERID STRING,
    FIRSTNAME STRING,
    LASTNAME STRING,
    GENDER STRING,
    LEVEL STRING
);
```
#### Times
```sql
CREATE OR REPLACE TRANSIENT TABLE TECHCATALYST_DE.PALONZO.TIME_DIM_TEMP (
    TS BIGINT,
    DATETIME STRING, 
    START_TIME STRING,
    YEAR STRING,
    MONTH STRING,
    DAYOFMONTH INT,
    WEEKOFYEAR INT,
    PARTITION_YEAR STRING,
    PARTITION_MONTH STRING
);
```
#### Artists
```sql
CREATE OR REPLACE TRANSIENT TABLE TECHCATALYST_DE.PALONZO.ARTIST_DIM_TEMP (
    ARTIST_ID STRING,
    ARTIST_NAME STRING,
    ARTIST_LOCATION STRING, 
    ARTIST_LATITUDE DOUBLE,
    ARTIST_LONGITUDE DOUBLE
);
```
#### Song Plays
```sql
CREATE OR REPLACE TRANSIENT TABLE TECHCATALYST_DE.PALONZO.SONGPLAYS_FACT_TEMP (
    SONGPLAY_ID INTEGER,
    TS BIGINT,
    USERID STRING,
    LEVEL STRING,
    SONG_ID STRING,
    ARTIST_ID STRING,
    SESSIONID INTEGER,
    LOCATION STRING,
    USERAGENT STRING
);
```
