# Partitioning Data on S3

Storing data on S3 is one of the primary tasks as part of building a data lake on AWS. However, partitioning the data at the time of storing it is key to improving performance and reducing costs at the time of running queries on it using Amazon Athena. Athena leverages Hive for [partitioning](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterPartition) data. By partitioning in the right way, we can restrict the amount of data scanned by each query.

To better understand this, let's use a real world example. The [**Global Database of Events, Language and Tone (GDELT)**](https://www.gdeltproject.org/) project monitors the world's broadcast, print, and web news from nearly every corner of every country in over 100 languages and identifies the people, locations, organizations, counts, themes, sources, emotions, quotes, images and events driving our global society every second of every day.

## Exploring GDELT Data

GDELT generates a CSV file everyday and writes it to an S3 bucket. The details can be found [here](https://registry.opendata.aws/gdelt/). Let's now explore this data using AWS CLI. The bucket is in us-east-1 and is publicly available to read. Our CLI credentials will need at least S3 Read access. For this demo session, I recommend using the AWS-managed AdministratorAccess policy.

### List GDELT contents
1. Run the following to list all the contents of the bucket at root.
    ```
        aws s3 ls s3://gdelt-open-data
    ```
    We will notice that there are two sub-folders - events/ and v2/.
1. Let's ignore v2/ and further explore events/.
    ```
        aws s3 ls s3://gdelt-open-data/events/
    ```
    We will notice that there are several csv files with a date stamp in the file name. Each csv file has columns containing the year, month and date information as well. However, there is no partitioning enabled. When a query is run against this dateset in Athena with a WHERE clause filtering the data to say year 2018, the entire dataset has to be scanned to determine records from 2018. As the dataset grows, this can become very inefficient and expensive. Let us now see how to store this data in one of our own buckets with partitioning enabled.


## Store GDELT Data with Partitioning

We will use GDELT data to create our own data lake on Amazon S3 but we will do it with partitioning enabled. To do this, all we have to do is include one or more partition identifiers in the S3 path. Including multiple such identifiers will result in composite partitioning similar to composite indexes in a regular database. We will implement composite partitioning using year, month and day. Please note that these are just examples for learning purposes and that we could choose any partition identifiers that suit our use case. To understand it better, let's do it ourselves.

### Create an S3 bucket
Let us sstart by creating an S3 bucket in our own AWS account. I recommend creating it in us-east-1 since the source GDELT data is in us-east-1 and will be faster to copy it if both the source and destination buckets are in the same region.
```
aws s3 mb s3://<yournamehere>-gdelt-open-data --region us-east-1
```
### Copy GDELT Data
Copy multiple randomly selected files from the publicly available gdelt-open-data bucket to the newly created bucket above. Modify the destination path to include partitioning information.

1. Let's start with data from 2017.
    ```
    aws s3 cp s3://gdelt-open-data/events/20170202.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=02/day=02/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170401.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=04/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170413.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=04/day=13/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170704.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=07/day=04/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170821.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=08/day=21/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171001.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=10/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171031.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=10/day=31/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171201.export.csv s3://<yournamehere>-gdelt-open-data/year=2017/month=12/day=01/export.csv
    ```
1. Let's now copy data from 2018.
    ```
    aws s3 cp s3://gdelt-open-data/events/20180101.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=01/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20180110.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=01/day=10/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20180315.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=03/day=15/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20180317.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=03/day=17/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20180805.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=08/day=05/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20180820.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=08/day=20/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20181125.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=11/day=25/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20181231.export.csv s3://<yournamehere>-gdelt-open-data/year=2018/month=12/day=31/export.csv
    ```
1. Let's now do 2019.
    ```
    aws s3 cp s3://gdelt-open-data/events/20190108.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=01/day=08/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190122.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=01/day=22/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190310.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=03/day=10/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190320.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=03/day=20/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190501.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=05/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190531.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=05/day=31/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190610.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=06/day=10/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20190621.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=06/day=21/export.csv
    ```
We now have GDELT data stored in our S3 bucket with partitioning enabled. Now, while querying with Amazon Athena, only the related partitions is scanned. For example, if we need to query data from May 2018, only the partitions related to that year and month are scanned. Please note, however, that if we need to query data from May of all years, all data will be scanned. This is similar to how a composite index in a database works.

### Partition Table
We now have data stored in our S3 bucket with partitioning enabled as indicated by the table below.

Year | Month | Day
------------ | ------------- | -------------
2017 | February | 02
2017 | April | 01
2017 | April | 13
2017 | July | 04
2017 | August | 21
2017 | October | 01
2017 | October | 31
2017 | December | 01
2018 | January | 01
2018 | January | 10
2018 | March | 15
2018 | March | 17
2018 | August | 05
2018 | August | 20
2018 | November | 25
2018 | December | 31
2019 | January | 08
2019 | January | 22
2019 | March | 10
2019 | March | 20
2019 | May | 01
2019 | May | 31
2019 | June | 10
2019 | June | 21

## Next Steps

[Next](crawling_partitioned_data_with_glue.md), we will learn how to crawl the data we copied to our very own data lake using AWS Glue.