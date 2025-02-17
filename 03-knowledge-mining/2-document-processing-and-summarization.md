# Lab 2 - Document Processing and Summarization with Azure Document Intelligence and AI Service for Language

### Estimated Duration: 2 Hours 30 Minutes

Azure Document Intelligence and AI Service for Language streamline document processing by extracting key information and automating summaries, leveraging AI to enhance data extraction accuracy and deliver actionable insights efficiently.

## Lab Objectives

- Task 1 - Explore the dashboard of COVID-19 data
- Task 2 - Explore lab scenario
- Task 3 - Creating Azure Search Indexes
- Task 4 - Querying Azure Search Indexes
- Task 5 - Updating Azure Search Indexes
- Task 6 - Using the Document Intelligence Studio
- Task 7 - Document Summarization via AI Service for Language Integration

## Task 1 - Explore the dashboard of COVID-19 data

Understanding the source datasets is very important in AI and ML. To help you expedite the process, we have created a Power BI dashboard you can use to explore them at the beginning of each lab.

![Azure AI in a Day datasets](./media/SHC1.png)

To get more details about the source datasets, check out the [Data Overview](https://github.com/CloudLabsAI-Azure/ai-in-a-day/blob/main/data-overview.md) section.

To explore the dashboard of COVID-19 data, open the `Azure-AI-in-a-Day-Data-Overview` file located on the desktop (**C:\Users\public\desktop**) 💻 of the virtual machine provided with your environment. If you see the `Introducing the updated mobile layout` pop-up screen, then close it by clicking on `Got it`. Collapse the **Fields** and **Visualizations** tabs to see the clear report.

 ![Azure AI in a Day datasets](./media/powerbireportopen.png)

> **Note:** Please close and reopen the Power BI Desktop document if it throws an error on the first attempt.

## Task 2 - Explore lab scenario

Another critical problem to deal with when it comes to the volumes of research documents covering COVID-19 is the problem of advanced indexing and searching their content. The specific internal structure of research papers (including citations, contributors, and various entities like diagnosis, forms of examination, family relations, genes, medication, symptoms or signs, and treatments) form a reach semantic graph that goes way beyond simple document categorization. An analyst would benefit significantly from exploring the corpus of documents in a way that considers all these complex relationships.

Using the AI Search capabilities, we will create a complex index of documents that allows an analyst to perform an advanced search and explore the inter-document graph relationships.

The following diagram highlights the portion of the general architecture covered by this lab.

![Azure AI in a Day datasets](../media/updated-arch-lab2.png)

The high-level steps covered in the lab are:

- Explore dashboard of COVID-19 data
- Explore lab scenario
- Explore the document search process
- Explore the graph search process
- Add a set of new documents and trigger the index update process
- Explore the document and graph search and identify updated results

## Task 3 - Creating Azure Search Indexes

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/SHC2a.3.1.png)

1. Select the **AI-in-a-Day** resource group.

1. Locate **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and select it.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/inn6.png)

1. Copy the search service URL and paste this into a text file. Also make a note of the service account name, which comes before `.search.windows.net`.

    ![The Search service's URL is copied to the clipboard.](media/copy-azure-search-url.png)

1. Navigate to the **Keys (1)** under Settings and **copy the Primary admin key (2)**. Paste this into a text file.

    ![The Search service's API key is copied to the clipboard.](media/inn7.png)

1. Navigate to Storage account named **aiinadaystorage<inject key="DeploymentID" enableCopy="false"/>** **(1)**, select **Access keys** **(2)** under Security + networking from left-menu. Click on **Show** of the connection string under Key1 **(3)** to see the connection string and **copy the connection string** **(4)** under Key1. Paste this into a text file.

   ![](media/ai-sa-cs.png)

1. Open **File Explorer** from the task bar and navigate to the path `C:\Temp\AzureSearch\`. There are six files, three prefixed with `abstracts` and three with `covid19temp`.

1. Open the `abstracts_datasource.schema` file with a text editor and replace the segment starting `<< TODO:` with your Storage account connection string **<inject key="storageAccountConnectionString" enableCopy="true"/>** and then save the file.

    ![The abstract data source is ready to be updated.](media/edit-abstracts-datasource.png)

1. Open the `covid19temp_datasource.schema` file with a text editor and replace the segment starting `<< TODO:` with your Storage account connection string **<inject key="storageAccountConnectionString" enableCopy="true"/>** and then save the file.

1. Now, open the `AzureSearchIndex.ps1` file with a text editor and copy the code present in it.

1. Open a new PowerShell prompt and navigate to the following directory:

    ```
    cd C:\Temp\AzureSearch\
    ```
 
1. Paste the copied code from the **AzureSearchIndex.ps1** file and press enter to create an Azure Search data source, index, and indexer.

    ![The Create-AzureSearchIndex function has been created in PowerShell.](media/create-azuresearchindex.png)

1. In the same PowerShell prompt, call this function for the `abstracts` index and the `covid19temp` index.

    Make sure to update the Azure Search account name and Azure Search API key in the below commands and then run.
   
     - Azure Search Account Name: **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>**
     - Azure Search API key: You saved the Primary admin key in the text editor in step 5, use that

    ```powershell
    Create-AzureSearchIndex "C:/Temp/AzureSearch/abstracts_datasource.schema" "C:/Temp/AzureSearch/abstracts.schema" "C:/Temp/AzureSearch/abstracts_indexer.schema" "AZURE SEARCH ACCOUNT NAME" "API KEY"
    ```
    
    ```powershell
    Create-AzureSearchIndex "C:/Temp/AzureSearch/covid19temp_datasource.schema" "C:/Temp/AzureSearch/covid19temp.schema" "C:/Temp/AzureSearch/covid19temp_indexer.schema" "AZURE SEARCH ACCOUNT NAME" "API KEY"
    ```

    ![The Create-AzureSearchIndex function has been run to create a new index.](media/create-azuresearchindex-use.png)

   > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

   <validation step="6e7f9bb9-9bed-4f51-ac7b-c6aa82222f50" />

## Task 4 - Querying Azure Search Indexes

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/SHC2a.3.1.png)

2. Select the **AI-in-a-Day** resource group.

3. Locate **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and select it.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/inn6.png)

4. Select the **Indexes** tab under Search management and ensure that you have two indexes created. If the Document Count is 0 for either, wait a couple of minutes and select **Refresh** until the document count appears.

    ![The list of Azure Search indexes.](media/AI1.png)

5. Once documents are available, Navigate to Overview **(1)** of Search service and then select **Search Explorer (2)** to open up the Search Explorer.

    ![The Search Explorer option is selected.](media/innovate4.png)

6. Choose the **covid19temp (1)** index and enter `RNA interference`**(2)** into the Query string input box, and then select **Search (3)**. This will return the documents which include the phrase "RNA interference."

    ![Articles with the phrase RNA interference.](media/L2T4S6.png)

7. We can also see how many articles match a certain search string. In the Query string input box, enter the phrase `Brazil&$count=true` **(1)** and then select **Search (2)**.  This will return 53 documents.

    ![53 articles reference Brazil.](media/image03.png)

8. Each document returns a large number of fields, but we can specify the fields we would like to see. In the Query string input box, enter the phrase `UNC Chapel Hill&$select=metadata/authors, metadata/title` **(1)** and then select **Search (2)**. This will return the title as well as detailed information on each author.

    ![Paper titles and authors referencing UNC Chapel Hill.](media/image04.png)

9. The Azure Search service can also generate a demo application. Return to the search service and select the **covid19temp** index.

    ![The covid19temp index is selected.](media/inn8.png)

10. Select the **Create Demo App** option.

    ![The Create Demo App option is selected.](media/lab2a-task4-step10.png)

11. On the first tab, select `metadata.title` **(1)** for the Title and `abstract.text` **(2)** for the Description. Then select **Next (3)** twice and click on **Create Demo App**. After the prompt, select **Download** to download an HTML file named `AzSearch.html`.

    ![Create a demo app.](media/demoappupd.png)

12. Open the demo app HTML file. In the search box, enter the phrase **RNA interference (1)** and select the **Search icon (2)**. This will return 497 papers relating to RNA interference.

    ![Use the demo app.](media/L2T4S12.png)

## Task 5 - Updating Azure Search Indexes

1. On the desktop, select the Azure Storage Explorer.  

    ![Storage explorer is selected on the desktop.](media/storage-exp-desktop.png)

2. Select the **Connect (1)** option and then click on **Storage account or service (2)**.

    ![storageaccount](media/storage-account.1.png)

3. Select **Connection String (Key or SAS) (1)** under the Select Connection Method Window and then select **Next (2)**.

    ![The Use a connection string option is selected.](media/connectionstring.1.png)

4. Paste in your storage account **Connection string (1)** **<inject key="storageAccountConnectionString" enableCopy="true"/>**. Then, select **Next (2)** to continue and **Connect (3)** to complete the operation.

    ![The connection string is filled in.](media/new-lab2a-1.1.png)

    ![Connect is selected on the storage explorer page](media/new-lab2a-2.1.png)

5. In Azure Storage Explorer, navigate down the **aiinadaystorage<inject key="DeploymentID" enableCopy="false"/> (1)** attached storage and expand **Blob containers (2)** expand the **blob container (3)** and select `covid19temp` **(4)**.  Double-click the **comm_use_subset (5)** to enter that folder.

    ![The comm_use_subset folder is selected.](media/new-lab2a-3.png)

6. Enter the **pdf_json_refresh** folder. Then, in the **Select All (1)** menu, choose **Select All Cached (2)**. This will highlight all 100 records in the folder.  Select **Copy (3)** to copy these documents.

    ![Select the PDF refresh folder.](media/select-pdf-refresh-folder01.png)
    
    ![Select all cached items and copy them.](media/azure-storage-explorer-201.png)

7. Navigate up to **comm_use_subset** by selecting the upward arrow and then double-click **pdf_json (1)**. Inside this folder, select **Paste (2)** to paste the 100 documents into the **pdf_json** folder. When it finishes, you should have **965 total documents (3)**.

    ![Navigate into the pdf_json folder.](media/upward-arrow01.png)

    ![Paste all cached items into the pdf_json folder.](media/azure-storage-explorer-301.png)

8. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials if not logged in already. Then, select **Resource groups**.

    ![Open Azure resource group](media/SHC2a.3.1.png)

9. Select the **AI-in-a-Day** resource group.

10. Select the **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** Search service.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/inn6.png)

11. Navigate to the **Indexers (1)** section and select the **covid19temp (2)** indexer.

    ![The covid19temp indexer is selected.](media/AI2.png)

12. Select the **Run** option to process the 100 documents. Although we can configure an indexer to run periodically, this indexer will only run when manually engaged.  Select **Yes** to run the indexer.

    ![The covid19temp indexer is set to run.](media/azure-search-indexers-run.1upd.png)

13. The indexer will run. It should be completed within 15-30 seconds to process the 100 new documents. You may need to select **Refresh** to see the indexer's progress.

    ![The covid19temp indexer has finished running.](media/azure-search-indexers-refreshupd.png)

14. Return to the **Indexes** tab for the Search service and ensure that the **covid19temp** index has 965 documents. If it still reads 865, wait 30 seconds and select **Refresh** to check again.

    ![The covid19temp index has finished updating.](media/inn10.png)

15. Select the **covid19temp** index to return to the Search Explorer. When we had 865 documents, 53 of them pertained to Brazil. We can confirm that this update was successful by entering `Brazil&$count=true` **(1)** and selecting **Search (2)**. This will now return 57 results **(3)** instead of the prior 53.

    ![57 documents pertaining to Brazil.](media/L2T5S15.png)

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="b927b6a3-f2fd-4047-9a54-233ef39525c5" />

    >**Note**: If you face any issues on validation, please perform the next steps till the end of this lab and then click on validate button again.

## Task 6 - Using the Document Intelligence Studio

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

      ![Open Azure resource group](media/SHC2a.3.1.png)

1. Select the **AI-in-a-Day** resource group.

1. Select the **aiinadaystorage<inject key="DeploymentID" enableCopy="false"/>** Storage account.

      ![The Storage account is highlighted from the list of services in the AI-in-a-Day Resource Group](media/inn15.png)

1. Under **Settings**, navigate to the **Resource sharing (CORS)** page. 

      ![The CORS is highlighted from the list of services in the AI-in-a-Day Resource Group](media/storaheaccount-CORS.png)
    
1. On the Resource Sharing (CORS) page, ensure that you are on the **Blob service** **(1)** tab, and enter the following values into the table **(2)** and then select **Save** **(3)** to save the CORS settings.  

    | Parameter                   | Value                                              |
    | --------------------------- | -------------------------------------------------- |
    | Allowed origins             | Enter `https://formrecognizer.appliedai.azure.com` |
    | Allowed methods             | Select all of the available methods.               |
    | Allowed headers             | Enter `*`                                          |
    | Exposed headers             | Enter `*`                                          |
    | Max age                     | Enter `200`                                        |

      ![The CORS options are set for the storage account](media/storage-cors-blob.png)

1. Return to the Azure Portal page with your storage account. Navigate back to the **AI-in-a-Day** resource group and select the Document Intelligence resource **aiinaday-formrecog<inject key="DeploymentID" enableCopy="false"/>**.

      ![The AI Services service is selected](media/inn11.png)

1. Select the **Keys and Endpoint** **(1)** option under Resource Management and click on **Show Keys** **(2)**. Then, copy the value for **KEY 1** **(3)** and the **Endpoint** **(4)**. Paste these into a text file for later use.

      ![The AI Services key and endpoint are selected](media/inn12.png)

1. Navigate to the [Document Intelligence Studio](https://formrecognizer.appliedai.azure.com/), scroll-down to **Custom models** and select **Get Started** under **Custom model**. Use the credentials provided in the Environment Details to Sign-in.

      ![Create new custom model](media/updated-document-ai.png)

1. In the Custom models page, under **My Project** click on **+ Create a project**.
  
      ![Project](media/updated-doc-ai-2.png)

1. In the Enter Project Details pane, enter the Project Name as **covid19abstract (1)** and add the description as **Extracting Abstract from the documents (2)**. Click on **Continue (3)**.

      ![The covid19abstract project has been created](media/lab2a-t6-frs3.1.png)
    
1. In the **Configure service resource**, provide the following details and click on **Continue (4)**.
   
    | Parameter                   | Value                                |
    | --------------------------- | -------------------------------------|
    | Subscription                | Select the default subscription  **(1)**    |
    | Resource Group              | Select `AI-in-a-Day` **(2)**                |
    | Document Intelligence or AI Service Resource| aiinaday-formrecog<inject key="DeploymentID" enableCopy="false"/> **(3)**|
    

      ![Project](media/form-service.png)
    
1. Next in the connect training data source, select the below values from the drop-down and click on **Continue (6)**.

    | Parameter                   | Value                                |
    | --------------------------- | -------------------------------------|
    | Subscription                | Select the default subscription **(1)**     |
    | Resource Group              | Select `AI-in-a-Day` **(2)**                |
    | Storage account             | Select aiinadaystorage<inject key="DeploymentID" enableCopy="false"/> **(3)** |
    | Blob container              | Select `covid19temp` **(4)**                 |
    | Folder path                 | Enter `papers` **(5)**                      |

      ![Project](media/form-training.png)
    
    > **Note**: If you are unable to select the Storage Account in the Connect training data source page, signout and signin from the Document Intelligence Studio with the given credentials. Re-perform the task from Step-8.

1. Review the details and click on **Create project**.

      ![Project](media/SHC2a.6.13.png)
  
1. After creating a new project, you will be sent to the project for tagging in Label data. Select **+Add a Field** **(1)** to create a new field click on **Field** **(2)**, type `Abstract` in the Field, and hit enter. By this, you have created a new Abstract Field.

      ![The Abstract tag has been created](media/innovate5.png)
    
      ![The Abstract tag has been created](media/lab2a-t6-frs9.png)

1. Click on Run Layout and wait for the layout to complete for the first document. Locate the document’s abstract. Note that in some documents, the abstract may be on the second page. However, after running the layout, the abstract may not be automatically detected, and all text might be highlighted in yellow. In this case, you will need to manually select each word in the abstract before tagging.

      ![Running layout for a document](media/L2-T6-S15.png)

      ![Running layout for a document](media/lab2a-t6-frs7.png)

1. Once the layout is generated for the first document, move on to the next document. We will tag each of the five papers, so navigate to each in turn, allowing the layout to be processed. To ensure tagging is successful, you must first run the layout for a document, navigate to another document, and then return to the first document before beginning the tagging process. Layout generation happens only once per document, so after it is generated, you can return to the document and proceed with tagging.

1. Return to the second PDF and manually select each word in the **Abstract** section. Once highlighted, select the **Abstract** tag to tag this section. You will need to select each word individually rather than selecting a box. After tagging, you should see a tag logo next to the PDF. If the tag logo appears, it confirms that tagging was successful for this document.

      ![The first PDF has been viewed, and the second PDF has been tagged](media/L2-T6-S16.1.png)

1. Return to the first PDF and highlight the word **ABSTRACT** and the abstract. If the abstract is lengthy, as in this example, it is okay to include just the first paragraph. Then, select the **Abstract** tag to tag this document. Ensure that the viewed icon (an eye) changes to a tag icon. If it does not change to a tag but instead changes to a blank spot without any icons, tagging was unsuccessful. In the event that tagging is unsuccessful, select another document, wait for it to have its layout run, and then return to the prior document and try tagging again.

      ![The first PDF has been tagged](media/L2-T6-S17.png)

1. Continue tagging until all five of the top papers are tagged. Once we have tagged five documents, select the **Train** option.
    
      ![The first five PDFs have been tagged](media/L2-T6-S18.png)

1. In a pop-up to Train a new model, enter **Abstracts (1)** as the ModelID, and select the **Neural (Recommended) (2)** from the drop-down as Build Mode. Then click on **Train (3)**.

      ![The option to train a model has been selected](media/form-model.png)

1. Training a model may take up to 45-60 minutes to succeed. Click on **Go to models**. 

      ![The option to train a model has been selected](media/lab2a-t6-frs14.png)

    >**Note**: As Training a model will take up to 45-60 minutes to succeed. No need to wait for it, you can continue with next Lab. Come back later and review it after an hour.

1. After the model has finished training, you will see that the Status has succeeded.  Although the estimated accuracy is not great, we will use this model.

      ![The Abstracts model has been trained](media/lab2a-t6-frs15.png)

1. From the left menu, select the **Test (1)**. Click on **Browse for a file (2)**. 

      ![An analyzed document](media/innovate6.png)
    
1. In the Upload Files pop-up, Navigate to `C:\Temp\AzureSearch\`, select `2020.09.25.20201616v1.pdf` file **(2)** and then click on **Open** **(3)**.
    
      ![An analyzed document](media/innovate7.png)
   
1. Choose `2020.09.25.20201616v1.pdf` file, select **Run Analysis (1)**. Note that the abstract is on **page 2 (2)** of the PDF. View the **Results (3)** on the right side of the page.
    
      ![An analyzed document](media/innovate8.png)

1. Select **Result (1)** and click on **download icon (2)** to download JSON file. Find the location where the script was downloaded and observe the result code.

      ![An analyzed document](media/lab2a-t6-frs19.1.png)
    
1. For now, you have used custom models with Neural build mode. You can use any sample document which contains Tables and Signatures to Test/Analyze using Template build mode. Please find the reference to explore more about Document Intelligence Studio: [Quickstart: Document Intelligence Studio - Azure Applied AI Services | Microsoft Docs](https://learn.microsoft.com/en-us/azure/ai-services/document-intelligence/overview?view=doc-intel-3.1.0)

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

    <validation step="24474dca-c3ab-413f-ba34-eb278f3c895f" />

## Task 7 - Document Summarization via AI Service for Language Integration

Summarization is one of the features offered by [Azure AI Service for Language](https://docs.microsoft.com/en-us/azure/AI-services/language-service/overview), a collection of machine learning and AI algorithms in the cloud for developing intelligent applications that involve written language. Use this article to learn more about this feature, and how to use it in your applications.

In general, there are two approaches for automatic document summarization: extractive and abstractive. This API provides extractive summarization. Extractive summarization is a feature that produces a summary by extracting sentences that collectively represent the most important or relevant information within the original content. This feature is designed to shorten content that users consider too long to read. Extractive summarization condenses articles, papers, or documents to key sentences. The AI models used by the API are provided by the service, you just have to send content for analysis.

In this task, we are creating a text summarization application with the client library for Python. You will create a Python application that can summarize documents or text-based customer service conversations.

1. Return to the Azure Portal page and navigate to the **AI-in-a-Day** resource group and select the Azure AI services multi-service account **aiinaday-cogsv<inject key="DeploymentID" enableCopy="false"/>**.

    ![The AI Services service is selected](media/inn13.png)

2. Select the **Keys and Endpoint (1)** option under Resource Management.  Then, copy the value for **KEY 1 (2)** and the **Endpoint (3)**.  Paste these into a text file for later use.

    ![The AI Services key and endpoint are selected](media/inn14.png)

3. In the labvm, open a command prompt (`cmd.exe`). To do this, open the Windows menu, type in `cmd`, and select the **Command Prompt** application.

    ![The Command Prompt application is selected](media/open-cmd.png)

4. Run the below command. To run this, you must have Python installed on the machine. If `python.exe` is not accessible as part of the path--meaning you get an error when trying to run `pip`, navigate to where Python is installed.  The `pip.exe` program is inside the `\Scripts\` folder.

    ```bash
    pip install azure-ai-textanalytics==5.2.0b1
    ```

    ![Pip has installed the azureai text analytics package for Python](media/lab2a-t7-pip-azureai.png)

5. Open **File Explorer** and navigate to the path `C:\Temp\AzureSearch\`. Select the file named **summarization.py** and open it in **Notepad or IDLE**. 

6. Replace the **key** and **endpoint** in the file with AI services multi-service account named **aiinaday-cogsv<inject key="DeploymentID" enableCopy="false"/>** which you have already copied in step-2 of the same task and finally save the file.
    
    ![summarization](media/lab2a-t7-summarization.png) 

7. Navigate to the command prompt and run the following commands.

   ```bash
   cd C:\Temp\AzureSearch\
   ```
   ```bash
   python summarization.py
   ```
   
    ![summarization](media/lab2a-t7-summ-results.png)

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

   <validation step="9783aabe-40ad-4cba-b3b6-6b1a967e27c6" />

You can find more references about Document Summarization from here: [Quickstart: Get started with Language Studio - Azure AI Services | Microsoft Docs](https://docs.microsoft.com/en-us/azure/AI-services/language-service/language-studio). Use this article to learn about Language Studio, and testing features of Azure AI Service for Language Integration.

## Summary 

In this lab, you have explored Azure Document Intelligence and AI Services and extracted, analyzed, and summarized key information from documents.

### You have successfully completed the lab
