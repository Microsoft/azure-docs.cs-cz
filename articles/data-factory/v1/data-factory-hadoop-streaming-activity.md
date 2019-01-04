---
title: Transformace dat pomocí streamované aktivitě Hadoop – Azure | Dokumentace Microsoftu
description: Zjistěte, jak pomocí streamované aktivitě Hadoop ve službě Azure data factory transformovat data spuštěním streamování Hadoop programy na vyžádání/svůj vlastní clusteru služby HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f4bdeee08e81c16dfdd03620eb1fc61251f90400
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54025169"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí streamované aktivitě Hadoop ve službě Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita hivu](data-factory-hive-activity.md) 
> * [Aktivita pig](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Spark](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí systému Hadoop, streamování aktivity ve službě Data Factory](../transform-data-using-hadoop-streaming.md).


Můžete použít aktivitu HDInsightStreamingActivity vyvolat úlohu streamování Hadoop z kanálu služby Azure Data Factory. Následující fragment kódu JSON ukazuje syntaxi pro použití HDInsightStreamingActivity v souboru JSON kanálu. 

Aktivita streamování HDInsight ve službě Data Factory [kanálu](data-factory-create-pipelines.md) spustí programy streamování Hadoop na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivity transformace dat](data-factory-data-transformation-activities.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](data-factory-introduction.md) a udělat kurz: [Vytvoření prvního kanálu dat](data-factory-build-your-first-pipeline.md) před čtením tohoto článku. 

## <a name="json-sample"></a>Vzorek kódu JSON
HDInsight cluster se automaticky vyplní příklad programy (wc.exe a cat.exe) a data (davinci.txt). Ve výchozím nastavení název kontejneru, který se používá cluster HDInsight je název samotného clusteru. Například pokud je název clusteru myhdicluster, název kontejneru objektů blob přidružené by myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Je třeba počítat s následujícím:

1. Nastavte **linkedServiceName** název propojené služby, který odkazuje na vaše HDInsight cluster, podle kterého se spouští úloha streamování mapreduce.
2. Nastavit typ aktivity na **HDInsightStreaming**.
3. Pro **Mapovač** vlastnost, zadejte název spustitelného souboru mapování. V tomto příkladu je cat.exe Mapovač spustitelný soubor.
4. Pro **redukční funkci** vlastnost, zadejte název spustitelného souboru redukční funkci. V tomto příkladu je wc.exe redukční funkci spustitelný soubor.
5. Pro **vstupní** vlastnost typu, zadejte pro mapovač vstupní soubor (včetně umístění). Příklad: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample je kontejner objektů blob, příklad/data/Gutenberg je složka, a davinci.txt objektu blob.
6. Pro **výstup** vlastnost typu, zadejte název výstupního souboru (včetně umístění) pro redukční funkci. Výstup úlohy streamování Hadoop je zapsán do umístění určeného pro tuto vlastnost.
7. V **filePaths** části, zadejte cesty pro spustitelné soubory mapovací a redukční funkci. Příklad: "adfsample/example/apps/wc.exe" adfsample je kontejner objektů blob, příklad/aplikace je složka, a wc.exe spustitelný soubor.
8. Pro **fileLinkedService** vlastnost, zadejte propojenou službu Azure Storage, který představuje úložiště Azure, který obsahuje soubory zadané v části filePaths.
9. Pro **argumenty** vlastnost, zadejte argumenty pro úlohu streamování.
10. **GetDebugInfo** vlastnost je volitelný prvek. Pokud je nastaven na chybu, protokoly se stáhnou pouze při selhání. Pokud je nastavena na hodnotu Always, protokoly budou staženy vždy bez ohledu na stav spuštění.

> [!NOTE]
> Jak je znázorněno v příkladu, zadáte pro streamované aktivitě Hadoop pro výstupní datovou sadu **výstupy** vlastnost. Tato datová sada je jenom fiktivní datovou sadu, která je nutná Centrum umožňující prosazovat plán kanálu. Není potřeba zadat všechny vstupní datové sady pro aktivity **vstupy** vlastnost.  
> 
> 

## <a name="example"></a>Příklad:
Kanál v tomto názorném postupu spustí streamovací program počet slov v mapování/zmenšování v clusteru Azure HDInsight. 

### <a name="linked-services"></a>Propojené služby
#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Nejprve vytvořte propojenou službu, která propojení služby Azure Storage, který se používá cluster Azure HDInsight k Azure data factory. Pokud zkopírujete/vložíte následující kód, nezapomeňte nahradit název účtu a klíč účtu název a klíč služby Azure Storage. 

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight, propojené služby
V dalším kroku vytvoříte propojenou službu, která propojí váš cluster Azure HDInsight do služby Azure data factory. Pokud zkopírujete/vložíte následující kód, název clusteru HDInsight nahraďte názvem vašeho clusteru HDInsight a změnit hodnoty uživatelského jména a hesla. 

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Datové sady
#### <a name="output-dataset"></a>Výstupní datová sada
Kanál v tomto příkladu nepřijímá žádné vstupy. Zadejte výstupní datovou sadu streamování aktivity HDInsight. Tato datová sada je jenom fiktivní datovou sadu, která je nutná Centrum umožňující prosazovat plán kanálu. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Kanál
Kanál v tomto příkladu má pouze jednu aktivitu, která je typu: **HDInsightStreaming**. 

HDInsight cluster se automaticky vyplní příklad programy (wc.exe a cat.exe) a data (davinci.txt). Ve výchozím nastavení název kontejneru, který se používá cluster HDInsight je název samotného clusteru. Například pokud je název clusteru myhdicluster, název kontejneru objektů blob přidružené by myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Viz také
* [Aktivita hivu](data-factory-hive-activity.md)
* [Aktivita pig](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

