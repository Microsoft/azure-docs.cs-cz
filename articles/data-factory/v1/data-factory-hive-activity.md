---
title: Transformace dat pomocí Hive aktivita – Azure | Microsoft Docs
description: Zjistěte, jak pomocí aktivity Hive v objektu pro vytváření dat Azure ke spouštění dotazů Hive v clusteru HDInsight na vyžádání nebo vaše vlastní.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 80083218-743e-4da8-bdd2-60d1c77b1227
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: e8d3b83c8508ae5913975edcbf89f4e70a8b08be
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37050844"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformace dat pomocí Hive aktivity v Azure Data Factory 
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
> Tento článek se týká verze 1 služby Data Factory. Pokud používáte aktuální verze služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity Hive v datové továrně](../transform-data-using-hadoop-hive.md).

Aktivity HDInsight Hive v datové továrně [kanálu](data-factory-create-pipelines.md) provádí dotazy Hive na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivit transformace dat](data-factory-data-transformation-activities.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a proveďte kurz: [sestavit svůj první kanál dat](data-factory-build-your-first-pipeline.md) před přečtení tohoto článku. 

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
## <a name="syntax-details"></a>Syntaxe podrobnosti
| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, co se používá aktivitu pro |Ne |
| type |HDinsightHive |Ano |
| vstupy |Vstupy spotřebovávané aktivitou Hive |Ne |
| výstupy |Výstupy produkované aktivitou Hive |Ano |
| linkedServiceName |Referenční dokumentace ke clusteru HDInsight registrován jako propojené služby v datové továrně |Ano |
| skript |Zadejte vloženého skriptu Hive |Ne |
| cestu ke skriptu |Uložení skriptu Hive v Azure blob storage a zadejte cestu k souboru. Pomocí vlastnosti 'skript' nebo 'scriptPath'. Obě nelze použít společně. Název souboru je malá a velká písmena. |Ne |
| definuje |Zadejte parametry dvojic klíč/hodnota pro odkazování v rámci skriptu Hive pomocí 'hiveconf. |Ne |

## <a name="example"></a>Příklad:
Zvažte příklad herní protokolů analýzy, kam chcete identifikovat čas strávený uživatelé hraní her spuštění ve vaší společnosti. 

Následující protokol je ukázkový herní protokol, který je čárkou (`,`) oddělený a obsahuje následující pole – ProfileID, SessionStart, doba trvání, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript podregistru** zpracovat tato data:

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

Spuštění tohoto skriptu Hive v kanálu pro vytváření dat, musíte udělat následující

1. Vytvoření propojené služby k registraci [vlastní HDInsight výpočetní cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurovat [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Umožňuje volat tuto propojenou službu "HDInsightLinkedService".
2. Vytvoření [propojená služba](data-factory-azure-blob-connector.md) ke konfiguraci připojení k hostování dat úložiště objektů Blob v Azure. Umožňuje volání této propojené služby "StorageLinkedService"
3. Vytvoření [datové sady](data-factory-create-datasets.md) odkazující na vstupní a výstupní data. Umožňuje volání vstupní datovou sadu "HiveSampleIn" a výstupní datovou sadu "HiveSampleOut"
4. Kopírovat dotaz Hive jako soubor do úložiště objektů Blob Azure nakonfigurovali v kroku #2. Pokud je úložiště pro hostování dat jiný než hostování tento soubor dotazu, vytvořte samostatné propojenou službu úložiště Azure a na ni odkazuje v rámci aktivity. Použití **scriptPath** zadat cestu k souboru dotazu hive a **scriptLinkedService** k určení úložiště Azure, která obsahuje soubor skriptu. 
   
   > [!NOTE]
   > Můžete zadat také vloženého skriptu Hive v definici aktivity pomocí **skriptu** vlastnost. Tento přístup není doporučujeme jako všechny speciální znaky ve skriptu v rámci je nutné dokumentu JSON předcházet a může způsobit problémy ladění. Osvědčeným postupem je postupujte podle kroku #4.
   > 
   > 
5. Vytvoření kanálu s aktivita HDInsightHive. Aktivity procesy nebo transformací data.

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
6. Nasaďte kanál. V tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. 
7. Monitorování pomocí zobrazení monitorování a správu objekt pro vytváření dat kanál. V tématu [monitorování a Správa kanálů služby Data Factory](data-factory-monitor-manage-pipelines.md) článku. 

## <a name="specifying-parameters-for-a-hive-script"></a>Zadání parametrů pro skript Hive
V tomto příkladu herní protokoly jsou denně požity do Azure Blob Storage a jsou uložené ve složce rozdělený do oddílů pomocí data a času. Chcete Parametrizace skriptu Hive a předat vstupní složky dynamicky za běhu a také vytvořit několik výstup rozdělený do oddílů pomocí data a času.

Chcete-li použít parametrizované skriptu Hive, postupujte takto

* Zadejte parametry v **definuje**.

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
* Ve skriptu Hive, najdete pomocí parametru **${hiveconf:parameterName}**. 
  
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
* [Pig aktivity](data-factory-pig-activity.md)
* [Činnost MapReduce](data-factory-map-reduce.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

