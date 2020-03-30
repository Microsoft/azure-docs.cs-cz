---
title: Transformace dat pomocí aktivity prasete v Azure Data Factory
description: Zjistěte, jak můžete pomocí aktivity prasete v datové továrně Azure spouštět skripty Pig na vyžádání nebo ve vlastním clusteru HDInsight.
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
ms.openlocfilehash: 5b8e7201a6239ef1fe83fb89d4b361995e305bbf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74703193"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí aktivity prasete v Azure Data Factory
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma transformace dat pomocí aktivity Pig v datové továrně](../transform-data-using-hadoop-pig.md).


Aktivita HDInsight Pig v [datové továrně](data-factory-create-pipelines.md) provádí dotazy Pig na [vašem vlastním](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo na [vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) založeném hdinsight clusteru windows/linuxu. Tento článek vychází z článku [aktivit transformace dat,](data-factory-data-transformation-activities.md) který představuje obecný přehled transformace dat a podporovaných transformačních aktivit.

> [!NOTE] 
> Pokud jste s Azure Data Factory teprve noví, přečtěte si [úvodní článek azure data factory](data-factory-introduction.md) a proveďte kurz: [Sestavte si první datový kanál](data-factory-build-your-first-pipeline.md) před přečtením tohoto článku. 

## <a name="syntax"></a>Syntaxe

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

| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, k čemu se aktivita používá |Ne |
| type |HDinsightPig |Ano |
| Vstupy |Jeden nebo více vstupů spotřebovaných aktivitou prasete |Ne |
| Výstupy |Jeden nebo více výstupů produkovaných aktivitou prasete |Ano |
| linkedServiceName |Odkaz na cluster HDInsight registrovaný jako propojená služba v Datové továrně |Ano |
| . |Zadejte vložku skriptu Prase |Ne |
| scriptPath |Uložte skript Pig do úložiště objektů blob Azure a zadejte cestu k souboru. Použijte vlastnost 'script' nebo 'scriptPath'. Obojí nelze použít společně. Název souboru rozlišuje malá a velká písmena. |Ne |
| Definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování ve skriptu Pig |Ne |

## <a name="example"></a>Příklad
Podívejme se na příklad analýzy herních protokolů, kde chcete identifikovat čas strávený hráči, kteří hrají hry spuštěné vaší společností.

Následující ukázkový herní protokol je oddělený soubor čárkou (,). Obsahuje následující pole – ProfileID, SessionStart, Duration, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Pig skript** pro zpracování těchto údajů:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Chcete-li spustit tento skript Pig v kanálu data factory, postupujte takto:

1. Vytvořte propojenou službu pro [registraci vlastního výpočetního clusteru HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurujte [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Nazvěme tuto propojenou službu **HDInsightLinkedService**.
2. Vytvořte [propojenou službu](data-factory-azure-blob-connector.md) pro konfiguraci připojení k úložišti objektů Blob Azure, které jsou hostující data. Nazvěme tuto propojenou **službu StorageLinkedService**.
3. Vytvořte [datové sady](data-factory-create-datasets.md) směřující na vstupní a výstupní data. Pojďme volat vstupní datovou sadu **PigSampleIn** a výstupní datovou sadu **PigSampleOut**.
4. Zkopírujte dotaz Pig do souboru Azure Blob Storage nakonfigurované v kroku #2. Pokud se úložiště Azure, které jsou hostiteli dat, liší od úložiště, které hostuje soubor dotazu, vytvořte samostatnou propojenou službu Azure Storage. Odkazovat na propojenou službu v konfiguraci aktivity. Pomocí **aplikace scriptPath** určete cestu k souboru prasečího skriptu a **skriptuLinkedService**. 
   
   > [!NOTE]
   > Můžete také zadat Pig skript vřádku v definici aktivity pomocí vlastnosti **script.** Tento přístup však nedoporučujeme, protože všechny speciální znaky ve skriptu musí být uvozeny a mohou způsobit problémy s laděním. Osvědčeným postupem je postupovat podle #4 kroků.
   >
   >
5. Vytvořte kanál s aktivitou HDInsightPig. Tato aktivita zpracovává vstupní data spuštěním skriptu Pig v clusteru HDInsight.

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
6. Nasaďte potrubí. Podrobnosti [najdete v článku Vytváření kanálů.](data-factory-create-pipelines.md) 
7. Sledujte kanál pomocí zobrazení monitorování a správy datové továrny. Podrobnosti najdete v článku [Monitorování a správa kanálů datové továrny.](data-factory-monitor-manage-pipelines.md)

## <a name="specifying-parameters-for-a-pig-script"></a>Určení parametrů pro pig skript
Vezměme si následující příklad: herní protokoly jsou ingestovány denně do úložiště objektů blob Azure a uloženy ve složce rozdělené na základě data a času. Chcete parametrizovat pig skript a předat umístění vstupní složky dynamicky za běhu a také vytvořit výstup rozdělený s datem a časem.

Chcete-li použít parametrizovaný skript Pig, postupujte takto:

* Definujte parametry v **definicích**.

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
* Ve skriptu prasete viz parametry pomocí "**$parameterName**", jak je znázorněno v následujícím příkladu:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Viz také
* [Aktivita úlu](data-factory-hive-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Aktivita streamování hadoopu](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
