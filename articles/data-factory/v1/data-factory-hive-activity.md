---
title: Transformace dat pomocí aktivity Hivu – Azure | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí aktivity Hivu ve službě Azure data factory ke spouštění dotazů Hive v clusteru HDInsight na vyžádání/svůj vlastní.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 3dda16450f5454b4fae6d18235b05b7bb29a8b91
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54018854"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hivu ve službě Azure Data Factory 
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformovat data pomocí aktivity Hivu ve službě Data Factory](../transform-data-using-hadoop-hive.md).

Aktivita HDInsight Hive ve službě Data Factory [kanálu](data-factory-create-pipelines.md) spustí dotazy Hive na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivity transformace dat](data-factory-data-transformation-activities.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](data-factory-introduction.md) a udělat kurz: [Vytvoření prvního kanálu dat](data-factory-build-your-first-pipeline.md) před čtením tohoto článku. 

## <a name="syntax"></a>Syntaxe

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Podrobnosti o syntaxi
| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, k čemu aktivita slouží |Ne |
| type |HDinsightHive |Ano |
| vstupy |Vstupy spotřebovávané aktivitou Hivu |Ne |
| výstupy |Výstupy vytvořená aktivitou Hive |Ano |
| linkedServiceName |Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory |Ano |
| skript |Zadejte vložený skript Hive |Ne |
| Cesta ke skriptu |Store skriptu Hivu ve službě Azure blob storage a zadejte cestu k souboru. Vlastnost 'script' nebo "scriptPath". Obě nelze použít společně. Název souboru je velká a malá písmena. |Ne |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu Hive pomocí "hiveconf. |Ne |

## <a name="example"></a>Příklad:
Zvažte příklad herních protokolů analytics, ve které chcete určit čas strávený uživatelé hraní her spustili ve vaší společnosti. 

Následující protokol je ukázkový herní protokol, což je čárkou (`,`) oddělené a obsahuje následující pole – ID profilu, SessionStart, doba trvání, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript podregistru** ke zpracování těchto dat:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Chcete-li spustit tento skript Hive v kanálu služby Data Factory, musíte udělat následující

1. Vytvoření propojené služby pro registraci [vlastní HDInsight výpočetní cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurovat [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Pojmenujme tuto propojenou službu "HDInsightLinkedService".
2. Vytvoření [propojená služba](data-factory-azure-blob-connector.md) ke konfiguraci připojení k hostování dat úložiště objektů Blob v Azure. Pojmenujme tuto propojenou službu "StorageLinkedService"
3. Vytvoření [datových sad](data-factory-create-datasets.md) odkazující na vstupní a výstupní data. Pojmenujme vstupní datové sady "HiveSampleIn" a výstupní datovou sadu "HiveSampleOut"
4. Kopírovat dotaz Hive jako soubor do Azure Blob Storage nakonfigurovali v kroku #2. Pokud úložiště pro hostování dat je jiný než ten, který je hostitelem tento soubor dotazu, vytvořte samostatné služby propojené služby Azure Storage a na něj odkazovat v rámci aktivity. Použití **scriptPath** zadat cestu k souboru dotazu hive a **scriptLinkedService** zadat úložiště Azure, která obsahuje soubor skriptu. 
   
   > [!NOTE]
   > Můžete také poskytnout vložený skript Hive v definici aktivity pomocí **skript** vlastnost. Tento přístup není doporučujeme jako všechny speciální znaky ve skriptu v rámci dokumentu JSON se třeba být uvozeny řídicími znaky a může způsobit problémy ladění. Osvědčeným postupem je postupujte podle kroku #4.
   > 
   > 
5. Vytvoření kanálu s aktivitou HDInsightHive. Aktivita, zpracovává nebo transformuje data.

    ```JSON   
    {   
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                {
                    "name": "HiveSampleIn"
                }
                ],
                "outputs": [
                {
                    "name": "HiveSampleOut"
                }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
            ]
        }
    }
    ```
6. Nasaďte kanál. Zobrazit [vytváření kanálů](data-factory-create-pipelines.md) , kde najdete podrobnosti. 
7. Monitorování kanálu pomocí zobrazení dat objekt pro vytváření monitorování a správu. Zobrazit [monitorování a Správa kanálů Data Factory](data-factory-monitor-manage-pipelines.md) , kde najdete podrobnosti. 

## <a name="specifying-parameters-for-a-hive-script"></a>Zadání parametrů pro skript Hive
V tomto příkladu herních protokolů je možné denně zpracovat do služby Azure Blob Storage a jsou uloženy ve složce rozdělit na oddíly s datem a časem. Chcete parametrizovat skriptu Hivu a předat vstupní složky dynamicky za běhu a také generovat výstup rozdělený do oddílů pomocí data a času.

Použití parametrizovaného skriptu Hive, proveďte následující

* Definujte parametry v **definuje**.

    ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* Ve skriptu Hive, odkazovat na parametr pomocí **${hiveconf:parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
## <a name="see-also"></a>Viz také
* [Aktivita pig](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

