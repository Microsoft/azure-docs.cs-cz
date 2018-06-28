---
title: Provádění aktivity kanál v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak můžete pomocí kanálu aktivity spustit vyvolat jeden objekt pro vytváření dat kanál z jiného objektu pro vytváření dat kanál.
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
ms.date: 01/10/2018
ms.author: shlo
ms.openlocfilehash: 2aa25004fb9c2e914cd8c669095953e174686197
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "37051759"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Provádění aktivity kanál v Azure Data Factory
Aktivita spustit kanál umožňuje kanálu pro vytváření dat má být vyvolán jiné kanálu.

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
jméno | Název aktivity kanálu execute. | Řetězec | Ano
type | Musí být nastavena na: **ExecutePipeline**. | Řetězec | Ano
kanál | Kanál odkaz na závislé kanálu, který volá tento kanál. Referenční objekt kanálu má dvě vlastnosti: **Název_odkazu** a **typu**. Vlastnost Název_odkazu Určuje název kanálu odkaz. Vlastnost typu musí být nastavena na PipelineReference. | PipelineReference | Ano
parameters | Parametry, které mají být předány vyvolaná kanálu | Objekt JSON, která mapuje názvy parametrů hodnot argumentů | Ne
waitOnCompletion | Definuje, zda provedení aktivity čeká na provedení závislé kanálu ukončíte. | Výchozí hodnota je false. | Logická hodnota | Ne

## <a name="sample"></a>Ukázka
Tento scénář má dva kanály:

- **Hlavní kanálu** -tento kanál obsahuje jednu aktivitu spustit kanál, který volá vyvolaná kanálu. Hlavní kanálu přebírá dva parametry: `masterSourceBlobContainer`, `masterSinkBlobContainer`.
- **Vyvolaná kanálu** -tento kanál obsahuje jednu aktivitu kopírování, který kopíruje data z zdroje Azure Blob do Azure Blob podřízený. Vyvolaná kanálu přebírá dva parametry: `sourceBlobContainer`, `sinkBlobContainer`.

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
            "sinkBlobCountainer": {
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

### <a name="invoked-pipeline-definition"></a>Definice vyvolaná kanálu

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
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=*****",
        "type": "SecureString"
      }
    }
  }
}
```

**Zdroj datové sady**
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

**Jímky datové sady**
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

V tomto příkladu spustit hlavního kanálu, nejsou následující hodnoty pro parametry masterSourceBlobContainer a masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Hlavní kanálu předá tyto hodnoty na vyvolanou kanál, jak je znázorněno v následujícím příkladu: 

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
        "sinkBlobCountainer": {
          "value": "@pipeline().parameters.masterSinkBlobContainer",
          "type": "Expression"
        }
      },

      ....
}

```
## <a name="next-steps"></a>Další postup
Najdete v části Další aktivity toku řízení podporovaných službou Data Factory: 

- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webové aktivity](control-flow-web-activity.md)
