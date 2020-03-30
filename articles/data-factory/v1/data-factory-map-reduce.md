---
title: Vyvolání programu MapReduce z Azure Data Factory
description: Zjistěte, jak zpracovat data spuštěním programů MapReduce v clusteru Azure HDInsight z továrny dat Azure.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 598a16d25ba375b984a966cba190181edbda3d15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703154"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Vyvolat programy MapReduce z datové továrny
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, [přečtěte si téma transformace dat pomocí aktivity MapReduce v datové továrně](../transform-data-using-hadoop-map-reduce.md).


Aktivita HDInsight MapReduce v [kanálu](data-factory-create-pipelines.md) Data Factory provádí programy MapReduce na [vašem vlastním](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo na [vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) založeného hdinsight clusteru windows/linux. Tento článek vychází z článku [aktivit transformace dat,](data-factory-data-transformation-activities.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud jste s Azure Data Factory teprve noví, přečtěte si [úvodní článek azure data factory](data-factory-introduction.md) a proveďte kurz: [Sestavte si první datový kanál](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku.  

## <a name="introduction"></a>Úvod
Kanál ve společnosti Azure pro data zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje použití HDInsight MapReduce aktivity.

Podrobnosti o spouštění skriptů Pig/Hive v clusteru HDInsight založeném na Windows/Linuxu z kanálu pomocí aktivit HDInsight Pig a Hive najdete v tématu [Pig](data-factory-pig-activity.md) [and](data-factory-hive-activity.md) Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON pro HDInsight MapReduce aktivity
V definici JSON pro aktivitu HDInsight: 

1. Nastavte **typ** **aktivity** na **HDInsight**.
2. Zadejte název třídy pro vlastnost **className.**
3. Zadejte cestu k souboru JAR včetně názvu souboru pro vlastnost **jarFilePath.**
4. Zadejte propojenou službu, která odkazuje na úložiště objektů blob Azure, které obsahuje soubor JAR pro vlastnost **jarLinkedService.**   
5. Zadejte všechny argumenty pro program MapReduce v části **argumenty.** Za běhu se zobrazí několik dalších argumentů (například mapreduce.job.tags) z rozhraní MapReduce. Chcete-li odlišit argumenty s Argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (-s, --input, --output atd., jsou možnosti bezprostředně následované jejich hodnotami).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "-s",
                            "SIMILARITY_LOGLIKELIHOOD",
                            "--input",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                            "--output",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                            "--maxSimilaritiesPerItem",
                            "500",
                            "--tempDir",
                            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                        ]
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Aktivita HDInsight MapReduce můžete použít ke spuštění libovolného souboru dlaně MapReduce v clusteru HDInsight. V následující ukázce json definice kanálu aktivity HDInsight je nakonfigurován tak, aby spouštět soubor Mahout JAR.

## <a name="sample-on-github"></a>Ukázka na GitHubu
Ukázku pro použití aktivity HDInsight MapReduce si můžete stáhnout z: [Ukázky data továrny na GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Spuštění programu Počet slov
Kanál v tomto příkladu spustí program Mapa/snížení počtu slov v clusteru Azure HDInsight.   

### <a name="linked-services"></a>Propojené služby
Nejprve vytvoříte propojenou službu pro propojení úložiště Azure, které používá cluster Azure HDInsight, s toutožnou továrně dat Azure. Pokud zkopírujete nebo vložíte následující kód, nezapomeňte nahradit **název účtu** a **klíč účtu** názvem a klíčem úložiště Azure. 

#### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage

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
Dále vytvoříte propojenou službu, která propojí váš cluster Azure HDInsight s totodatovou továrnou Azure. Pokud zkopírujete nebo vložíte následující kód, nahraďte **název clusteru HDInsight** názvem clusteru HDInsight a změňte hodnoty uživatelského jména a hesla.   

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
Kanál v tomto příkladu nepřijímá žádné vstupy. Zadáte výstupní datovou sadu pro aktivitu HDInsight MapReduce. Tato datová sada je pouze fiktivní datová sada, která je vyžadována k řízení plánu kanálu.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Kanál
Kanál v tomto příkladu má pouze jednu aktivitu, která je typu: HDInsightMapReduce. Některé z důležitých vlastností v JSON jsou: 

| Vlastnost | Poznámky |
|:--- |:--- |
| type |Typ musí být nastaven na **HDInsightMapReduce**. |
| Classname |Název třídy je: **počet slov** |
| jarFilePath |Cesta k souboru jar obsahující třídu. Pokud zkopírujete nebo vložíte následující kód, nezapomeňte změnit název clusteru. |
| jarLinkedService |Azure Storage propojené služby, která obsahuje soubor jar. Tato propojená služba odkazuje na úložiště, které je přidruženo ke clusteru HDInsight. |
| Argumenty |Program počtu slov trvá dva argumenty, vstup a výstup. Vstupní soubor je soubor davinci.txt. |
| frequency/interval |Hodnoty pro tyto vlastnosti odpovídají výstupní datové sadě. |
| linkedServiceName |odkazuje na propojenou službu HDInsight, kterou jste vytvořili dříve. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Spuštění programů Spark
Pomocí aktivity MapReduce můžete na svém clusteru HDInsight Spark spouštět programy Spark. Podrobnosti najdete v článku [Vyvolání programů Spark ze služby Azure Data Factory](data-factory-spark.md).  

[developer-reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: https://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Viz také
* [Aktivita úlu](data-factory-hive-activity.md)
* [Aktivita prasat](data-factory-pig-activity.md)
* [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

