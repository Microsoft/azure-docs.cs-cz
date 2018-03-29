---
title: Apache Spark strukturovaných streamování z Kafka Cosmos Azure DB - Azure HDInsight | Microsoft Docs
description: Naučte se používat Apache Spark strukturovaných streamování ke čtení dat z Apache Kafka a uložte ho do Azure Cosmos DB. V tomto příkladu stream dat pomocí poznámkového bloku Jupyter z Spark v HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/26/2018
ms.author: larryfr
ms.openlocfilehash: 7346a45cf04b50369cc7b853b985a8b0bc865493
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Použít Spark strukturovaná streamování s Kafka a Azure Cosmos DB

Naučte se používat strukturované datové proudy Spark ke čtení dat z Apache Kafka v Azure HDInsight a potom uložení dat do Azure Cosmos DB.

Azure Cosmos DB je globálně distribuované více modelu databáze. Tento příklad používá model rozhraní API pro SQL databáze. Další informace najdete v tématu [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md) dokumentu.

Vysílání datového proudu strukturovaná Spark je modul zpracování datového proudu, který je založený na Spark SQL. Umožňuje express streamování výpočty stejná jako výpočetní batch na statických dat. Další informace o strukturovaných streamování najdete v tématu [strukturovaných streamování Průvodce programováním [Alpha]](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) na Apache.org.

> [!IMPORTANT]
> Tento příklad používá Spark 2.2 na HDInsight 3.6.
>
> Kroky v tomto dokumentu vytvořte skupinu prostředků Azure, která obsahuje oba Spark v HDInsight a Kafka na clusteru HDInsight. Tyto clustery jsou obě nachází v rámci virtuální síť Azure, což umožňuje clusteru Spark přímo komunikovat s Kafka clusteru.
>
> Po dokončení kroků v tomto dokumentu, nezapomeňte odstranit clustery nadbytečné náklady.

## <a name="create-the-clusters"></a>Vytváření clusterů

Apache Kafka v HDInsight neposkytuje přístup k zprostředkovatelé Kafka prostřednictvím veřejného Internetu. Všechno, co komunikuje se Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu jsou Kafka i Spark clusterů umístěné v virtuální sítě Azure. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram clustery Spark a Kafka v virtuální sítě Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Kafka služby je omezený na komunikaci v rámci virtuální sítě. Jiné služby v clusteru, například SSH a Ambari, jsou přístupné přes internet. Další informace o veřejné porty, které jsou k dispozici s HDInsight naleznete v tématu [porty a identifikátory URI používají v prostředí HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Když vytvoříte virtuální síť Azure, Kafka, a clustery Spark ručně, je jednodušší použít šablonu Azure Resource Manager. Použijte následující kroky k nasazení virtuální sítě Azure, Kafka a clustery k předplatnému Azure z Spark.

1. Na následující tlačítko použijte pro přihlášení do Azure a otevřete šablonu na portálu Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Šablona Azure Resource Manager se nachází v úložišti GitHub pro tento projekt ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Tato šablona vytváří v následujících zdrojích informací:

    * Kafka v clusteru HDInsight 3.6.

    * Spark v HDInsight 3.6 clusteru.

    * Virtuální síť Azure, který obsahuje clusterů HDInsight.

        > [!NOTE]
        > Virtuální sítě vytvořené pomocí šablony používá 10.0.0.0/16 adresní prostor.

    * Databáze aplikace API Azure Cosmos databáze SQL.

    > [!IMPORTANT]
    > Strukturované streamování poznámkového bloku použitý v tomto příkladu vyžaduje Spark v HDInsight 3.6. Pokud používáte starší verzi Spark v HDInsight, zobrazí se chyba při použití poznámkového bloku.

2. Následující informace slouží k naplnění položek na **vlastní nasazení** části:
   
    ![HDInsight vlastní nasazení](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Předplatné**: Vyberte své předplatné Azure.
   
    * **Skupina prostředků**: vytvoření skupiny nebo vyberte nějaký existující. Tato skupina obsahuje clusteru HDInsight.

    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Název účtu DB cosmos**: Tato hodnota se používá jako název účtu Cosmos DB.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro Spark a Kafka clusterů. Například zadáním **myhdi** vytvoří cluster Spark s názvem __spark myhdi__ a Kafka clusteru s názvem **kafka myhdi**.

    * **Verze clusteru**: HDInsight verze clusteru.

        > [!IMPORTANT]
        > Tento příklad je testován s HDInsight 3.6 a nemusí fungovat s jinými typy clusteru.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce pro clustery Spark a Kafka.

    * **Heslo pro přihlášení clusteru**: uživatelské heslo správce pro clustery Spark a Kafka.

    * **Uživatelské jméno SSH**: SSH, aby uživatel vytvořil pro clustery Spark a Kafka.

    * **Heslo SSH**: heslo pro uživatele SSH pro clustery Spark a Kafka.

3. Pro čtení **podmínky a ujednání**a potom vyberte **souhlasím s podmínkami a ujednáními výše uvedených**.

4. Nakonec zkontrolujte **připnout na řídicí panel** a pak vyberte **nákupu**. Chcete-li vytvořit clustery trvá asi 20 minut.

> [!IMPORTANT]
> To může trvat až 45 minut pro vytvoření clusterů, virtuální síť a účet Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Vytvoření databáze Cosmos databáze a kolekce.

Projekt v tomto dokumentu ukládá data do databáze. Cosmos. Před spuštěním kódu, je nutné nejprve vytvořit _databáze_ a _kolekce_ v instanci databáze Cosmos. Musíte také načíst koncový bod dokumentu a _klíč_ používá k ověřování žádostí Cosmos DB. 

Jeden ze způsobů, jak provést jde použít [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Následující skript se vytvoří databáze s názvem `kafkadata` a kolekci s názvem `kafkacollection`. Pak vrátí primární klíč.

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

Koncový bod dokumentu a informace o primárním klíči je podobná následující text:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> Uložte koncový bod a hodnoty klíčů potřeby v Jupyter Notebooks.

## <a name="get-the-kafka-brokers"></a>Získat Kafka zprostředkovatelé

Kód v tomto příkladu se připojí k Kafka zprostředkovatele hostitele v clusteru Kafka. Pokud chcete najít adresy dvou zprostředkovatele hostitelů Kafka, použijte následující příklad PowerShell nebo Bash:

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
> Příklad Bash očekává `$CLUSTERNAME` tak, aby obsahovala název clusteru Kafka.
>
> Tento příklad používá [jq](https://stedolan.github.io/jq/) nástroj analyzovat data z dokumentu JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Po zobrazení výzvy zadejte heslo pro účet Clusterové přihlášení (správce)

Výstup se bude podobat následujícímu:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Tyto informace uložte, protože se používá v následujících částech tohoto dokumentu.

## <a name="get-the-notebooks"></a>Získat poznámkových bloků

Kód pro tento příklad popsané v tomto dokumentu je k dispozici na [ https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb ](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Nahrát poznámkových bloků

Nahrát poznámkových bloků z projektu do vaší Spark v clusteru HDInsight pomocí následujících kroků:

1. Ve webovém prohlížeči připojte do poznámkového bloku Jupyter v clusteru Spark. V následující adresu URL, nahraďte `CLUSTERNAME` s názvem vaší __Spark__ clusteru:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací clusteru (správce) a heslo použité při vytváření clusteru.

2. Použijte v horní pravé části stránky se __nahrát__ tlačítko Odeslat __datového proudu taxíkem data na kafka.ipynb__ souboru do clusteru. Vyberte __otevřete__ spusťte.

3. Najít __datového proudu taxíkem data na kafka.ipynb__ položku v seznamu poznámkových bloků a vyberte __nahrát__ tlačítko vedle ní.

4. Opakujte kroky 1 – 3 načíst __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ poznámkového bloku.

## <a name="load-taxi-data-into-kafka"></a>Načítání dat taxíkem do Kafka

Jakmile soubory byly odeslány, vyberte __datového proudu taxíkem data na kafka.ipynb__ záznam, tím otevřete Poznámkový blok. Postupujte podle kroků v poznámkovém bloku k načtení dat do Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Zpracování taxíkem dat pomocí Spark strukturovaných streamování

Na domovské stránce poznámkového bloku Jupyter, vyberte __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ položku. Ze Kafka a do Azure DB Cosmos pomocí Spark strukturovaných Streaming, postupujte podle kroků v poznámkovém bloku k datům datového proudu.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili použití Spark strukturovaných streamování, najdete v následujících dokumentech Další informace o práci s Spark, Kafka a Cosmos databázi Azure:

* [Jak používat Spark streamování (DStream) s Kafka](hdinsight-apache-spark-with-kafka.md).
* [Začněte s Poznámkový blok Jupyter a Spark v HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md)
