# Lab 06: Using Delta Lake with Spark in Azure Synapse Analytics

## Lab Scenario

Delta Lake is an open source project to build a transactional data storage layer on top of a data lake. Delta Lake adds support for relational semantics for both batch and streaming data operations, and enables the creation of a *Lakehouse* architecture in which Apache Spark can be used to process and query data in tables that are based on underlying files in the data lake. In this lab, you will explore Delta Lake which is an open source relational storage area for Spark that you can use to implement a data lakehouse architecture in Azure Synapse Analytics.

### Objectives
  
After completing this lab, you will be able to:

+ Task 1: Provision an Azure Synapse Analytics workspace
+ Task 2: Create delta tables.
+ Task 3: Create catalog tables.
+ Task 4: Use delta tables for streaming data.
+ Task 5: Query a delta table from a serverless SQL pool.

## Task 1: Provision an Azure Synapse Analytics workspace

You'll need an Azure Synapse Analytics workspace with access to data lake storage and an Apache Spark pool that you can use to query and process files in the data lake.

In this task, we will provision an Azure Synapse Analytics workspace using a PowerShell script and an ARM template. This will involve setting up a Cloud Shell, cloning a repository, and running a setup script to create the required resources for working with Delta Lake.

1. Use the **[\>_]** button to the right of the search bar at the top of the page to create a new Cloud Shell in the **Azure portal**.

    ![Azure portal with a cloud shell pane](./images/DA-image1.png)

1. The first time you open the Cloud Shell, you may be prompted to choose the type of shell you want to use (Bash or PowerShell). If so, select PowerShell.

    ![Azure portal with a cloud shell pane](./images/DA-image2.png)

    > **Note**: If you have previously created a cloud shell that uses a *Bash* environment, use the the drop-down menu at the top left of the cloud shell pane to change it to ***PowerShell***.

1. On Getting started window choose **Mount storage account(1)** then under Storage account subscription select your available **subscription (2)** from the dropdown and click on **Apply (3)**.

   ![Azure portal with a cloud shell pane](./images/DA-image3.png)

1. Within the Mount storage account pane, select **we will create a storage aacount for you (1)** and click **Next (2)**.

    ![Azure portal with a cloud shell pane](./images/DA-image4.png).
   
1. Note that you can resize the cloud shell by dragging the separator bar at the top of the pane, or by using the **&#8212;**, **&#9723;**, and **X** icons at the top right of the pane to minimize, maximize, and close the pane. For more information about using the Azure Cloud Shell, see the [Azure Cloud Shell documentation](https://docs.microsoft.com/azure/cloud-shell/overview).

1. In the PowerShell pane, enter the following commands to clone this repo:

    ```
    rm -r synapse -f
    git clone https://github.com/CloudLabsAI-Azure/Data-Analytics-with-Azure-Synapse synapse
    ```

1. After the repo has been cloned, enter the following commands to change to the folder for this exercise and run the **setup.ps1** script it contains:

    ```
    cd synapse/Allfiles/labs/07
    ./setup.ps1
    ```

1. If prompted, choose which subscription you want to use (this will only happen if you have access to multiple Azure subscriptions).

1. When prompted, enter a suitable password to be set for your Azure Synapse SQL pool.

    > **Note**: Be sure to remember this password!

1. Wait for the script to complete - this typically takes around 10 minutes, but in some cases may take longer. While you are waiting, review the [What is Delta Lake](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-what-is-delta-lake) article in the Azure Synapse Analytics documentation.

## Task 2: Create delta tables

The script provisions an Azure Synapse Analytics workspace and an Azure Storage account to host the data lake, then uploads a data file to the data lake.

In this task, we will create delta tables by first exploring data in the data lake and then loading it into a delta table. We will update the data and use features like time travel to view historical data, and examine the Delta Lake table's history.

### Task 2.1: Explore the data in the data lake

1. After the script has completed, in the Azure portal, search and select **Resource group** and select **analytics (1)** resource group that it created, and select your **Synapse workspace (2)**.

    ![Azure portal with a cloud shell pane](./images/DA-image6.png)

    ![Azure portal with a cloud shell pane](./images/DA-image(7).png)

1. In the **Overview** page for your Synapse workspace, in the **Open Synapse Studio** card, select **Open** to open Synapse Studio in a new browser tab; signing in if prompted.

   ![Azure portal with a cloud shell pane](./images/DA-image(8).png)
   
1. On the left side of **Synapse Studio**, use the **&rsaquo;&rsaquo;** icon to expand the menu - this reveals the different pages within Synapse Studio that you'll use to manage resources and perform data analytics tasks.

   ![Azure portal with a cloud shell pane](./images/DA-image(9).png)
 
1. On the **Data** page, view the **Linked** tab and verify that your workspace includes a link to your Azure Data Lake Storage Gen2 storage account, which should have a name similar to **synapsexxxxxxx(Primary - datalakexxxxxxx)**.

    ![Azure portal with a cloud shell pane](./images/DA-image(13).png)

1. Expand your storage account and verify that it contains a file system container named **files**.

    ![Azure portal with a cloud shell pane](./images/DA-image(14).png)

1. Select the **files** container, and note that it contains a folder named **products**. This folder contains the data you are going to work with in this exercise.

    ![Azure portal with a cloud shell pane](./images/DA-image26.png)

1. Open the **products** folder, and observe that it contains a file named **products.csv**.

   ![Azure portal with a cloud shell pane](./images/DA-image27.png)

1. Select **products.csv**, and then in the **New notebook** list on the toolbar, select **Load to DataFrame**.

   ![Azure portal with a cloud shell pane](./images/DA-image28.png)

1. In the **Notebook 1** pane that opens, in the **Attach to** list, select the **sparkxxxxxxx (1)** Spark pool and ensure that the **Language** is set to **PySpark (Python) (2)**.

   ![Azure portal with a cloud shell pane](./images/DA-image29.png)

1. Review the code in the first (and only) cell in the notebook, which should look like this:

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/products/products.csv', format='csv'
    ## If header exists uncomment line below
    ##, header=True
    )
    display(df.limit(10))
    ```

1. Uncomment the *,header=True* line (because the products.csv file has the column headers in the first line), so your code looks like this:

    ```Python
    %%pyspark
    df = spark.read.load('abfss://files@datalakexxxxxxx.dfs.core.windows.net/products/products.csv', format='csv'
    ## If header exists uncomment line below
    , header=True
    )
    display(df.limit(10))
    ```

1. Use the **&#9655;** icon to the left of the code cell to run it, and wait for the results. The first time you run a cell in a notebook, the Spark pool is started - so it may take a minute or so to return any results. Eventually, the results should appear below the cell, and they should be similar to this:

    | ProductID | ProductName | Category | ListPrice |
    | -- | -- | -- | -- |
    | 771 | Mountain-100 Silver, 38 | Mountain Bikes | 3399.9900 |
    | 772 | Mountain-100 Silver, 42 | Mountain Bikes | 3399.9900 |
    | ... | ... | ... | ... |

    ![Azure portal with a cloud shell pane](./images/DA-image31.png)

### Task 2.2: Load the file data into a delta table

1. Under the results returned by the first code cell, use the **+ Code** button to add a new code cell. Then enter the following code in the new cell and run it:

    ```Python
    delta_table_path = "/delta/products-delta"
    df.write.format("delta").save(delta_table_path)
    ```

    ![Azure portal with a cloud shell pane](./images/DA-image32.png)

    > **Note**: If the **+ Code** option isn't visible, try hovering your mouse cursor below the results to reveal it.

1. On the **files (1)** tab, use the **&#8593;** **(2)** icon in the toolbar to return to the root of the **files** container, and note that a new folder named **delta (3)** has been created. Open this folder and the **products-delta (4)** table it contains, where you should see the parquet format file(s) containing the data.

    ![Azure portal with a cloud shell pane](./images/DA-image(33).png)

    ![Azure portal with a cloud shell pane](./images/DA-image34.png)

1. Return to the **Notebook 1** tab and add another new code cell. Then, in the new cell, add the following code and run it:

    ```Python
    from delta.tables import *
    from pyspark.sql.functions import *

    # Create a deltaTable object
    deltaTable = DeltaTable.forPath(spark, delta_table_path)

    # Update the table (reduce price of product 771 by 10%)
    deltaTable.update(
        condition = "ProductID == 771",
        set = { "ListPrice": "ListPrice * 0.9" })

    # View the updated data as a dataframe
    deltaTable.toDF().show(10)
    ```

    ![](./images/synap-gp-t4-2.png)

    The data is loaded into a **DeltaTable** object and updated. You can see the update reflected in the query results.

1. Add another new code cell with the following code and run it:

    ```Python
    new_df = spark.read.format("delta").load(delta_table_path)
    new_df.show(10)
    ```

    ![](./images/synap-gp-t4-3.png)

    The code loads the delta table data into a data frame from its location in the data lake, verifying that the change you made via a **DeltaTable** object has been persisted.

1. Modify the code you just ran as follows, specifying the option to use the *time travel* feature of delta lake to view a previous version of the data.

    ```Python
    new_df = spark.read.format("delta").option("versionAsOf", 0).load(delta_table_path)
    new_df.show(10)
    ```

    ![](./images/synap-gp-t4-4.png)

    When you run the modified code, the results show the original version of the data.

1. Add another new code cell with the following code and run it:

    ```Python
    deltaTable.history(10).show(20, False, True)
    ```

    ![](./images/synap-gp-t4-5.png)

    The history of the last 20 changes to the table is shown - there should be two (the original creation, and the update you made.)

## Task 3: Create catalog tables

So far you've worked with delta tables by loading data from the folder containing the parquet files on which the table is based. You can define *catalog tables* that encapsulate the data and provide a named table entity that you can reference in SQL code. Spark supports two kinds of catalog tables for delta lake:

- *External* tables that are defined by the path to the parquet files containing the table data.
- *Managed* tables, that are defined in the Hive metastore for the Spark pool.

In this task, we will create catalog tables. We will create an external table and a managed table in Spark, compare them, and drop them to observe the difference between external and managed tables. We will also create a new table using SQL based on the Delta Lake data.

### Task 3.1: Create an external table

1. In a new code cell, add and run the following code:

    ```Python
    spark.sql("CREATE DATABASE AdventureWorks")
    spark.sql("CREATE TABLE AdventureWorks.ProductsExternal USING DELTA LOCATION '{0}'".format(delta_table_path))
    spark.sql("DESCRIBE EXTENDED AdventureWorks.ProductsExternal").show(truncate=False)
    ```

    ![](./images/synap-gp-t4-6.png)

    This code creates a new database named **AdventureWorks** and then creates an external tabled named **ProductsExternal** in that database based on the path to the parquet files you defined previously. It then displays a description of the table's properties. Note that the **Location** property is the path you specified.

1. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    USE AdventureWorks;

    SELECT * FROM ProductsExternal;
    ```

    ![](./images/synap-gp-t4-7.png)

    The code uses SQL to switch context to the **AdventureWorks** database (which returns no data) and then query the **ProductsExternal** table (which returns a resultset containing the products data in the Delta Lake table).

### Task 3.2: Create a managed table

1. In a new code cell, add and run the following code:

    ```Python
    df.write.format("delta").saveAsTable("AdventureWorks.ProductsManaged")
    spark.sql("DESCRIBE EXTENDED AdventureWorks.ProductsManaged").show(truncate=False)
    ```

    ![](./images/synap-gp-t4-8.png)

    This code creates a managed tabled named **ProductsManaged** based on the DataFrame you originally loaded from the **products.csv** file (before you updated the price of product 771). You do not specify a path for the parquet files used by the table - this is managed for you in the Hive metastore, and shown in the **Location** property in the table description (in the **files/synapse/workspaces/synapsexxxxxxx/warehouse** path).

1. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    USE AdventureWorks;

    SELECT * FROM ProductsManaged;
    ```

    ![](./images/synap-gp-t4-9.png)

    The code uses SQL to query the **ProductsManaged** table.

### Task 3.3: Compare external and managed tables

1. In a new code cell, add and run the following code:

    ```sql
    %%sql

    USE AdventureWorks;

    SHOW TABLES;
    ```

    ![](./images/synap-gp-t4-10.png)

    This code lists the tables in the **AdventureWorks** database.

1. Modify the code cell as follows, add run it:

    ```sql
    %%sql

    USE AdventureWorks;

    DROP TABLE IF EXISTS ProductsExternal;
    DROP TABLE IF EXISTS ProductsManaged;
    ```

    This code drops the tables from the metastore.

1. Return to the **files** tab and view the **files/delta/products-delta** folder. Note that the data files still exist in this location. Dropping the external table has removed the table from the metastore, but left the data files intact.

1. View the **files/synapse/workspaces/synapsexxxxxxx/warehouse** folder, and note that there is no folder for the **ProductsManaged** table data. Dropping a managed table removes the table from the metastore and also deletes the table's data files.

### Task 3.4: Create a table using SQL

1. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    USE AdventureWorks;

    CREATE TABLE Products
    USING DELTA
    LOCATION '/delta/products-delta';
    ```

1. Add a new code cell, and then enter and run the following code:

    ```sql
    %%sql

    USE AdventureWorks;

    SELECT * FROM Products;
    ```

    Observe that the new catalog table was created for the existing Delta Lake table folder, which reflects the changes that were made previously.

## Task 4: Use delta tables for streaming data

Delta lake supports streaming data. Delta tables can be a *sink* or a *source* for data streams created using the Spark Structured Streaming API. In this example, you'll use a delta table as a sink for some streaming data in a simulated internet of things (IoT) scenario.

In this task, we will use delta tables for streaming data. We will create a stream to simulate IoT data, write it to a delta table, and query it. We will add more data to the stream and observe the updates in the delta table.

1. Return to the **Notebook 1** tab and add a new code cell. Then, in the new cell, add the following code and run it:

    ```python
    from notebookutils import mssparkutils
    from pyspark.sql.types import *
    from pyspark.sql.functions import *

    # Create a folder
    inputPath = '/data/'
    mssparkutils.fs.mkdirs(inputPath)

    # Create a stream that reads data from the folder, using a JSON schema
    jsonSchema = StructType([
    StructField("device", StringType(), False),
    StructField("status", StringType(), False)
    ])
    iotstream = spark.readStream.schema(jsonSchema).option("maxFilesPerTrigger", 1).json(inputPath)

    # Write some event data to the folder
    device_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"ok"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''
    mssparkutils.fs.put(inputPath + "data.txt", device_data, True)
    print("Source stream created...")
    ```

    Ensure the message *Source stream created...* is printed. The code you just ran has created a streaming data source based on a folder to which some data has been saved, representing readings from hypothetical IoT devices.

1. In a new code cell, add and run the following code:

    ```python
    # Write the stream to a delta table
    delta_stream_table_path = '/delta/iotdevicedata'
    checkpointpath = '/delta/checkpoint'
    deltastream = iotstream.writeStream.format("delta").option("checkpointLocation", checkpointpath).start(delta_stream_table_path)
    print("Streaming to delta sink...")
    ```

    This code writes the streaming device data in delta format.

1. In a new code cell, add and run the following code:

    ```python
    # Read the data in delta format into a dataframe
    df = spark.read.format("delta").load(delta_stream_table_path)
    display(df)
    ```

    This code reads the streamed data in delta format into a dataframe. Note that the code to load streaming data is no different to that used to load static data from a delta folder.

1. In a new code cell, add and run the following code:

    ```python
    # create a catalog table based on the streaming sink
    spark.sql("CREATE TABLE IotDeviceData USING DELTA LOCATION '{0}'".format(delta_stream_table_path))
    ```

    This code creates a catalog table named **IotDeviceData** (in the **default** database) based on the delta folder. Again, this code is the same as would be used for non-streaming data.

1. In a new code cell, add and run the following code:

    ```sql
    %%sql

    SELECT * FROM IotDeviceData;
    ```

    This code queries the **IotDeviceData** table, which contains the device data from the streaming source.

1. In a new code cell, add and run the following code:

    ```python
    # Add more data to the source stream
    more_data = '''{"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"ok"}
    {"device":"Dev1","status":"error"}
    {"device":"Dev2","status":"error"}
    {"device":"Dev1","status":"ok"}'''

    mssparkutils.fs.put(inputPath + "more-data.txt", more_data, True)
    ```

    This code writes more hypothetical device data to the streaming source.

1. In a new code cell, add and run the following code:

    ```sql
    %%sql

    SELECT * FROM IotDeviceData;
    ```

    This code queries the **IotDeviceData** table again, which should now include the additional data that was added to the streaming source.

1. In a new code cell, add and run the following code:

    ```python
    deltastream.stop()
    ```

    This code stops the stream.

## Task 5: Query a delta table from a serverless SQL pool

In addition to Spark pools, Azure Synapse Analytics includes a built-in serverless SQL pool. You can use the relational database engine in this pool to query delta tables using SQL.

In this lab, we will query a delta table from a serverless SQL pool. We will use SQL code to query Delta Lake format files and catalog tables, demonstrating the capability to perform SQL queries on data managed by Spark.

1. In the **files** tab, browse to the **files/delta** folder.

1. Select and right click the **products-delta (1)** folder, in the **New SQL script (2)** drop-down list, select **Select TOP 100 rows (3)**.

      ![Azure portal with a cloud shell pane](./images/DA-image36.png)

1. In the **Select TOP 100 rows** pane, in the **File type** list, select **Delta format** and then select **Apply**.

1. Review the SQL code that is generated, which should look like this:

    ```sql
    -- This is auto-generated code
    SELECT
        TOP 100 *
    FROM
        OPENROWSET(
            BULK 'https://datalakexxxxxxx.dfs.core.windows.net/files/delta/products-delta/',
            FORMAT = 'DELTA'
        ) AS [result]
    ```

1. Use the **&#9655; Run** icon to run the script, and review the results. They should look similar to this:

    | ProductID | ProductName | Category | ListPrice |
    | -- | -- | -- | -- |
    | 771 | Mountain-100 Silver, 38 | Mountain Bikes | 3059.991 |
    | 772 | Mountain-100 Silver, 42 | Mountain Bikes | 3399.9900 |
    | ... | ... | ... | ... |

    This demonstrates how you can use a serverless SQL pool to query delta format files that were created using Spark, and use the results for reporting or analysis.

1. Replace the query with the following SQL code:

    ```sql
    USE AdventureWorks;

    SELECT * FROM Products;
    ```

1. Run the code and observe that you can also use the serverless SQL pool to query Delta Lake data in catalog tables that are defined the Spark metastore.

    ![](./images/synap-gp-t4-22.png)

> **Congratulations** on completing the lab! Now, it's time to validate it. Here are the steps:
> - Hit the Validate button for the corresponding task. If you receive a success message, you have successfully validated the lab. 
> - If not, carefully read the error message and retry the step, following the instructions in the lab guide.
> - If you need any assistance, please contact us at cloudlabs-support@spektrasystems.com. We are available 24/7 to help you out.

<validation step="61bfb30e-1ad6-4205-97ae-935789802c65" />

## Summary

In this lab, we provisioned an Azure Synapse Analytics workspace and explored Delta Lake by creating and managing delta tables. We loaded data into delta tables, used features like time travel and table history, and created catalog tables to compare external and managed tables. We also worked with streaming data, simulating IoT data to demonstrate how delta tables can be used as data sinks. Finally, we queried delta tables from a serverless SQL pool to showcase the integration between Spark and SQL-based analysis.

## Review

In this lab, you have accomplished the following:

- Provision an Azure Synapse Analytics workspace
- Create delta tables
- Create catalog tables
- Use delta tables for streaming data
- Query a delta table from a serverless SQL pool

## You have successfully completed the lab.
