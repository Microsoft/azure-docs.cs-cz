---
title: Transformace dat pomocí aktivity streamování Hadoop – Azure
description: Přečtěte si, jak můžete pomocí aktivity streamování Hadoop v Azure Data Factory transformovat data spuštěním programů pro streamování Hadoop na vyžádání nebo vlastním clusterem HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 4c3ff8f2-2c00-434e-a416-06dfca2c41ec
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7f07365da699a40f5b51917104a68a62affa3d9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74703370"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity streamování Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md) 
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita aktualizace prostředku služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity streamování Hadoop v Data Factory](../transform-data-using-hadoop-streaming.md).


Aktivitu HDInsightStreamingActivity můžete použít k vyvolání úlohy streamování Hadoop z Azure Data Factoryho kanálu. Následující fragment kódu JSON ukazuje syntaxi pro použití HDInsightStreamingActivity v souboru JSON kanálu. 

Aktivita streamování HDInsight v [kanálu](data-factory-create-pipelines.md) Data Factory spouští programy pro streamování Hadoop na [vašem](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight založeném na systému Windows/Linux nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](data-factory-data-transformation-activities.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud se Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a udělejte si kurz: [Vytvoření prvního datového kanálu](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

## <a name="json-sample"></a>Ukázka JSON
Cluster HDInsight se automaticky vyplní příklady programů (wc.exe a cat.exe) a dat (davinci.txt). Ve výchozím nastavení je název kontejneru, který je používán clusterem HDInsight, názvem samotného clusteru. Pokud je název clusteru například myhdicluster, je název přidruženého kontejneru objektů BLOB myhdicluster. 

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

1. Nastavte **linkedServiceName** na název propojené služby, která odkazuje na cluster HDInsight, na kterém je spuštěná úloha streamování MapReduce.
2. Nastavte typ aktivity na **HDInsightStreaming**.
3. Pro vlastnost **Mapper** zadejte název spustitelného souboru mapovače. V tomto příkladu cat.exe je spustitelný soubor mapovače.
4. V poli vlastnost pro **snížení** zadejte název spustitelného souboru pro zmenšení. V tomto příkladu je wc.exe spustitelný soubor pro snížení.
5. Pro vlastnost typ **vstupu** zadejte vstupní soubor (včetně umístění) pro mapovač. V příkladu: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` : adfsample je kontejnerem objektů blob, příkladem/data/Gutenberg je složka a davinci.txt je objekt BLOB.
6. Pro vlastnost typ **výstupu** zadejte výstupní soubor (včetně umístění) pro redukci. Výstup úlohy služby streamování Hadoop se zapisuje do umístění zadaného pro tuto vlastnost.
7. V části **cesty** souborů zadejte cesty pro soubory Mapper a zmenšení. V příkladu: "adfsample/example/Apps/wc.exe", adfsample je kontejnerem objektů blob, příkladem/aplikacemi je složka a wc.exe je spustitelný soubor.
8. Pro vlastnost **fileLinkedService** určete propojenou službu Azure Storage, která představuje úložiště Azure, které obsahuje soubory zadané v části cesty souborů.
9. Pro vlastnost **arguments (argumenty** ) zadejte argumenty pro úlohu streamování.
10. Vlastnost **GetDebugInfo –** je volitelný element. Pokud je nastavená chyba, protokoly se stáhnou pouze při selhání. Pokud je nastavené na vždycky, protokoly se stáhnou vždycky bez ohledu na stav spuštění.

> [!NOTE]
> Jak je znázorněno v příkladu, zadáváte výstupní datovou sadu pro aktivitu streamování Hadoop pro vlastnost **výstupy** . Tato datová sada je pouze fiktivní datová sada, která je požadována pro řízení plánu kanálu. Pro aktivitu **pro vlastnost** Inputs není nutné zadávat žádnou vstupní datovou sadu.  
> 
> 

## <a name="example"></a>Příklad
Kanál v tomto návodu spustí v clusteru Azure HDInsight program pro vytváření map a snižování počtu slov. 

### <a name="linked-services"></a>Propojené služby
#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Nejdřív vytvoříte propojenou službu, která propojí Azure Storage, kterou cluster Azure HDInsight používá pro Azure Data Factory. Pokud zkopírujete/vložíte následující kód, nezapomeňte nahradit název účtu a klíč účtu názvem a klíčem vašeho Azure Storage. 

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

#### <a name="azure-hdinsight-linked-service"></a>Propojená služba Azure HDInsight
V dalším kroku vytvoříte propojenou službu, která propojí cluster Azure HDInsight s objektem pro vytváření dat Azure. Pokud zkopírujete/vložíte následující kód, nahraďte název clusteru HDInsight názvem vašeho clusteru HDInsight a změňte hodnoty uživatelského jména a hesla. 

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
Kanál v tomto příkladu nepřijímá žádné vstupy. Pro aktivitu streamování HDInsight určíte výstupní datovou sadu. Tato datová sada je pouze fiktivní datová sada, která je požadována pro řízení plánu kanálu. 

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
Kanál v tomto příkladu má pouze jednu aktivitu typu: **HDInsightStreaming**. 

Cluster HDInsight se automaticky vyplní příklady programů (wc.exe a cat.exe) a dat (davinci.txt). Ve výchozím nastavení je název kontejneru, který je používán clusterem HDInsight, názvem samotného clusteru. Pokud je název clusteru například myhdicluster, je název přidruženého kontejneru objektů BLOB myhdicluster.  

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
* [Aktivita v podregistru](data-factory-hive-activity.md)
* [Aktivita prasete](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

