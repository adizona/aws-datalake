# Crawling Partitioned Data with Glue

Discovering data and cataloging metadata about it is a vital step to understand and keep track of what's being written to a data lake. In this article, we will accomplish this using Glue. AWS Glue is a fully managed ETL (extract, transform, and load) service that makes it simple and cost-effective to categorize your data, clean it, enrich it, and move it reliably between various data stores.

## AWS Glue Data Catalog

The AWS Glue Data Catalog is an index to the location, schema, and runtime metrics of your data. Information in the Data Catalog is stored as metadata tables, where each table specifies a single data store. There are multiple ways to add metadata tables to a data catalog but typically, it is done by running a crawler to take inventory of the data in data stores.

## AWS Glue Crawlers

An AWS Glue crawler crawls data stores to look for commonalities and upon completion, creates (or updates, if it's not the first run) one or more tables in the AWS Glue Data Catalog. We can then use Amazon Athena to run queries against these catalog tables. We can also define ETL jobs that use the catalog tables as sources and targets.

For more information on AWS Glue Crawlers, please visit [here](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html).

