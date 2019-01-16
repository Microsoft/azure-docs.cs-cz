---
title: Transformovat data pomocí Pig aktivity ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak můžete pomocí aktivita Pig ve službě Azure data factory ke spouštění skriptů Pig na vyžádání/svůj vlastní clusteru služby HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: fe1ef8c141c4a4daa443f800181f8e6e3199d0cc
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54331295"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformovat data pomocí Pig aktivity ve službě Azure Data Factory
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
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformovat data pomocí Pig aktivity ve službě Data Factory](../transform-data-using-hadoop-pig.md).


Aktivita HDInsight Pig ve službě Data Factory [kanálu](data-factory-create-pipelines.md) Pig dotazy se spustí na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivity transformace dat](data-factory-data-transformation-activities.md) článek, který nabízí obecný přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud do služby Azure Data Factory začínáte, přečtěte si [Úvod do služby Azure Data Factory](data-factory-introduction.md) a udělat kurz: [Vytvoření prvního kanálu dat](data-factory-build-your-first-pipeline.md) před čtením tohoto článku. 

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

## <a name="syntax-details"></a>Podrobnosti o syntaxi
| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, k čemu aktivita slouží |Ne |
| type |HDinsightPig |Ano |
| vstupy |Jeden nebo více vstupů spotřebovávané aktivitou Pig |Ne |
| výstupy |Jeden nebo více výstupů produkované aktivitou Pig |Ano |
| linkedServiceName |Odkaz na clusteru HDInsight zaregistrovaný jako propojenou službu ve službě Data Factory |Ano |
| skript |Zadejte vložený skript Pig |Ne |
| Cesta ke skriptu |Skript Pig Store ve službě Azure blob storage a zadejte cestu k souboru. Vlastnost 'script' nebo "scriptPath". Obě nelze použít společně. Název souboru je velká a malá písmena. |Ne |
| definuje |Zadejte parametry jako páry klíč/hodnota pro odkazování v rámci skript Pig |Ne |

## <a name="example"></a>Příklad:
Zvažte příklad herních protokolů analytics, ve které chcete určit čas strávený hráči, hraní her spustili ve vaší společnosti.

Následující ukázkový herní protokol je soubor oddělených čárkami (,). Obsahuje následující pole – ID profilu, SessionStart, doba trvání, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Skript Pig** ke zpracování těchto dat:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Chcete-li spustit tento skript Pig v kanálech Data Factory, proveďte následující kroky:

1. Vytvoření propojené služby pro registraci [vlastní HDInsight výpočetní cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurovat [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Pojmenujme tuto propojenou službu **HDInsightLinkedService**.
2. Vytvoření [propojená služba](data-factory-azure-blob-connector.md) ke konfiguraci připojení k hostování dat úložiště objektů Blob v Azure. Pojmenujme tuto propojenou službu **StorageLinkedService**.
3. Vytvoření [datových sad](data-factory-create-datasets.md) odkazující na vstupní a výstupní data. Pojmenujme vstupní datová sada **PigSampleIn** a výstupní datovou sadu **PigSampleOut**.
4. Zkopírujte tento dotaz Pig v souboru nakonfigurovali v kroku #2 Azure Blob Storage. Pokud Azure storage, který je hostitelem dat se liší od jednoho, který je hostitelem soubor dotazu, vytvořte samostatné služby propojené služby Azure Storage. Odkazují na propojenou službu v konfiguraci aktivity. Použijte ** scriptPath ** pro zadání cesty k souboru skriptu pig a **scriptLinkedService**. 
   
   > [!NOTE]
   > Můžete také poskytnout vložený skript Pig v definici aktivity pomocí **skript** vlastnost. Ale tento přístup nedoporučuje jako všechny speciální znaky ve skriptu musí být uvozena a může způsobit problémy ladění. Osvědčeným postupem je postupujte podle kroku #4.
   >
   >
5. Vytvoření kanálu s aktivitou HDInsightPig. Tato aktivita zpracovává vstupní data pomocí Pig skriptu na clusteru HDInsight.

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
6. Nasaďte kanál. Zobrazit [vytváření kanálů](data-factory-create-pipelines.md) , kde najdete podrobnosti. 
7. Monitorování kanálu pomocí zobrazení dat objekt pro vytváření monitorování a správu. Zobrazit [monitorování a Správa kanálů Data Factory](data-factory-monitor-manage-pipelines.md) , kde najdete podrobnosti.

## <a name="specifying-parameters-for-a-pig-script"></a>Zadání parametrů pro skript Pig
Podívejte se na následující příklad: herních protokolů jsou možné denně zpracovat do služby Azure Blob Storage a uloží do složky dělené podle data a času. Chcete parametrizovat skript Pig a předat vstupní složky dynamicky za běhu a také generovat výstup rozdělený do oddílů pomocí data a času.

Použití parametrizovaného skript Pig, postupujte takto:

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
* V skript Pig odkazovat na parametry pomocí "**$parameterName**" jak je znázorněno v následujícím příkladu:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Viz také
* [Aktivita hivu](data-factory-hive-activity.md)
* [Aktivita MapReduce](data-factory-map-reduce.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
