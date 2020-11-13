---
title: Transformace dat pomocí aktivity podregistru – Azure
description: Přečtěte si, jak můžete použít aktivitu podregistru v Azure Data Factory v1 ke spouštění dotazů na podregistr na vyžádání nebo vlastní cluster HDInsight.
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
ms.openlocfilehash: 0271029814071b3a692209d3a2015cfdbe5fa941
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616769"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformuje data pomocí aktivity podregistru v Azure Data Factory 
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity podregistru v Data Factory](../transform-data-using-hadoop-hive.md).

Aktivita podregistru HDInsight v Data Factoryovém [kanálu](data-factory-create-pipelines.md) spouští dotazy na podregistry ve [vašem](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight založeném na systému Windows/Linux nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](data-factory-data-transformation-activities.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud se Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a udělejte si kurz: [Vytvoření prvního datového kanálu](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

## <a name="syntax"></a>Syntax

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
| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| name |Název aktivity |Ano |
| description |Text popisující, k čemu se aktivita používá |Ne |
| typ |HDinsightHive |Ano |
| vztahují |Vstupy spotřebované aktivitou podregistru |Ne |
| činnosti |Výstupy vytvářené aktivitou podregistru |Ano |
| linkedServiceName |Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory |Ano |
| script |Zadat vložený skript podregistru |Ne |
| scriptPath |Uložte skript podregistru do úložiště objektů BLOB v Azure a zadejte cestu k souboru. Použijte vlastnost Script nebo scriptPath. Nelze použít současně. V názvu souboru se rozlišují malá a velká písmena. |Ne |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu pro podregistr pomocí příkazu ' hiveconf '. |Ne |

## <a name="example"></a>Příklad
Podívejme se na příklad analýzy herních protokolů, u kterých chcete zjistit čas strávený uživateli, kteří hry zahájili při hraní her, které vaše společnost spustila. 

Následující protokol je ukázkový protokol hry, který je oddělen čárkou ( `,` ) a obsahuje následující pole – ProfileID, SessionStart, Duration, SrcIPAddress a Gametype.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript podregistru** pro zpracování těchto dat:

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

Chcete-li spustit tento skript podregistru v kanálu Data Factory, je nutné provést následující akci:

1. Vytvořte propojenou službu pro registraci [vlastního výpočetního clusteru HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurujte [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Pojďme zavolat tuto propojenou službu "HDInsightLinkedService".
2. Vytvořte [propojenou službu](data-factory-azure-blob-connector.md) , abyste nakonfigurovali připojení k úložišti objektů BLOB v Azure, která hostují data. Pojďme zavolat tuto propojenou službu "StorageLinkedService"
3. Vytvořte datové [sady](data-factory-create-datasets.md) ukazující na vstupní a výstupní data. Pojďme zavolat vstupní datovou sadu "HiveSampleIn" a výstupní datovou sadu "HiveSampleOut".
4. Zkopírujte dotaz na podregistr jako soubor do Azure Blob Storage nakonfigurovaný v kroku #2. Pokud se úložiště pro hostování dat liší od toho, který hostuje tento soubor dotazů, vytvořte samostatnou propojenou službu Azure Storage a v aktivitě na ni uveďte. Pomocí **ScriptPath** zadejte cestu k souboru dotazů na podregistr a **ScriptLinkedService** určete úložiště Azure, které obsahuje soubor skriptu. 
   
   > [!NOTE]
   > Do definice aktivity můžete také zadat vložený skript podregistru pomocí vlastnosti **skriptu** . Tento postup nedoporučujeme, protože všechny speciální znaky ve skriptu v dokumentu JSON musí být uvozené řídicím znakem a můžou způsobit problémy s laděním. Osvědčeným postupem je postupovat podle kroků #4.
   > 
   > 
5. Vytvořte kanál s aktivitou HDInsightHive. Aktivita zpracovává nebo transformuje data.

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
6. Nasaďte kanál. Podrobnosti najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . 
7. Monitorujte kanál pomocí zobrazení pro monitorování a správu objektu pro vytváření dat. Podrobnosti najdete v článku [monitorování a Správa kanálů Data Factory](data-factory-monitor-manage-pipelines.md) . 

## <a name="specifying-parameters-for-a-hive-script"></a>Určení parametrů pro skript podregistru
V tomto příkladu jsou protokoly her ingestované každý den do Azure Blob Storage a jsou uložené ve složce s oddíly s datem a časem. Chcete parametrizovat skript podregistru a dynamicky předat umístění vstupní složky během běhu a také vytvořit výstup rozdělený do oddílů s datem a časem.

Pokud chcete použít parametrizovaný skript podregistru, udělejte toto:

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
* V rámci skriptu pro podregistr, použijte parametr **$ {hiveconf: ParameterName}**. 
  
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
* [Aktivita prasete](data-factory-pig-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

