# Partitioning Data on S3

1. Start by creating an S3 bucket in your AWS account. I would recommend to create it in us-east-1 since the source GDELT data is in us-east-1 and would be fast to copy over.
    ```
    aws s3 mb s3://<yournamehere>-gdelt-open-data --region us-east-1
    ```
1. Copy multiple randomly selected files from the publicly available gdelt-open-data bucket to the newly created bucket in your AWS account. Modify the destination path to include partitioning information.
    ```
    aws s3 cp s3://gdelt-open-data/events/20170202.export.csv s3://<yournamehere>-gdelt-open-data/events/year=2017/month=02/day=02/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170401.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=04/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170413.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=04/day=13/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170704.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=07/day=04/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20170821.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=08/day=21/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171001.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=10/day=01/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171031.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=10/day=31/export.csv
    ```
    ```
    aws s3 cp s3://gdelt-open-data/events/20171201.export.csv s3:// <yournamehere>-gdelt-open-data/events/year=2017/month=12/day=01/export.csv
    ```


We now have data stored in our S3 bucket with partitioning enabled.