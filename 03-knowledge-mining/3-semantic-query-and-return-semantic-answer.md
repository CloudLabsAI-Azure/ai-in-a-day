# Lab 3 - Knowledge Mining with Azure AI Search

### Estimated Duration: 2 Hours

This lab covers Azure AI Search Services, particularly [Semantic ranking](https://docs.microsoft.com/en-us/azure/search/semantic-ranking) using [semantic query](https://docs.microsoft.com/en-us/azure/search/semantic-how-to-query-request?tabs=semanticConfiguration%2Cportal#create-a-semantic-configuration) and [return a semantic answer](https://docs.microsoft.com/en-us/azure/search/semantic-answers?tabs=semanticConfiguration).

Semantic search is a premium feature in Azure AI Search that invokes a semantic ranking algorithm over a result set and returns semantic captions (and optionally semantic answers), with highlights over the most relevant terms and phrases. Both captions and answers are returned in query requests formulated using the "semantic" query type.

The semantic ranking is an extension of the query execution pipeline that improves precision by reranking the top matches of an initial result set. In order to complete processing within the expected latency of a query operation, inputs to the semantic ranker are consolidated and reduced so that the underlying summarization and reranking steps can be completed as quickly as possible.

>**Note**: Ensure to complete Lab 2 before proceeding with this lab.

## Lab Objectives

- Task 1 - Enabling the Semantic ranker in the AI Search service
- Task 2 - Creating a Semantic Configuration
- Task 3 - Semantic search using the semantic configuration in Azure Portal
- Task 4 - Semantic Query using REST APIs

## Task 1 - Enabling the Semantic ranker in the AI Search service

1. Navigate to **AI-in-a-Day** resource group in the [Azure portal](https://portal.azure.com).

2. Locate **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and select it.

   ![The Search service is highlighted from the list of services in the AI-in-a-Day Resource Group](media/inn6.png)
   
3. Select the **Semantic ranker (1)** under settings in the left menu. From the Semantic ranker pane, select the **Free** tier by clicking on **Select Plan (2)**. Once you click on a select plan, a free tier plan will be selected for the Semantic search.

   ![Semantic search service to be selected](media/inn16.png)

   
## Task 2 - Creating a Semantic Configuration

1. Navigate to the **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and then select **Indexes (1)**. You will be able to see the list of indexes, click on the **covid19temp (2)** index for adding semantic configuration.

   ![Semantic config](media/AI4.png)
   
2. In the **covid19temp** index pane, select **Semantic configurations** **(1)** and click on **+ Add semantic configuration** **(2)**.

   ![Semantic config](media/inn20.png)

3. You will see a tab appear on the right side with **New semantic configuration**. Enter the below values:

    | Parameter                   | Value                                        |
    | --------------------------- | -------------------------------------------- |
    | Name (1)                    | my-semantic-config                           |
    | Title field (2)             | Select `metadata/title` from the drop-down   |
    | Field name under Content fields (3) | Select `bib_entries/BIBREFO/title` from the drop-down |
    | Field name under Key fields (4)    | Select `bib_entries/BIBREFO/ref_id` from the drop-down |
  
   Click on **Save** **(5)**.
  
   ![Semantic configuration](media/lab2b-new-sc.png)
  
4. You will be able to see the added semantic configuration **(1)** under the Semantic Configurations tab. Click on **Save** **(2)** to save the changes made.

   ![Semantic configuration](media/inn21.png)

## Task 3 - Semantic search using the semantic configuration in Azure Portal

1. Navigate to **Overview** **(1)** of **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and then click on **Search explorer (2)**.

   ![Semantic search](media/inn17.png)
   
2. In the **Search explorer** pane, select the Index **covid19temp** **(1)**.Click on the **Query options** **(2)**.
  ![Semantic search](media/L3T3S2.png)

3. In a popup on the right side, turn **On** **(1)** the Semantic ranker, select **my-semantic-config** **(2)** from the drop-down under Semantic configuration, which you created in the previous task. Click on **Close** **(3)**. 

   ![Semantic search](media/L3T3S3.png)
   
4. Click on **Search** **(1)**.Wait for a few seconds to complete the search and scroll down to the **Results** **(2)**part on the same page. You will be able to see the output for the semantic search using semantic configuration.

   ![Semantic search](media/L3T3S4.png)
   
## Task 4 - Semantic Query using REST APIs

In this task, you are going to perform the semantic search using a query in [REST APIs](https://docs.microsoft.com/en-us/azure/search/search-get-started-rest). For now, you will perform with [Postman desktop app](https://www.getpostman.com/) to send requests to Azure AI Search.

1. In the **Search service** resource **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** pane, select **Keys** under settings from left menu.

    ![Semantic search](media/inn18.png)
   
1. Make sure to copy **key**, which you can find below the **Manage query keys**, and save it in the text file for later use.

    ![Semantic search](media/inn19.png)
   
1. Navigate to **LABVM Desktop** and open the **Postman** application by double-clicking on it.

    ![Semantic search](media/lab2b-ssp11.png)
   
1. In the Overview page of the Postman app, click on **Create a request** under Get Started.

    ![Semantic search](media/lab2b-ssp12.png)
   
1. Select the **POST (1)** Method from the drop-down. Enter the request URL given below and make sure to replace the search service name as **aiinaday-cog-<inject key="DeploymentID" enableCopy="false"/>** and the index name as **covid19temp**.

   `https://[search-service-name].search.windows.net/indexes/[index-name]/docs/search` **(2)**.
   
   ![Semantic search](media/lab2b-ssp13.png)

1. In the Parameters section, enter the below values for **api-version** **(1)** and **api-key** **(2)**.

    | Parameter           | Value                                        |
    | --------------------| -------------------------------------------- |
    | api-version         | 2021-04-30-Preview                           |
    | api-key             | Enter the manage query key which you have copied earlier in Step - 2    |
   
   After updating the parameters, your **Request URL** **(3)** should be the same as shown in the below screenshot.
   
    ![Semantic search](media/lab2b-ssp14.png)

1. For adding a query, you need to select the **Body** **(1)** section. Then click on **raw** **(2)** as code type and select **JSON** **(3)** as an extension from the drop-down. Copy-paste the query which is given below into the **coding area** **(4)** and click on **Send** **(5)**.

   ```bash
   {
       "search": "What is Endoplasmic Reticulum",
       "queryType": "semantic",
       "queryLanguage": "en-us",
       "semanticConfiguration": "my-semantic-config",
       "answers": "extractive|count-3",
       "captions": "extractive|highlight-true",
       "count": "true"
   }
   ```
   
    ![Semantic search](media/newlatest15.png)
   
1. You will see a **Sending request** in the **Response** section, which will take a few seconds to get the response from the AI search service. Once you get the response check that **Network Status: 200 OK**, review the response and explore with your query requests.

    ![Semantic search](media/lab2b-ssp16.png)

   <validation step="f8d865b8-5e35-495f-97ac-fe80f35e8465" />

    > **Congratulations** on completing the task! Now, it's time to validate it. Here are the steps:
    > - Hit the Validate button for the corresponding task.
    > - If you receive a success message, you can proceed to the next task. If not, carefully read the error message and retry the step, following the instructions in the lab guide.
    > - If you need any assistance, please contact us at labs-support@spektrasystems.com. We are available 24/7 to help you out.


   >**Note**: If you face an issue that the request failed with 401 Forbidden or 403 Forbidden error, this might be caused due to passing invalid authentication credentials or an invalid api-key. For more information, reference this link ```https://docs.microsoft.com/en-us/rest/api/searchservice/http-status-codes#common-http-status-codes```

## Summary

In this lab, you worked on indexing and exploring data, extracting insights, and enabling advanced search capabilities across structured and unstructured content.

### You have successfully completed the lab
