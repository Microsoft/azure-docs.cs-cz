---
title: Apache Spark dat z Apache Kafka do Azure Cosmos DB – Azure HDInsight
description: Naučte se používat strukturované streamování Apache Spark ke čtení dat z Apache Kafka a jejich následnému uložení do Azure Cosmos DB. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: hrasheed
ms.openlocfilehash: 0d8c6929705ab29ced25a847bf7c5a72d57aa49b
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71037295"
---
# <a name="use-apache-spark-structured-streaming-with-apache-kafka-and-azure-cosmos-db"></a>Použití strukturovaného streamování Apache Spark s Apache Kafka a Azure Cosmos DB

Naučte se používat [](https://spark.apache.org/) [strukturované streamování](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html) Apache Spark ke čtení dat z [Apache Kafka](https://kafka.apache.org/) v Azure HDInsight a pak data ukládat do Azure Cosmos DB.

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) je globálně distribuovaná databáze s více modely. V tomto příkladu se používá databázový model rozhraní SQL API. Další informace najdete v dokumentu [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md) .

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. Další informace o strukturovaném streamování najdete v tématu [Průvodce programováním strukturovaného streamování](https://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) na adrese Apache.org.

> [!IMPORTANT]  
> V tomto příkladu se použil Spark 2,2 ve službě HDInsight 3,6.
>
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Cokoli, co se mluví do Kafka, musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu se clustery Kafka a Spark nacházejí ve službě Azure Virtual Network. Následující diagram znázorňuje komunikaci mezi clustery:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/apache-kafka-spark-structured-streaming-cosmosdb/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

I když můžete vytvořit clustery Azure Virtual Network, Kafka a Spark ručně, je snazší použít šablonu Azure Resource Manager. Pomocí následujících kroků nasadíte clustery Azure Virtual Network, Kafka a Spark do svého předplatného Azure.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="./media/apache-kafka-spark-structured-streaming-cosmosdb/resource-manager-deploy.png" alt="Deploy to Azure"/>
    </a>

    Šablona Azure Resource Manager se nachází v úložišti GitHub pro tento projekt ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Tato šablona vytvoří následující prostředky:

   * Cluster Kafka ve službě HDInsight 3.6.

   * Spark v clusteru HDInsight 3,6.

   * Virtuální síť Azure obsahující clustery HDInsight.

       > [!NOTE]  
       > Virtuální síť vytvořená šablonou používá adresní prostor 10.0.0.0/16.

   * Azure Cosmos DB databáze rozhraní SQL API.

     > [!IMPORTANT]  
     > Poznámkový blok strukturovaného streamování použitý v tomto příkladu vyžaduje Spark ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

2. K naplnění položek v části **vlastní nasazení** použijte následující informace:

    ![Hodnoty vlastního nasazení HDInsight](./media/apache-kafka-spark-structured-streaming-cosmosdb/hdi-custom-parameters.png)

    * **Předplatné:** Vyberte své předplatné Azure.

    * **Skupina prostředků**: Vytvořte skupinu nebo vyberte některou existující. Tato skupina obsahuje cluster HDInsight.

    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Název účtu Cosmos DB**: Tato hodnota se používá jako název účtu Cosmos DB.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro clustery Spark a Kafka. Například zadáním **myhdi** se vytvoří cluster Spark s názvem __Spark-Myhdi__ a cluster Kafka s názvem **Kafka-myhdi**.

    * **Verze clusteru**: Verze clusteru HDInsight.

        > [!IMPORTANT]  
        > Tento příklad je testován pomocí HDInsight 3,6 a nemusí fungovat s jinými typy clusterů.

    * **Uživatelské jméno přihlášení clusteru**: Uživatelské jméno správce pro clustery Spark a Kafka.

    * **Heslo pro přihlášení ke clusteru**: Heslo uživatele správce pro clustery Spark a Kafka.

    * **Uživatelské jméno SSH**: Uživatel SSH, který se má vytvořit pro clustery Sparku a Kafka.

    * **Heslo SSH**: Heslo pro uživatele SSH pro clustery Spark a Kafka

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec vyberte **koupit**. Vytvoření clusteru trvá přibližně 20 minut.

> [!IMPORTANT]  
> Vytvoření clusterů, virtuálních sítí a Cosmos DB účtu může trvat až 45 minut.

## <a name="create-the-cosmos-db-database-and-collection"></a>Vytvoření databáze a kolekce Cosmos DB

Projekt použitý v tomto dokumentu ukládá data v Cosmos DB. Před spuštěním kódu musíte nejprve vytvořit _databázi_ a _kolekci_ v instanci Cosmos DB. Také je nutné načíst koncový bod dokumentu a _klíč_ , který slouží k ověření požadavků na Cosmos DB. 

Jedním ze způsobů, jak to provést, je použití rozhraní příkazového [řádku Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). V následujícím skriptu se vytvoří databáze s názvem `kafkadata` a kolekcí s názvem `kafkacollection`. Pak vrátí primární klíč.

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
az cosmosdb database create --name $name --db-name $databaseName --resource-group $resourceGroupName
# Create the collection
az cosmosdb collection create --collection-name $collectionName --name $name --db-name $databaseName --resource-group $resourceGroupName

# Get the endpoint
az cosmosdb show --name $name --resource-group $resourceGroupName --query documentEndpoint

# Get the primary key
az cosmosdb list-keys --name $name --resource-group $resourceGroupName --query primaryMasterKey
```

Informace o koncovém bodu dokumentu a primárním klíči jsou podobné následujícímu textu:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]  
> Uložte koncový bod a hodnoty klíče, jak jsou potřeba v poznámkových blocích Jupyter.

## <a name="get-the-apache-kafka-brokers"></a>Získat zprostředkovatele Apache Kafka

Kód v tomto příkladu se připojí k hostitelům zprostředkovatele Kafka v clusteru Kafka. Pokud chcete najít adresy dvou hostitelů služby Kafka Broker, použijte následující příklad PowerShellu nebo bash:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
$clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
    -Credential $creds `
    -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content
$brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
($brokerHosts -join ":9092,") + ":9092"
```

> [!NOTE]  
> Bash příklad očekává `$CLUSTERNAME` , že bude obsahovat název clusteru Kafka.
>
> Tento příklad používá nástroj [JQ](https://stedolan.github.io/jq/) k analýze dat z dokumentu JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Po zobrazení výzvy zadejte heslo pro účet přihlášení clusteru (správce).

Výstup se bude podobat následujícímu:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Tyto informace uložte, jak se používá v následujících částech tohoto dokumentu.

## <a name="get-the-notebooks"></a>Získat poznámkové bloky

Kód příkladu popisovaného v tomto dokumentu je dostupný na adrese [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Nahrání poznámkových bloků

Pomocí následujících kroků nahrajte poznámkové bloky z projektu do clusteru Spark v HDInsight:

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. V pravém horním rohu stránky použijte tlačítko __nahrát__ a nahrajte do clusteru soubor __Stream-taxislužby-data-to-Kafka. ipynb__ . Vyberte __Otevřít__ a zahajte nahrávání.

3. V seznamu poznámkových bloků Najděte položku __Stream-taxislužby-data-to-Kafka__ a vyberte možnost Odeslat vedle ní tlačítko __nahrát__ .

4. Zopakováním kroků 1-3 načtěte Poznámkový blok __Stream-data-on-Kafka-to-Cosmos-DB. ipynb__ .

## <a name="load-taxi-data-into-kafka"></a>Načtení dat taxislužby do Kafka

Po nahrání souborů vyberte položku __Stream-taxislužby-data-to-Kafka. ipynb__ a otevřete Poznámkový blok. Pokud chcete načíst data do Kafka, postupujte podle kroků v poznámkovém bloku.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Zpracování taxislužby dat pomocí strukturovaného streamování Sparku

Na domovské stránce [Jupyter notebook](https://jupyter.org/) vyberte položku __datový proud-data-from-Kafka-to-Cosmos-DB. ipynb__ . Postupujte podle kroků v poznámkovém bloku pro streamování dat z Kafka a do Azure Cosmos DB pomocí strukturovaného streamování Sparku.

## <a name="next-steps"></a>Další postup

Teď, když jste se seznámili s používáním Apache Spark strukturovaného streamování, najdete v následujících dokumentech Další informace o práci s Apache Spark, Apache Kafka a Azure Cosmos DB:

* [Jak používat streamování Apache Spark (DStream) s Apache Kafka](hdinsight-apache-spark-with-kafka.md).
* [Začínáme s Jupyter Notebook a Apache Spark v HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md)
