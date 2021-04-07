---
title: Vyvolat program MapReduce z Azure Data Factory
description: Naučte se zpracovávat data spuštěním programů MapReduce v clusteru Azure HDInsight z objektu pro vytváření dat Azure.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f1f54e972d59d3de3b0f93b3150ee1150eb6f612
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104786392"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Vyvolat programy MapReduce z Data Factory
> [!div class="op_single_selector" title1="Aktivity transformace"]
> * [Aktivita v podregistru](data-factory-hive-activity.md) 
> * [Aktivita prasete](data-factory-pig-activity.md)
> * [Aktivita MapReduce](data-factory-map-reduce.md)
> * [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Aktivita Sparku](data-factory-spark.md)
> * [Aktivita Provedení dávky služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-batch-execution-activity.md)
> * [Aktivita Aktualizace prostředků služby Azure Machine Learning Studio (klasická verze)](data-factory-azure-ml-update-resource-activity.md)
> * [Aktivita Uložená procedura](data-factory-stored-proc-activity.md)
> * [Aktivita U-SQL služby Data Lake Analytics](data-factory-usql-activity.md)
> * [Vlastní aktivita rozhraní .NET](data-factory-use-custom-activities.md)

> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity MapReduce v Data Factory](../transform-data-using-hadoop-map-reduce.md).


Aktivita MapReduce služby HDInsight v [kanálu](data-factory-create-pipelines.md) Data Factory spouští programy MapReduce na [vašem](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight založeném na systému Windows nebo Linux na [vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](data-factory-data-transformation-activities.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud se Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a udělejte si kurz: [Vytvoření prvního datového kanálu](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku.  

## <a name="introduction"></a>Úvod
Kanál v datové továrně Azure zpracovává data v propojených službách úložiště pomocí propojených výpočetních služeb. Obsahuje posloupnost aktivit, kde každá aktivita provádí určitou operaci zpracování. Tento článek popisuje použití aktivity HDInsight MapReduce.

Podrobnosti o spouštění skriptů na bázi vepřového a podregistru v clusteru HDInsight se systémem Windows/Linux z kanálu najdete [v tématu věnovaném vepřovým](data-factory-pig-activity.md) sádl a aktivitám podregistru [služby HDInsight](data-factory-hive-activity.md) . 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON pro aktivitu MapReduce služby HDInsight
V definici JSON aktivity HDInsight: 

1. Nastavte **typ** **aktivity** na **HDInsight**.
2. Zadejte název třídy pro vlastnost **ClassName** .
3. Zadejte cestu k souboru JAR včetně názvu souboru pro vlastnost **jarFilePath** .
4. Zadejte propojenou službu, která odkazuje na Blob Storage Azure, která obsahuje soubor JAR pro vlastnost **jarLinkedService** .   
5. V části **argumenty** zadejte argumenty pro program MapReduce. Za běhu vidíte několik dalších argumentů (například: MapReduce. job. Tags) z rozhraní MapReduce. Chcete-li odlišit argumenty pomocí argumentů MapReduce, zvažte použití možnosti i hodnoty jako argumentů, jak je znázorněno v následujícím příkladu (-s,--Input,--Output atd.) jsou možnosti, které bezprostředně následují jejich hodnoty).

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
   Aktivitu HDInsight MapReduce můžete použít ke spuštění všech souborů JAR MapReduce v clusteru HDInsight. V následující ukázkové definici JSON kanálu je aktivita HDInsight nakonfigurovaná tak, aby spouštěla soubor Mahout JAR.

## <a name="sample-on-github"></a>Ukázka na GitHubu
Můžete si stáhnout ukázku pro použití aktivity HDInsight MapReduce z: [Data Factory ukázky na GitHubu](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Spuštění programu počet slov
Kanál v tomto příkladu spustí program pro mapování/zmenšení počtu slov v clusteru Azure HDInsight.   

### <a name="linked-services"></a>Propojené služby
Nejdřív vytvoříte propojenou službu, která propojí Azure Storage, kterou cluster Azure HDInsight používá pro Azure Data Factory. Pokud zkopírujete/vložíte následující kód, nezapomeňte nahradit **název účtu** a klíč **účtu** názvem a klíčem vašeho Azure Storage. 

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
V dalším kroku vytvoříte propojenou službu, která propojí cluster Azure HDInsight s objektem pro vytváření dat Azure. Pokud zkopírujete/vložíte následující kód, nahraďte **název clusteru HDInsight** názvem vašeho clusteru HDInsight a změňte hodnoty uživatelského jména a hesla.   

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
Kanál v tomto příkladu nepřijímá žádné vstupy. Pro aktivitu MapReduce HDInsight zadáte výstupní datovou sadu. Tato datová sada je pouze fiktivní datová sada, která je požadována pro řízení plánu kanálu.  

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
Kanál v tomto příkladu má pouze jednu aktivitu typu: HDInsightMapReduce. Mezi důležité vlastnosti ve formátu JSON patří: 

| Vlastnost | Poznámky |
|:--- |:--- |
| typ |Typ musí být nastaven na **HDInsightMapReduce**. |
| NázevTřídy |Název třídy je: **WORDCOUNT** |
| jarFilePath |Cesta k souboru jar obsahujícímu třídu Pokud zkopírujete/vložíte následující kód, nezapomeňte změnit název clusteru. |
| jarLinkedService |Azure Storage propojená služba, která obsahuje soubor JAR. Tato propojená služba odkazuje na úložiště, které je přidružené ke clusteru HDInsight. |
| náhodné |Program WORDCOUNT přijímá dva argumenty, vstup a výstup. Vstupním souborem je davinci.txt soubor. |
| frequency/interval |Hodnoty těchto vlastností se shodují s výstupní datovou sadou. |
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

## <a name="run-spark-programs"></a>Spouštění programů Spark
Pomocí aktivity MapReduce můžete na svém clusteru HDInsight Spark spouštět programy Spark. Podrobnosti najdete v článku [Vyvolání programů Spark ze služby Azure Data Factory](data-factory-spark.md).  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Viz také
* [Aktivita v podregistru](data-factory-hive-activity.md)
* [Aktivita prasete](data-factory-pig-activity.md)
* [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)