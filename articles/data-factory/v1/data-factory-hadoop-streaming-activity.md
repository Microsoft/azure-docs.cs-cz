---
title: Transformace dat pomocí aktivity streamování Hadoop – Azure
description: Zjistěte, jak můžete použít aktivitu streamování Hadoop v továrně dat Azure k transformaci dat spuštěním programů Hadoop Streaming v clusteru HDInsight na vyžádání nebo ve vlastním clusteru HDInsight.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703370"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity streamování Hadoop v Azure Data Factory
> [!div class="op_single_selector" title1="Transformační aktivity"]
> * [Aktivita úlu](data-factory-hive-activity.md) 
> * [Aktivita prasat](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
> * [Aktivita jiskry](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, [přečtěte si téma transformace dat pomocí aktivity streamování Hadoop v datové továrně](../transform-data-using-hadoop-streaming.md).


Můžete použít HDInsightStreamingActivity aktivity vyvolat hadoop streaming úlohy z kanálu Azure Data Factory. Následující fragment JSON zobrazuje syntaxi pro použití HDInsightStreamingActivity v souboru JSON kanálu. 

Aktivita streamování HDInsight v [kanálu](data-factory-create-pipelines.md) Data Factory provádí programy Hadoop Streaming na [vašem vlastním](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo na [vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) windows/linuxového clusteru HDInsight. Tento článek vychází z článku [aktivit transformace dat,](data-factory-data-transformation-activities.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud jste s Azure Data Factory teprve noví, přečtěte si [úvodní článek azure data factory](data-factory-introduction.md) a proveďte kurz: [Sestavte si první datový kanál](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

## <a name="json-sample"></a>Vzorek JSON
Cluster HDInsight je automaticky naplněn ukázkovými programy (wc.exe a cat.exe) a daty (davinci.txt). Ve výchozím nastavení je název kontejneru, který používá cluster HDInsight, název samotného clusteru. Například pokud název clusteru je myhdicluster, název kontejneru objektů blob přidružené by myhdicluster. 

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

1. Nastavte **linkedServiceName** na název propojené služby, která odkazuje na clusterHDInsight, na kterém je spuštěna úloha streamování mapreduce.
2. Nastavte typ aktivity na **HDInsightStreaming**.
3. Pro vlastnost **mapovače** zadejte název spustitelného objektu mapovače. V příkladu cat.exe je mapper spustitelný soubor.
4. Pro vlastnost **reduktoru** zadejte název spustitelného souboru reduktoru. V příkladu wc.exe je spustitelný soubor reduktoru.
5. Pro vlastnost **input** type zadejte vstupní soubor (včetně umístění) pro mapovač. V příkladu: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt`: adfsample je kontejner objektů blob, example/data/Gutenberg je složka a davinci.txt je objekt blob.
6. Pro vlastnost **výstupního** typu zadejte výstupní soubor (včetně umístění) pro reduktor. Výstup úlohy streamování Hadoop je zapsán do umístění určeného pro tuto vlastnost.
7. V části **filePaths** určete cesty pro pořizovač mapovačů a redukčních spustitelných souborů. V příkladu: "adfsample/example/apps/wc.exe", adfsample je kontejner objektů blob, příklad/aplikace je složka a wc.exe je spustitelný soubor.
8. Pro vlastnost **fileLinkedService** zadejte propojenou službu Azure Storage, která představuje úložiště Azure, které obsahuje soubory zadané v části filePaths.
9. Pro vlastnost **argumenty** zadejte argumenty pro úlohu streamování.
10. Vlastnost **getDebugInfo** je volitelný prvek. Pokud je nastavena na selhání, protokoly jsou staženy pouze při selhání. Pokud je nastavena na vždy, protokoly jsou vždy staženy bez ohledu na stav spuštění.

> [!NOTE]
> Jak je znázorněno v příkladu, zadáte výstupní datovou sadu pro aktivitu streamování Hadoop pro vlastnost **výstupy.** Tato datová sada je pouze fiktivní datová sada, která je vyžadována k řízení plánu kanálu. Není nutné zadat žádné vstupní datové sady pro aktivitu pro **vlastnost inputs.**  
> 
> 

## <a name="example"></a>Příklad
Kanál v tomto návodu spustí program mapování/snížení počtu slov v clusteru Azure HDInsight. 

### <a name="linked-services"></a>Propojené služby
#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Nejprve vytvoříte propojenou službu pro propojení úložiště Azure, které používá cluster Azure HDInsight, s toutožnou továrně dat Azure. Pokud zkopírujete nebo vložíte následující kód, nezapomeňte nahradit název účtu a klíč účtu názvem a klíčem úložiště Azure. 

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
Dále vytvoříte propojenou službu, která propojí váš cluster Azure HDInsight s totodatovou továrnou Azure. Pokud zkopírujete nebo vložíte následující kód, nahraďte název clusteru HDInsight názvem clusteru HDInsight a změňte hodnoty uživatelského jména a hesla. 

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
Kanál v tomto příkladu nepřijímá žádné vstupy. Zadáte výstupní datovou sadu pro aktivitu streamování HDInsight. Tato datová sada je pouze fiktivní datová sada, která je vyžadována k řízení plánu kanálu. 

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

Cluster HDInsight je automaticky naplněn ukázkovými programy (wc.exe a cat.exe) a daty (davinci.txt). Ve výchozím nastavení je název kontejneru, který používá cluster HDInsight, název samotného clusteru. Například pokud název clusteru je myhdicluster, název kontejneru objektů blob přidružené by myhdicluster.  

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
* [Aktivita úlu](data-factory-hive-activity.md)
* [Aktivita prasat](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

