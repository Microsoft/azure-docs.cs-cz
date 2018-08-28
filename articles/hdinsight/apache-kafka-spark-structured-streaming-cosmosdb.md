---
title: Strukturované streamování z Kafka do služby Azure Cosmos DB – Azure HDInsight Apache Sparku
description: Zjistěte, jak používat strukturované streamování Apache Sparku číst data ze systému Apache Kafka a uloží je do služby Azure Cosmos DB. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: jasonh
ms.openlocfilehash: c18234e50711b2496b793263ca8d314f16347cbe
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43107791"
---
# <a name="use-spark-structured-streaming-with-kafka-and-azure-cosmos-db"></a>Použití strukturovaného streamování Sparku se Kafka a Azure Cosmos DB

Zjistěte, jak pomocí strukturovaného streamování Sparku číst data ze systému Apache Kafka v Azure HDInsight, a potom uložení dat do služby Azure Cosmos DB.

Azure Cosmos DB je globálně distribuovaná vícemodelová databáze. Tento příklad používá model databáze SQL API. Další informace najdete v tématu [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md) dokumentu.

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. Další informace o strukturovaném streamování najdete v [průvodci programováním pro strukturované streamování](http://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html) na webu Apache.org.

> [!IMPORTANT]
> Tento příklad používá Spark 2.2 ve HDInsight 3.6.
>
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Nic, která komunikuje Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu clustery Kafka i Spark nacházejí ve službě Azure virtual network. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

I když můžete vytvořit virtuální síť Azure, Kafka, a clustery Spark ručně, je jednodušší použít šablonu Azure Resource Manageru. V následujícím návodu pro nasazení virtuální sítě Azure, Kafka a clustery se svým předplatným Azure Spark.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-scala-kafka-cosmosdb%2Fmaster%2Fazuredeploy.json" target="_blank">
    <img src="http://azuredeploy.net/deploybutton.png"/>
    </a>

    Šablony Azure Resource Manageru se nachází v úložišti GitHub pro tento projekt ([https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb)).

    Tato šablona vytvoří následující prostředky:

    * Cluster Kafka ve službě HDInsight 3.6.

    * Spark v clusteru HDInsight 3.6.

    * Virtuální síť Azure obsahující clustery HDInsight.

        > [!NOTE]
        > Virtuální sítě vytvořené pomocí šablony používá adresní prostor 10.0.0.0/16.

    * Databáze aplikace SQL API služby Azure Cosmos DB.

    > [!IMPORTANT]
    > Poznámkový blok strukturovaného streamování použitý v tomto příkladu vyžaduje Spark ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

2. Pomocí následujících informací vyplňte položky na **vlastní nasazení** části:
   
    ![HDInsight vlastního nasazení](./media/apache-kafka-spark-structured-streaming-cosmosdb/parameters.png)

    * **Předplatné**: Vyberte své předplatné Azure.
   
    * **Skupina prostředků**: Vytvořte skupinu nebo vyberte existující. Tato skupina obsahuje HDInsight cluster.

    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Název účtu služby cosmos DB**: Tato hodnota se používá jako název účtu služby Cosmos DB.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro Spark a clusterů Kafka. Například zadání **myhdi** vytvoří Spark cluster __spark myhdi__ a cluster Kafka s názvem **kafka myhdi**.

    * **Verze clusteru**: verze clusteru HDInsight.

        > [!IMPORTANT]
        > V tomto příkladu je testovat pomocí HDInsight 3.6 a další typy clusterů, nemusí fungovat.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce clusterů Spark a Kafka.

    * **Heslo přihlášení clusteru**: heslo správce pro clusterů Spark a Kafka.

    * **Uživatelské jméno SSH**: SSH uživatele k vytvoření clusterů Spark a Kafka.

    * **Heslo SSH**: heslo pro uživatele SSH pro clusterů Spark a Kafka.

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**. Vytvoření clusteru trvá přibližně 20 minut.

> [!IMPORTANT]
> Může trvat až 45 minut vytvářet clustery, virtuální síť a účet služby Cosmos DB.

## <a name="create-the-cosmos-db-database-and-collection"></a>Vytvoření databáze Cosmos DB a kolekce

V tomto dokumentu projektu ukládá data ve službě Cosmos DB. Před spuštěním kódu, musíte nejdřív vytvořit _databáze_ a _kolekce_ ve vaší instanci služby Cosmos DB. Musíte také načíst koncový bod dokumentu a _klíč_ používá k ověření požadavků ve službě Cosmos DB. 

Můžete provést například jde použít [příkazového řádku Azure CLI 2.0](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). Tento skript vytvoří databázi s názvem `kafkadata` a kolekci s názvem `kafkacollection`. Pak vrátí primární klíč.

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

Koncový bod dokumentu a informacemi o primárním klíči se podobá následujícímu textu:

```text
# endpoint
"https://mycosmosaccount.documents.azure.com:443/"
# key
"YqPXw3RP7TsJoBF5imkYR0QNA02IrreNAlkrUMkL8EW94YHs41bktBhIgWq4pqj6HCGYijQKMRkCTsSaKUO2pw=="
```

> [!IMPORTANT]
> Uložte koncový bod a hodnoty klíče, podle potřeby poznámkové bloky Jupyter.

## <a name="get-the-kafka-brokers"></a>Získání zprostředkovatelů Kafka

Kód v tomto příkladu se připojí k hostiteli zprostředkovatele Kafka v clusteru Kafka. K vyhledání adresy dvěma hostiteli zprostředkovatele Kafka použijte následující příklad PowerShell nebo Bash:

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
> Očekává, že v příkladu Bash `$CLUSTERNAME` tak, aby obsahovala název clusteru Kafka.
>
> V tomto příkladu [jq](https://stedolan.github.io/jq/) nástroj a analyzovat data z dokumentu JSON.

```bash
curl -u admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
```

Po zobrazení výzvy zadejte heslo pro účet clusteru (správce)

Výstup se bude podobat následujícímu:

`wn0-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092,wn1-kafka.0owcbllr5hze3hxdja3mqlrhhe.ex.internal.cloudapp.net:9092`

Tyto informace uložte, protože se používá v následujících částech tohoto dokumentu.

## <a name="get-the-notebooks"></a>Získat poznámkových bloků

Kód příkladu popisovaného v tomto dokumentu je dostupný na adrese [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka-cosmosdb).

## <a name="upload-the-notebooks"></a>Nahrání poznámkových bloků

Následujícím postupem nahrání poznámkových bloků z projektu na Spark na clusteru HDInsight:

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. V horní pravé straně stránky, použijte __nahrát__ tlačítko Nahrát __Stream taxislužby data na kafka.ipynb__ souboru do clusteru. Vyberte __Otevřít__ a zahajte nahrávání.

3. Najít __Stream taxislužby data na kafka.ipynb__ položky v seznamu poznámkových bloků a vyberte __nahrát__ tlačítko vedle něj.

4. Opakujte kroky 1-3 pro načtení __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ poznámkového bloku.

## <a name="load-taxi-data-into-kafka"></a>Načtení dat taxislužby do Kafka

Jakmile se nahrály soubory, vyberte __Stream taxislužby data na kafka.ipynb__ záznam, tím otevřete Poznámkový blok. Postupujte podle kroků v poznámkovém bloku k načtení dat do systému Kafka.

## <a name="process-taxi-data-using-spark-structured-streaming"></a>Zpracování dat taxislužby pomocí strukturovaného streamování Sparku

Na domovské stránce Poznámkový blok Jupyter, vyberte __Stream-data-from-Kafka-to-Cosmos-DB.ipynb__ položka. Postupujte podle kroků v poznámkovém bloku pro streamování dat z Kafka a do služby Azure Cosmos DB pomocí strukturovaného streamování Sparku.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili, jak pomocí strukturovaného streamování Sparku, najdete v následujících dokumentech Další informace o práci s Spark, Kafka a Azure Cosmos DB:

* [Jak používat streamování Sparku (DStream) s využitím systému Kafka](hdinsight-apache-spark-with-kafka.md)
* [Začínáme s poznámkovými bloky Jupyter a Sparkem ve službě HDInsight](spark/apache-spark-jupyter-spark-sql.md)
* [Vítá vás Azure Cosmos DB](../cosmos-db/introduction.md)
