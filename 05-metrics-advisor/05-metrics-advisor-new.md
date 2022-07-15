# Lab 4 - Data monitoring and anomaly detection using Metrics Advisor in Azure Cognitive Services 

This lab covers the Metrics Advisor service features from Azure Cognitive Services.

## Before the hands-on lab

**Duration**: 30 minutes

You should follow all of the steps provided in this section _before_ taking part in the hands-on lab ahead of time as some of these steps take time.

## Task 1 - Prepare Azure Machine Learning workspace

1. Navigate to [the Azure portal](https://portal.azure.com) and log in with your credentials. Then, select **Resource groups**.

    ![Open Azure resource group](media/azure-open-resource-groups.png)

2. Select the **AI-in-a-Day** resource group.

3. Select the storage account named **aiinadaystorage<inject key="DeploymentID" enableCopy="false"/>**.

    ![Locate storage account in Azure Portal](media/select-azure-storage-account01.png)

4.  Select `Access keys` from the left side menu, and then select `Show keys`. Save the storage account name, the `key1 Key` value, and the `key1 - Connection string` value for later use.

    ![Storage account name and key](media/datastore-03.png)

## Task 2 - Prepare the COVID cases per age group dataset

1. Navigate to back to the resource group `AI-in-a-Day`. Select the AML Workspace named **ai-in-a-day-<inject key="DeploymentID" enableCopy="false"/>**
  
    ![Select AML](media/select-aml-ws.png)
   
2. Click on the **Launch Studio** button.

    ![Launch Studio](media/launchstudio_lab05.png)

2. In Azure Machine learning studio, select **Notebooks**. If you see any `What's New in Notebooks` pop-up, close that.

    ![Launch Studio](media/select-notebook-1.png)

3. In the Notebooks environment, check if `preparemetricsfeeddata.ipynb` is there in **Users\odl_user_<inject key="DeploymentID" enableCopy="false"/>** folder. If it is not there download and import using next steps.

    ![Launch Studio](media/lab-user-1.png)

4. If the folder does not contain `preparemetricsfeeddata.ipynb` notebooks, download the following items to your local machine:

    [Prepare metrics feed data](https://aiinadayaiw.blob.core.windows.net/aiinaday/preparemetricsfeeddata.ipynb)

5. Upload the file by selecting the Upload button from the screen's top right corner, then selecting the blue Upload button to confirm.

    ![Launch Studio](media/select-upload-1.png)
   
   - Click on `Click to browse and select file(s)` and select the file which was just downloaded.
   - Check the "I trust contents of the File" and select upload. 
   
    ![Launch Studio](media/select-upload-2.png)

6. With the Azure Machine Learning Studio and the Jupyter notebook environment open, select the `preparemetricsfeeddata.ipynb` from **Notebooks** under **Author**.

    ![preparemetricfeeddata notebook](media/notebook_05.png)

7. You have to **Choose an available Jupyter kernel**, select **Python 3.8 - AzureML** from the drop-down, and then select **Start Jupyter kernel**.

    ![select kernal for preparemetricfeeddata notebook](media/AI-notebook-kernal-update.png)
    
>**Note**: Please ensure to click on the **Authenticate** button if a pop-up notification comes like below.

   ![authenticate pop-up](media/AI-notebook-authenticate.png)
   
8. Make sure **Python 3.8 -AzureML** kernel is selected as shown in the below screenshot.

   ![Kernal selected](media/AI-notebook-python3.8-kernal.png) 

> **Note**: Make sure you replace the `<BLOBSTORAGE_ACCOUNT_NAME>` and `<BLOBSTORAGE_ACCOUNT_KEY>` values in the variable initialization cell with the values you have noted down at the end of the previous task.

   The notebook will guide you through a list of steps to prepare a time series-based dataset containing JSON files to be fed into the Metrics Advisor workspace. Each JSON file will contain daily data representing the count of COVID-positive cases by age group.

9. Execute the notebook cell by cell (using either Ctrl + Enter to stay on the same cell or Shift + Enter to advance to the next cell) and observe the results of each cell execution.

## Hands-on Lab

## Task 1 - Explore the dashboard of COVID-19 data

Understanding the source datasets is very important in AI and ML. To help you expedite the process, we have created a Power BI dashboard you can use to explore them at the beginning of each lab.

![Azure AI in a Day datasets](../media/data-overview-01-01.png)

To get more details about the source datasets, check out the [Data Overview](../data-overview.md) section.

To explore the dashboard of COVID-19 data, open the `Azure-AI-in-a-Day-Data-Overview.pbix` file located on the desktop of the virtual machine provided with your environment.

## Task 2 - Explore lab scenario

Besides collecting data about COVID-19 cases, it is also essential to ensure the accuracy of the reporting. Accuracy check is where continuous monitoring of incoming data feeds doubled by automatic detection of anomalies plays a critical role. Data is valid for analysis as long as it is reliable and contains the minimum possible number of errors. With distributed data sources and numerous reporting entities, automatic anomaly detection is the best choice to minimize that number.

Using Cognitive Services Metrics Advisor, we will demonstrate how to improve the case surveillance data quality by identifying as early as possible anomalies.

The following diagram highlights the portion of the general architecture covered by this lab.

![Architecture for Lab 5](./../media/Architecture-5.png)

The high-level steps covered in the lab are:

- Explore the dashboard of COVID-19 data
- Explore the lab scenario
- Onboard your time series data in the Metrics Advisor
- Explore anomalies detected in your data
- Perform root cause analysis
- Explore anomalies with hard thresholds (optional)

## Task 3 -  Deploy Azure Cognitive Services Metrics Advisor instance

1. Navigate to the Azure portal and click on the menu icon, and select **+ Create a resource**

   ![](media/menu_icon.png)  

2. In the search bar, type **Metrics Advisor** and then select **Create**

   ![](media/AI-metric-advisor-create.png)  

3. Under Project details on Create Metrics Advisor page provide the following details and click **Review + Create**

    * Subscription : Select the available Subscription
    * Resource Group : **Ai-in-a-Day**
    * Region : Select the available Region from the dropdown
    * Name : **metricsadvisor-<inject key="DeploymentID" enableCopy="false"/>** 
    * Pricing tier: **S0**
    * Make sure to mark checkbox ☑ for **I confirm I have read and understood the notice below**.
   
     ![](media/AI-metric-advisor-latest.png)

4. Once the validation is passed, click on **Create**. The deployment could take up to **60 minutes** to complete, although it normally finishes in less than **10 minutes**. Please wait until the deployment gets succeeded and then you can proceed with the next task.

## Task 4 - Configure the "COVID cases by age group" Metrics Advisor data feed

1. Back to the Home page in Azure Portal, in the list of your recent resources, locate the Azure Metrics Advisor workspace and select it. If you are prompted to sign in again, use the same lab Azure credentials you used at the previous step.
![Open Azure Metrics Advisor](./media/metrics-advisor.png)

2. On the Metrics Advisor Overview page, select the `Go to workspace` link in the first section to start working with the web-based [Metrics Advisor workspace](https://metricsadvisor.azurewebsites.net/).

    ![Start the web-based workspace](./media/metrics-advisor-overview.png)

3. On the Metrics Advisor welcome page, select your directory, subscription, and workspace information and select **Get started**. You are now prepared to create your first Data feed.

   > **Note:-** If you see that **Get Started** button is not available for you, it means that the metric advisor service is not fully available yet. Please wait for 20-30 minutes and try again after refreshing the page.

    ![Connect to Metrics Advisor workspace](./media/metrics-advisor-connect.png)

4. With the Metrics Advisor workspace opened, select the **Add datafeed** option from the left navigation menu.

5. Add the data feed by connecting to your time-series data source. Start by selecting the following parameters:

    - **Source type**: `Azure Blob Storage (JSON)`
    - **Granularity**: `Daily`
    - **Ingest data since (UTC)**: `2022-02-01`
    - **Connection string**: provide the connection string from the blob storage access keys page. (`key1 - Connection string` copied on **Before the hands-on lab** part or else please  follow Task 1 of Before the hands-on lab) 
    - **Container**: `jsonmetrics`
    - **Blob template**: `%Y-%m-%d.json` (since the daily json files are provided in with naming format)
    - **JSON format version**: `v2` (since we'll be using the age group dimension in our data schema)

    ![Data feed source properties](./media/adddatafeed1.png)

6. Select the **Load data button** to validate the configured connection. You can find the **Load Data** option just below the **JSON format version** on the right side, so scroll to the right to see the button. If there is an error at this step, check that your connection string and blob template are correct and your Metrics Advisor instance is able to connect to the data source.

7. Once the data schema is loaded and shown like below, configure the appropriate fields as Dimension, Measure or Timestamp, and select **Verify schema**.

    ![Schema configuration](./media/schemconfig1.png)

8. Scroll down to the bottom of the page. For **Automatic roll-up** settings, select the **I need the service to roll-up my data** **(1)** option, select the link **Set roll-up columns**, and include both dimensions **(3)**.

    ![Automatic rollup settings](./media/automaticrollup1.png)

9. In the **Advanced settings (1)** section, inside **Ingestion options**, set **Stop retrying after (2)** to **0** hours to stop the ingestion process after the first run. 
    
    ![Advanced settings](./media/advancedsettings.png)

10. In the **Misc** section, choose the option to **Fill previous** for the anomaly detection model.

    ![Misc settings](./media/fillprevious.png)

11. Provide the **Data feed name**: `covid-ages` and select **Submit** to confirm and submit the data feed.

    ![Submit schema configuration](./media/submitdatafeed.png)

12. Wait for the ingestion progress dialog and select the **Details** link in order to observe the ingestion log by timestamp. Wait until the ingestion completes with success for all ingested json files.

    ![Check the ingestion progress](./media/ingestionprogress1.png)


## Task 5 - Explore anomalies detected in data

After the data feed is added, Metrics Advisor will attempt to ingest metric data from the specified start date. It will take some time for data to be fully ingested, and you can view the ingestion status by clicking Ingestion progress at the top of the data feed page. If data is ingested, Metrics Advisor will apply detection and continue to monitor the source for new data.

When detection is applied, you can select one of the metrics listed in the data feed to find the Metric detail to:

- View visualizations of all time series slices under this metric
- Update detecting configuration to meet expected results
- Set up notification for detected anomalies


1. Select the **Visit data-feed: covid-ages** button to navigate to the data feed overview page.

    ![Check the ingestion progress](./media/ingestionprogress2.png)

2. In the data feed page, select the `count` metric under the **Metrics** section.

    ![Go to the count metric details page](./media/browsemetricdata1.png)

3. To modify the inspected time window, change the start and end time of the interval from the calendar above the graphic representations.
    
    ![Change monitoring time window](./media/AI-metric-advisor-count-overview.png)

4. Make sure you see the ingestion process completed on recent historical data **(1)** as illustrated in the picture below. When all data is ingested, in the left configuration section, under the **Metric-level configuration** **(2)**, change the default metric-level configuration to use Smart detection, set the sensitivity level to 81, and use this configuration:
    - Value **Out of boundary** is an anomaly 
    - Do not report the anomaly until **10%** of the latest **1** points are detected as anomalies.

    > **Note:-** It can take 30 minutes to complete recent historical data, so please wait or continue with the next lab and come back later and continue this lab.

    ![Metric-level configuration](./media/metric-level-configuration-smart1.png)

>Note
>
> - To view the diagnostic insights, click on the red dots **(3)** on time series visualizations, which represent detected anomalies, and select the link **To incident hub**. 
>
> - Spend a few minutes to change some parameters inside the **Metric-level configuration** section and observe the change of reported anomalies (red points) on the series data representation.

5. In the metrics browser page, select the **Incidents** tab and click on the **Dimension filter** **(1)** to see the incidents related to the number of cases that required hospitalization **hosp_ym = Yes** **(2)** and **Confirm** **(3)**. 

    ![Filter incidents by hospitalization steps](./media/AI-metric-advisor-filter.png)

6. From the list, select the anomaly reported for SUM of hospitalized cases.
    
    ![Filter incidents by hospitalization](./media/AI-metric-advisor-sum-yes.png)

## Task 6 - Perform root cause analysis

1. In the incidents hub, notice the **Root cause** section where you should find reported the main contributors for the detected anomaly, age groups that contributed to the sum of cases reported as anomaly. Also, in the **Diagnostic** tree, hover on each age group node to investigate its contribution to the incident.
    ![Incident Hub diagnostics](./media/root_cause_incidents_hub1.png)

2. In the **Diagnostics** **(1)** section, navigate to **Metrics drill-down** **(2)** and notice the current point Value (number of cases) and the **Diff**  from the identified **Baseline** **(3)**. Choose the **age_group** dimension to drill-down by it and check the same **Delta** percent and **Diff** value from the baseline for the anomalies detected at the current point. 

     ![Metrics drill down](./media/metricdrill1.png)

    ![Metrics drill down](./media/metrics-drill-down1.png)


## Task 7 - Explore anomalies with hard thresholds (optional)

As an optional exercise, you can create a different detection configuration based on hard thresholds rather than smart detection.

To do this, go back to the metric detection configuration screen and try a new configuration as follows **(1)**:

- **Hard threshold**
- Value **Out of range** Min: **1000** and Max: **9000** is an anomaly
- Do not report the anomalies until 100% of the latest **10** points are detected as anomalies

We should have marked as anomalies the points in time where a number of cases are below or above a fixed threshold for 10 continuous points in time.(Notice the yellow dots **(2)** before the reported anomalies and how the threshold is delimited by the red horizontal lines **(3)**).

![Hard threshold](./media/AI-metric-advisor-hardthreshold.png)

## Task 8 - Anomaly detection methods (Optional)

Metrics Advisor offers multiple anomaly detection methods:  **Hard threshold, Smart detection, Change threshold**.

### Subtask 1: Smart Detection

Smart detection is powered by machine learning that learns patterns from historical data and uses them for future detection. When using this method, the  **Sensitivity**  is the most important parameter for tuning the detection results.

1. Under the **Metric-level configuration**, select the **Smart detection** configuration. Drag the **Sensitivity** slider to a smaller value and notice the visualization change on the right side. 

    ![](./media/smart-lower.png)

2. Sensitivity can affect the width of the expected value range of each point. Now drag the **Sensitivity** slider to a larger value and notice the expected value range will be tighter and more anomalies will be reported.

    ![](./media/smart-larger.png)

### Subtask 2: Change threshold

Change threshold is normally used when metric data generally stay around a certain range. The threshold is set according to **Change percentage**. The **Change threshold**  mode is able to detect anomalies in the scenarios:

-   Your data is normally stable and smooth. You want to be notified when there are fluctuations.
-   Your data is normally unstable and fluctuates a lot. You want to be notified when it becomes too stable or flat.

1.  Under the **Metric-level configuration**, select  **Change threshold** as your anomaly detection method. 

    ![](./media/change-threshold.png)
    

2. Select the **out of threshold** to detect fluctuations and set the value to **10%**. Any data point that changes over 10% compared to the previous one will be detected as an outlier.

    ![](./media/out-of-threshold.png)

3. Now select **within the threshold** to detect flat lines in your data and set the value to **0.01%**. Any data point that changes within 0.01% compared to the previous one will be detected as an outlier. Because the threshold is so small (0.01%), it detects flat lines in the data as outliers.

    ![](./media/within-threshold.png)


>**Note**: For more information, see [Anomaly detection methods](https://docs.microsoft.com/en-us/azure/applied-ai-services/metrics-advisor/how-tos/configure-metrics#anomaly-detection-methods).

## Task 9 - Configuring Metrics 

1. To browse the metrics for a specific data feed, go to the **Data feeds** page and select **covid-ages** from the feeds. This will display a list of metrics associated with it.

2. Select **count** to see its details. In this view.

   ![](./media/metrics-browse.png)

When you first view a metric's details, you can load a time series by letting Metrics Advisor choose one for you or by specifying values to be included for each dimension.

3. You can also select time ranges and change the layout of the page (1)

4. You can select the **Incidents** tab to view anomalies (2).

    ![Metrics drill down](./media/layout-browse.png)

## Task 10 - Fine Tuning the detection configuration

A metric can apply one or more detection configurations. There's a default configuration for each metric, which you can edit or add to according to your monitoring needs. The new auto-tuning feature makes this distinction between anomaly types possible. As of now, there are five supported anomaly patterns:

-   Spike
-   Dip
-   Increase
-   Decrease
-   Steady

>**Note**: The auto-tuning feature is only applied to the 'Smart detection' method.

1. After the metrics are onboarded to Metrics Advisor, the system will try to perform statistics on the metrics to categorize **anomaly pattern** types and **series value** distribution. By providing this functionality, you can further fine tune the configuration based on their specific preferences. In the beginning, it will show a status of **Initializing**.

    [![Screenshot of Metrics Advisor U I with Initializing auto-tuning text visible.](https://docs.microsoft.com/en-us/azure/applied-ai-services/metrics-advisor/media/metrics/autotuning-initializing.png)](https://docs.microsoft.com/en-us/azure/applied-ai-services/metrics-advisor/media/metrics/autotuning-initializing.png#lightbox)

### Subtask 1 - Choose to enable auto-tuning on anomaly pattern and series value

1. Once the initializing is completed, select the **Auto Tuning** option as shown below.

    ![](./media/auto-tuning-select.png)

You will be greeted with the **Auto tuning preference** page.

1. The **Auto tuning preference** feature enables you to tune detection configuration from two perspectives **anomaly pattern** and **series value** based on your specific use case. In our case, we will choose the **Anomaly pattern.**

2. For the  **anomaly pattern**  option, the system will list out different anomaly patterns that were observed with the metric. You can choose which ones you're interested in and select them, and the unselected patterns will have their sensitivity **reduced** by default.

    ![](./media/auto-tuning-select-1.png)

3. Select the **Apply Pattern preference** option **(1)**, followed by selecting **Spike** **(2)** as the anomaly pattern, and then select **Next** **(3)** to go to the next section.

### Subtask 2 - Tune the configuration for selected anomaly patterns

The next step is to fine-tune the configuration for each. There's a global **sensitivity** that is applied for all series. For each anomaly pattern, you can tune the **adjustment**, which is based on the global **sensitivity**.

1. You must tune each anomaly pattern that has been chosen individually. In our case, the **Spike** **(1)** anomaly pattern will be visible. Under the **Adjustment for anomaly patterns**, adjust the slider to change the **sensitivity**. Now select **Next** **(2)** to set up alerts.

    ![](./media/pattern-based-sensitivity.png)

### Subtask 3 - Set up alert rules

Once the detection configuration for capturing valid anomalies is tuned, it's still important to input **alert rules** to make sure the final alert rules can meet eventual business needs. There are a number of rules that can be set, like **filter rules** or **snooze continuous alert rules**.

1. On the **Setup alert Rules** page under the **Configuration name** enter **Test-Alert** **(1)** and leave the **Hooks** unselected. Expand the **Filter anomaly based on value** section **(2)** and the **Advanced Settings** **(3)**.

    ![](./media/setup-alert-rules.png)

2. On the **Filter anomaly based on value**, select the **This Metric** setting, and from the drop-down select **Out of range** **(2)**. Leave the **Snooze successive anomalies** **(3)** unchanged. Select the **Anomalies in all series** **(4)** under the **Alert Scope** section. Now select **Save and close** **(5)**.

    ![](./media/setup-alert-rules-1.png)

3. You can see the newly configured **Test-Alert** under the **metric-level configuration** pane on the left.

    ![](./media/setup-alert-rules-2.png)

After configuring all the settings described in the section above, the system will orchestrate them together and automatically detect anomalies based on your inputted preferences. The goal is to get the best configuration that works for each metric, which can be achieved much easier through the use of the new  **auto-tuning**  capability.


## Task 11 - Tune the configuration for a specific series or group

1. Select **Advanced configuration** below the metric level configuration options to see the group level configuration.

    ![](./media/advanced-config-1.png)

2. You can add a configuration for an individual series or group of series by clicking the **'+'** icon in this window. 
    
    ![](./media/detect-config.png)

3. Select the **'+'** for **Configuration for series group**. Specify at least one **dimension value** **(1)** for a group-level configuration to identify a group of series. Leave the conditions as default and select **Save**.

    ![](./media/series-group-config-01.png)

4. Under the **Metric-level configuration** pane, notice the **Series group configs** value has changed to **1**.

    ![](./media/series-group-config-2.png)

5. Select **Advanced configuration** once again to add **specific series** level configuration.

    ![](./media/advanced-config-1.png)

6. Select the **'+'** for **Configuration for specific series**. Specify all **dimension values** **(1)** and **(2)** for series-level configuration to identify a specific series. Leave the **Conditions** as default and select **Save**.

    ![](./media/specific-config-01.png)

7. Under the **Metric-level configuration** pane, notice the **Specific series configs** value has changed to **1**.

    ![](./media/series-specific-config-2.png)

8. This configuration will be applied to the group of series or specific series instead of the metric level configuration. After setting the conditions for this group, **save**.

    ![](./media/save-config-01.png)

## Task 12 - Preset events

Sometimes, expected events and occurrences (such as holidays) can generate anomalous data. Using preset events, you can add flags to the anomaly detection output during specified times. This feature should be configured after your data feed is onboarded. Each metric can only have one preset event configuration.

>**Note**: Preset event configuration will take holidays into consideration during anomaly detection and may change your results. It will be applied to the data points ingested after you save the configuration.

1. Select the **Configure Preset Event** button next to the metrics drop-down list on each metric details page.

    ![](./media/configure-preset.png)

2. In the window that appears, Make sure **Enable holiday event** **(1)** is selected to use the configuration.

3. On the **Strategy** option, select **Suppress holiday** **(2)**.

4. On the **Country Dimension** option select **age_group** **(3)** from the drop-down. You will notice a new field is added named **Country of interest**.

5. On the **Country of interest** field, you will see two options. On the **Standard country code**, select your country from the drop-down and on the **age_group** option select
**__SUM__**.

6. On the **Holiday option**, select **Only NON-PTO Holidays**.
Leave the rest of the options as default. The **Holiday event** pane should look like this: 

    ![](./media/holiday-event.png)

7. The **Cycle event** is used to reduce anomalies if they follow a cyclic pattern, but it will report an anomaly if multiple data points don't follow the pattern. Select the downward arrow to expand the **Cycle event** **(1)**.

8. Make sure  **Enable Cycle event** **(2)** is selected to use the configuration.

9. On the **Cycle option**, select **MoM** **(3)**.

    ![](./media/cycle-event.png)

10. On the **Strict mode**, select **Yes** **(4)** and click on **Save** to save the preset.

11. You have now successfully configured **Present events**.

>**Note**: For more information, see [Present Events](https://docs.microsoft.com/en-us/azure/applied-ai-services/metrics-advisor/how-tos/configure-metrics#preset-events).

## Task 13 - View recent incidents

Metrics Advisor detects anomalies on all your time series data as they're ingested. However, not all anomalies need to be escalated because they might not have a significant impact. Aggregation will be performed on anomalies to group related ones into incidents. 

1. To view these incidents select the **Incident** tab on the metrics details page.

    ![](./media/select-incident.png)

2. From the **Incident** page, select an incident to go to the **Incidents Analysis** page, where you can see more details about it. 

    ![](./media/view-incident.png)
    
    ![](./media/view-incident-1.png)

3. On the **Incident hub**, select **Home** to find the page where you can find all the incidents under the specific metric.

    ![](./media/view-incident-2.png)
