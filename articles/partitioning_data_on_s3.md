# Partitioning Data on S3

1. Start by creating an S3 bucket in your AWS account. I would recommend to create it in us-east-1 since the source GDELT data is in us-east-1 and would be fast to copy over.
    ```
    aws s3 mb s3://<yournamehere>-gdelt-open-data --region us-east-1
    ```
1. Copy multiple randomly selected files from the publicly available gdelt-open-data bucket to the newly created bucket in your AWS account. Modify the destination path to include partitioning information.
    ```
    aws s3 cp s3://gdelt-open-data/events/20170202.export.csv s3://<yournamehere>-gdelt-open-data/events/year=2017/month=02/day=02/export.csv
    ```

We now have data stored in our S3 bucket with partitioning enabled.