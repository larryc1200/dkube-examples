# Clinical Regression Example
 
 This example takes clinical data, RNA data, and images as inputs and uses regression to train a model that will predict how long the person is expected to take to recover.  The training flow is:

 - Preprocess the clinical data and images
 - Split the training data into training, validation, and test datasets
 - Train the model
 - Deploy the model
 - Test the model with a WebApp

 This example provides a fully automated way to train and deploy the model, and a manual approach that highlights the DKube UI.

## Create Code Repo

 The Code Repo contains the program code and other associated files for developing and running your model training.

 - Navigate to `Code` menu on the left side of the screen
 - Select `+ Code`
   - **Name:** `<your-code-repo>`  **(Your choice of name)**
   - **Code Source:** `Git`
   - **URL:** `https://github.com/oneconvergence/dkube-examples.git`
   - **Branch:** `tensorflow`
   - Leave the other fields in their current selection and `Submit`

## 1. Automated Setup and Execution using Kubeflow Pipelines

 The clinical regression can be run in an automated manner through a Kubeflow Pipeline. This section explains how to execute this.

### Create JupyterLab IDE

 A JupterLab IDE is used to create the pipeline that trains the model.

 - Navigate to `IDEs` menu on the left
 - Select `+ JupyterLab`
   - **Name:** `<your-ide-name>`  **(Your choice of name)**
   - **Code:** Select *\<your-code-repo\>*  **(From the Code Repo selection step)**
   - **Framework:** `Tensorflow`
   - **Framework Version:** `2.0.0`
   - **Image:** `ocdr/dkube-datascience-tf-cpu-multiuser:v2.0.0-17`
   > **Note** The image should default to the correct selection, but you should check to ensure that it is the right one
   - Leave the other fields in their current selection and `Submit`

### Create & Run Pipeline

 A JupyterLab script is used to create the training pipeline.

 - Navigate to `IDEs` menu on the left
 - When the JupyterLab instance is running, select the icon on the right to open a new JL tab
 - Navigate to <code>workspace/**\<your-code-repo\>**/clinical_reg</code>
 - Open `pipeline.ipynb`
 - Select `Run` menu at the top and Select `Run All Cells` <br><br>
 - This will create the Kubeflow Pipeline to:
   - Create the datasets
   - Proprocess the data
   - Split them in to train, validate, & test
   - Train the model
   - Deploy the model
   - Start the inference WebApp <br><br>
 - The pipeline will be automatically created within DKube, and a pipeline run will be started <br><br>
 - Navigate to `Pipelines` menu to view the pipeline graph and track the progress
 - Select `Runs` tab on the top
 - Your new pipeline run will be the first entry
 - Select `View Pipeline` on the Run row to see the entire pipeline
 - Use the backarrow to view the list of pipelines again
 - Select the pipeline name to view the pipeline progress

### View Resources Created by Pipeline

 After the pipeline has completed its execution, you can view the Runs, Datasets, and Models created.

<!---
### Upload Pipeline to DKube

 Before execution, the pipeline needs to be uploaded to DKube.

 - Navigate to `Pipelines` menu
 - Select `+ Upload Pipeline`
 - **Pipeline Description:** Your choice of description
 - Select `Upload a File`
 - Choose the file that you downloaded in the previous step
 - Select `Create`

### Create Experiment (Optional)

 A Kubeflow Pipeline must run within an Experiment.  If you already have an Experiment for the pipeline runs, skip this step and go to the next section on creating a pipeline run.  If you do not have an Experiment, this section explains how to create one.

 - Return to main `Pipelines` menu
 - Select `Experiments` tab
 - Select `+ Create Experiment`
   - **Experiment Name:** Your choice of name
   - Select `Next` <br><br>
 - Go back to the main `Pipelines` menu

### Create and Execute Pipeline Run

 - Navigate to the main `Pipelines` menu
 - Select the pipeline that you just uploaded
 - Select `+ Create run`
   - The top names will be prepopulates
   - Choose your Experiment from the dropdown
   - **user:** `Your username`
   - **auth_token:** Authentication token from the `Developer Settings` menu at the top right of the screen
   - `Start` the pipeline
-->

<!---
### Download pipeline to Jupyterlab

1. Start any of the jupyterlab notebook from the IDE tab.
2. Once running, click the jupyterlab icon to launch jupyterlab
3. Open terminal in Jupyterlab and run
   ```
   > wget https://raw.githubusercontent.com/oneconvergence/dkube-examples/tensorflow/clinical_reg/pipeline_withslurm.ipynb
   ```
4. Open pipeline.ipynb and run cells to generate the tar file and create run.
5. Download the tar file by right-clicking on it(optional).
6. Upload the tar file into the DKube pipeline UI(optional).

# Deploy model.(Optional)
-  Go to Model Catalog and from model version click deploy model.
-  Give name. 
-  Serving image: default 
-  Deployment type: Test
-  Select transformer
   -  Transformer script: `clinical_reg/transformer.py`
-  Deploy using: CPU and Submit. 
-  Deployed Model will be available in Model Serving.
-->

## Test Inference.

1. Download the csv data file [cli_inp.csv](sample_data/cli_inp.csv) and any sample image from images folder from [sample_data/images](sample_data/images)
2. open https://{your-dkube-url}/inference,
   - Eg: https://1.2.3.4:32222/#/dsinference
3. In DKube UI, once the pipeline run has completed, navigate to ‘Deployments’ on the left pane
4. Copy the ‘Endpoint’ URL in the row using the clipboard icon
5. Enter the Endpoint URL into the Model Serving URL field of inference page,
6. Copy the token from ‘Developer Settings’ and paste into ‘Authorization Token’ box
7. Select Model Type as ‘Regression’ on the next dropdown selection
8. Click ‘Upload Image’ to load image from [1], ‘Upload File’ to load csv from [1]
9.  Click ‘Predict’ to run Inference.

## 2. Regression Notebook Workflow(Repos will be created by the pipeline above).

1. Go to IDE section
2. Create Notebook 
   - Give a name 
   - Code: regression
   - Framework : Tensorflow
   - Framework version : 2.3
   - Datasets: 
         - i.   clinical Mount point: /opt/dkube/input/clinical 
         - ii.  images Mount point: /opt/dkube/input/images 
         - iii. rna Mount Point: /opt/dkube/input/rna
i3. Submit
4. Open workflow.ipynb from location `workspace/regression/clinical_reg` 
   - Run cells and wait for output (In case of running the notebook second time, restart the kernel)
5. Delete if workflow.py is already there and export the workflow notebook as executable. 
   - Upload it into Juyterlab. 
   - Make changes in py file, comment/remove the following line numbers: 
        -i. 239-240
        -ii. 268 
        -iii. 435-end 
  -  Save and commit the workflow.py
6. Create a model named workflow with source none.
7. Create training run using workflow.py 
   - Give a name 
   - Code: regression 
   - Framework : Tensorflow
   - Framework version : 2.0.0
   - Startup command: python workflow.py 
   - Datasets: 
        - i.   clinical Mount point: /opt/dkube/input/clinical 
        - ii.  images Mount point: /opt/dkube/input/images 
        - iii. rna Mount Point: /opt/dkube/input/rna 
   - Output model: workflow, Mount point : /opt/dkube/output
