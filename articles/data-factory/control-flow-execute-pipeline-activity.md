---
title: Spuštění aktivity kanálu v Azure Data Factory
description: Zjistěte, jak můžete použít aktivitu spuštění kanálu k vyvolání jednoho kanálu datové továrny z jiného kanálu datové továrny.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 26dd7c4f33360030b13ddbfc1516396436724c40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75440443"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Spuštění aktivity kanálu v Azure Data Factory
Aktivita spuštění kanálu umožňuje kanálu služby Data Factory volat jiný kanál.

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "MyPipeline",
    "properties": {
        "activities": [
            {
                "name": "ExecutePipelineActivity",
                "type": "ExecutePipeline",
                "typeProperties": {
                    "parameters": {                        
                        "mySourceDatasetFolderPath": {
                            "value": "@pipeline().parameters.mySourceDatasetFolderPath",
                            "type": "Expression"
                        }
                    },
                    "pipeline": {
                        "referenceName": "<InvokedPipelineName>",
                        "type": "PipelineReference"
                    },
                    "waitOnCompletion": true
                 }
            }
        ],
        "parameters": [
            {
                "mySourceDatasetFolderPath": {
                    "type": "String"
                }
            }
        ]
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity kanálu spuštění. | Řetězec | Ano
type | Musí být nastavena na: **ExecutePipeline**. | Řetězec | Ano
Potrubí | Odkaz na závislý kanál, který tento kanál vyvolá. Referenční objekt kanálu má dvě vlastnosti: **referenceName** a **type**. Vlastnost referencename určuje název referenčního kanálu. Vlastnost type musí být nastavena na PipelineReference. | Odkaz na kanál | Ano
parameters | Parametry, které mají být předány vyvolaný kanál | Objekt JSON, který mapuje názvy parametrů na hodnoty argumentů | Ne
waitOnCompletion | Definuje, zda spuštění aktivity čeká na dokončení spuštění závislého kanálu. Výchozí hodnota je false. | Logická hodnota | Ne

## <a name="sample"></a>Ukázka
Tento scénář má dva kanály:

- **Hlavní kanál** – Tento kanál má jednu aktivitu spustit kanálu, která volá vyvolaný kanál. Hlavní kanál má dva `masterSourceBlobContainer`parametry: , `masterSinkBlobContainer`.
- **Vyvolaný kanál** – tento kanál má jednu aktivitu copy, která kopíruje data ze zdroje objektů Blob Azure do jímky objektů Blob Azure. Vyvolaný kanál má `sourceBlobContainer` `sinkBlobContainer`dva parametry: , .

### <a name="master-pipeline-definition"></a>Definice hlavního kanálu

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ExecutePipeline",
        "typeProperties": {
          "pipeline": {
            "referenceName": "invokedPipeline",
            "type": "PipelineReference"
          },
          "parameters": {
            "sourceBlobContainer": {
              "value": "@pipeline().parameters.masterSourceBlobContainer",
              "type": "Expression"
            },
            "sinkBlobContainer": {
              "value": "@pipeline().parameters.masterSinkBlobContainer",
              "type": "Expression"
            }
          },
          "waitOnCompletion": true
        },
        "name": "MyExecutePipelineActivity"
      }
    ],
    "parameters": {
      "masterSourceBlobContainer": {
        "type": "String"
      },
      "masterSinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

### <a name="invoked-pipeline-definition"></a>Vyvolání definice kanálu

```json
{
  "name": "invokedPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "SourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```

**Propojená služba**

```json
{
    "name": "BlobStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=*****;AccountKey=*****"
    }
  }
}
```

**Zdrojová datová sada**
```json
{
    "name": "SourceBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sourceBlobContainer",
        "type": "Expression"
      },
      "fileName": "salesforce.txt"
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

**Datová sada jímky**
```json
{
    "name": "sinkBlobDataset",
    "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@pipeline().parameters.sinkBlobContainer",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "BlobStorageLinkedService",
      "type": "LinkedServiceReference"
    }
  }
}
```

### <a name="running-the-pipeline"></a>Spuštění kanálu

Chcete-li spustit hlavní kanál v tomto příkladu, jsou předány následující hodnoty pro parametry masterSourceBlobContainer a masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Hlavní kanál předá tyto hodnoty do vyvolaný kanál, jak je znázorněno v následujícím příkladu: 

```json
{
    "type": "ExecutePipeline",
    "typeProperties": {
      "pipeline": {
        "referenceName": "invokedPipeline",
        "type": "PipelineReference"
      },
      "parameters": {
        "sourceBlobContainer": {
          "value": "@pipeline().parameters.masterSourceBlobContainer",
          "type": "Expression"
        },
        "sinkBlobContainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory: 

- [Aktivita For Each](control-flow-for-each-activity.md)
- [Získat aktivitu metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
