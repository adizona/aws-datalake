# Partitioning Data on S3




## Partition Table

Year | Month | Day
------------ | ------------- | -------------
2017 | May | 01
| June | 20
| August | 15
2018 | September | 23



1. Start by creating an S3 bucket in your AWS account. I would recommend to create it in us-east-1 since the source GDELT data is in us-east-1 and would be fast to copy over.
    ```
    aws s3 mb s3://<yournamehere>-gdelt-open-data --region us-east-1
    ```
1. Copy multiple randomly selected files from the publicly available gdelt-open-data bucket to the newly created bucket in your AWS account. Modify the destination path to include partitioning information.
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
        ```eiifcckjbeikijfbvidenhvbkukfghrjkrkgvbrftdnu
        
        ```
        aws s3 cp s3://gdelt-open-data/events/20190610.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=06/day=10/export.csv
        ```
        ```
        aws s3 cp s3://gdelt-open-data/events/20190621.export.csv s3://<yournamehere>-gdelt-open-data/year=2019/month=06/day=21/export.csv
        ```

We now have data stored in our S3 bucket with partitioning enabled.