# Lab 2a - Document Processing and Summarization with Azure Form Recognizer and Cognitive Service for Language

This lab covers Azure Cognitive Services, particularly [Azure Cognitive Search](https://azure.microsoft.com/services/search/) and the [Form Recognizer](https://azure.microsoft.com/services/cognitive-services/form-recognizer/) service.

## Task 1 - Explore the dashboard of COVID-19 data

Understanding the source datasets is very important in AI and ML. To help you expedite the process, we have created a Power BI dashboard you can use to explore them at the beginning of each lab.

![Azure AI in a Day datasets](../media/data-overview-01-01.png)

To get more details about the source datasets, check out the [Data Overview](https://github.com/CloudLabsAI-Azure/ai-in-a-day/blob/main/data-overview.md) section.

To explore the dashboard of COVID-19 data, open the `Azure-AI-in-a-Day-Data-Overview` file located on the desktop (**C:\Users\public\desktop**) 💻 of the virtual machine provided with your environment. If you see the `Introducing the updated mobile layout` pop-up screen, then close it by clicking on `Got it`. Collapse the **Fields** and **Visualizations** tabs to see the clear report.

 ![Azure AI in a Day datasets](./media/powerbireportopen.png)

> **Note:** Please close and reopen the Power BI Desktop document if it throws an error in the first attempt.

## Task 2 - Explore lab scenario

Another critical problem to deal with when it comes to the volumes of research documents covering COVID-19 is the problem of advanced indexing and searching their content. The specific internal structure of research papers (including citations, contributors, and various entities like diagnosis, forms of examination, family relations, genes, medication, symptoms or signs, and treatments) form a reach semantic graph that goes way beyond simple document categorization. An analyst would benefit significantly from exploring the corpus of documents in a way that considers all these complex relationships.

Using the Cognitive Search capabilities, we will create a complex index of documents that allows an analyst to perform an advanced search and explore the inter-document graph relationships.

The following diagram highlights the portion of the general architecture covered by this lab.

![Architecture for Lab 3](./../media/Architecture-3.png)

The high-level steps covered in the lab are:

- Explore dashboard of COVID-19 data
- Explore lab scenario
- Explore the document search process
- Explore the graph search process
- Add a set of new documents and trigger the index update process
- Explore the document and graph search and identify updated results

## Task 3 - Creating Azure Search Indexes

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/azure-open-resource-groups.png)

2. Select the **AI-in-a-Day** resource group.

3. Locate **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>**, select that.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/select-azure-search-service1.png)

4. Copy the search service URL and paste this into a text file. Also make a note of the service account name, which comes before `.search.windows.net`.

    ![The Search service's URL is copied to the clipboard.](media/copy-azure-search-url.png)

5. Navigate to the Keys setting and copy the Primary admin key. Paste this into a text file.

    ![The Search service's API key is copied to the clipboard.](media/lab2-t3-step5.png)

6. Download the `schemas.zip` from https://github.com/CloudLabsAI-Azure/ai-in-a-day/raw/main/03-knowledge-mining/schemas.zip. Unzip `schemas.zip`. There are six files, three prefixed with `abstracts` and three with `covid19temp`. Save these to a directory such as `C:\Temp\AzureSearch\`.

7. Open the `abstracts_datasource.schema` file with a text editor and replace the segment starting `<< TODO:` with your Storage account connection string **<inject key="storageAccountConnectionString" enableCopy="true"/>** and then save the file.  Do the same for `covid19temp_datasource.schema`.

    ![The abstract data source is ready to be updated.](media/edit-abstracts-datasource.png)

8. Open a new PowerShell prompt. Navigate to the following directory `cd C:\Temp\AzureSearch\`. Enter the following code, to create an Azure Search data source, index, and indexer.

    ```powershell
    function Create-AzureSearchIndex {
        param
        (
            [string]$DataSourceFilePath,
            [string]$IndexFilePath,
            [string]$IndexerFilePath,
            [string]$AccountName,
            [string]$ApiKey
        )
        
        $Header = @{
            "api-key" = $ApiKey
        }
        $BaseUri = "https://" + $AccountName + ".search.windows.net"
        
        # Create Data Source
        $Uri = $BaseUri + "/datasources?api-version=2020-06-30"
        Invoke-RestMethod -Method Post -Uri $Uri -Header $header -ContentType "application/json" -InFile $DataSourceFilePath
        
        # Create Index
        $Uri = $BaseUri + "/indexes?api-version=2020-06-30"
        Invoke-RestMethod -Method Post -Uri $Uri -Header $header -ContentType "application/json" -InFile $IndexFilePath
        
        # Create Indexer
        $Uri = $BaseUri + "/indexers?api-version=2020-06-30"
        Invoke-RestMethod -Method Post -Uri $Uri -Header $header -ContentType "application/json" -InFile $IndexerFilePath
    }
    ```

    ![The Create-AzureSearchIndex function has been created in PowerShell.](media/create-azuresearchindex.png)

9. In the same PowerShell prompt, call this function for the `abstracts` index and the `covid19temp` index. Then, fill in your Azure Search account name and Azure Search API key.
    - Azure Search Account Name: **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>**
    - Azure Search API key: You saved the Primary admin key in the text editor in step 5, use that

    ```powershell
    Create-AzureSearchIndex "C:/Temp/AzureSearch/abstracts_datasource.schema" "C:/Temp/AzureSearch/abstracts.schema" "C:/Temp/AzureSearch/abstracts_indexer.schema" "AZURE SEARCH ACCOUNT NAME" "API KEY"

    Create-AzureSearchIndex "C:/Temp/AzureSearch/covid19temp_datasource.schema" "C:/Temp/AzureSearch/covid19temp.schema" "C:/Temp/AzureSearch/covid19temp_indexer.schema" "AZURE SEARCH ACCOUNT NAME" "API KEY"
    ```

    ![The Create-AzureSearchIndex function has been run to create a new index.](media/create-azuresearchindex-use.png)

## Task 4 - Querying Azure Search Indexes

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/azure-open-resource-groups.png)

2. Select the **AI-in-a-Day** resource group.

3. Locate **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>**, select that.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/select-azure-search-service1.png)

4. Select the **Indexes** tab and ensure that you have two indexes created. If the Document Count is 0 for either, wait a couple of minutes and select **Refresh** until the document count appears.

    ![The list of Azure Search indexes.](media/search-service-indexes.png)

5. Once documents are available, select **Search Explorer** to open up the Search Explorer.

    ![The Search Explorer option is selected.](media/search-service-explorer.png)

6. Choose the **covid19temp** index and enter `RNA interference` into the Query string input box, and then select **Search**. This will return the documents which include the phrase "RNA interference."

    ![Articles with the phrase RNA interference.](media/search-explorer-rna-interference.png)

7. We can also see how many articles match a certain search string. In the Query string input box, enter the phrase `Brazil&$count=true` and then select **Search**.  This will return 53 documents.

    ![53 articles reference Brazil.](media/search-explorer-brazil-1.png)

8. Each document returns a large number of fields, but we can specify the fields we would like to see. In the Query string input box, enter the phrase `UNC Chapel Hill&$select=metadata/authors, metadata/title` and then select **Search**. This will return the title as well as detailed information on each author.

    ![Paper titles and authors referencing UNC Chapel Hill.](media/search-explorer-unc.png)

9. The Azure Search service can also generate a demo application. Return to the search service and select the **covid19temp** index.

    ![The covid19temp index is selected.](media/azure-search-indexes-covid19temp.png)

10. Select the **Create Demo App** option.

    ![The Create Demo App option is selected.](media/create-demo-app.png)

11. On the first tab, select `metadata.title` for the Title and `abstract.text` for the Description. Then select **Create Demo App**. After the prompt, select **Download** to download an HTML file named `AzSearch.html`.

    ![Create a demo app.](media/create-demo-app-1.png)

12. Open the demo app HTML file. In the search box, enter the phrase "RNA interference" and select the Search icon. This will return 497 papers relating to RNA interference.

    ![Use the demo app.](media/search-demo-app.png)

## Task 5 - Updating Azure Search Indexes

1. On the desktop, select the Azure Storage Explorer.  

    ![Storage explorer is selected on the desktop.](media/storage-exp-desktop.png)

2. Select the **Connect (1)** option and then click on **Storage account or service (2)**.

    ![storageaccount](media/storage-account.png)

3. Select **Connection String(Key or SAS)** under the Select Connection Method Window and then select **Next**

    ![The Use a connection string option is selected.](media/connectionstring.png)

4. Paste in your storage account connection string **<inject key="storageAccountConnectionString" enableCopy="true"/>**.  Then, select **Next** to continue and **Connect** to complete the operation.

    ![The connection string is filled in.](media/new-lab2a-1.png)

    ![Connect is selected on the storage explorer page](media/new-lab2a-2.png)

5. In Azure Storage Explorer, navigate down the **(1)  lab03** attached storage and expand **(2) Blob containers** select the **(3) `covid19temp` blob container**.  Double-click the **(4)  comm_use_subset** to enter that folder.

    ![The comm_use_subset folder is selected.](media/new-lab2a-3.png)

6. Enter the **pdf_json_refresh** folder. Then, in the **(1) Select All** menu, choose **(2) Select All Cached**. This will highlight all 100 records in the folder.  Select **(3) Copy** to copy these documents.

    ![Select the PDF refresh folder.](media/select-pdf-refresh-folder01.png)
    
    ![Select all cached items and copy them.](media/azure-storage-explorer-201.png)

7. Navigate up to **comm_use_subset** by selecting the upward arrow and then double-click **pdf_json (1)**. Inside this folder, select **Paste (2)** to paste the 100 documents into the **pdf_json** folder. When it finishes, you should have **965 total documents (3)**.

    ![Navigate into the pdf_json folder.](media/upward-arrow01.png)

    ![Paste all cached items into the pdf_json folder.](media/azure-storage-explorer-301.png)

8. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials if not logged in already. Then, select **Resource groups**.

    ![Open Azure resource group](media/azure-open-resource-groups.png)

9. Select the **AI-in-a-Day** resource group.

10. Select the **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** Search service.

    ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/select-azure-search-service1.png)

11. Navigate to the **Indexers** section and select the **covid19temp** indexer.

    ![The covid19temp indexer is selected.](media/azure-search-indexers.png)

12. Select the **Run** option to process the 100 documents. Although we can configure an indexer to run periodically, this indexer will only run when manually engaged.  Select **Yes** to run the indexer.

    ![The covid19temp indexer is set to run.](media/azure-search-indexers-run.png)

13. The indexer will run. It should complete within 15-30 seconds to process the 100 new documents. You may need to select **Refresh** to see the indexer's progress.

    ![The covid19temp indexer has finished running.](media/azure-search-indexers-refresh.png)

14. Return to the **Indexes** tab for the Search service and ensure that the **covid19temp** index has 965 documents. If it still reads 865, wait 30 seconds and select **Refresh** to check again.

    ![The covid19temp index has finished updating.](media/azure-search-indexes-update.png)

15. Select the **covid19temp** index to return to the Search explorer. When we had 865 documents, 53 of them pertained to Brazil. We can confirm that this update was successful by entering `Brazil&$count=true` and selecting **Search**. This will now return 57 results instead of the prior 53.

    ![57 documents pertaining to Brazil.](media/search-explorer-brazil-2.png)

## Task 6 - Using the Form Recognizer Studio

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/azure-open-resource-groups.png)

2. Select the **AI-in-a-Day** resource group.

3. Select the **aiinadaystorage<inject key="DeploymentID" enableCopy="false"/>** Storage account.

    ![The Storage account is highlighted from the list of services in the AI-in-a-Day Resource Group](media/select-azure-storage-account1.png)

4. Under **Settings**, navigate to the **Resource sharing(CORS)** page. Ensure that you are on the **Blob service** tab, and enter the following values into the table.

    ![The CORS is highlighted from the list of services in the AI-in-a-Day Resource Group](media/Storage-account-Cors-1.png)

   | Parameter                   | Value                                |
   | --------------------------- | -------------------------------------|
   | Allowed origins             | Enter `*`                            |
   | Allowed methods             | Select all of the available methods. |
   | Allowed headers             | Enter `*`                            |
   | Exposed headers             | Enter `content-length`               |
   | Max age                     | Enter `200`                          |

   ![The CORS options are set for the storage account](media/storage-account-cors.png)

5. Select **Save** to save the CORS settings.

6. Return to the Azure Portal page with your storage account. Navigate back to the **AI-in-a-Day** resource group and select the Cognitive services multi-service account **aiinaday-cogsv<inject key="DeploymentID" enableCopy="false"/>**.

    ![The Cognitive Services service is selected](media/cognitive-service-latest-select.png)

7. Select the **Keys and Endpoint** option under Resource Management. Then, copy the value for **KEY 1** and the **Endpoint**. Paste these into a text file.

    ![The Cognitive Services key and endpoint are selected](media/cognitive-service-latest.png)

8. Click [here](https://github.com/CloudLabsAI-Azure/ai-in-a-day/raw/main/03-knowledge-mining/pdf/2020.09.25.20201616v1.pdf) to download the pdf file named `2020.09.25.20201616v1.pdf` for this lab. Save this to a directory such as `C:\Temp\AzureSearch\`.

> **Note!** : You can follow this link to download the file. (https://github.com/CloudLabsAI-Azure/ai-in-a-day/raw/main/03-knowledge-mining/pdf/2020.09.25.20201616v1.pdf)

9. Navigate to the [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/), scroll-down to **Custom models** and select **Create new** under **Custom model**. Use the credentials provided in the Environment Details to Sign-in.

    ![Create new custom model](media/lab2a-t6-frs1.png)

10. In the Custom models page, under **My Project** click on **+ Create a project**.
  
    ![Project](media/lab2a-t6-frs2.png)

11. In the Enter Project Details pane, enter the Project Name as **covid19abstract** and add the description as **Extracting Abstract from the documents**. Click on **Continue**.

    ![The covid19abstract project has been created](media/lab2a-t6-frs3.png)
    
12. In the Configure service resource, for **Access By**, select **API endpoint and key**. Enter the **Cognitive Service endpoint** and **API key**, which you have copied to the text file at the earlier of this task. Check the box and leave default for API version. Click on **Continue**.

    ![Project](media/lab2a-t6-frs4.png)
    
13. Next in the connect training data source, select the below values from the drop-down and click on **Continue**.

    | Parameter                   | Value                                |
    | --------------------------- | -------------------------------------|
    | Subscription                | Select the default subscription      |
    | Resource Group              | Select `AI-in-a-Day`                 |
    | Storage account             | Select aiinadaystorage<inject key="DeploymentID" enableCopy="false"/> |
    | Blob container              | Select `covid19temp`                 |
    | Folder path                 | Enter `papers`                       |

    ![Project](media/lab2a-t6-frs5.png)

14. Review the details and click on **Create project**.

    ![Project](media/lab2a-t6-frs6.png)
  
15. After creating a new project, you will be sent to the project for tagging in Label data. Select **+** **(1)** to create a new **Field** **(2)**, type `Abstract` in the Field, and hit enter. By this, you have created a new Abstract Field.

    ![The Abstract tag has been created](media/lab2a-t6-frs8.png)
    
    ![The Abstract tag has been created](media/lab2a-t6-frs9.png)

16. Wait for the layout to be run for the first document and locate the document's abstract. Note that for some documents, the abstract is on the second page. Then, move on to the next document. We will tag each of the five papers, so navigate to each in turn, allowing the layout to be run. In order for tagging to be successful, we must first run the layout of a document, navigate to another document, and return to this first document before we begin tagging. Layout generation happens once per document, after which we can return to it and tag our abstract.

    ![Running layout for a document](media/lab2a-t6-frs7.png)

17. Return to the second PDF and select each word in the **Abstract** section. After highlighting this, select the **Abstract** tag to tag this section. Note that you will need to select each word individually rather than selecting a box. After selecting the **Abstract** tag, you should see a tag logo next to the PDF. If you see the tag logo, this means that tagging was successful for this document.

    ![The first PDF has been viewed, and the second PDF has been tagged](media/new-lab2a-4.png)

18. Return to the first PDF and highlight the word **ABSTRACT** and the abstract. If the abstract is lengthy, as in this example, it is okay to include just the first paragraph. Then, select the **Abstract** tag to tag this document. Ensure that the viewed icon (an eye) changes to a tag icon. If it does not change to a tag but instead changes to a blank spot without any icons, tagging was unsuccessful. In the event that tagging is unsuccessful, select another document, wait for it to have its layout run, and then return to the prior document and try tagging again.

    ![The first PDF has been tagged](media/lab2a-t6-frs11.png)

19. Continue tagging until all five of the top papers are tagged. Once we have tagged five documents, select the **Train** option.
    
    ![The first five PDFs have been tagged](media/lab2a-t6-frs12.png)

20. In a pop-up to Train a new model, enter **Abstracts** as the ModelID, and select the **Neural** from the drop-down as Build Mode. Then click on **Train**.

    ![The option to train a model has been selected](media/lab2a-t6-frs13.png)

21. Training a model may take up to 10-15 minutes to get succeed. Click on **Go to models**. 

    ![The option to train a model has been selected](media/lab2a-t6-frs14.png)

22. After the model has finished training, you will see that the Status has succeeded.  Although the estimated accuracy is not great, we will use this model.

    ![The Abstracts model has been trained](media/lab2a-t6-frs15.png)

23. From the left menu, select the **Test**(1). Click on **Browse for a file**(2) and navigate to `C:\Temp\AzureSearch\` and select `2020.09.25.20201616v1.pdf`. Select **Analyze**(3) to see the results. Note that the abstract is on page 2 of the PDF.

    ![An analyzed document](media/new-lab2a-5.png)

24. Select **Result** and click on **download icon** to download JSON file. Find the location where the script was downloaded and observe the result code.

    ![An analyzed document](media/lab2a-t6-frs19.png)
    
25. For now, you have used custom models with Neural build mode. You can use any sample document which contains Tables and Signatures to Test/Analyze using Template build mode. Please find the reference to explore more about Form Recognizer Studio: [Quickstart: Form Recognizer Studio - Azure Applied AI Services | Microsoft Docs](https://docs.microsoft.com/en-us/azure/applied-ai-services/form-recognizer/quickstarts/try-v3-form-recognizer-studio)

## Task 7 - Document Summarization via Cognitive Service for Language integration

1. In the labvm, open a command prompt (`cmd.exe`). To do this, open the Windows menu, type in `cmd`, and select the **Command Prompt** application.

    ![The Command Prompt application is selected](media/open-cmd.png)

2. Run the below command. To run this, you must have Python installed on the machine. If `python.exe` is not accessible as part of the path--meaning you get an error when trying to run `pip`, navigate to where Python is installed.  The `pip.exe` program is inside the `\Scripts\` folder.

    ```bash
    pip install azure-ai-textanalytics==5.2.0b1
    ```

    ![Pip has installed the azureai text analytics package for Python](media/lab2a-t7-pip-azureai.png)

3. Create a new file named **summarization.py** in the location `C:\Temp\AzureSearch\` and add the below code in the file. Make sure to replace the **Key** and the **Endpoint** of the Cognitive services multi-service account **aiinaday-cogsv<inject key="DeploymentID" enableCopy="false"/>** which you have copied to the text file in the previous tasks.

 ```bash
key = "paste-your-key-here"
endpoint = "paste-your-endpoint-here"

from azure.ai.textanalytics import TextAnalyticsClient
from azure.core.credentials import AzureKeyCredential

# Authenticate the client using your key and endpoint 
def authenticate_client():
    ta_credential = AzureKeyCredential(key)
    text_analytics_client = TextAnalyticsClient(
            endpoint=endpoint, 
            credential=ta_credential)
    return text_analytics_client

client = authenticate_client()

# Example method for summarizing text
def sample_extractive_summarization(client):
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import (
        TextAnalyticsClient,
        ExtractSummaryAction
    ) 

    document = [
        "The extractive summarization feature uses natural language processing techniques to locate key sentences in an unstructured text document. "
        "These sentences collectively convey the main idea of the document. This feature is provided as an API for developers. " 
        "They can use it to build intelligent solutions based on the relevant information extracted to support various use cases. "
        "In the public preview, extractive summarization supports several languages. It is based on pretrained multilingual transformer models, part of our quest for holistic representations. "
        "It draws its strength from transfer learning across monolingual and harness the shared nature of languages to produce models of improved quality and efficiency. "
    ]

    poller = client.begin_analyze_actions(
        document,
        actions=[
            ExtractSummaryAction(max_sentence_count=4)
        ],
    )

    document_results = poller.result()
    for result in document_results:
        extract_summary_result = result[0]  # first document, first result
        if extract_summary_result.is_error:
            print("...Is an error with code '{}' and message '{}'".format(
                extract_summary_result.code, extract_summary_result.message
            ))
        else:
            print("Summary extracted: \n{}".format(
                " ".join([sentence.text for sentence in extract_summary_result.sentences]))
            )

sample_extractive_summarization(client)
 ```
    
   ![summarization](media/lab2a-t7-summarization.png) 

5. Navigate to the command prompt and run the following commands.

   ```bash
   cd C:\Temp\AzureSearch\
   python summarization.py
   ```
   
   ![summarization](media/lab2a-t7-summ-results.png)

