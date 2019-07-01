# Crawling Partitioned Data with Glue

Discovering data and cataloging metadata about it is a vital step to understand and keep track of what's being written to a data lake. In this article, we will accomplish this using Glue. AWS Glue is a fully managed ETL (extract, transform, and load) service that makes it simple and cost-effective to categorize your data, clean it, enrich it, and move it reliably between various data stores.

## AWS Glue Data Catalog

The AWS Glue Data Catalog is an index to the location, schema, and runtime metrics of your data. Information in the Data Catalog is stored as metadata tables, where each table specifies a single data store. There are multiple ways to add metadata tables to a data catalog but typically, it is done by running a crawler to take inventory of the data in data stores.

## AWS Glue Crawlers

An AWS Glue Crawler crawls data stores to look for commonalities and upon completion, creates (or updates, if it's not the first run) one or more tables in the AWS Glue Data Catalog. We can then use Amazon Athena to run queries against these catalog tables. We can also define ETL jobs that use the catalog tables as sources and targets.

### Partitioned S3 Data

AWS Glue Crawlers can crawl both S3 and DynamoDB data stores. We will be using S3 as our data store, as a follow up to my [previous article](partitioning_data_on_s3.md). The output of the crawler is one or more metadata tables in the AWS Glue Data Catalog. If all the crawled files on S3 have the same schema, the crawler creates one such table. When the Crawler detects multiple folders in a bucket, it determines the root of a table in the folder structure and determines which folders are partitions of a table. When the majority of schemas at a folder level are similar, the crawler creates partitions of a table instead of two separate tables.

For more information on AWS Glue Crawlers, please visit [here](https://docs.aws.amazon.com/glue/latest/dg/add-crawler.html).

## Defining a Crawler using CloudFormation

We will use the CloudFormation template found [here](../files/templates/glue_crawler_gdelt.json) to define our Crawler and all its supporting pieces. Let's now investigate each resource defined in the template.

### AWS Glue Classifiers

One of the first actions a Crawler takes, as it interrogates a data store, is to classify the data to determine the format, schema, and all associated properties. AWS Glue uses classifiers for this task. A classifier tries to recognize the format of the data. The classifier then returns a certainty number to indicate how certain the format recognition was.

AWS Glue provides a set of built-in classifiers, but we can also create custom classifiers. AWS Glue invokes custom classifiers first, in the order that we specify in our crawler definition. Depending on the results that are returned from custom classifiers, AWS Glue might also invoke built-in classifiers. If a classifier returns certainty=1.0 during processing, it indicates that it's 100 percent certain that it can create the correct schema. AWS Glue then uses the output of that classifier.

For more information on AWS Glue Classifiers, please visit [here](https://docs.aws.amazon.com/glue/latest/dg/add-classifier.html).