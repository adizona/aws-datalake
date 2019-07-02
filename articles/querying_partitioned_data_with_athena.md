# Querying Partitioned Data With Athena

After [writing data to our data lake](partitioning_data_on_s3.md) and [cataloging the metadata](crawling_partitioned_data_with_glue.md), the real value is in consuming this data for analytics and BI purposes. Let us do that with Amazon Athena.

Amazon Athena is an interactive query service that makes it easy to analyze data in Amazon S3 directly using standard SQL. Athena is serverless, so there is no infrastructure to set up or manage, and you pay only for the queries you run. Athena scales automatically—executing queries in parallel—so results are fast, even with large datasets and complex queries.

For more information on Amazon Athena, please visit [here](https://docs.aws.amazon.com/athena/latest/ug/what-is.html).

## Accessing the Glue Data Catalog

When AWS Glue creates a table, it registers it in its own AWS Glue Data Catalog. Athena uses the AWS Glue Data Catalog to store and retrieve this metadata, using it when we run queries to analyze the underlying dataset.

For more information on this and to know more about Athena's way of handling tables, databases and its own internal data catalog, please visit [here](https://docs.aws.amazon.com/athena/latest/ug/understanding-tables-databases-and-the-data-catalog.html).

## Querying GDELT Data

Let us get started.

### Opening the Amazon Athena console

1.  After signing in to the AWS console, verify that we are in us-east-1 by using the region menu on the top right and clicking **US East (N. Virginia)**.
1.  We start by clicking the **Services** menu on the top-left. 
1.  Under **Analytics**, let us click **Athena**.
1.  This should take us to the **Query Editor**. If not, we can do so by clicking **Query Editor** on the sub-menu on the top-left.

### Running our first query against the partitioned data

1.  On the left, let us select gdeltdb as the Database. This should show us the table (<<yournamehere>>_gdelt_open_data) that the Glue crawler created.
1.  In the New Query 1 sub-window, let us type **SELECT * FROM "gdeltdb"."yournamehere_gdelt_open_data" WHERE year = '2018' and month = '01' and day = '10';** and click **Run Query**.

We can see that this query scanned about 72 MB of data to get the results. Now let's see what happens when we run the same query without using the partitioning information.

### Querying without using partitioning

1.  Let's click the **+** sign next to New Query 1 tab to open a new query sub-window.
1.  In the New Query 2 sub-window, let us type **SELECT * FROM "gdeltdb"."yournamehere_gdelt_open_data" WHERE yearmonthday = 20180110;** and click **Run Query**.

We can see that this query scanned about 1.41 GB of data to get the results. This is because it had to read the entire dataset to determine the records that match the date mentioned in the query.