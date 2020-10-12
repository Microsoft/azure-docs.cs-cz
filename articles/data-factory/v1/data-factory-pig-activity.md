---
title: Transformace dat pomocí aktivity prasete v Azure Data Factory
description: Zjistěte, jak můžete pomocí aktivity prasete v Azure Data Factory v1 spouštět skripty pro vepřové prostředí na vyžádání nebo vlastním clusteru HDInsight.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9e20b7a92d054a6664a00064fa7263b1150c3df9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282574"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity prasete v Azure Data Factory
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity prasete v Data Factory](../transform-data-using-hadoop-pig.md).


Aktivita prasete v HDInsight v [kanálu](data-factory-create-pipelines.md) Data Factory spouští dotazy na prasaty na [vašem](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) clusteru HDInsight založeném na systému Windows/Linux nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Tento článek se sestavuje na článku [aktivity transformace dat](data-factory-data-transformation-activities.md) , který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud se Azure Data Factory teprve začínáte, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a udělejte si kurz: [Vytvoření prvního datového kanálu](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

## <a name="syntax"></a>Syntax

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
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
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Podrobnosti syntaxe

| Vlastnost | Popis | Povinné |
| --- | --- | --- |
| name |Název aktivity |Yes |
| Popis |Text popisující, k čemu se aktivita používá |No |
| typ |HDinsightPig |Yes |
| vztahují |Jeden nebo více vstupů spotřebovaných aktivitou prasete |No |
| činnosti |Jeden nebo více výstupů vyprodukovaných aktivitou prasete |Yes |
| linkedServiceName |Odkaz na cluster HDInsight registrovaný jako propojená služba v Data Factory |Yes |
| script |Zadat vložený skript prasete |No |
| scriptPath |Uložte skript prasete do úložiště objektů BLOB v Azure a zadejte cestu k souboru. Použijte vlastnost Script nebo scriptPath. Nelze použít současně. V názvu souboru se rozlišují malá a velká písmena. |No |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skriptu pro vepřové prostředí. |No |

## <a name="example"></a>Příklad
Podívejme se na příklad analýzy herních protokolů, u kterých chcete zjistit čas strávený hráči, které hrají hry spuštěné vaší společností.

Následující ukázkový protokol hry je čárkou oddělený soubor (,). Obsahuje následující pole – ProfileID, SessionStart, Duration, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript prasete** pro zpracování těchto dat:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Chcete-li spustit tento skript pro vepřový postup v kanálu Data Factory, proveďte následující kroky:

1. Vytvořte propojenou službu pro registraci [vlastního výpočetního clusteru HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurujte [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Pojďme zavolat tuto propojenou službu **HDInsightLinkedService**.
2. Vytvořte [propojenou službu](data-factory-azure-blob-connector.md) , abyste nakonfigurovali připojení k úložišti objektů BLOB v Azure, která hostují data. Pojďme zavolat tuto propojenou službu **StorageLinkedService**.
3. Vytvořte datové [sady](data-factory-create-datasets.md) ukazující na vstupní a výstupní data. Pojďme zavolat vstupní datovou sadu **PigSampleIn** a výstupní datovou sadu **PigSampleOut**.
4. Zkopírujte dotaz na prase do souboru, který je Blob Storage Azure nakonfigurovaný v kroku #2. Pokud úložiště Azure, které hostuje data, se liší od toho, který hostuje soubor dotazů, vytvořte samostatnou propojenou službu Azure Storage. Podívejte se na propojenou službu v konfiguraci aktivity. Pomocí **ScriptPath** zadejte cestu k souboru skriptu prasete a **scriptLinkedService**. 
   
   > [!NOTE]
   > Můžete také zadat vložený skript prasete do definice aktivity pomocí vlastnosti **skriptu** . Tento přístup však nedoporučujeme, protože všechny speciální znaky ve skriptu musí být uvozeny řídicím znakem a mohou způsobit problémy s laděním. Osvědčeným postupem je postupovat podle kroků #4.
   >
   >
5. Vytvořte kanál s aktivitou HDInsightPig. Tato aktivita zpracuje vstupní data spuštěním skriptu prasete v clusteru HDInsight.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Nasaďte kanál. Podrobnosti najdete v článku [vytvoření kanálů](data-factory-create-pipelines.md) . 
7. Monitorujte kanál pomocí zobrazení pro monitorování a správu objektu pro vytváření dat. Podrobnosti najdete v článku [monitorování a Správa kanálů Data Factory](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>Určení parametrů pro skript vepřového sádla
Vezměte v úvahu následující příklad: herní protokoly se každý den ingestují do služby Azure Blob Storage a ukládají se do složky na základě data a času. Chcete parametrizovat skript prasete a dynamicky předat umístění vstupní složky během běhu a také vytvořit rozdělený výstup s použitím data a času.

Chcete-li použít parametrizovaný skript prasete, postupujte následovně:

* Definujte parametry v **definuje**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* V rámci skriptu pro vepřové zobrazení použijte parametry pomocí příkazu '**$ParameterName**', jak je znázorněno v následujícím příkladu:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Viz také
* [Aktivita v podregistru](data-factory-hive-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Aktivita streamování Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
