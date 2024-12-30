# Data Analytics with Azure Synapse 

### Overall Estimated Duration : **120 minutes**

## Overview  

**Overview**

Azure Synapse Analytics is an integrated analytics service that combines big data and data warehousing capabilities, enabling efficient data integration, exploration, and analysis. It provides powerful tools for data ingestion, transformation, and querying at scale.

In this hands-on lab, you will learn how to load data into a dedicated SQL pool in Azure Synapse Analytics. You will provision a Synapse workspace, prepare data from Azure Data Lake Storage, and use SQL techniques like COPY and CTAS to load and optimize data. Additionally, you'll handle slowly changing dimensions and perform post-load optimizations to ensure efficient query performance and data integrity.

## Objective 

**Load Data in Relational Data warehouse in Synapse:** This lab offers a step-by-step approach to building and optimizing a relational data warehouse using Azure Synapse Analytics. Begin by provisioning an Azure Synapse Analytics workspace, setting up the foundational environment for data integration and analytics. Prepare data for ingestion by verifying the dedicated SQL pool and exploring data files in Azure Data Lake Storage. Load data into staging tables using SQL scripts and leverage the COPY statement for efficient data transfer. Finally, perform post-load optimizations, such as rebuilding indexes and updating statistics, to enhance query performance and maintain data integrity. This lab equips participants with the essential skills to manage, optimize, and analyze data efficiently in a dedicated SQL pool environment.

## Prerequisites

Participants should have:

- **Understanding of Data Warehousing Concepts:** Basic knowledge of relational databases and data warehousing principles.
- **Familiarity with SQL:** Experience in writing and executing SQL queries.
- **Knowledge of Azure Services:** Basic familiarity with Azure Synapse Analytics and Azure Data Lake Storage.
- **Cloud Platform Experience:** Understanding of cloud-based environments and resource management in Azure.
- **Data Integration Basics:** Awareness of data ingestion techniques and ETL (Extract, Transform, Load) processes.

## Architecture 

The lab architecture is designed to demonstrate the end-to-end process of managing and analyzing data using Azure Synapse Analytics, providing participants with hands-on experience through several key tasks. It starts by provisioning an Azure Synapse Analytics workspace, which serves as the central hub for managing resources, data pipelines, and analytics workloads. The flow proceeds to preparing data for loading, where participants configure the necessary settings for data ingestion and transformation. Following this, data warehouse tables are loaded with relevant datasets, utilizing optimized methods for efficient processing and querying. The final task involves performing post-load optimization, where performance tuning and optimization techniques are applied to ensure the best possible query performance and resource utilization. 

### Architecture Diagram

   ![Azure portal with a cloud shell pane](./Lab-Scenario-Preview/media/lab9.png) 

## Explanation of Components 

The architecture for this lab involves the following key components: 

- **Azure Synapse Analytics Workspace:** Serves as the central hub for managing resources, data pipelines, and analytics workloads, enabling users to provision and organize all components necessary for data processing and analysis.

- **Data Preparation:** This involves configuring Synapse Studio to verify the status of the dedicated SQL pool, inspecting data files in Azure Data Lake Storage, and setting up data transformations or staging tables for loading. This step ensures data is properly prepared for loading into the data warehouse.

- **Dedicated SQL Pool:** A high-performance, scalable environment within Azure Synapse used to store structured data in the data warehouse. It enables efficient querying and analysis of large datasets, when loading data using SQL scripts into staging and target tables.

- **Post-Load Optimization:** Focuses on improving query performance after data has been loaded into the warehouse. This includes tasks like rebuilding indexes and updating statistics, to enhance query execution and optimize resource usage.

## Getting Started with the Lab 

Once you're ready to dive in, your virtual machine and lab guide will be right at your fingertips within your web browser.
 
![Access Your VM and Lab Guide](../Labs/images/labguide-1.png)

### Virtual Machine & Lab Guide
 
Your virtual machine is your workhorse throughout the workshop. The lab guide is your roadmap to success. 

**Note**: Kindly ensure that you are following the instructions carefully to ensure the lab runs smoothly and provides an optimal user experience.
 
## Exploring Your Lab Resources
 
To get a better understanding of your lab resources and credentials, navigate to the **Environment Details** tab.
 
![Explore Lab Resources](../Labs/images/env-1.png)
 
## Utilizing the Split Window Feature
 
For convenience, you can open the lab guide in a separate window by selecting the **Split Window** button from the Top right corner.
 
![Use the Split Window Feature](../Labs/images/spl.png) 

## Lab Guide Zoom In/Zoom Out
 
To adjust the zoom level for the environment page, click the **A↕ : 100%** icon located next to the timer in the lab environment.

![](./images/zoom.png)
 
## Managing Your Virtual Machine
 
Feel free to start, stop, or restart your virtual machine as needed from the **Resources** tab. Your experience is in your hands!
 
![Manage Your Virtual Machine](../Labs/images/res.png)

## Let's Get Started with Azure Portal
 
1. On your virtual machine, click on the Azure Portal icon as shown below:
 
   ![Launch Azure Portal](../Labs/images/sc900-image(1).png)

2. You'll see the **Sign into Microsoft Azure** tab. Here, enter your credentials:
 
   - **Email/Username:** <inject key="AzureAdUserEmail"></inject>
 
       ![Enter Your Username](../Labs/images/sc900-image-1.png)
 
3. Next, provide your password:
 
   - **Password:** <inject key="AzureAdUserPassword"></inject>
 
      ![Enter Your Password](../Labs/images/sc900-image-2.png)

1. If you see the pop-up **Action Required**, click **Ask Later**.

   ![Launch Azure Portal](../Labs/images/action.png)

    >**NOTE:** Do not enable MFA, select **Ask Later**.
     
4. If prompted to stay signed in, you can click "No."
 
5. If a **Welcome to Microsoft Azure** pop-up window appears, simply click "Cancel" to skip the tour.
  
## Support Contact
 
The CloudLabs support team is available 24/7, 365 days a year, via email and live chat to ensure seamless assistance at any time. We offer dedicated support channels tailored specifically for both learners and instructors, ensuring that all your needs are promptly and efficiently addressed.

Learner Support Contacts:
- Email Support: cloudlabs-support@spektrasystems.com
- Live Chat Support: https://cloudlabs.ai/labs-support

Now, click on **Next** from the lower right corner to move on to the next page.

  ![Start Your Azure Journey](../Labs/images/sc900-image(3).png)

### Happy Learning!!
