# MNIST DIGITS CLASSIFICATION EXAMPLE 

 This example uses the `mnist` model to identify a digit from an image.  It steps through a simple DKube workflow.

## Create Code Repo

 - From the `Code` menu on the left, select `+ Add Code` with the following fields:
   - **Name:** `<your-code-repo>`
   - **Code Source:** `Git`
   - **URL:**: `https://github.com/oneconvergence/dkube-examples.git`
   - **Branch:** `tensorflow`
 - Leave the other fields in their current selection and `Add Code`

## Create Dataset Repo

 - From the `Datasets` menu, select `+ Add Dataset` with the following fields:
   - **Name:** `<your dataset-repo`
   - **Dataset source:** `Other`
   - **URL:** `https://s3.amazonaws.com/img-datasets/mnist.pkl.gz`
 - Leave the other fields in their current selection and `Add Dataset`  

## Create Model Repo

 - From the `Models` menu, select `+ Add Model` with the following fields:
   - **Name:** `<your model-repo`
 - Leave the other fields in their current selection and `Add Dataset`  

## Create & Launch JupyterLab Notebook

 - Ensure that all of Repos above are in the `Ready` state
 - From the `IDEs` menu, select `+ Add JupyterLab` with the following fields:
   - `Basic` tab
     - **Name:** `<your-IDE-name`
     - **Code:** `<your-code-repo>`  **(Created druing the Code Repo step)**
     - **Framework:** `tensorflow`
     - **Framework Version:** `2.0.0`
     - **Image:** `ocdr/dkube-datascience-tf-cpu-multiuser:v2.0.0-17`
     > **Note** The default Tensorflow Image should fill in automatically, but ensure that it is correct
   - `Repos` tab
      - **Inputs** > **Datasets**: `<your-dataset-repo>`  **(Created druing the Dataset Repo step)**
      - **Mount Path:** `/mnist`
 - Leave the other fields in their current selection and `Submit` <br><br>
 - Once the IDE is running and the JupyterLab icon on the right is active, select it to launch a JupyterLab window
   - Navigate to <code>workspace/**\<your-code-repo\>**/mnist</code>
   - Open `train.ipynb`
     - `Run All Cells` from the menu at the top
     - Change the `EPOCHS` variable in the 2nd cell "5" and rerun all cells
     - You can view the difference in output at the bottom of the script
     > **Note** You would normally be developing your code in JupyterLab, and once you were satisfied you would create a Python file from the `ipynb` file.  In this example, a Python file is already ready for execution.

## Run Training Job

- From the `Runs` menu, select `+ Run` > `Training` with the following fields:
   - `Basic` tab
     - **Name:** `<your-run-name`
     - **Code:** `<your-code-repo>`  **(Created druing the Code Repo step)**
     - **Framework:** `tensorflow`
     - **Framework Version:** `2.0.0`
     - **Image:** `ocdr/dkube-datascience-tf-cpu-multiuser:v2.0.0-17`
     > **Note** The default Tensorflow Image should fill in automatically, but ensure that it is correct
     - **Start-up Command:** `python mnist/train.py`
   - `Repos` tab
      - **Inputs** > **Datasets**: `<your-dataset-repo>`  **(Created druing the Dataset Repo step)**
        - **Mount Path:** `/mnist` <br><br>
      - **Outputs** > **Models**: `<your-model-repo>`  **(Created druing the Model Repo step)**
        - **Mount Path:** `/model`
      > **Note** Ensure that you add the Model into the `Outputs` section, and not the `Inputs` section
   - Leave the other fields in their current selection and `Submit`
   - Your Run will show up from the `Runs` menu screen <br><br>
   - Clone the Run by selecting the checkbox and choosing `Clone` from the top buttons
     - Leave the `Basic` and `Repos` tabs the same
     - On the `Configuration` tab
       - Select the `+` button next to `Environment Variables`
       - **Key:** `EPOCHS`   ****Must be in upper case)**
       - **Value:** `5`
     - `Submit`

## Compare Runs

 - Wait for both Runs to `complete`
 - From the `Runs` menu, select both Run checkboxes, then select `Compare` button
 - Scroll down and choose **Y-Axis:** `train_accuracy`

## Run Katib-Based Hyperparameter Tuning
 
 - Go to https://github.com/larryc1200/dkube-examples/tree/tensorflow/mnist/tuning.yaml
 - Select `Raw`
 - Right-click & `Save as...` "tuning.yaml"

 - From the `Runs` menu, select the first Run checkbox, then select `Clone`
   - Leave the `Basic` and `Repos` tabs the same
   - On the `Configuration` tab
     - Select `Upload Tuning Definition`
     - Choose the `tuning.yaml` file that you saved
   - `Submit` <br><br>
 - Wait for Run to complete
 - View the results by selecting the Katib icon on the right of the Run line




### Tuning.yaml file Details:
 - **objective**: The metric that you want to optimize. 
 - **goal** parameter is mandatory in tuning.yaml file.
 -**objectiveMetricName:** Katib uses the objectiveMetricName and additionalMetricNames to monitor how the hyperparameters work with the model. Katib records the value of the best objectiveMetricName metric.
 - **parameters** : The range of the hyperparameters or other parameters that you want to tune for your machine learning (ML) model.
 - **parallelTrialCount**: The maximum number of hyperparameter sets that Katib should train in parallel. The default value is 3.
 - **maxTrialCount**: The maximum number of trials to run.
 - **maxFailedTrialCount**: The maximum number of failed trials before Katib should stop the experiment.
 - **algorithm**: Search algorithm to find the best hyper parameters. Value must be one of following:
   - random
   - bayesianoptimization
   - hyperband
   - cmaes
   - enas

## Deploy Model (DKube version 2.1.x.x)
- Repos->Models->mnist: select a model version
- Deploy
- Name: mnist
- Type: Test
- Transformer: True
- Transformer script: mnist/transformer.py
- Submit

## Publish and Deploy Model (Dkube version 2.2.x.x)
- Repos->Models->mnist: select a model version
- Click on Publish model icon under ACTIONS column.
- Name: mnist
- Transformer: True
- Transformer script: mnist/transformer.py
- Submit
### Deploy model
- Click on Model catalog and select the published model.
- Click on the deploy model icon under ACTIONS column.
- Enter the deploy model name and select CPU and click Submit.
- Check in Model Serving and wait for the deployed model to change to running state.

## Publish and Deploy Model (Dkube version 3.0.x.x)
- Models->mnist: select a model version
- Click on Publish model icon under ACTIONS column
- Transformer: True
- Transformer script: mnist/transformer.py
- Submit
### Deploy model
- Click on Models in the navigation pane
- Click on the drop down next to 'Owned by me' and select 'Published'
- Click on the published model 'mnist'
- Select the published version and click on the deploy model icon under ACTIONS column
- Enter the deploy model name, select Deployment / Test and select Deploy using / CPU. Click Submit
- Check in Deployments and wait for the deployed model to change to running state

## Test inference
- Go to
  - Deployments in 2.1.x.x version
  - Model Serving in 2.2.x.x version
  - Deployments in 3.0.x.x version
- Copy the prediction Endpoint for the model
- Create a browser tab and go to https://<dkube_url>/inference
- Paste the Endpoint URL
- Copy Auth token from Developer settings in Dkube page and Paste in inference page
- Choose mnist
- Upload 3.png from repo
- Click predict

## Automate using pipelines
Run this [pipeline](https://github.com/oneconvergence/dkube-examples/blob/tensorflow/mnist/pipeline.ipynb) to automate training and serving using kubeflow pipelines.
