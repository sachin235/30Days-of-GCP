# Explore Machine Learning Models with Explainable AI: Challenge Lab
## Start a JupyterLab Notebook instance

-  In the Cloud Console, in the Search bar, type in  **Notebook**.
-  Select  **Notebook**  for  **AI Platform**.
-  On the Notebook instances page, click  **New Instance**.
-   In the Customize instance menu, select the 2.3 version of TensorFlow and then select **_without_  GPUs**.
-  In the  **New notebook instance**  dialog, accept the default options and click  **Create**.

# ![img6a](./Assets/img6a.png)

-  After a few minutes, the AI Platform console will display your instance name, followed by Open Jupyterlab.  
      
    Click  **Open JupyterLab**. Your notebook is now set up.

## Download the Challenge Notebook

-  In your notebook, click the  **terminal**.
# ![img6b](./Assets/img6b.png)
-  Clone the repo:
```
git clone https://github.com/GoogleCloudPlatform/training-data-analyst
```
-  Go to the enclosing folder:  `training-data-analyst/quests/dei`
-  Open the notebook file  `what-if-tool-challenge.ipynb`.
# ![img6c](./Assets/img6c.png)

- Download and import the dataset  `hmda_2017_ny_all-records_labels`  by running the first to the eighth cells (the  **Get the Train & Test Data**  section).


## Build and train your models

1.  In the second cell of the  **Train your first model on the complete dataset**  section, add the following lines to create the model.

```
model = Sequential()
model.add(layers.Dense(200, input_shape=(input_size,), activation='relu'))
model.add(layers.Dense(50, activation='relu'))
model.add(layers.Dense(20, activation='relu'))
model.add(layers.Dense(1, activation='sigmoid'))
model.compile(loss='mean_squared_error', optimizer='adam', metrics=['accuracy'])
model.fit(train_data, train_labels, epochs=10, batch_size=2048, validation_split=0.1)
```

1.a. change dir name and save place  
- replace this

```
# Save your model
!mkdir -p saved_complete_model
model.save('saved_complete_model') 
```

- with this

```
# Save your model
!mkdir -p saved_model
model.save('saved_model/my_model')

```
 

   # ![img6d](./Assets/img6d.png) 


2.  Copy the code for training the second model. Modify  `model`  to  `limited_model`  as well as  `train_data, train_labels`  to  `limited_train_data, limited_train_labels`. The code for the second model should look like the following.

```

limited_model = Sequential()
limited_model.add(layers.Dense(200, input_shape=(input_size,), activation='relu'))
limited_model.add(layers.Dense(50, activation='relu'))
limited_model.add(layers.Dense(20, activation='relu'))
limited_model.add(layers.Dense(1, activation='sigmoid'))
limited_model.compile(loss='mean_squared_error', optimizer='adam', metrics=['accuracy'])
limited_model.fit(limited_train_data, limited_train_labels, epochs=10, batch_size=2048, validation_split=0.1)
```
2.a. change the file name
- replace this

```
# Save your model
!mkdir -p saved_limited_model
limited_model.save('saved_limited_model') 
```

- with this

```
# Save your model
!mkdir -p saved_limited_model
limited_model.save('saved_limited_model/my_limited_model') 
```

3.  Run the cells in this section and wait for the finish of model training.


## Deploy the models to AI Platform

Moving on to the  **Deploy your models to the AI Platform**  section in the notebook.

1.  Navigate to Storage and create a bucket with unique bucket name(use GCP Project ID). 
2.  Replace the values of `GCP_PROJECT` and `MODEL_BUCKET` with your project ID and the bucket name(Project ID: qs://<PROJECT_ID>) respectively.
3.  change `MODEL_NAME` AND `LIM_MODEL_NAME` to `complete_model` and `limited_model` respectively.
4.  Run those three cells and then confirm the created bucket and the uploaded model files in the Cloud Storage.
5. Like this
```

GCP_PROJECT = 'PROJECT_ID'
MODEL_BUCKET = 'gs://PROJECT_ID'
MODEL_NAME = 'complete_model' #do not modify
LIM_MODEL_NAME = 'limited_model' #do not modify
VERSION_NAME = 'v1'
REGION = 'us-central1'
```

4. Modify lines 
- replace this
```
!gsutil cp -r ./saved_complete_model $MODEL_BUCKET
!gsutil cp -r ./limited_model $MODEL_BUCKET

```
- with this
```
!gsutil cp -r ./saved_model $MODEL_BUCKET
!gsutil cp -r ./saved_limited_model $MODEL_BUCKET
```

### Create your first AI Platform model: complete_model

4.  Add the following codes to the notebook cells for your COMPLETE model.

```
!gcloud ai-platform models create $MODEL_NAME --regions $REGION  
```

```
!gcloud ai-platform versions create $VERSION_NAME \
--model=$MODEL_NAME \
--framework='TENSORFLOW' \
--runtime-version=2.1 \
--origin=$MODEL_BUCKET/saved_model/my_model \
--staging-bucket=$MODEL_BUCKET \
--python-version=3.7

```

Created your first AI Platform model: complete_model  
(With parameters --runtime-version=2.1, --python-version=3.7)

### Create your second AI Platform model: limited_model


5.  Add the following codes to the notebook cells for your LIMITED model.

```
!gcloud ai-platform models create $LIM_MODEL_NAME --regions $REGION
```
    
```
!gcloud ai-platform versions create $VERSION_NAME \
--model=$LIM_MODEL_NAME \
--framework='TENSORFLOW' \
--runtime-version=2.1 \
--origin=$MODEL_BUCKET/saved_limited_model/my_limited_model \
--staging-bucket=$MODEL_BUCKET \
--python-version=3.7
```
    
**Remark:** The gcloud ai-platform command group should be  `versions`  rather than  `version`.

# ![img6g](./Assets/img6g.png)

Created your second AI Platform model: limited_model  
(With params --runtime-version=2.1, --python-version=3.7)

### Troubleshooting runtime version issue

While the notebook guided to create the models with runtime version 2.1 and Python 3.7, the checkpoint message specified the required runtime version = 1.14 as shown in the below picture.

# ![img6h](./Assets/img6h.png)

Checkpoint requirement for creating your AI Platform models

Unfortunately, it still doesn’t work if you just change the runtime version from 2.1 to 1.14. The runtime version 1.14 must be coupled with Python 3.5, according to the  AI Platform Documentation. Thus, after replacing the runtime and Python version numbers, correspondingly, the codes for creating the AI Platform models should be modified as shown below.

##### Create your first AI Platform model: complete_model  
(Fixed with --runtime-version=1.14, --python-version=3.5)

# ![img6i](./Assets/img6i.png)

##### Create your second AI Platform model: limited_model  
(Fixed with --runtime-version=1.14, --python-version=3.5)

## Use the What-If Tool to explore biases

Run the last cell in the notebook to activate What-If Tool. Explore the differences between the two models and you should be able to get the answers as follows:

1. In the Performance and Fairness tab, slice by sex (applicant_sex_name_Female). How does the complete model compare to the limited model for females?

    **Ans:** The complete model has equal performance across sexes, whereas the limited model is much worse on females.


2. Click on one of the datapoints in the middle of the arc. In the datapoint editor, change (applicant_sex_name_Female) to 0, and (applicant_sex_name_Male) to 1. Now run the inference again. How does the model change?

    **Ans:** The limited model has a significantly larger delta than the complete model, whereas the complete model has almost no change.

3. In the Performance and Fairness tab, use the fairness buttons to see the thresholds for the sexes for demographic parity between males and females. How does this change the thresholds for the limited model?

    **Ans:** The thresholds have to be wildly different for the limited model.

**Congratulations! You have completed this challenge lab.**
