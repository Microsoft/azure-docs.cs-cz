---
title: Pro každou aktivitu v Azure Data Factory
description: Pro každou aktivitu definuje opakující se tok řízení v kanálu. Používá se pro iterace přes kolekci a spustit zadané aktivity.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: b8f95f22553a3b4639b1aba6576ce844116ae20b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73679882"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Pro každou aktivitu v Azure Data Factory
Aktivita ForEach definuje opakující se tok řízení v kanálu. Tato aktivita se používá k opakování v kolekci a spouští zadané aktivity ve smyčce. Implementace smyčky této aktivity se podobá struktuře smyčky Foreach v programovacích jazycích.

## <a name="syntax"></a>Syntaxe
Vlastnosti jsou popsány dále v tomto článku. Vlastnost items je kolekce a každá položka v kolekci `@item()` je označována pomocí, jak je znázorněno v následující syntaxi:  

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

## <a name="type-properties"></a>Vlastnosti typu

Vlastnost | Popis | Povolené hodnoty | Požaduje se
-------- | ----------- | -------------- | --------
jméno | Název aktivity for-each. | Řetězec | Ano
type | Musí být nastavena na **ForEach** | Řetězec | Ano
isSequential | Určuje, zda má být smyčka spuštěna postupně nebo paralelně.  Maximálně 20 opakování smyčky lze provést současně paralelně). Například pokud máte ForEach aktivity iterace přes aktivitu kopírování s 10 různých zdrojových a jímací datové sady s **isSequential nastavena** na False, všechny kopie jsou provedeny najednou. Výchozí hodnota je False. <br/><br/> Pokud "isSequential" je nastavena na False, ujistěte se, že je správná konfigurace pro spuštění více spustitelných souborů. V opačném případě by tato vlastnost měla být používána s opatrností, aby nedošlo ke konfliktům zápisu. Další informace naleznete v části [Paralelní spuštění.](#parallel-execution) | Logická hodnota | Ne. Výchozí hodnota je False.
batchCount | Počet dávek, který má být použit pro řízení počtu paralelního spuštění (když isSequential je nastavena na false). | Celé číslo (maximálně 50) | Ne. Výchozí hodnota je 20.
Items | Výraz, který vrací Pole JSON, které má být iterováno. | Výraz (který vrací pole JSON) | Ano
Aktivity | Aktivity, které mají být provedeny. | Seznam aktivit | Ano

## <a name="parallel-execution"></a>Paralelní provádění
Pokud **isSequential** je nastavena na false, aktivita iterates paralelně s maximálně 20 souběžných iterací. Toto nastavení je třeba používat s opatrností. Pokud souběžné iterace zapisují do stejné složky, ale do různých souborů, je tento přístup v pořádku. Pokud souběžné iterace zapisují souběžně do přesně stejného souboru, tento přístup s největší pravděpodobností způsobí chybu. 

## <a name="iteration-expression-language"></a>Jazyk výrazu iterace
V ForEach aktivity zadejte pole, které má být iterováno přes pro **položky**vlastnosti . Slouží `@item()` k iterace přes jeden výčet v ForEach aktivity. Například pokud **položky** je pole: [1, `@item()` 2, 3], vrátí 1 v první iteraci, 2 v druhé iteraci a 3 ve třetí iteraci.

## <a name="iterating-over-a-single-activity"></a>Iterace přes jednu aktivitu
**Scénář:** Zkopírujte ze stejného zdrojového souboru v objektu Blob Azure do více cílových souborů v objektu Blob Azure.

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

### <a name="blob-dataset-definition"></a>Definice datové sady objektu blob

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

### <a name="run-parameter-values"></a>Spustit hodnoty parametrů

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterate přes více aktivit
Je možné iterate přes více aktivit (například: kopírování a webové aktivity) v ForEach aktivity. V tomto scénáři doporučujeme abstrahovat více aktivit do samostatného kanálu. Potom můžete použít [ExecutePipeline aktivity](control-flow-execute-pipeline-activity.md) v kanálu s ForEach aktivity vyvolat samostatný kanál s více aktivitami. 


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

### <a name="example"></a>Příklad
**Scénář:** Iterate přes InnerPipeline v rámci ForEach aktivity s execute Pipeline aktivity. Vnitřní kanál kopie s definice schématu parametrizované.

#### <a name="master-pipeline-definition"></a>Definice hlavního kanálu

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

#### <a name="inner-pipeline-definition"></a>Definice vnitřního potrubí

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

#### <a name="source-dataset-definition"></a>Definice zdrojové datové sady

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

#### <a name="master-pipeline-parameters"></a>Hlavní parametry kanálu
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

## <a name="aggregating-outputs"></a>Agregace výstupů

Chcete-li agregovat výstupy __foreach__ aktivity, použijte _proměnné_ a _připojit proměnné_ aktivity.

Nejprve deklarujte `array` _proměnnou_ v kanálu. Potom vyvolat _Append Variable aktivity_ uvnitř každý __foreach__ smyčky. Následně můžete načíst agregaci z pole.

## <a name="limitations-and-workarounds"></a>Omezení a řešení

Zde jsou některá omezení foreach aktivity a navrhovaná řešení.

| Omezení | Alternativní řešení |
|---|---|
| Nelze vnořit ForEach smyčky uvnitř jiné smyčky ForEach (nebo Do smyčky). | Navrhněte dvouúrovňové potrubí, kde vnější potrubí s vnější foreach smyčky iterates přes vnitřní potrubí s vnořené smyčky. |
| ForEach Aktivita má `batchCount` maximálně 50 pro paralelní zpracování a maximálně 100 000 položek. | Navrhněte dvouúrovňový kanál, kde vnější kanál s ForEach aktivity iterates přes vnitřní potrubí. |
| | |

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Získat aktivitu metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
