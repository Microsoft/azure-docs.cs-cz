---
title: Transformace dat pomocí streamované aktivitě Hadoop - Azure | Microsoft Docs
description: Zjistěte, jak můžete pomocí streamované aktivitě Hadoop v objektu pro vytváření dat Azure pro transformaci dat spuštěním streamování Hadoop programy na na vyžádání nebo vaše vlastní cluster HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3d5832f63a3ebe7583d18fcd863c8cc60b9b045d
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37048760"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí streamované aktivitě Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Aktivita Hive](data-factory-hive-activity.md) 
> * [Pig aktivity](data-factory-pig-activity.md)
> * [Činnost MapReduce](data-factory-map-reduce.md)
> * [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Spark aktivity](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivity rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [transformace dat pomocí aktivit v datové továrně streamování Hadoop](../transform-data-using-hadoop-streaming.md).


Můžete použít HDInsightStreamingActivity aktivity vyvolání úlohu streamování Hadoop z kanál služby Azure Data Factory. Následující fragment kódu JSON ukazuje syntaxi pro použití HDInsightStreamingActivity v souboru JSON kanálu. 

HDInsight streamované aktivitě v datové továrně [kanálu](data-factory-create-pipelines.md) provede streamování Hadoop programy na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivit transformace dat](data-factory-data-transformation-activities.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a proveďte kurz: [sestavit svůj první kanál dat](data-factory-build-your-first-pipeline.md) před přečtení tohoto článku. 

## <a name="json-sample"></a>Ukázka JSON
HDInsight cluster se automaticky zadá příklad programy (wc.exe a cat.exe) a data (davinci.txt). Ve výchozím nastavení je název kontejneru, který je používán clusteru HDInsight název samotného clusteru. Například pokud je název clusteru myhdicluster, bude název kontejneru objektu blob přidruženého myhdicluster. 

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

1. Nastavte **linkedServiceName** název propojené služby, která odkazuje na vaše HDInsight clusteru na úlohu streamování mapreduce běží.
2. Nastavte typ aktivity na **HDInsightStreaming**.
3. Pro **mapper** vlastnost, zadejte název spustitelného souboru mapper. V příkladu je cat.exe mapper spustitelný soubor.
4. Pro **reduktorem** vlastnost, zadejte název spustitelného souboru reduktorem. V příkladu je wc.exe reduktorem spustitelný soubor.
5. Pro **vstupní** zadejte vlastnost, zadejte vstupní soubor (včetně umístění) pro mapper. Příklad: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample je kontejner objektů blob, například/data/Gutenberg je složka, a davinci.txt je objekt blob.
6. Pro **výstup** zadejte vlastnost, pro reduktorem zadejte výstupní soubor (včetně umístění). Výstup úlohy streamování Hadoop je zapsán do umístění zadané pro tuto vlastnost.
7. V **filePaths** části, zadejte cesty pro spustitelné soubory mapper a reduktorem. Příklad: "adfsample/example/apps/wc.exe" adfsample je kontejner objektů blob, příklad nebo aplikací je složka a wc.exe je spustitelný soubor.
8. Pro **fileLinkedService** vlastnost, zadejte služby Azure Storage, propojené, který představuje službu Azure storage, který obsahuje soubory zadané v části filePaths.
9. Pro **argumenty** vlastnost, zadejte argumenty pro úlohu streamování.
10. **Getdebuginfo –** vlastnost je volitelná elementu. Pokud je nastavena k chybě, protokoly se stáhnou pouze při selhání. Pokud je nastavený na vždy, protokoly budou staženy vždy bez ohledu na stav spuštění.

> [!NOTE]
> Jak je znázorněno v příkladu, zadáte pro streamované aktivitě Hadoop pro datovou sadu výstupů **výstupy** vlastnost. Tato datová sada je právě fiktivní datovou sadu, která je potřeba jednotka plán kanálu. Není potřeba zadat aktivity pro všechny vstupní datové sady **vstupy** vlastnost.  
> 
> 

## <a name="example"></a>Příklad:
Kanál v tomto návodu se spustí program streamování mapy nebo snižte počet slov v clusteru Azure HDInsight. 

### <a name="linked-services"></a>Propojené služby
#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Nejdřív vytvoříte propojené služby Azure Storage, který se používá cluster Azure HDInsight do Azure data factory propojení. Pokud jste zkopírujte a vložte následující kód, nezapomeňte nahradit název účtu a klíč účtu název a klíč vašeho úložiště Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Azure propojené služby HDInsight
Dále vytvoříte propojené služby propojení clusteru Azure HDInsight do Azure data factory. Pokud jste zkopírujte a vložte následující kód, nahraďte název clusteru HDInsight s názvem clusteru HDInsight a změňte hodnoty uživatelské jméno a heslo. 

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
#### <a name="output-dataset"></a>Výstupní datové sady
Kanál v tomto příkladu nevyžaduje žádné vstupy. Zadáte datovou sadu výstupů aktivity HDInsight streamování. Tato datová sada je právě fiktivní datovou sadu, která je potřeba jednotka plán kanálu. 

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
Kanál v tomto příkladu má jenom jedna aktivita, která je typu: **HDInsightStreaming**. 

HDInsight cluster se automaticky zadá příklad programy (wc.exe a cat.exe) a data (davinci.txt). Ve výchozím nastavení je název kontejneru, který je používán clusteru HDInsight název samotného clusteru. Například pokud je název clusteru myhdicluster, bude název kontejneru objektu blob přidruženého myhdicluster.  

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
* [Aktivita Hive](data-factory-hive-activity.md)
* [Pig aktivity](data-factory-pig-activity.md)
* [Činnost MapReduce](data-factory-map-reduce.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

