# GCP Cost Dashboard – BETA Program

This guide explores the newly announced managed connector for Google BigQuery and demonstrates how to build a modern ETL pipeline for GCP Cost with AWS Glue Studio without writing code.


## 	Prerequisites


##### 1. Create a GCP Billing Export:

- One export is related to one billing account id in GCP.

- To setup an export, please follow this documentation: https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup

##### 2. Create a GCP Pricing Export

- When enabling Cloud Billing export, ensure to include Pricing data.

- https://cloud.google.com/billing/docs/how-to/export-data-bigquery-setup#enable-bq-export

##### 3. Configure BigQuery Connection Secret:

- To connect to Google BigQuery from AWS Glue, see and complete points 1-4 in Configuring BigQuery connections. You must create and store your Google Cloud Platform credentials in a Secrets Manager secret, then associate that secret with a Google BigQuery AWS Glue connection.

- When selecting GCP IAM roles for your service account, add or create a role that would grant appropriate permissions to run BigQuery jobs to *both read and write*, and create BigQuery tables in the single billing dataset.

- Video to create the secret: 1:54-2:38 https://youtu.be/n6fkX5LpEYY?t=114

  

At the end of prerequisites, you should have the following values:

|         Value       | Sample                        |
|----------------|-------------------------------|
|1 or more BigQuery billing table|`project.dataset.gcp_billing_export_v1_BILLING_ACCOUNT_ID`|
|1 or more BigQuery pricing table|`project.dataset.cloud_pricing_export`|
|1 Secret in AWS with json key of GCP SA stored in base64 with credentials as key|`bigquery_credentials`|



  

## Infrastructure Deployment Guide

##### 1. Open Cloudformation in your AWS console, in your CID account.

https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks?filteringText=&filteringStatus=active&viewNested=true

##### 2. Select ‘Stack’, then ‘Create Stack’ and finally ‘Upload a template file’.
- Upload `GCP-Cost-Dashboard-Stack.yaml`, click **Next**

##### 3. Insert parameters:

- `Prefix` => a prefix for the resources created

- `BucketName` => a bucket to store exctracted data. Provide a name based on your naming convention.

- `GCPFullTableName` => this is a COMMADELIMITEDLIST type, and should includes your billing export tables ID delimited by a “,”. example: value1,value2. To retrieve your tables ID, just select the three dots on the right of a table and click “Copy ID”.

- `GCPPricingFullTableName` => your pricing table id. To retrieve your tables ID, just select the three dots on the right of a table and click “Copy ID”.

- `GCPConnectionName` => a name for the connection. Default can be good or adapt based on your naming convention needs.

- `GCPBillingLocation`, `GCPPricingLocation`, `GCPJobBookmarksKeys` and `TargetCatalogDBName` are reserved and must not be changed.

##### 5. Click on Next until completing the form.

##### 6. Launch the cloudformation and wait until it’s complete.

##### 7. Test the import:

- Go on your newly created Glue Jobs and **run** them to start an import.

- Wait until completes.

## Dashboards Deployment Guide

1. On cloudshell install cid with command: `pip3 install -U cid-cmd`

2. Upload `GCP-Cost-Dashboard.yaml` to your cloudshell.

3. Launch command: `cid-cmd deploy --resources GCP-Cost-Dashboard.yaml`