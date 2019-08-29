---
title: Spustit aktivitu kanálu v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak můžete pomocí aktivity spustit kanál vyvolat jeden Data Factory kanál z jiného kanálu Data Factory.
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
ms.openlocfilehash: 1611f740f6b55ecf9f15ecd234d63b5e95baeba1
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70141710"
---
# <a name="execute-pipeline-activity-in-azure-data-factory"></a>Spustit aktivitu kanálu v Azure Data Factory
Aktivita spustit kanál umožňuje Data Factory kanálu vyvolat jiný kanál.

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

Vlastnost | Popis | Povolené hodnoty | Požadováno
-------- | ----------- | -------------- | --------
name | Název aktivity spustit kanál | Řetězec | Ano
type | Musí být nastavené na: **ExecutePipeline**. | Řetězec | Ano
kanál | Odkaz na kanál na závislý kanál, který tento kanál vyvolá Objekt odkazu na kanál má dva vlastnosti: **odkaz** a **typ**. Vlastnost FileReference Určuje název kanálu odkazu. Vlastnost Type musí být nastavená na PipelineReference. | PipelineReference | Ano
parameters | Parametry, které se mají předat vyvolanému kanálu | Objekt JSON, který mapuje názvy parametrů na hodnoty argumentu | Ne
waitOnCompletion | Definuje, zda provádění aktivit čeká na dokončení zpracování závislého kanálu. Výchozí hodnota je false. | Logická hodnota | Ne

## <a name="sample"></a>Ukázka
Tento scénář má dva kanály:

- **Hlavní kanál** – tento kanál má jednu aktivitu spuštění kanálu, která volá vyvolaný kanál. Hlavní kanál používá dva parametry: `masterSourceBlobContainer`,. `masterSinkBlobContainer`
- **Vyvolaný kanál** – tento kanál má jednu aktivitu kopírování, která kopíruje data ze zdroje objektů blob Azure do jímky objektů BLOB v Azure. Vyvolaný kanál používá dva parametry: `sourceBlobContainer`, `sinkBlobContainer`.

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

### <a name="invoked-pipeline-definition"></a>Vyvolaná definice kanálu

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

Pro spuštění hlavního kanálu v tomto příkladu jsou předány následující hodnoty pro parametry masterSourceBlobContainer a masterSinkBlobContainer: 

```json
{
  "masterSourceBlobContainer": "executetest",
  "masterSinkBlobContainer": "executesink"
}
```

Hlavní kanál přepošle tyto hodnoty na vyvolaný kanál, jak je znázorněno v následujícím příkladu: 

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
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
