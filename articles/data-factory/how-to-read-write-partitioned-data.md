---
title: Tom, jak číst nebo zapisovat data ve službě Azure Data Factory rozdělit na oddíly | Dokumentace Microsoftu
description: Zjistěte, jak ke čtení nebo zápis dělených dat ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: shlo
ms.openlocfilehash: 24464d110b00508cfb3fde4ab1a050773511e255
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091045"
---
# <a name="how-to-read-or-write-partitioned-data-in-azure-data-factory"></a>Tom, jak číst nebo zapisovat data ve službě Azure Data Factory rozdělit na oddíly

Ve službě Azure Data Factory verze 1, může čtení nebo zápis dělených dat s použitím **SliceStart**, **SliceEnd**, **WindowStart**, a **WindowEnd** systémové proměnné. V aktuální verzi Data Factory můžete toto chování dosáhnout pomocí parametr kanálu a počáteční aktivační události nebo plánovaný čas jako hodnotu parametru. 

## <a name="use-a-pipeline-parameter"></a>Parametr kanálu 

Ve službě Data Factory verze 1, můžete použít **partitionedBy** vlastnost a **SliceStart** systémová proměnná, jak je znázorněno v následujícím příkladu: 

```json
"folderPath": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/{Year}/{Month}/{Day}/",
"partitionedBy": [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "%M" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "%d" } }
],
```

Další informace o **partitonedBy** vlastnost, naleznete v tématu [kopírování dat do nebo z úložiště objektů Blob v Azure pomocí Azure Data Factory](v1/data-factory-azure-blob-connector.md#dataset-properties). 

K dosažení tohoto chování v aktuální verzi Data Factory: 

1. Definování *kanálu parametr* typu **řetězec**. V následujícím příkladu je název parametru kanálu **windowStartTime**. 
2. Nastavte **folderPath** v definici datové sady odkazují na hodnoty parametrů kanálu. 
3. Skutečná hodnota pro parametr předáte při volání kanálu na vyžádání. Můžete také předat trigger's počáteční nebo naplánovaném čase dynamicky za běhu. 

```json
"folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
},
```

## <a name="pass-in-a-value-from-a-trigger"></a>Předejte hodnotu z aktivační události

Následující aktivační událost pro přeskakující okno Definice aktivační události, čas zahájení okna aktivační událost se předá jako hodnotu pro parametr kanálu **windowStartTime**: 

```json
{
    "name": "MyTrigger",
    "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": "1",
            "startTime": "2018-05-15T00:00:00Z",
            "delay": "00:10:00",
            "maxConcurrency": 10
        },
        "pipeline": {
            "pipelineReference": {
                "type": "PipelineReference",
                "referenceName": "MyPipeline"
            },
            "parameters": {
                "windowStartTime": "@trigger().outputs.windowStartTime"
            }
        }
    }
}
```

## <a name="example"></a>Příklad:

Tady je ukázková definice datové sady:

```json
{
  "name": "SampleBlobDataset",
  "type": "AzureBlob",
  "typeProperties": {
    "folderPath": {
      "value": "adfcustomerprofilingsample/logs/marketingcampaigneffectiveness/@{formatDateTime(pipeline().parameters.windowStartTime, 'yyyy/MM/dd')}/",
      "type": "Expression"
    },
    "format": {
      "type": "TextFormat",
      "columnDelimiter": ","
    }
  },
  "structure": [
    { "name": "ProfileID", "type": "String" },
    { "name": "SessionStart", "type": "String" },
    { "name": "Duration", "type": "Int32" },
    { "name": "State", "type": "String" },
    { "name": "SrcIPAddress", "type": "String" },
    { "name": "GameType", "type": "String" },
    { "name": "Multiplayer", "type": "String" },
    { "name": "EndRank", "type": "String" },
    { "name": "WeaponsUsed", "type": "Int32" },
    { "name": "UsersInteractedWith", "type": "String" },
    { "name": "Impressions", "type": "String" }
  ],
  "linkedServiceName": {
    "referenceName": "churnStorageLinkedService",
    "type": "LinkedServiceReference"
  }
}
```

Definice kanálu: 

```json
{
    "properties": {
        "activities": [{
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": {
                    "value": "@concat(pipeline().parameters.blobContainer, '/scripts/', pipeline().parameters.partitionHiveScriptFile)",
                    "type": "Expression"
                },
                "scriptLinkedService": {
                    "referenceName": "churnStorageLinkedService",
                    "type": "LinkedServiceReference"
                },
                "defines": {
                    "RAWINPUT": {
                        "value": "@concat('wasb://', pipeline().parameters.blobContainer, '@', pipeline().parameters.blobStorageAccount, '.blob.core.windows.net/logs/', pipeline().parameters.inputRawLogsFolder, '/')",
                        "type": "Expression"
                    },
                    "Year": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'yyyy')",
                        "type": "Expression"
                    },
                    "Month": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'MM')",
                        "type": "Expression"
                    },
                    "Day": {
                        "value": "@formatDateTime(pipeline().parameters.windowStartTime, 'dd')",
                        "type": "Expression"
                    }
                }
            },
            "linkedServiceName": {
                "referenceName": "HdiLinkedService",
                "type": "LinkedServiceReference"
            },
            "name": "HivePartitionGameLogs"
        }],
        "parameters": {
            "windowStartTime": {
                "type": "String"
            },
            "blobStorageAccount": {
                "type": "String"
            },
            "blobContainer": {
                "type": "String"
            },
            "inputRawLogsFolder": {
                "type": "String"
            }
        }
    }
}
```

## <a name="next-steps"></a>Další postup

Kompletní návod, jak vytvořit datovou továrnu s kanálem, naleznete v tématu [rychlý start: vytvoření datové továrny](quickstart-create-data-factory-powershell.md). 

