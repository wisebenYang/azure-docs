---
title: Get started with R Server on HDInsight - Azure | Microsoft Docs
description: Learn how to create an Apache Spark on HDInsight cluster that includes R Server and submit an R script on the cluster.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.assetid: b5e111f3-c029-436c-ba22-c54a4a3016e3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/23/2018
ms.author: nitinme

---
# Get started with R Server cluster on Azure HDInsight

Azure HDInsight includes an R Server option to be integrated into your HDInsight cluster. This option allows R scripts to use Spark and MapReduce to run distributed computations. In this article, you learn how to create an R Server on HDInsight cluster. You then learn how to run an R script that demonstrates using Spark for distributed R computations.


## Prerequisites

* **An Azure subscription**: Before you begin this tutorial, you must have an Azure subscription. For more information, see [Get Microsoft Azure free trial](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **A Secure Shell (SSH) client**: An SSH client is used to remotely connect to the HDInsight cluster and run commands directly on the cluster. For more information, see [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).


<a name="create-hdi-custer-with-aure-portal"></a>
## Create the cluster using the Azure portal

1. Sign in to the [Azure portal](https://portal.azure.com).

2. Click **Create a resource** > **Data + Analytics** > **HDInsight**.

3. From **Basics**, enter the following information:

    * **Cluster Name**: The name of the HDInsight cluster.
    * **Subscription**: Select the subscription to use.
    * **Cluster login username** and **Cluster login password**: The login when accessing the cluster over HTTPS. You use these credentials to access services such as the Ambari Web UI or REST API.
    * **Secure Shell (SSH) username**: The login used when accessing the cluster over SSH. By default the password is the same as the cluster login password.
    * **Resource Group**: The resource group to create the cluster in.
    * **Location**: The Azure region to create the cluster in.

        ![Cluster basic details](./media/r-server-get-started/clustername.png)

4. Select **Cluster type**, and then set the following values in the **Cluster configuration** section:

    * **Cluster Type**: R Server

    * **Operating system**: Linux

    * **Version**: R Server 9.1 (HDI 3.6). Release notes for the available versions of R Server are available on [docs.microsoft.com](https://docs.microsoft.com/machine-learning-server/whats-new-in-r-server#r-server-91).

    * **R Studio community edition for R Server**: This browser-based IDE is installed by default on the edge node. Clear the check box if you prefer to not have it installed. If you choose to have it installed, the URL for accessing the RStudio Server login is available on the portal application blade for your cluster once it’s been created.

        ![Cluster basic details](./media/r-server-get-started/clustertypeconfig.png)

4. After selecting the cluster type, use the __Select__ button to set the cluster type. Next, use the __Next__ button to finish basic configuration.

5. From the **Storage** section, select or create a Storage account. For the steps in this document, leave the other fields in this section at the default values. Use the __Next__ button to save storage configuration.

    ![Set the storage account settings for HDInsight](./media/r-server-get-started/cluster-storage.png)

6. From the **Summary** section, review the configuration for the cluster. Use the __Edit__ links to change any settings that are incorrect. Finally, use the __Create__ button to create the cluster.

    ![Set the storage account settings for HDInsight](./media/r-server-get-started/clustersummary.png)

    > [!NOTE]
    > It can take up to 20 minutes to create the cluster.

<a name="connect-to-rstudio-server"></a>
## Connect to RStudio Server

If you chose to install RStudio Server Community Edition as part of your HDInsight cluster, then you can access the RStudio login using one of the following two methods:

* **Option 1** - Go to the following URL (where **CLUSTERNAME** is the name of the R Server cluster you created):

        https://CLUSTERNAME.azurehdinsight.net/rstudio/

* **Option 2** - Open the R Server cluster in the Azure portal, under **Quick links** click **R Server Dashboards**.

     ![Set the storage account settings for HDInsight](./media/r-server-get-started/dashboard-quick-links.png)

    From **Cluster Dashboards**, click **R Studio Server**.

    ![Set the storage account settings for HDInsight](./media/r-server-get-started/r-studio-server-dashboard.png)

   > [!IMPORTANT]
   > Regardless of the method used, the first time you log in you need to authenticate twice.  For the first authentication prompt, provide the *cluster Admin userid* and *password*. For the second authentication prompt, provide the *SSH userid* and *password*. Subsequent log ins only require the SSH credentials.

Once you are connected, your screen should resemble the following screenshot:

![Connect to RStudio](./media/r-server-get-started/connect-to-r-studio.png)

## Run a sample job

You can submit a job using ScaleR functions. Here is an example of the commands used to run a job:

	# Set the HDFS (WASB) location of example data.
	bigDataDirRoot <- "/example/data"

	# Create a local folder for storaging data temporarily.
	source <- "/tmp/AirOnTimeCSV2012"
	dir.create(source)

	# Download data to the tmp folder.
	remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
	download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
	download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
	download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
	download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
	download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
	download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
	download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
	download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
	download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
	download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
	download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
	download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))

	# Set directory in bigDataDirRoot to load the data.
	inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

	# Create the directory.
	rxHadoopMakeDir(inputDir)

	# Copy the data from source to input.
	rxHadoopCopyFromLocal(source, bigDataDirRoot)

	# Define the HDFS (WASB) file system.
	hdfsFS <- RxHdfsFileSystem()

	# Create info list for the airline data.
	airlineColInfo <- list(
	DAY_OF_WEEK = list(type = "factor"),
	ORIGIN = list(type = "factor"),
	DEST = list(type = "factor"),
	DEP_TIME = list(type = "integer"),
	ARR_DEL15 = list(type = "logical"))

	# Get all the column names.
	varNames <- names(airlineColInfo)

	# Define the text data source in HDFS.
	airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)

	# Define the text data source in local system.
	airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)

	# Specify the formula to use.
	formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"

	# Define the Spark compute context.
	mySparkCluster <- RxSpark()

	# Set the compute context.
	rxSetComputeContext(mySparkCluster)

	# Run a logistic regression.
	system.time(
		modelSpark <- rxLogit(formula, data = airOnTimeData)
	)

	# Display a summary.
	summary(modelSpark)

<a name="connect-to-edge-node"></a>
## Connect to the cluster edge node

In this section, you learn how to connect to the edge node of an R Server HDInsight cluster using SSH. For familiarity on using SSH, see [Use SSH with HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

The SSH command to connect to the R Server cluster edge node is:

   `ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net`

To find the SSH command for your cluster, from the Azure portal click the cluster name, click **SSH + Cluster login**, and then for **Hostname**, select the edge node. This displays the SSH Endpoint information for the edge node.

![Image of the SSH Endpoint for the edge node](./media/r-server-get-started/sshendpoint.png)

If you used a password to secure your SSH user account, you are prompted to enter it. If you used a public key, you may have to use the `-i` parameter to specify the matching private key. For example:

	ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Once connected, you get at a prompt similar to the following:

	sshuser@ed00-myrclu:~$

<a name="use-r-console"></a>
## Use the R Server console

1. From the SSH session, use the following command to start the R console:  

		R

2. You should see an output with the version of R Server, in addition to other information.
	
3. From the `>` prompt, you can enter R code. R Server on HDInsight includes packages that allow you to easily interact with Hadoop and run distributed computations. For example, use the following command to view the root of the default file system for the HDInsight cluster:

		rxHadoopListFiles("/")

4. You can also use the WASB style addressing.

		rxHadoopListFiles("wasb:///")

5. To quit the R console, use the following command:

        quit()

## Automated cluster creation

You can automate the creation of R Server cluster for HDInsight by using Azure Resource Manager templates, the SDK, and the PowerShell.

* To create an R Server cluster using an Azure Resource Management template, see [Deploy an R Server for HDInsight cluster](https://azure.microsoft.com/resources/templates/101-hdinsight-rserver/).
* To create an R Server cluster using the .NET SDK, see [Create Linux-based clusters in HDInsight using the .NET SDK.](../hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md)
* To create an R Server cluster using powershell, see the article on [Create HDInsight clusters using Azure PowerShell](../hdinsight-hadoop-create-linux-clusters-azure-powershell.md).

## Delete the cluster

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## Troubleshoot

If you run into issues with creating HDInsight clusters, see [access control requirements](../hdinsight-administer-use-portal-linux.md#create-clusters).

## Next steps

In this article you learned how to create a new R Server cluster in Azure HDInsight and the basics of using the R console from an SSH session. The following articles explain other ways of managing and working with R Server on HDInsight:

* [Submit jobs from R Tools for Visual Studio](r-server-submit-jobs-r-tools-vs.md)
* [Manage R Server cluster on HDInsight](r-server-hdinsight-manage.md)
* [Operationalize R Server cluster on HDInsight](r-server-operationalize.md)
* [Compute context options for R Server cluster on HDInsight](r-server-compute-contexts.md)
* [Azure Storage options for R Server cluster on HDInsight](r-server-storage.md)
