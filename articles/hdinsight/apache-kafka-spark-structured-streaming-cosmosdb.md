---
title: Apache Spark & Apache Kafka s Cosmos DB - Azure HDInsight
description: Naučte se používat Apache Spark Structured Streaming ke čtení dat z Apache Kafka a pak je uložit do Azure Cosmos DB. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/18/2019
ms.openlocfilehash: 04faafca0811e60ded47d1e91a82054a1c1cdb25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74406159"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Použití strukturovaného streamování Apache Spark s Apache Kafka a Azure Cosmos DB

Naučte se používat [Apache Spark](https://spark.apache.org/) [Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) ke čtení dat z Apache [Kafka](https://kafka.apache.org/) na Azure HDInsight a pak je uložit do Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná databáze s více modely. Tento příklad používá databázový model rozhraní SQL API. Další informace najdete v dokumentu [Uvítání v Azure Cosmos DB.](../cosmos-db/introduction.md)

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. Další informace o strukturovaném streamování najdete v [průvodci programováním strukturovaného streamování](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) na Apache.org.

> [!IMPORTANT]  
> Tento příklad použil Spark 2.2 na HDInsight 3.6.
>
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka na HDInsight neposkytuje přístup k makléřům Kafka přes veřejný internet. Cokoliv, co mluví s Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu clustery Kafka a Spark jsou umístěny ve virtuální síti Azure. Následující diagram znázorňuje, jak komunikace proutí mezi clustery:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Zatímco virtuální síť Azure, clustery Kafka a Spark můžete vytvořit ručně, je jednodušší použít šablonu Azure Resource Manager. Pomocí následujících kroků nasadit virtuální síť Azure, Kafka a Spark clustery do předplatného Azure.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Šablona Azure Resource Manager je umístěna v úložišti GitHub pro tento projekt ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Tato šablona vytvoří následující prostředky:

   * Cluster Kafka ve službě HDInsight 3.6.

   * Spark na HDInsight 3.6 clusteru.

   * Virtuální síť Azure obsahující clustery HDInsight. Virtuální síť vytvořená šablonou používá adresní prostor 10.0.0.0/16.

   * Databáze rozhraní SQL API Azure Cosmos DB.

    > [!IMPORTANT]  
    > Poznámkový blok strukturovaného streamování použitý v tomto příkladu vyžaduje Spark ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

1. K vyplnění položek v části **Vlastní nasazení** použijte následující informace:

    |Vlastnost |Hodnota |
    |---|---|
    |Předplatné|Vyberte své předplatné Azure.|
    |Skupina prostředků|Vytvořte skupinu nebo vyberte existující skupinu. Tato skupina obsahuje cluster HDInsight.|
    |Název účtu Cosmos DB|Tato hodnota se používá jako název účtu Cosmos DB. Název může obsahovat pouze malá písmena, čísla a pomlčku (-) znak. Musí být mezi 3-31 znaků na délku.|
    |Název základního clusteru|Tato hodnota se používá jako základní název clusterů Spark a Kafka. Například zadání **myhdi** vytvoří cluster Spark s názvem __spark-myhdi__ a cluster Kafka s názvem **kafka-myhdi**.|
    |Verze clusteru|Verze clusteru HDInsight. Tento příklad je testován s HDInsight 3.6 a nemusí pracovat s jinými typy clusterů.|
    |Uživatelské jméno přihlášení clusteru|Uživatelské jméno správce clusterů Spark a Kafka.|
    |Heslo přihlášení clusteru|Heslo uživatele správce pro clustery Spark a Kafka.|
    |Uživatelské jméno SSH|Uživatel SSH vytvořit pro clustery Spark a Kafka.|
    |Ssh heslo|Heslo pro uživatele SSH pro clustery Spark a Kafka.|

    ![Hodnoty vlastního nasazení HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

1. Přečtěte si **obchodní podmínky**a pak vyberte Souhlasím s **výše uvedenými podmínkami**.

1. Nakonec vyberte **možnost Koupit**. Vytvoření clusterů, virtuální sítě a účtu Cosmos DB může trvat až 45 minut.

## <a name="create-the-cosmos-db-database-and-collection"></a>Vytvoření databáze a kolekce Cosmos DB

Projekt použitý v tomto dokumentu ukládá data v Cosmos DB. Před spuštěním kódu musíte nejprve vytvořit _databázi_ a _kolekci_ v instanci Cosmos DB. Je také nutné načíst koncový bod dokumentu a _klíč_ použitý k ověření požadavků cosmos DB.

Jedním ze způsobů, jak to provést, je použití [příkazového příkazu Kons](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)Azure . Následující skript vytvoří databázi `kafkadata` s názvem `kafkacollection`a kolekci s názvem . Potom vrátí primární klíč.

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

Koncový bod dokumentu a informace o primárním klíči jsou podobné následujícímu textu:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Uložte koncového bodu a hodnoty klíče, jak jsou potřeba v Poznámkových bloků Jupyter.

## <a name="get-the-notebooks"></a>Získejte poznámkové bloky

Kód příkladu popsaného v tomto dokumentu [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)je k dispozici na adrese .

## <a name="upload-the-notebooks"></a>Nahrání poznámkových bloků

Pomocí následujících kroků můžete poznámkové bloky z projektu nahrát do clusteru Spark na HDInsightu:

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. Z pravé horní strany stránky nahrajte do clusteru soubor __Stream-taxi-data-to-kafka.ipynb__ pomocí tlačítka __Nahrát.__ Vyberte __Otevřít__ a zahajte nahrávání.

3. V seznamu poznámkových bloků najděte položku __Stream-taxi-data-to-kafka.ipynb__ a vedle ní vyberte tlačítko __Nahrát.__

4. Opakujte kroky 1-3 pro načtení __poznámkového bloku Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__

## <a name="load-taxi-data-into-kafka"></a>Načíst data taxi do Kafky

Po nahrání souborů otevřete poznámkový blok položkou __Stream-taxi-data-to-kafka.ipynb.__ Podle pokynů v poznámkovém bloku načtěte data do Kafky.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Zpracování dat taxi pomocí strukturovaného streamování Spark

Na domovské stránce [poznámkového bloku Jupyter](https://jupyter.org/) vyberte položku __Stream-data-from-Kafka-to-Cosmos-DB.ipynb.__ Podle pokynů v poznámkovém bloku můžete streamovat data z Kafky a do Azure Cosmos DB pomocí strukturovaného streamování Spark.

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili používat apache spark strukturované streamování, najdete v následujících dokumentech další informace o práci s Apache Spark, Apache Kafka a Azure Cosmos DB:

* [Jak používat Apache Spark streaming (DStream) s Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Začněte s Jupyter Notebook a Apache Spark na HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md)
