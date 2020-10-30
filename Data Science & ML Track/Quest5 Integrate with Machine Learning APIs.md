# Integrate with Machine Learning APIs: Challenge Lab

## Task 1: Configure a service account to access the Machine Learning APIs, BigQuery, and Cloud Storage

- In the Cloud Shell, create a new service account that provides credentials for the script using the following commands. (Remember to replace <Your_Project_ID> with your GCP project ID)

```
export PROJECT=<Your_Project_ID>
gcloud iam service-accounts create my-account --display-name my-account
```

- Once you have created the account, bind the BigQuery Admin and Cloud Storage Admin roles to the Service Account to provide the IAM permissions required to process files from Cloud Storage and insert the result data into a BigQuery table.

```
gcloud projects add-iam-policy-binding $PROJECT --member=serviceAccount:my-account@$PROJECT.iam.gserviceaccount.com --role=roles/bigquery.admin
gcloud projects add-iam-policy-binding $PROJECT --member=serviceAccount:my-account@$PROJECT.iam.gserviceaccount.com --role=roles/storage.admin
```


## Task 2: Create and download a credential file for your Service Account
- Run the following commands to download the JSON format IAM credentials file for the service account, and configure the name of the credential file as an environment variable.

```
gcloud iam service-accounts keys create key.json --iam-account=my-account@$PROJECT.iam.gserviceaccount.com
export GOOGLE_APPLICATION_CREDENTIALS=key.json
```


## Task 3: Modify the Python script to extract text from image files
- Navigate to Storage in the Cloud Console, then click on the bucket name to explore the image files and the Python script that have been provided for you.

- Run the following gsutil command to copy the file analyze-images.py from the Cloud Storage bucket into the Cloud Shell.
```
gsutil cp gs://$PROJECT/analyze-images.py .
```

- Open the Cloud Shell Editor to review and edit the script file.

- You need to add your codes to the following part of the script file:

TBD: Create a Vision API image object called image_object

Ref: google.cloud.vision_v1.types.Image

```
        image_object = vision.types.Image()
        image_object.content = file_content
```

TBD: Detect text in the image and save the response data into an object called response

Ref: google.cloud.vision_v1.ImageAnnotatorClient.document_text_detection

```
        response = vision_client.document_text_detection(image=image_object)
```

**Note:** Make sure that you indent the codes correctly.


## Task 4: Modify the Python script to translate the text using the Translation API

- You need to add your codes to the following part of the script file:

TBD: For non EN locales pass the description data to the translation API

Ref: google.cloud.translate_v2.client.Client.translate

```
            translation = translate_client.translate(desc, target_language='en')

```

## Task 5: Identify the most common non-English language used in the signs in the data set

- You need to remove the comment characters to enable the line of code in the second last line of the script.


### Process the image files using the updated Python
- Save the changes and then run the modified script file in the Cloud Shell:

```
export BUCKET=$PROJECT
python analyze-images.py $PROJECT $BUCKET
```


### Confirm that image data has been successfully uploaded to BigQuery
- Go back to the Cloud Console, navigate to BigQuery.

- Preview the table ```image_text_detail``` in the dataset called ```image_classification_dataset``` in your project.

- Confirm that image data has been successfully processed by running the following Query in BigQuery:
```
SELECT locale,COUNT(locale) as lcount FROM image_classification_dataset.image_text_detail GROUP BY locale ORDER BY lcount DESC
```


## Congratulations! You completed this challenge lab.
