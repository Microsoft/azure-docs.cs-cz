---
title: Transformace dat pomocí Pig aktivity v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak můžete pomocí aktivity Pig v objektu pro vytváření dat Azure ke spouštění skriptů Pig na na vyžádání nebo vaše vlastní cluster HDInsight.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 5af07a1a-2087-455e-a67b-a79841b4ada5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: d65851f73ca3cc0bc85caff39b3a322900b78843
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622512"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformace dat pomocí Pig aktivity v Azure Data Factory
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
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [transformace dat pomocí Pig aktivity v datové továrně verze 2](../transform-data-using-hadoop-pig.md).


Aktivita HDInsight Pig v datové továrně [kanálu](data-factory-create-pipelines.md) provádí Pig dotazy na [vlastní](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo [na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) clusteru HDInsight se systémem Windows nebo Linux. Tento článek vychází [aktivit transformace dat](data-factory-data-transformation-activities.md) článek, který poskytne Obecné přehled o transformaci dat a aktivity podporované transformace.

> [!NOTE] 
> Pokud jste do Azure Data Factory nové, přečtěte si [Úvod do Azure Data Factory](data-factory-introduction.md) a proveďte kurz: [sestavit svůj první kanál dat](data-factory-build-your-first-pipeline.md) před přečtení tohoto článku. 

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
## <a name="syntax-details"></a>Syntaxe podrobnosti
| Vlastnost | Popis | Požaduje se |
| --- | --- | --- |
| jméno |Název aktivity |Ano |
| description |Text popisující, co se používá aktivitu pro |Ne |
| type |HDinsightPig |Ano |
| vstupy |Jeden nebo více vstupů spotřebovávané aktivitou Pig |Ne |
| výstupy |Jeden nebo více výstupů produkované aktivitou Pig |Ano |
| linkedServiceName |Referenční dokumentace ke clusteru HDInsight registrován jako propojené služby v datové továrně |Ano |
| Skript |Zadejte vložený skript Pig |Ne |
| cestu ke skriptu |Uložte skript Pig v Azure blob storage a zadejte cestu k souboru. Pomocí vlastnosti 'skript' nebo 'scriptPath'. Obě nelze použít společně. Název souboru je malá a velká písmena. |Ne |
| definuje |Zadejte parametry dvojic klíč/hodnota pro odkazování v rámci skript Pig |Ne |

## <a name="example"></a>Příklad:
Zvažte příklad herní protokolů analýzy, kam chcete identifikovat čas strávený hráči, hraní her spuštění ve vaší společnosti.

Následující příklad herní protokolu je soubor oddělených čárkou (,). Obsahuje následující pole – ProfileID, SessionStart, doba trvání, SrcIPAddress a GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Vepřových skriptu** zpracovat tato data:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Spustit tento skript Pig v objektu pro vytváření dat kanál, proveďte následující kroky:

1. Vytvoření propojené služby k registraci [vlastní HDInsight výpočetní cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) nebo nakonfigurovat [výpočetní cluster HDInsight na vyžádání](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Umožňuje volání Tato propojená služba **HDInsightLinkedService**.
2. Vytvoření [propojená služba](data-factory-azure-blob-connector.md) ke konfiguraci připojení k hostování dat úložiště objektů Blob v Azure. Umožňuje volání Tato propojená služba **StorageLinkedService**.
3. Vytvoření [datové sady](data-factory-create-datasets.md) odkazující na vstupní a výstupní data. Umožňuje volání vstupní datové sady **PigSampleIn** a výstupní datovou sadu **PigSampleOut**.
4. Zkopírujte tento dotaz Pig v souboru Azure Blob Storage, nakonfigurovali v kroku #2. Pokud službu Azure storage, který je hostitelem dat se liší od ten, který je hostitelem soubor dotazů, vytvořte samostatné propojenou službu úložiště Azure. Naleznete propojené služby v konfiguraci aktivity. Použijte ** scriptPath ** zadat cestu k souboru skriptu pig a **scriptLinkedService**. 
   
   > [!NOTE]
   > Vložený skript Pig v definici aktivity můžete zadat taky pomocí **skriptu** vlastnost. Ale nedoporučujeme jako všechny speciální znaky v je nutné skript nutné uvést tento přístup a může způsobit problémy ladění. Osvědčeným postupem je postupujte podle kroku #4.
   > 
   > 
5. Vytvoření kanálu s aktivitou HDInsightPig. Tato aktivita zpracovává vstupní data tak, že spustíte skript Pig na clusteru HDInsight.

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
6. Nasaďte kanál. V tématu [vytváření kanálů](data-factory-create-pipelines.md) článku. 
7. Monitorování pomocí zobrazení monitorování a správu objekt pro vytváření dat kanál. V tématu [monitorování a Správa kanálů služby Data Factory](data-factory-monitor-manage-pipelines.md) článku.

## <a name="specifying-parameters-for-a-pig-script"></a>Zadání parametrů pro skript Pig
Podívejte se na následující příklad: herní protokoly jsou požity denně do Azure Blob Storage a uložena ve složce oddílů na základě datum a čas. Chcete Parametrizace skript Pig a předat vstupní složky dynamicky za běhu a také vytvořit několik výstup rozdělený do oddílů pomocí data a času.

Pokud chcete používat parametrizované skript Pig, postupujte takto:

* Zadejte parametry v **definuje**.

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
* Ve skriptu Pig odkazování na parametry pomocí '**$parameterName**, jak je znázorněno v následujícím příkladu:

    ```  
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);    
    GroupProfile = Group PigSampleIn all;        
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);        
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```
## <a name="see-also"></a>Viz také
* [Aktivita Hive](data-factory-hive-activity.md)
* [Činnost MapReduce](data-factory-map-reduce.md)
* [Streamované aktivitě Hadoop](data-factory-hadoop-streaming-activity.md)
* [Vyvolání programů Spark](data-factory-spark.md)
* [Vyvolání skriptů jazyka R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

