---
title: Volání programů MapReduce z Azure Data Factory
description: Zjistěte, jak můžete zpracovávat data pomocí programů MapReduce v clusteru Azure HDInsight ze služby Azure data factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: c34db93f-570a-44f1-a7d6-00390f4dc0fa
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 309ddcf68d03f34ca3309d76d15cc3928037c667
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017442"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Volání programů MapReduce z Data Factory
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformovat data pomocí aktivity MapReduce ve službě Data Factory](../transform-data-using-hadoop-map-reduce.md).


Aktivita HDInsight MapReduce ve službě Data Factory [kanálu](data-factory-create-pipelines.md) programů MapReduce se spustí na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivity transformace dat](data-factory-data-transformation-activities.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](data-factory-introduction.md) a udělat kurz: [Vytvoření prvního kanálu dat](data-factory-build-your-first-pipeline.md) před čtením tohoto článku.  

## <a name="introduction"></a>Úvod
Kanál v objektu pro vytváření dat Azure zpracovává data v propojené služby s využitím propojené výpočetní služby. Obsahuje posloupnost aktivit, kde každá aktivita vykonává zpracování specifické pro operaci. Tento článek popisuje pomocí aktivity MapReduce HDInsight.

Zobrazit [Pig](data-factory-pig-activity.md) a [Hive](data-factory-hive-activity.md) Další informace o spouštění Pigu a Hivu skripty v HDInsight se systémem Windows/Linux clusteru z kanálu pomocí aktivity HDInsight Pigu a Hivu. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON pro činnost MapReduce s HDInsight
V definici JSON aktivity HDInsight: 

1. Nastavte **typ** z **aktivity** k **HDInsight**.
2. Zadejte název třídy pro **className** vlastnost.
3. Zadejte cestu k souboru JAR, včetně názvu souboru **jarFilePath** vlastnost.
4. Zadání propojené služby, který odkazuje na Azure Blob Storage, který obsahuje soubor JAR pro **jarLinkedService** vlastnost.   
5. Zadejte všechny argumenty pro MapReduce program **argumenty** oddílu. Za běhu, se zobrazí několik dalších argumentů (například: mapreduce.job.tags) z rozhraní MapReduce. K rozlišení vaše argumenty s argumenty MapReduce, zvažte použití možnosti a hodnoty jako argumenty, jak je znázorněno v následujícím příkladu (- s, – vstup, – výstup atd., jsou možnosti bezprostředně následovat jejich hodnoty).

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
Aktivita MapReduce HDInsight můžete použít ke spuštění souboru jar jakékoli MapReduce v clusteru HDInsight. V následující definici JSON ukázkový kanál je nakonfigurován na aktivitu HDInsight umožňuje spustit soubor Mahout JAR.

## <a name="sample-on-github"></a>Ukázka na Githubu
Si můžete stáhnout ukázku pomocí aktivity MapReduce HDInsight: [Ukázky data Factory na Githubu](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Spuštění programu počet slov
Kanál v tomto příkladu spustí program počet slov mapování/zmenšování v clusteru Azure HDInsight.   

### <a name="linked-services"></a>Propojené služby
Nejprve vytvořte propojenou službu, která propojení služby Azure Storage, který se používá cluster Azure HDInsight k Azure data factory. Pokud zkopírujete/vložíte následující kód, nezapomeňte nahradit **název účtu** a **klíč účtu** názvem a klíčem vašeho úložiště Azure. 

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

#### <a name="azure-hdinsight-linked-service"></a>Azure HDInsight, propojené služby
V dalším kroku vytvoříte propojenou službu, která propojí váš cluster Azure HDInsight do služby Azure data factory. Pokud zkopírujete/vložíte následující kód, nahraďte **název clusteru HDInsight** s názvem vašeho clusteru HDInsight, a změnit uživatelské jméno a heslo hodnoty.   

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
Kanál v tomto příkladu nepřijímá žádné vstupy. Zadejte výstupní datovou sadu pro činnost MapReduce HDInsight. Tato datová sada je jenom fiktivní datovou sadu, která je nutná Centrum umožňující prosazovat plán kanálu.  

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
Kanál v tomto příkladu má pouze jednu aktivitu, která je typu: HDInsightMapReduce. Zde jsou některé důležité vlastnosti v kódu JSON: 

| Vlastnost | Poznámky |
|:--- |:--- |
| type |Typ musí být nastavený na **HDInsightMapReduce**. |
| Název třídy |Je název třídy: **wordcount** |
| jarFilePath |Cesta k souboru jar obsahující třídu. Pokud zkopírujete/vložíte následující kód, nezapomeňte změnit název clusteru. |
| jarLinkedService |Propojená služba Azure Storage, který obsahuje soubor jar. Tato propojená služba odkazuje na úložiště, který je spojen s clusterem HDInsight. |
| argumenty |Aplikace wordcount přebírá dva argumenty, vstup a výstup. Vstupní soubor je soubor davinci.txt. |
| frequency/interval |Hodnoty pro tyto vlastnosti odpovídají výstupní datovou sadu. |
| linkedServiceName |odkazuje na HDInsight propojenou službu, kterou jste vytvořili dříve. |

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
* [Aktivita hivu](data-factory-hive-activity.md)
* [Aktivita pig](data-factory-pig-activity.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

