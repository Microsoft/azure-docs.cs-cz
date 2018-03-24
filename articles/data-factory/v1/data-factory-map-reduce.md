---
title: Vyvolání MapReduce programu ze služby Azure Data Factory
description: Zjistěte, jak ke zpracování dat spuštěnými programy MapReduce v clusteru Azure HDInsight ze služby Azure data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: f625a8a5bc5c477e785ec645b37b0604052d11b4
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Vyvolání MapReduce programy z objektu pro vytváření dat
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
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [transformace dat pomocí činnost MapReduce v datové továrně verze 2](../transform-data-using-hadoop-map-reduce.md).


Činnost HDInsight MapReduce v datové továrně [kanálu](data-factory-create-pipelines.md) provede MapReduce programy na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivit transformace dat](data-factory-data-transformation-activities.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a proveďte kurz: [sestavit svůj první kanál dat](data-factory-build-your-first-pipeline.md) před přečtení tohoto článku.  

## <a name="introduction"></a>Úvod
Kanál v objektu pro vytváření dat Azure zpracovává data v služby propojené úložiště pomocí propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita provede konkrétní zpracování operace. Tento článek popisuje použití činnost MapReduce HDInsight.

V tématu [Pig](data-factory-pig-activity.md) a [Hive](data-factory-hive-activity.md) podrobnosti o spouštění Pig nebo Hive skriptů na HDInsight se systémem Windows nebo Linux clusteru z kanálu pomocí aktivity HDInsight Pig a Hive. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON pro činnost MapReduce s HDInsight
V definici JSON aktivity HDInsight: 

1. Nastavte **typ** z **aktivity** k **HDInsight**.
2. Zadejte název třídy pro **className** vlastnost.
3. Zadejte cestu k souboru JAR, včetně názvu souboru pro **jarFilePath** vlastnost.
4. Zadejte propojené služby, která odkazuje na Azure Blob Storage, který obsahuje soubor JAR pro **jarLinkedService** vlastnost.   
5. Zadejte všechny argumenty pro MapReduce program **argumenty** části. V době běhu zobrazí několik další argumenty (například: mapreduce.job.tags) z rozhraní MapReduce. Chcete-li rozlišit vaší argumenty s argumenty MapReduce, zvažte, pomocí možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstupní atd., jsou možnosti bezprostředně následované jejich hodnoty).

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
Činnost MapReduce HDInsight můžete spustit libovolný soubor jar MapReduce v clusteru služby HDInsight. V následující definici JSON ukázkový kanál aktivita HDInsight je nakonfigurována pro spuštění soubor Mahout JAR.

## <a name="sample-on-github"></a>Ukázce na Githubu
Můžete si stáhnout ukázku pro činnost MapReduce HDInsight z používání: [ukázky Data Factory na webu GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Spuštění programu počet slov
Kanál v tomto příkladu spustí program Mapa nebo snižte počet slov v clusteru Azure HDInsight.   

### <a name="linked-services"></a>Propojené služby
Nejdřív vytvoříte propojené služby Azure Storage, který se používá cluster Azure HDInsight do Azure data factory propojení. Pokud jste zkopírujte a vložte následující kód, nezapomeňte nahradit **název účtu** a **klíč účtu** zadejte název a klíč vašeho úložiště Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Azure propojené služby HDInsight
Dále vytvoříte propojené služby propojení clusteru Azure HDInsight do Azure data factory. Pokud jste zkopírujte a vložte následující kód, nahraďte **název clusteru HDInsight** s názvem clusteru HDInsight a změnit uživatelské jméno a heslo hodnoty.   

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
Kanál v tomto příkladu nevyžaduje žádné vstupy. Výstupní datové zadáte pro činnost MapReduce HDInsight. Tato datová sada je právě fiktivní datovou sadu, která je potřeba jednotka plán kanálu.  

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
Kanál v tomto příkladu má jenom jedna aktivita, která je typu: HDInsightMapReduce. Jsou některé důležité vlastností v kódu JSON: 

| Vlastnost | Poznámky |
|:--- |:--- |
| type |Typ musí být nastavený na **HDInsightMapReduce**. |
| className |Název třídy je: **wordcount** |
| jarFilePath |Cesta k soubor jar obsahující třídy. Pokud jste zkopírujte a vložte následující kód, nezapomeňte změnit název clusteru. |
| jarLinkedService |Propojená služba, která obsahuje soubor jar Azure Storage. Tato propojená služba odkazuje na úložiště, které je přidružené ke clusteru HDInsight. |
| Argumenty |Wordcount program přebírá dva argumenty, vstup a výstup. Vstupní soubor je soubor davinci.txt. |
| frequency/interval |Hodnoty těchto vlastností odpovídat výstupní datovou sadu. |
| linkedServiceName |odkazuje propojená služba HDInsight, které jste vytvořili dříve. |

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

## <a name="run-spark-programs"></a>Spouštět programy Spark
Pomocí aktivity MapReduce můžete na svém clusteru HDInsight Spark spouštět programy Spark. Podrobnosti najdete v článku [Vyvolání programů Spark ze služby Azure Data Factory](data-factory-spark.md).  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

## <a name="see-also"></a>Viz také
* [Aktivita Hive](data-factory-hive-activity.md)
* [Pig aktivity](data-factory-pig-activity.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

