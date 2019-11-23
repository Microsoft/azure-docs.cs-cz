---
title: Apache Spark & Apache Kafka with Cosmos DB - Azure HDInsight
description: Learn how to use Apache Spark Structured Streaming to read data from Apache Kafka and then store it into Azure Cosmos DB. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406159"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Use Apache Spark Structured Streaming with Apache Kafka and Azure Cosmos DB

Learn how to use [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) to read data from [Apache Kafka](https://kafka.apache.org/) on Azure HDInsight, and then store the data into Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) is a globally distributed, multi-model database. This example uses a SQL API database model. For more information, see the [Welcome to Azure Cosmos DB](../cosmos-db/introduction.md) document.

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. For more information on Structured Streaming, see the [Structured Streaming Programming Guide](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) at Apache.org.

> [!IMPORTANT]  
> This example used Spark 2.2 on HDInsight 3.6.
>
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka on HDInsight doesn't provide access to the Kafka brokers over the public internet. Anything that talks to Kafka must be in the same Azure virtual network as the nodes in the Kafka cluster. For this example, both the Kafka and Spark clusters are located in an Azure virtual network. The following diagram shows how communication flows between the clusters:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

While you can create an Azure virtual network, Kafka, and Spark clusters manually, it's easier to use an Azure Resource Manager template. Use the following steps to deploy an Azure virtual network, Kafka, and Spark clusters to your Azure subscription.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    The Azure Resource Manager template is located in the GitHub repository for this project ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Tato šablona vytvoří následující prostředky:

   * Cluster Kafka ve službě HDInsight 3.6.

   * A Spark on HDInsight 3.6 cluster.

   * Virtuální síť Azure obsahující clustery HDInsight. The virtual network created by the template uses the 10.0.0.0/16 address space.

   * An Azure Cosmos DB SQL API database.

    > [!IMPORTANT]  
    > Poznámkový blok strukturovaného streamování použitý v tomto příkladu vyžaduje Spark ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

1. Use the following information to populate the entries on the **Custom deployment** section:

    |Vlastnost |Hodnota |
    |---|---|
    |Předplatné|Vyberte své předplatné Azure.|
    |Skupina prostředků|Create a group or select an existing one. This group contains the HDInsight cluster.|
    |Cosmos DB Account Name|This value is used as the name for the Cosmos DB account. The name can only contain lowercase letters, numbers, and the hyphen (-) character. It must be between 3-31 characters in length.|
    |Base Cluster Name|This value is used as the base name for the Spark and Kafka clusters. For example, entering **myhdi** creates a Spark cluster named __spark-myhdi__ and a Kafka cluster named **kafka-myhdi**.|
    |Cluster Version|The HDInsight cluster version. This example is tested with HDInsight 3.6, and may not work with other cluster types.|
    |Uživatelské jméno přihlášení clusteru|The admin user name for the Spark and Kafka clusters.|
    |Heslo přihlášení clusteru|The admin user password for the Spark and Kafka clusters.|
    |Ssh User Name|The SSH user to create for the Spark and Kafka clusters.|
    |Ssh Password|The password for the SSH user for the Spark and Kafka clusters.|

    ![HDInsight custom deployment values](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

1. Finally, select **Purchase**. It may take up to 45 minutes to create the clusters, virtual network, and Cosmos DB account.

## <a name="create-the-cosmos-db-database-and-collection"></a>Create the Cosmos DB database and collection

The project used in this document stores data in Cosmos DB. Before running the code, you must first create a _database_ and _collection_ in your Cosmos DB instance. You must also retrieve the document endpoint and the _key_ used to authenticate requests to Cosmos DB.

One way to do this is to use the [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). The following script will create a database named `kafkadata` and a collection named `kafkacollection`. It then returns the primary key.

```azurecli
#!/bin/bash

# Replace 'myresourcegroup' with the name of your resource group
resourceGroupName='myresourcegroup'
# Replace 'mycosmosaccount' with the name of your Cosmos DB account name
name='mycosmosaccount'

# WARNING: If you change the databaseName or collectionName
#          then you must update the values in the Jupyter notebook
databaseName='kafkadata'
collectionName='kafkacollection'

# Create the database
az cosmosdb sql database create --account-name $name --name $databaseName --resource-group $resourceGroupName

# Create the collection
az cosmosdb sql container create --account-name $name --database-name $databaseName --name $collectionName --partition-key-path "/my/path" --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb keys list --name $name --resource-group $resourceGroupName --type keys
```

The document endpoint and primary key information is similar to the following text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Save the endpoint and key values, as they are needed in the Jupyter Notebooks.

## <a name="get-the-notebooks"></a>Get the notebooks

Kód příkladu popisovaného v tomto dokumentu je dostupný na adrese [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Nahrání poznámkových bloků

Use the following steps to upload the notebooks from the project to your Spark on HDInsight cluster:

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. From the upper right side of the page, use the __Upload__ button to upload the __Stream-taxi-data-to-kafka.ipynb__ file to the cluster. Vyberte __Otevřít__ a zahajte nahrávání.

3. Find the __Stream-taxi-data-to-kafka.ipynb__ entry in the list of notebooks, and select __Upload__ button beside it.

4. Repeat steps 1-3 to load the __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ notebook.

## <a name="load-taxi-data-into-kafka"></a>Load taxi data into Kafka

Once the files have been uploaded, select the __Stream-taxi-data-to-kafka.ipynb__ entry to open the notebook. Follow the steps in the notebook to load data into Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Process taxi data using Spark Structured Streaming

From the [Jupyter Notebook](https://jupyter.org/) home page, select the __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ entry. Follow the steps in the notebook to stream data from Kafka and into Azure Cosmos DB using Spark Structured Streaming.

## <a name="next-steps"></a>Další kroky

Now that you've learned how to use Apache Spark Structured Streaming, see the following documents to learn more about working with Apache Spark, Apache Kafka, and Azure Cosmos DB:

* [How to use Apache Spark streaming (DStream) with Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Start with Jupyter Notebook and Apache Spark on HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Welcome to Azure Cosmos DB](../cosmos-db/introduction.md)
