---
title: Aktivita ForEach ve službě Azure Data Factory | Dokumentace Microsoftu
description: Pro každou aktivitu definuje ve vašem kanálu opakovaný tok řízení. Používá se pro iterace nad kolekcí a spustit určené aktivity.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: shlo
ms.openlocfilehash: 68cdabd8d6e5921eabaa200169c0523352461733
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856940"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Aktivita ForEach ve službě Azure Data Factory
Aktivita ForEach definuje ve vašem kanálu opakovaný tok řízení. Tato aktivita se používá k opakování v kolekci a spouští zadané aktivity ve smyčce. Implementace smyčky této aktivity se podobá struktuře smyčky Foreach v programovacích jazycích.

## <a name="syntax"></a>Syntaxe
Vlastnosti jsou popsány dále v tomto článku. Vlastnost položky je kolekce a každá položka v kolekci je odkazován pomocí `@item()` jak je znázorněno v následující syntaxi:  

```json
{  
   "name":"MyForEachActivityName",
   "type":"ForEach",
   "typeProperties":{  
      "isSequential":"true",
        "items": {
            "value": "@pipeline().parameters.mySinkDatasetFolderPathCollection",
            "type": "Expression"
        },
      "activities":[  
         {  
            "name":"MyCopyActivity",
            "type":"Copy",
            "typeProperties":{  
               ...
            },
            "inputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@pipeline().parameters.mySourceDatasetFolderPath"
                  }
               }
            ],
            "outputs":[  
               {  
                  "referenceName":"MyDataset",
                  "type":"DatasetReference",
                  "parameters":{  
                     "MyFolderPath":"@item()"
                  }
               }
            ]
         }
      ]
   }
}

```

## <a name="type-properties"></a>Typ vlastnosti

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název pro každou aktivitu. | Řetězec | Ano
type | Musí být nastaveno na **ForEach** | Řetězec | Ano
isSequential | Určuje, zda smyčky by měl provádět sekvenčně nebo paralelně.  Maximálně 20 průchod cyklem lze provést najednou paralelně). Například, pokud máte iterace aktivity ForEach přes 10 různé zdroje a jímky datové sady s aktivitou kopírování **isSequential** nastavena na hodnotu False, jsou všechny kopie spouštěny najednou. Výchozí hodnota je False. <br/><br/> "IsSequential" je nastavený na hodnotu False, ujistěte se, že se správnou konfiguraci, kterou chcete spustit více spustitelných souborů. V opačném případě by měl tuto vlastnost použít opatrně vyhnout konflikty při zápisu. Další informace najdete v tématu [paralelní provádění](#parallel-execution) oddílu. | Logická hodnota | Ne. Výchozí hodnota je False.
batchCount | Počet v dávce má být použit pro řízení počet paralelních spuštění (Pokud isSequential je nastavena na hodnotu false). | Celé číslo (maximální 50) | Ne. Výchozí hodnota je 20.
Items | Výraz, který vrátí pole JSON na provést iteraci. | Výraz (který vrací pole JSON) | Ano
Aktivity | Činnosti, které mají být provedeny. | Seznam aktivit | Ano

## <a name="parallel-execution"></a>Paralelní provádění
Pokud **isSequential** je nastavena na hodnotu false, aktivita iteruje paralelně s délkou maximálně 20 souběžných iterací. Toto nastavení by měl používat opatrně. Souběžné iterací psaní do stejné složky, ale jiné soubory, je tento přístup v pořádku. Souběžné iterací souběžně psaní přesně stejný soubor, tento přístup pravděpodobně způsobí chybu. 

## <a name="iteration-expression-language"></a>Jazyk výrazů iterace
Aktivita ForEach, zadejte pole a provést iteraci pro vlastnost **položky**. " Použití `@item()` k iteraci přes jeden výčet v aktivitě ForEach. Například pokud **položky** je pole: [1, 2, 3], `@item()` vrátí 1 v první iteraci, ve druhém iterace 2 a 3 v třetí iterace.

## <a name="iterating-over-a-single-activity"></a>Iterace s použitím jedné aktivity
**Scénář:** Zkopírujte ze stejného zdrojového souboru v objektu Blob Azure do více cílových souborů v Azure Blob.

### <a name="pipeline-definition"></a>Definice kanálu

```json
{
    "name": "<MyForEachPipeline>",
    "properties": {
        "activities": [
            {
                "name": "<MyForEachActivity>",
                "type": "ForEach",
                "typeProperties": {
                    "isSequential": "true",
                    "items": {
                        "value": "@pipeline().parameters.mySinkDatasetFolderPath",
                        "type": "Expression"
                    },
                    "activities": [
                        {
                            "name": "MyCopyActivity",
                            "type": "Copy",
                            "typeProperties": {
                                "source": {
                                    "type": "BlobSource",
                                    "recursive": "false"
                                },
                                "sink": {
                                    "type": "BlobSink",
                                    "copyBehavior": "PreserveHierarchy"
                                }
                            },
                            "inputs": [
                                {
                                    "referenceName": "<MyDataset>",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@pipeline().parameters.mySourceDatasetFolderPath"
                                    }
                                }
                            ],
                            "outputs": [
                                {
                                    "referenceName": "MyDataset",
                                    "type": "DatasetReference",
                                    "parameters": {
                                        "MyFolderPath": "@item()"
                                    }
                                }
                            ]
                        }
                    ]
                }
            }
        ],
        "parameters": {
            "mySourceDatasetFolderPath": {
                "type": "String"
            },
            "mySinkDatasetFolderPath": {
                "type": "String"
            }
        }
    }
}

```

### <a name="blob-dataset-definition"></a>Definice datové sady objektů BLOB

```json
{  
   "name":"<MyDataset>",
   "properties":{  
      "type":"AzureBlob",
      "typeProperties":{  
         "folderPath":{  
            "value":"@dataset().MyFolderPath",
            "type":"Expression"
         }
      },
      "linkedServiceName":{  
         "referenceName":"StorageLinkedService",
         "type":"LinkedServiceReference"
      },
      "parameters":{  
         "MyFolderPath":{  
            "type":"String"
         }
      }
   }
}

```

### <a name="run-parameter-values"></a>Hodnoty parametrů spuštění

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterovat přes několik aktivit
Je možné k iteraci přes několik aktivit (například: kopírování a webové aktivity) v aktivitě ForEach. V tomto scénáři doporučujeme je abstraktní, více aktivit do samostatných kanálu. Potom můžete použít [určuje aktivita ExecutePipeline](control-flow-execute-pipeline-activity.md) v kanálu s aktivitou ForEach volat samostatnou kanál s více aktivit. 


### <a name="syntax"></a>Syntaxe

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "<MyForEachMultipleActivities>"
        "typeProperties": {
          "isSequential": true,
          "items": {
            ...
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "name": "<MyInnerPipeline>"
              "typeProperties": {
                "pipeline": {
                  "referenceName": "<copyHttpPipeline>",
                  "type": "PipelineReference"
                },
                "parameters": {
                  ...
                },
                "waitOnCompletion": true
              }
            }
          ]
        }
      }
    ],
    "parameters": {
      ...
    }
  }
}

```

### <a name="example"></a>Příklad:
**Scénář:** Iterujte přes InnerPipeline v rámci aktivity ForEach s aktivita spuštění kanálu. Vnitřní kanál kopíruje s definice schémat s parametry.

#### <a name="master-pipeline-definition"></a>Hlavní kanálu

```json
{
  "name": "masterPipeline",
  "properties": {
    "activities": [
      {
        "type": "ForEach",
        "name": "MyForEachActivity",
        "typeProperties": {
          "isSequential": true,
          "items": {
            "value": "@pipeline().parameters.inputtables",
            "type": "Expression"
          },
          "activities": [
            {
              "type": "ExecutePipeline",
              "typeProperties": {
                "pipeline": {
                  "referenceName": "InnerCopyPipeline",
                  "type": "PipelineReference"
                },
                "parameters": {
                  "sourceTableName": {
                    "value": "@item().SourceTable",
                    "type": "Expression"
                  },
                  "sourceTableStructure": {
                    "value": "@item().SourceTableStructure",
                    "type": "Expression"
                  },
                  "sinkTableName": {
                    "value": "@item().DestTable",
                    "type": "Expression"
                  },
                  "sinkTableStructure": {
                    "value": "@item().DestTableStructure",
                    "type": "Expression"
                  }
                },
                "waitOnCompletion": true
              },
              "name": "ExecuteCopyPipeline"
            }
          ]
        }
      }
    ],
    "parameters": {
      "inputtables": {
        "type": "Array"
      }
    }
  }
}

```

#### <a name="inner-pipeline-definition"></a>Vnitřní kanálu

```json
{
  "name": "InnerCopyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            }
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "name": "CopyActivity",
        "inputs": [
          {
            "referenceName": "sqlSourceDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sourceTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sourceTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sqlSinkDataset",
            "parameters": {
              "SqlTableName": {
                "value": "@pipeline().parameters.sinkTableName",
                "type": "Expression"
              },
              "SqlTableStructure": {
                "value": "@pipeline().parameters.sinkTableStructure",
                "type": "Expression"
              }
            },
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceTableName": {
        "type": "String"
      },
      "sourceTableStructure": {
        "type": "String"
      },
      "sinkTableName": {
        "type": "String"
      },
      "sinkTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="source-dataset-definition"></a>Zdroj definice datové sady

```json
{
  "name": "sqlSourceDataset",
  "properties": {
    "type": "SqlServerTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "sqlserverLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="sink-dataset-definition"></a>Definice datové sady jímky

```json
{
  "name": "sqlSinkDataSet",
  "properties": {
    "type": "AzureSqlTable",
    "typeProperties": {
      "tableName": {
        "value": "@dataset().SqlTableName",
        "type": "Expression"
      }
    },
    "structure": {
      "value": "@dataset().SqlTableStructure",
      "type": "Expression"
    },
    "linkedServiceName": {
      "referenceName": "azureSqlLS",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "SqlTableName": {
        "type": "String"
      },
      "SqlTableStructure": {
        "type": "String"
      }
    }
  }
}

```

#### <a name="master-pipeline-parameters"></a>Hlavní kanál parametry
```json
{
    "inputtables": [
        {
            "SourceTable": "department",
            "SourceTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ],
            "DestTable": "department2",
            "DestTableStructure": [
              {
                "name": "departmentid",
                "type": "int"
              },
              {
                "name": "departmentname",
                "type": "string"
              }
            ]
        }
    ]
    
}
```

## <a name="aggregating-outputs"></a>Agregování výstupy

Do agregační výstupů __foreach__ aktivity, _Variable_s, využijte prosím a _připojit proměnné_ aktivity.

Nejprve deklarujte `array` _proměnné_ v kanálu. Potom vyvolat _připojit proměnné_ aktivity v rámci jednotlivých __foreach__ smyčky. Následně můžete načíst agregaci z vašeho pole.

## <a name="limitations-and-workarounds"></a>Omezení a řešení

Tady jsou některá omezení aktivita ForEach a navrhovaná alternativní řešení.

| Omezení | Alternativní řešení |
|---|---|
| Nelze vnořovat smyčku ForEach uvnitř jiného smyčky ForEach (nebo do dokud smyčky). | Navrhněte dvouúrovňová kanálu, kde vnější kanál s vnější smyčky ForEach Iteruje přes kanál vnitřní s vnořené smyčky. |
| Aktivita ForEach může mít nejvýše `batchCount` 50 pro paralelní zpracování a maximálně 100 000 položek. | Navrhněte dvouúrovňová kanálu, kde vnější kanálu s aktivitou ForEach Iteruje přes vnitřní kanálu. |
| | |

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
