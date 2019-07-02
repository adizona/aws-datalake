# Querying Partitioned Data With Athena

After [writing data to our data lake](partitioning_data_on_s3.md) and [cataloging the metadata](crawling_partitioned_data_with_glue.md), the real value is in consuming this data for analytics and BI purposes. Let us do that with Amazon Athena.

Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 directly using standard SQL. Athena is serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run. Athena scales automatically—executing queries in parallel—so results are fast, even with large datasets and complex queries.

For more information on Amazon Athena, please visit [here](https://docs.aws.amazon.com/athena/latest/ug/what-is.html).