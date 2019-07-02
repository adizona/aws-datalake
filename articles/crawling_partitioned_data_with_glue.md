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

#### Custom Classifiers

We can define a custom classifier and provide it to our crawler. Custom classifiers can be created using a grok pattern, an XML tag, JavaScript Object Notation (JSON), or comma-separated values (CSV). An AWS Glue crawler calls a custom classifier and if the classifier recognizes the data, it returns the classification and schema of the data to the crawler. We need to define a custom classifier if our data doesn't match any built-in classifiers, or if we want to customize the tables that are created by the crawler. 

For our example, we will define a Custom classifier so that we can provide the crawler with the header of the data being crawled. This will ensure that the metadata table created in the Data Catalog has the column names as defined by GDELT [here](http://data.gdeltproject.org/documentation/GDELT-Data_Format_Codebook.pdf).

#### Define a custom Glue CSV classifier resource

We will use [AWS::Glue::Classifier](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-classifier.html) resource type to define our CSV classifier. Let's configure the [CsvClassifier](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-classifier-csvclassifier.html) object.

1.  **Name:** A unique name for our classifier. We will use **classifier-gdelt-csv**.
1.  **Delimiter:** We will use **\t**. This will indicate that the columns in the data are tab-delimited.
1.  **QuoteSymbol:** We will use **"** (Double Quotes).
1.  **ContainsHeader:** We will use "ABSENT" and define our own.
1.  **Header:** We will use this field to define an array of column names defined by GDELT [here](http://data.gdeltproject.org/documentation/GDELT-Data_Format_Codebook.pdf). The only caveat here is that the column names used here must be unique and must not conflict with the partition names. The partition names we defined in the previous article are year, month and day. For this reason, we will use yearmonthday, yearmonth and yearonly as the column names for the different date fields to avoid conflict.
1.  **DisableValueTrimming:** We will use **true**.
1.  **AllowSingleColumn:** We will use **false**.

### AWS Glue Database

When we create a metadata table (with a crawler or manually), we add it to a database. A database is used to organize tables in AWS Glue Data Catalog. A table can be in only one database at a time.

#### Define a Glue database resource

We will use [AWS::Glue::Database](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-database.html) resource type to define our Glue database.

1.  **CatalogId:** The AWS account ID for the account in which to create the catalog object. We will use the Ref intrinsic function with the AWS::AccountId pseudo parameter.
1.  **DatabaseInput:** The metadata for the database. We will use the [DatabaseInput](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-database-databaseinput.html) object.
    1. **Name:** A unique name for the database. We will use **gdeltdb**.

### AWS IAM Role

Since our crawler needs to perform certain operations such as reading data from our S3 bucket, we need to create an IAM role with the necessary permissions and assign it to the crawler.

#### Define an IAM Role

We will use [AWS::IAM::Role](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-iam-role.html) resource type to define the role for our crawler.

1.  **AssumeRolePolicyDocument:** The json object containing information about what process will be assuming the role.
1.  **ManagedPolicyArns:** A list of AWS-managed policy ARNs that will be assigned to the role. We will use the **AWSGlueServiceRole** and **AmazonS3ReadOnlyAccess** policies.
1.  **RoleName:** A unique name for the role. We will use **gdelt_crawler_role**.

### AWS Glue Crawler

Now that we have all our supporting pieces, let's define our crawler.

#### Define a Glue crawler resource

We will use [AWS::Glue::Crawler](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-glue-crawler.html) resource type to define our Glue crawler.

1.  **Name:** A unique name for our crawler. We will use **gdelt-crawler**.
1.  **Role:** We will use intrinsic function to get the ARN of the role we defined in the same template.
1.  **DatabaseName:** We will use intrinsic function to get the reference of the database we defined in the same template.
1.  **Classifiers:** This field takes an array. We will just populate the array with one element using intrinsic function to get the reference of the classifier we defined in the same template.
1.  **Targets:** A collection of targets to crawl. We will use the [Targets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-crawler-targets.html) object.
    1.  **S3Targets:** Specifies Amazon S3 targets. This field takes an array. We will just populate one element using the [S3Targets](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-properties-glue-crawler-s3target.html).
        1.  **Path:** The path to the Amazon S3 target. We will use our S3 bucket name here that we will provide as a stack parameter.

## Deploying the Crawler using CloudFormation template

Let us now deploy the crawler. We will navigate to the folder where we downloaded the CloudFormation template and execute the below. Please note that your CLI credentials will require permission to deploy CloudFormation templates. For this demo session, I recommend using the AWS-managed AdministratorAccess policy.
```
aws cloudformation deploy --template-file glue_crawler_gdelt.json --stack-name gdelt-crawler-stack --capabilities CAPABILITY_NAMED_IAM --parameter-overrides ParmGDELTBucket=<yournamehere>-gdelt-open-data --region us-east-1
```

### Executing the Crawler

Let us now start the crawler. Please note that your CLI credentials will require the necessary Glue permissions to do this. For this demo session, I recommend using the AWS-managed AdministratorAccess policy.

```
aws glue start-crawler --name gdelt-crawler --region us-east-1
```

### Monitoring the Crawler status

We can check the status of our crawler by executing the following.
```
aws glue get-crawler --name gdelt-crawler --region us-east-1
```
We can use the State field in the returned Crawler object to determine the status. The expected values are the following:
1.  **READY:** The crawler is currently not executing but ready to be started.
1.  **RUNNING:** The crawler is currently executing.
1.  **STOPPING:** The crawler has finished crawling the data and is in the process of wrapping things up.

### Verifying the Paritions

After the crawler is done, we can check to see if the partitions have been detected. Let's execute the following.
```
aws glue get-partitions --database-name gdeltdb --table-name <<yournamehere>>_gdelt_open_data --region us-east-1
```
We should see a Partitions object returned which contains an array of objects where each object represents a partition. The Values array within each partition object indicates the three identifiers for that partition. This verifies that the Glue crawler successfully detected our partitioned data.

## Next Steps

[Next](querying_partitioned_data_with_athena.md), we will learn how to take advantage of these partitions while running queries on the dataset using Amazon Athena.