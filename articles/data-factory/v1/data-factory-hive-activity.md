---
title: Transformace dat pomocí aktivity Hive – Azure
description: Zjistěte, jak můžete použít aktivitu Hive v datové továrně Azure ke spuštění dotazů Hive v clusteru HDInsight na vyžádání nebo ve vlastním clusteru HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: d153f8c316cbb76e063f07f7f823c8d9c4a21f87
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703359"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity Hive v Azure Data Factory 
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma transformace dat pomocí aktivity Hive v datové továrně](../transform-data-using-hadoop-hive.md).

Aktivita HDInsight Hive v [kanálu](data-factory-create-pipelines.md) Data Factory provádí dotazy Hive na [vašem vlastním](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo na [vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) windows/linuxového clusteru HDInsight. Tento článek vychází z článku [aktivit transformace dat,](data-factory-data-transformation-activities.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud jste s Azure Data Factory teprve noví, přečtěte si [úvodní článek azure data factory](data-factory-introduction.md) a proveďte kurz: [Sestavte si první datový kanál](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

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
## <a name="syntax-details"></a>Podrobnosti syntaxe
| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, k čemu se aktivita používá |Ne |
| type |HDinsightHive |Ano |
| Vstupy |Vstupy spotřebované aktivitou Hive |Ne |
| Výstupy |Výstupy produkované činností Hive |Ano |
| linkedServiceName |Odkaz na cluster HDInsight registrovaný jako propojená služba v Datové továrně |Ano |
| . |Zadání vlože skriptu Hive |Ne |
| scriptPath |Uložte skript Hive do úložiště objektů blob Azure a zadejte cestu k souboru. Použijte vlastnost 'script' nebo 'scriptPath'. Obojí nelze použít společně. Název souboru rozlišuje malá a velká písmena. |Ne |
| Definuje |Zadejte parametry jako dvojice klíč/hodnota pro odkazování ve skriptu Hive pomocí "hiveconf" |Ne |

## <a name="example"></a>Příklad
Podívejme se na příklad analýzy herních protokolů, kde chcete identifikovat čas strávený uživateli, kteří hrají hry spuštěné vaší společností. 

Následující protokol je ukázkový herní protokol, který`,`je čárka ( ) oddělena a obsahuje následující pole – ProfileID, SessionStart, Duration, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript Hive** pro zpracování těchto dat:

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

Chcete-li spustit tento skript Hive v kanálu Data Factory, musíte provést následující kroky

1. Vytvořte propojenou službu pro [registraci vlastního výpočetního clusteru HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurujte [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nazvěme tuto propojenou službu "HDInsightLinkedService".
2. Vytvořte [propojenou službu](data-factory-azure-blob-connector.md) pro konfiguraci připojení k úložišti objektů Blob Azure, které jsou hostující data. Nazvěme tuto propojenou službu "StorageLinkedService"
3. Vytvořte [datové sady](data-factory-create-datasets.md) směřující na vstupní a výstupní data. Zavolejte vstupní datovou sadu "HiveSampleIn" a výstupní datovou sadu "HiveSampleOut"
4. Zkopírujte dotaz Hive jako soubor do úložiště objektů blob Azure nakonfigurovaného v kroku #2. Pokud se úložiště pro hostování dat liší od úložiště, které je hostitelem tohoto souboru dotazu, vytvořte samostatnou propojenou službu Azure Storage a podívejte se na ji v aktivitě. Pomocí **scriptPath** určete cestu k souboru dotazu podregistru a **skriptLinkedService** k určení úložiště Azure, které obsahuje soubor skriptu. 
   
   > [!NOTE]
   > Můžete také zadat vsazený skript Hive v definici aktivity pomocí vlastnosti **script.** Tento přístup nedoporučujeme, protože všechny speciální znaky ve skriptu v dokumentu JSON musí být uvozeny a mohou způsobit problémy s laděním. Osvědčeným postupem je postupovat podle #4 kroků.
   > 
   > 
5. Vytvořte kanál s hdinsighthive aktivitou. Aktivita zpracovává/transformuje data.

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
6. Nasaďte potrubí. Podrobnosti [najdete v článku Vytváření kanálů.](data-factory-create-pipelines.md) 
7. Sledujte kanál pomocí zobrazení monitorování a správy datové továrny. Podrobnosti najdete v článku [Monitorování a správa kanálů datové továrny.](data-factory-monitor-manage-pipelines.md) 

## <a name="specifying-parameters-for-a-hive-script"></a>Určení parametrů pro skript Hive
V tomto příkladu jsou protokoly her ingestovány denně do úložiště objektů blob Azure a jsou uloženy ve složce rozdělené podle data a času. Chcete parametrizovat skript Hive a dynamicky předat umístění vstupní složky za běhu a také vytvořit výstup rozdělený s datem a časem.

Chcete-li použít parametrizovaný skript Hive, postupujte takto

* Definujte parametry v **definicích**.

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
* Ve skriptu Hive se podívejte na parametr pomocí **${hiveconf:parameterName}**. 
  
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
* [Aktivita prasat](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

