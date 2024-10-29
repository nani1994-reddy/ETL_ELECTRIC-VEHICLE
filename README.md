# ETL_ELECTRIC-VEHICLE 
# ETL Project: Electric Vehicle Population Data

## Overview

This project processes an Electric Vehicle (EV) population dataset, performs data cleaning using Pandas, and uploads the cleaned data to a Google Cloud Storage (GCS) bucket. This pipeline prepares the dataset for analysis or further use.

## Table of Contents

- Querying the Dataset
- Data Cleaning with Pandas
- Exporting Data to GCS
- How to Run the Code
- Project Files
- Conclusion

---

## Python Environment Setup

To ensure compatibility, set up an environment with the following libraries:

- `pandas`
- `numpy`
- `google-cloud-storage`

### Install Required Python Libraries

Run the following command to install the necessary packages:


# Setting Up Google Cloud Storage (GCS)

To store cleaned data files, you need a Google Cloud Storage bucket. Here are the steps to set up a new GCS bucket:

1 Login to Google Cloud Console:

  -Go to the Google Cloud Console.
    
2 Create a New Bucket:

  -Go to Storage > Browser from the menu.

  -Click on Create Bucket and configure the following settings:
  
  -    Name: Enter a globally unique name, e.g., ev-population-data-bucket.
  
  -Location Type:

-    Region: For storing data in one specific region.
  
-    Multi-region: For higher availability across multiple regions.

-    Storage Class: Choose Standard for frequent access or Nearline/Coldline for infrequent access.

-    Access Control:

-    Uniform: Recommended for simplicity, with permissions managed at the bucket level.

Assign Permissions:

-    In the bucket’s Permissions tab, click Grant Access

-    Add required roles:

-    Storage Object Admin: Provides full control over objects in the bucket.

-    Add the service account email if using one.

# Authentication Setup for GCS

 Service Account Authentication

1 Go to IAM & Admin > Service Accounts in the Google Cloud Console.

2 Create a Service Account:

-    Enter a name and description.

-    Add the following roles:

-    BigQuery User: Allows querying data.

   Storage Object Admin: Grants permission to upload data to GCS.

3 Download the JSON key for this service account and save it on your machine.

# Local Authentication

Set the environment variable to point to your JSON key file:

       export GOOGLE_APPLICATION_CREDENTIALS="path/to/your-service-account-key.json

# Querying the Dataset

The following code snippet retrieves data from the Electric_Vehicle_Population_Data.csv file, loaded with Pandas:

python

    import pandas as pd

    data = pd.read_csv('Electric_Vehicle_Population_Data.csv')
    print(data.head())  # Preview the 

# Data Cleaning with Pandas

After loading the dataset, data cleaning is performed using Pandas. Steps include:

1 Removing Duplicates: Eliminate duplicate rows.

2 Handling Missing Values: Fill missing values with 'unknown' for categorical fields.

3 Data Type Conversion: Convert columns such as Postal Code, Electric Range, and Base MSRP to numeric types.

4 Standardizing Text: Convert string fields to consistent text case.

5 Grouping by Make: Aggregating EV counts by vehicle make.

Example Code for Data Cleaning

python

    import pandas as pd
    import numpy as np

    # Remove duplicates
    data = data.drop_duplicates()

    # Fill missing values
    data['County'].fillna('unknown', inplace=True)
    data['Legislative District'].fillna('unknown', inplace=True)
    data['Electric Utility'].fillna('unknown', inplace=True)
    data['2020 Census Tract'].fillna('unknown', inplace=True)

    # Convert Postal Code, Electric Range, and Base MSRP to numeric
    data['Postal Code'] = pd.to_numeric(data['Postal Code'], errors='coerce').astype('Int64')
    data['Electric Range'] = pd.to_numeric(data['Electric Range'], errors='coerce')
    data['Base MSRP'] = pd.to_numeric(data['Base MSRP'], errors='coerce')

    # Preview cleaned data
    print(data.info())
    data.to_csv('EVcleaned_data.csv', index=False)

# Exporting Data to GCS

After data cleaning, export the dataset to a GCS bucket in CSV format:

python

    from google.cloud import storage

    def upload_to_gcs(bucket_name, source_file_name, destination_blob_name):
    client = storage.Client()
    bucket = client.bucket(bucket_name)
    blob = bucket.blob(destination_blob_name)
    blob.upload_from_filename(source_file_name)
    print(f"File {source_file_name} uploaded to {bucket_name}/{destination_blob_name}.")

    # Specify your GCS bucket and file paths
    bucket_name = "your-gcs-bucket-name"
    source_file_name = "EVcleaned_data.csv"
    destination_blob_name = "EVfinal_data.csv"

    # Upload to GCS
    upload_to_gcs(bucket_name, source_file_name, destination_blob_name)

# How to Run the Code

Set Up Google Cloud Authentication: Authenticate with Google Cloud by running:

bash

    gcloud auth application-default login

Run the Python Script: Ensure you have set up Google Cloud credentials and have the correct dataset path.

Verify the Upload: Check your GCS bucket in Google Cloud Console to confirm the upload of EVfinal_data.csv.

# Project Files

   Electric_Vehicle_Population_Data.csv: Original dataset.

   EVcleaned_data.csv: Cleaned dataset ready for upload.

   EV_data_etl.py: Python script for querying, cleaning, and exporting data.

# Conclusion

This ETL pipeline demonstrates the complete process of loading, cleaning, and exporting Electric Vehicle data to Google Cloud Storage. It can be extended with 
further transformations or automated using Google Cloud services like Cloud Functions or Cloud Run.
