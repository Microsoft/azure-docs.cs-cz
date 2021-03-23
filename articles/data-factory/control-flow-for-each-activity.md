---
title: Aktivita ForEach v Azure Data Factory
description: U každé aktivity definuje tok řízení opakování ve vašem kanálu. Používá se pro iteraci v kolekci a provádění zadaných aktivit.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/23/2019
ms.openlocfilehash: a0c3a3cbaa71d627f54550cf92c067afbb1eb3f0
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786205"
---
# <a name="foreach-activity-in-azure-data-factory"></a>Aktivita ForEach v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita ForEach definuje tok řízení opakování ve vašem kanálu. Tato aktivita se používá k opakování v kolekci a spouští zadané aktivity ve smyčce. Implementace smyčky této aktivity se podobá struktuře smyčky Foreach v programovacích jazycích.

## <a name="syntax"></a>Syntax
Vlastnosti jsou popsány dále v tomto článku. Vlastnost Items je kolekce a každá položka v kolekci je odkazována pomocí příkazu, jak je `@item()` znázorněno v následující syntaxi:  

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

Vlastnost | Popis | Povolené hodnoty | Vyžadováno
-------- | ----------- | -------------- | --------
name | Název aktivity for-each. | Řetězec | Ano
typ | Musí být nastaven na **foreach** | Řetězec | Ano
-Sekvenční | Určuje, zda má být smyčka provedena sekvenčně nebo paralelně.  Maximálně 20 iterací smyčky je možné spustit najednou paralelně. Například pokud máte aktivitu ForEach na iteraci s aktivitou kopírování s 10 různými datovými sadami zdroje a jímky s možností- **sekvenčním** nastavením na hodnotu false, všechny kopie se spustí najednou. Výchozí hodnota je false. <br/><br/> Pokud je "" "-sekvenční" nastaveno na hodnotu false, ujistěte se, že existuje správná konfigurace pro spouštění více spustitelných souborů. V opačném případě by tato vlastnost měla být použita s opatrností, aby nedocházelo ke konfliktům při zápisu. Další informace najdete v části [paralelní spuštění](#parallel-execution) . | Logická hodnota | Ne. Výchozí hodnota je false.
batchCount | Počet dávek, který se má použít k řízení počtu paralelního spuštění (Pokud je vlastnost-sekvenční nastavená na hodnotu false). Toto je horní limit souběžnosti, ale pro-každou aktivitu se na tomto čísle nespustí vždy. | Celé číslo (maximum 50) | Ne. Výchozí hodnota je 20.
Položky | Výraz, který vrací pole JSON, které se má iterovat. | Výraz (který vrací pole JSON) | Ano
Aktivity | Aktivity, které mají být provedeny. | Seznam aktivit | Ano

## <a name="parallel-execution"></a>Paralelní provádění
Pokud je vlastnost- **sekvenční** nastavená na hodnotu false, aktivita se prochází paralelně s maximálně 20 souběžnými iteracemi. Toto nastavení by se mělo používat opatrně. Pokud souběžné iterace zapisují do stejné složky, ale do různých souborů, je tento přístup v pořádku. Pokud souběžné iterace zapisuje současně do stejného souboru, tento přístup pravděpodobně způsobí chybu. 

## <a name="iteration-expression-language"></a>Jazyk výrazu iterace
V aktivitě ForEach zadejte pole, které se má iterovat pro **položky** vlastností. Použijte `@item()` k iterování v rámci jednoho výčtu v aktivitě foreach. Například pokud je **položka** pole: [1, 2, 3], `@item()` vrátí 1 v první iteraci, 2 v druhé iteraci a 3 ve třetí iteraci.

## <a name="iterating-over-a-single-activity"></a>Iterace v rámci jedné aktivity
**Scénář:** Kopírování ze stejného zdrojového souboru v objektu blob Azure do více cílových souborů v objektu blob Azure.

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

### <a name="run-parameter-values"></a>Spustit hodnoty parametrů

```json
{
    "mySourceDatasetFolderPath": "input/",
    "mySinkDatasetFolderPath": [ "outputs/file1", "outputs/file2" ]
}

```

## <a name="iterate-over-multiple-activities"></a>Iterace nad více aktivitami
Je možné iterovat více aktivit (například aktivity kopírování a webu) v aktivitě ForEach. V tomto scénáři doporučujeme, abyste do samostatného kanálu vyčerpali více aktivit. Pak můžete použít [aktivitu ExecutePipeline](control-flow-execute-pipeline-activity.md) v kanálu s aktivitou foreach k vyvolání samostatného kanálu s více aktivitami. 


### <a name="syntax"></a>Syntax

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
**Scénář:** Iterujte přes InnerPipeline v rámci aktivity ForEach s aktivitou spustit kanál. Kopie vnitřního kanálu se parametrizovanými definicemi schématu.

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

#### <a name="inner-pipeline-definition"></a>Definice vnitřního kanálu

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

#### <a name="master-pipeline-parameters"></a>Parametry hlavního kanálu
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

Chcete-li agregovat výstupy aktivity __foreach__ , použijte _proměnné_ a aktivitu _připojit proměnnou_ .

Nejdřív deklarujte `array` _proměnnou_ v kanálu. Pak v každé smyčce __foreach__ zavolejte aktivitu _připojit proměnnou_ . Následně můžete z pole načíst agregaci.

## <a name="limitations-and-workarounds"></a>Omezení a alternativní řešení

Tady jsou některá omezení aktivity ForEach a navrhovaná řešení.

| Omezení | Alternativní řešení |
|---|---|
| Nelze vnořit smyčku ForEach do jiné smyčky ForEach (nebo do smyčky do). | Navrhněte kanál se dvěma úrovněmi, kde vnější kanál s vnější smyčkou ForEach prochází přes vnitřní kanál s vnořenou smyčkou. |
| Aktivita ForEach má maximálně `batchCount` 50 pro paralelní zpracování a maximálně 100 000 položek. | Navrhněte kanál se dvěma úrovněmi, kde vnější kanál s aktivitou ForEach prochází přes vnitřní kanál. |
| SetVariable nelze použít v aktivitě ForEach, která běží paralelně, protože proměnné jsou globální pro celý kanál, nejsou vymezeny na ForEach nebo žádné jiné aktivity. | Zvažte použití sekvenčního příkazu ForEach nebo použití kanálu Execute uvnitř příkazu ForEach (proměnná/parametr zpracovávaný v podřízeném kanálu).|
| | |

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita získání metadat](control-flow-get-metadata-activity.md)
- [Aktivita vyhledávání](control-flow-lookup-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
