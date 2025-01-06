# Lab 1 - Azure Machine Learning Model Training

### Estimated Duration: 2 Hours

Azure Machine Learning model training involves using Azure’s cloud-based platform to build, train, and tune machine learning models with scalable compute resources, automated workflows, and integrated tools, streamlining the process from data preparation to model deployment.

### Source datasets used by the labs

### COVID-19 Case Surveillance Public Use Data

https://data.cdc.gov/Case-Surveillance/COVID-19-Case-Surveillance-Public-Use-Data/vbim-akqf

The COVID-19 case surveillance system database includes individual-level data reported to U.S. states and autonomous reporting entities, including New York City and the District of Columbia (D.C.), as well as U.S. territories and states. On April 5, 2020, COVID-19 was added to the Nationally Notifiable Condition List and classified as “immediately notifiable, urgent (within 24 hours)” by a Council of State and Territorial Epidemiologists (CSTE) Interim Position Statement (Interim-20-ID-01). CSTE updated the position statement on August 5, 2020 to clarify the interpretation of antigen detection tests and serologic test results within the case classification. The statement also recommended that all states and territories enact laws to make COVID-19 reportable in their jurisdiction, and that jurisdictions conducting surveillance should submit case notifications to CDC. COVID-19 case surveillance data are collected by jurisdictions and shared voluntarily with CDC.

The dataset contains 13.4 million rows of deidentified patient data.

### COVID-19 Open Research Dataset

https://azure.microsoft.com/en-us/services/open-datasets/catalog/covid-19-open-research/

In response to the COVID-19 pandemic, the [Allen Institute for AI](https://allenai.org/) has partnered with leading research groups to prepare and distribute the COVID-19 Open Research Dataset (CORD-19), a free resource of over 47,000 scholarly articles, including over 36,000 with full text, about COVID-19 and the coronavirus family of viruses for use by the global research community. This dataset is made available by the the Allen Institute of AI and [Semantic Scholar](https://pages.semanticscholar.org/coronavirus-research).

This dataset is intended to mobilize researchers to apply recent advances in natural language processing to generate new insights in support of the fight against this infectious disease.

The corpus may be updated as new research is published in peer-reviewed publications and archival services like [bioRxiv](https://www.biorxiv.org/), [medRxiv](https://www.medrxiv.org/), and others.

## Lab Objectives

- Task 1 - Explore dashboard of COVID-19 data
- Task 2 - Explore lab scenario
- Task 3 - Prepare Azure Machine Learning workspace
- Task 4 - Prepare data for the Machine Learning process
- Task 5 - Train a Machine Learning model with Automated ML
- Task 6 - Explore AutoML results
- Task 7 - Generate a Responsible AI dashboard
- Task 8 - Explore the Responsible AI dashboard

## Task 1 - Explore dashboard of COVID-19 data

Understanding the source datasets is very important in AI and ML. To help you expedite the process, we have created a Power BI dashboard you can use to explore them at the beginning of each lab.

![Azure AI in a Day datasets](./media/SHC1.png)

To get more details about the source datasets, check out the [Data Overview](https://github.com/CloudLabsAI-Azure/ai-in-a-day/blob/main/data-overview.md) section.

To explore the dashboard of COVID-19 data, open the `Azure-AI-in-a-Day-Data-Overview` file located on the desktop (**C:\Users\public\desktop**) 💻 of the virtual machine provided with your environment. If you see the `Introducing the updated mobile layout` pop-up screen, then close it by clicking on `Got it`. Collapse the **Fields** and **Visualizations** tabs to see the clear report.

> **Note:** Please close and reopen the Power BI Desktop document if it throws an error on the first attempt.

 ![Azure AI in a Day datasets](./media/powerbireportopen.png)

## Task 2 - Explore lab scenario

Given the magnitude of the COVID-19 problem, it comes naturally to have a lot of research on the topic. In fact, in 2020 alone, tens of thousands of papers have been published on COVID-19 alone. The sheer amount of communication on the subject makes it difficult for a researcher to grasp and structure all the relevant topics and details. Furthermore, pre-defined catalogs and paper classification might not always reflect their content in the most effective way possible.

Based on a set of existing research papers, we will use Natural Language Processing and Machine Learning to identify these papers' natural grouping. For each new document that gets into our system, we will use Machine Learning to classify it into one of the previously identified groups. We will use Automated ML (a feature of Azure Machine Learning) to train the best classification model and explain its behaviour.

The following diagram highlights the portion of the general architecture covered by this lab.

![Azure AI in a Day datasets](../media/updated-arch-lab1.png)

The high-level steps covered in the lab are:

- Explore dashboard of COVID-19 data
- Explore lab scenario
- Run word embedding process on natural language content of research papers
- Explore results of word embedding
- Run clustering of research papers and explore results
- Use the newly found clusters to label the research document and run the Auto ML process to train a classifier
- Run the classifier on "new" research papers
- Explain the best model produced by AutoML

## Task 3 - Prepare Azure Machine Learning workspace

1. In the **Azure portal**, navigate to `All Resources`.

    ![All Resources](./media/AllResources.png)
        
1. Locate **Machine Learning** resource named **<inject key="AML Workspace Name " enableCopy="true"/>** and select it.

    ![Navigate to Azure Machine Learning](./media/innovate10.png)
    
1. On the **Overview (1)** page, click on **Launch studio (2)**. If you are prompted to sign in again, use the same lab credentials you used to login to the Azure portal. Close the welcome **pop-up** if any with the cross icon.  
    
    ![Launch Machine Learning Studio](media/ml-workspace-launch.png)

1. In Azure Machine Learning Studio, select `Compute` **(1)** from the left side menu and verify that your compute instance is `running` **(2)**.
   
   * Compute instance name: notebook<inject key="DeploymentID" enableCopy="false"/>

    ![Verify Azure Machine Learning compute instance is running](media/ml-compute-status.png)

    >**Note**: If you launched Azure Machine Learning Studio right after your lab environment was provisioned, you might find the compute instance in a provisioning state. In this case, wait a few minutes until it changes its status to `Running`.

1. In the Azure Machine Learning Studio under **Compute** **(1)**, click on **... (2)** and, open the **Terminal** **(2)** environment.
    
   ![](media/ml-terminal.png)
   
1. Run the following commands and make sure all commands will execute successfully.
    
    ```  
    conda env create -f aiw-ai-kernel.yml
    ```
    
    ```
    conda activate aiw-ai-kernel
    ```
    
    ```
    ipython kernel install --user --name aiw-ai-kernel --display-name "Python (aiw-ai-kernel)"
    ```
   
   ![](./media/setkernel1.png)
   
   ![](./media/setkernel2.png)

1. From the `Application` section associated with the compute instance, select `Jupyter` **(1)**. 

    ![](media/ml-jupyter.png)

1. If you see **IMPORTANT NOTE: Always use trusted code**, then check on **Yes, I Understand** and then click on **Continue**.

    ![](./media/trustcode01.png)
    
   >**Note**: If prompted with Do you wish to trust this compute instance? webpage, click on **Click here to trust this compute instance**.
    
    ![](./media/upd-l1-t3-s14.png)
    
## Task 4 - Prepare data for the Machine Learning process

1. In the Jupyter application, navigate to the given path **\Users\odl_user_<inject key="DeploymentID" enableCopy="false"/>** and open `1. Data Preparation.ipynb` notebook.

   ![Select Note Book](./media/IMG015.png)
   
2. On Jupiter page, select **Kernel (1)**, then **Change kernel (2)** and make sure **Python (aiw-ai-kernel) (3)** is selected.

   ![](./media/IMG011.png)

3. Execute the cells inside `1. Data Preparation.ipynb` notebook one by one (Click on the 'Run' button or by using either Ctrl + Enter to stay on the same cell, or Shift + Enter to advance to the next cell) and observe the results of each cell execution.

   ![Run Note Book Cell](./media/IMG012.png)
   ![Note Book Cell Output](./media/SHC4.1.png)
   
 4. Please make sure to read the cells carefully and update the storage account name wherever required with **<inject key="Storage Account Name" enableCopy="false"/>**

     ![Note Book Cell Output](./media/aiupdates.png)

## Task 5 - Train a Machine Learning model with Automated ML

In this task, we'll use Azure Automated ML to train a machine learning model capable of determining the best cluster for a COVID-19 scientific article. It builds upon the work done in the Data Preparation notebook.

1. In the Azure Machine Learning Studio, switch to the `Automated ML` **(1)** section and select `+ New Automated ML job` **(2)** to start the Automated ML.

    ![Automated ML section is open. + New Automated ML run button is highlighted.](media/ml-newautomatedml.png)

2. In the `Submit an Automated ML job` wizard, for Experiment name `Select Existing` **(1)** and select Existing experiment as `COVID19_Classification` **(2)**. Click  `Next` **(3)** to proceed.

    ![COVID19Articles_Train_Vectors dataset is selected. Next button is highlighted.](media/inn5.png)
   
3. On the `Task type and data` page, make sure `Classification` **(1)** is selected. Select `COVID19Articles_Train_Vectors`**(2)** as your dataset and click `Next` **(3)** to proceed.

    ![Classification is selected as the machine learning task type for the experiment. The View additional configuration settings link is highlighted. ](media/innovate1.png)

    ![Classification is selected as the machine learning task type for the experiment. The View additional configuration settings link is highlighted. ](media/iinn9.png)   

5. On `Task settings` page, select the Target column to `cluster (Integer)` **(1)**. The values we're trying to predict are in the `cluster` column.

     ![COVID19Articles_Train_Vectors dataset is selected. Next button is highlighted.](media/inn1.png)

6. Scroll down on the same page, fill in the values listed below and click `Next` **(4)**.

    - Validation type : **k-fold cross validation (2)**
    - Number of cross validations: **5 (3)**
    - Leave Default for Test dataset

      ![COVID19Articles_Train_Vectors dataset is selected. Next button is highlighted.](media/inn2.png)
      
7. In order to be able to launch an Automated ML run, we need to provision an Azure ML compute cluster. On the `Compute` page, select compute type as `Compute Cluster` **(1)** and select Azure AML compute cluster as `aml-compute-cpu` **(2)** from the list of clusters then click on **Next.** If the list is empty select `+ New` **(3)** link.

    ![Select compute cluster dropdown list and create a new compute link are highlighted.](media/inn3.png)

   >**Note**: If you already have `aml-compute-cpu` cluster provisioned, feel free to skip to step 9.

8. On the `Create compute cluster` screen set the values listed below:

    - **Virtual machine priority (1)**: Dedicated
    - **Virtual machine type (2)**: CPU
    - **Virtual machine Size (3)**: Standard_DS3_v2

    ![Dedicated virtual machine priority, CPU virtual machine type, and Standard_DS3_v2 virtual machine size are selected. The next button is highlighted.](./media/SHC5.4.1.png)

    Select `Next` **(4)** to continue.

9. To configure cluster settings set the values given below:

    - **Compute name (1)**: aml-<inject key="DeploymentID" enableCopy="false"/>
    - **Minimum number of nodes (2)**: 0
    - **Maximum number of nodes (3)**: 4  

    Setting the number of maximum nodes to a higher value will allow Automated ML to run more experiments in parallel but will also increase your costs.

    ![Computer name is set to aml-compute-cpu. The minimum number of nodes is set to zero. The maximum number of nodes is set to four. The create button is highlighted.](./media/SHC5.5.1.png)    

10. On the Review page, select `Submit training job` **(1)**  to kick off the Automated ML experiment run. If this is the first time you are launching an experiment run in the Azure Machine Learning workspace, the total experiment time will be longer than the `training job time` we have set. This is because of the time needed to start the Compute Cluster and deploy the container images required to execute.

    ![Validation is selected as the machine learning task type for the experiment. The View additional configuration settings link is highlighted. ](media/inn4.png)

12. On the following screen, you will see the progress of your experiment run.

13. Now that you understand the process of launching an AutoML run, let's explore in the next task the results of an already completed AutoML run.

>**Note**: We have already executed in this environment an AutoML run that is very similar to the one you've just launched. This allows you to explore AutoML results without having to wait for the completion of the run.

## Task 6 - Explore AutoML results

1. In the Azure Machine Learning Studio, navigate to the **Jobs (1)** section and locate the **COVID19_Classification** experiment **(2)**. Select the experiment name link.

   ![Locate the completed experiment ](media/ml-job.png)

2. You will navigate to the experiment details page, where you should see the list of experiment runs. Locate the first run **(1)** listed here, which has the status **Completed**. The name of the run can be different in your environment and may not match the below screenshot.

   ![Locate the completed AutoML run](media/ml-completedjob.png)

3. On the **Run details** page, navigate to the **Models+ child jobs (1)** section. Check the values on the  **AUC weighted** column **(2)**, which is the primary metric selected in the AutoML run configuration. See how the best model was selected; this is the one with the maximum metric value. This is also the model for which the explanation was generated. Select **View explanation (3)**.

   ![Explore the models section of the AutoML run](media/innovate3.png)

4. On the **Explanations (preview) (1)** section, browse the available explanations **(2)** and investigate the **Model performance (3)** representation.

   ![View explanations](media/L1-T6-S4.png)

   <validation step="46d5c33f-126f-4f3c-9a15-a4596a0b876c"/>

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
   > - Hit the Validate button for the corresponding task. You can proceed to the next task if you receive a success message.
   > - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
   > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="46d5c33f-126f-4f3c-9a15-a4596a0b876c" />


## Task 7 - Generate a Responsible AI dashboard

Responsible AI is a governance framework that documents how a specific organization is addressing the challenges around artificial intelligence (AI) from both an ethical and legal point of view. Resolving ambiguity about where responsibility lies if something goes wrong is an important driver for responsible AI initiatives.

**Principles of responsible AI**: AI and the machine learning models that support it should be comprehensive, explainable, ethical and efficient.

 - Comprehensiveness – Comprehensive AI has clearly defined testing and governance criteria to prevent machine learning from being hacked easily.
 - Explainable AI is programmed to describe its purpose, rationale and decision-making process in a way that can be understood by the average end user.
 - Ethical AI initiatives have processes in place to seek out and eliminate bias in machine learning models.
 - Efficient AI is able to run continually and respond quickly to changes in the operational environment.

1. In the Jupyter application, navigate to the given path **\Users\odl_user_<inject key="DeploymentID" enableCopy="false"/>** and open `erroranalysis-dashboard-regression-superconductor.ipynb` notebook.

   ![Select Note Book](./media/IMG016.png)
   
1. Once the notebook is opened in the Jupyter application, select **Kernel** **(1)**. Then select **Change kernel** **(2)** and make sure to select **Python (aiw-ai-kernel) (3)**.

   ![](./media/IMG0011.png)

1. Execute the cells inside `erroranalysis-dashboard-regression-superconductor.ipynb` notebook one by one (Click on the **Run** button or by using either Ctrl + Enter to stay on the same cell, or Shift + Enter to advance to the next cell or) and observe the results of each cell execution.

   ![Run Note Book Cell](./media/IMG014.png)
   
1. Please make sure to read the cells carefully and run them one by one. Click on the **Endpoint** URL at the end of the notebook before moving to the next task.

   ![Note Book Cell Output](./media/ai-img3.png)

## Task 8 -  Explore the Responsible AI dashboard

1. Once you click on the endpoint, you will be navigated to the new tab. Select **Tree map (1)** from the drop-down next to **Error Explorer**, the tree visualization uses the mutual information between each feature and the error to best separate error instances from success instances hierarchically in the data. This simplifies the process of discovering and highlighting common failure patterns. Choose the **Mean squared error (2)** for **Select metric** and click on **Explanation** to view the results. 

   ![Run Note Book Cell](./media/ai-img4.png)
 
1. On the **Explanation** page, observe the box plot graph representing the data. In addition, you can explore the available options to view different representations of data.

   ![Run Note Book Cell](./media/ai-img5.png)

1. Navigate back to **Error explorer** page, and choose **Heat map (1)** form the drop-down next to **Error Explorer**. Choose **number_of_elements (2)** for **Rows: Feature 1** , **mean_atomic_mass (3)** for **Columns: Feature 2** and Set **Select mertic** to **Mean absolute error (4)** .

   ![Run Note Book Cell](./media/image02.png)

## Summary

In this lab, you explore the COVID-19 data dashboard, set up and prepare your Azure Machine Learning workspace, train and evaluate a model using Automated ML, and generate and analyze a Responsible AI dashboard.

### You have successfully completed the lab




