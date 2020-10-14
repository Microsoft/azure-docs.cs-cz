---
title: Aktivita vyhledávání v Azure Data Factory
description: Naučte se používat aktivitu vyhledávání k vyhledání hodnoty z externího zdroje. Na tento výstup můžete dál odkazovat pomocí úspěšných aktivit.
services: data-factory
documentationcenter: ''
author: linda33wj
ms.author: jingwang
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/14/2020
ms.openlocfilehash: 371afbd9380ed1ecf28d0b26e2b4c5cd16ae6317
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044067"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Aktivita vyhledávání v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita vyhledávání může načíst datovou sadu z libovolného zdroje dat podporovaného Azure Data Factory. Použijte ho v následujícím scénáři:
- Dynamicky určit, na které objekty se má pracovat v následné aktivitě místo hardwarového kódování názvu objektu. Některé příklady objektů jsou soubory a tabulky.

Aktivita vyhledávání načte a vrátí obsah konfiguračního souboru nebo tabulky. Vrátí také výsledek provedení dotazu nebo uložené procedury. Výstup aktivity vyhledávání se dá použít v následné aktivitě kopírování nebo transformace, pokud se jedná o hodnotu typu singleton. Výstup lze použít v aktivitě ForEach, pokud se jedná o pole atributů.

## <a name="supported-capabilities"></a>Podporované možnosti

Pro aktivitu vyhledávání jsou podporovány následující zdroje dat. 

Aktivita vyhledávání může vracet až 5000 řádků. Pokud sada výsledků obsahuje víc záznamů, vrátí se prvních 5000 řádků. Výstup aktivity vyhledávání podporuje až přibližně 4 MB velikosti. Pokud velikost překročí limit, aktivita se nezdaří. V současné době je nejdelší doba trvání aktivity vyhledávání v poli časový limit 24 hodin.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntax

```json
{
    "name":"LookupActivity",
    "type":"Lookup",
    "typeProperties":{
        "source":{
            "type":"<source type>"
        },
        "dataset":{
            "referenceName":"<source dataset name>",
            "type":"DatasetReference"
        },
        "firstRowOnly":<true or false>
    }
}
```

## <a name="type-properties"></a>Vlastnosti typu

Název | Popis | Typ | Povinné?
---- | ----------- | ---- | --------
integrován | Poskytuje odkaz na datovou sadu pro vyhledávání. Získejte podrobnosti z oddílu **Vlastnosti datové sady** v každém odpovídajícím článku konektoru. | Pár klíč/hodnota | Yes
source | Obsahuje vlastnosti zdroje specifické pro datovou sadu, která je stejná jako zdroj aktivity kopírování. Získejte podrobnosti z části **vlastnosti aktivity kopírování** v každém odpovídajícím článku konektoru. | Pár klíč/hodnota | Yes
firstRowOnly | Označuje, zda má být vrácen pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí formát je `true`.

> [!NOTE]
> 
> * Zdrojové sloupce s typem **ByteArray** se nepodporují.
> * **Struktura** není v definicích datových sad podporována. Pro textové soubory formátu použijte řádek záhlaví k zadání názvu sloupce.
> * Pokud je zdrojem vyhledávání soubor JSON, `jsonPathDefinition` nastavení pro změnu tvaru objektu JSON se nepodporuje. Budou načteny všechny objekty.

## <a name="use-the-lookup-activity-result"></a>Použít výsledek aktivity vyhledávání

Výsledek vyhledávání se vrátí v `output` části výsledku spuštění aktivity.

* **Pokud `firstRowOnly` je nastaven na `true` (výchozí)**, je výstupní formát, jak je znázorněno v následujícím kódu. Výsledkem hledání je pevný `firstRow` klíč. Chcete-li použít výsledek v následné aktivitě, použijte vzor  `@{activity('LookupActivity').output.firstRow.table` .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "schema":"dbo",
            "table":"Table1"
        }
    }
    ```

* **Pokud `firstRowOnly` je nastaven na `false` **, výstupní formát je znázorněn v následujícím kódu. `count`Pole indikuje, kolik záznamů je vráceno. Podrobné hodnoty se zobrazí pod pevným `value` polem. V takovém případě je aktivita vyhledávání následována [aktivitou foreach](control-flow-for-each-activity.md). Předáte pole `value` aktivity ForEach `items` pomocí vzoru `@activity('MyLookupActivity').output.value` . Chcete-li získat přístup k prvkům v `value` poli, použijte následující syntaxi: `@{activity('lookupActivity').output.value[zero based index].propertyname}` . Příklad: `@{activity('lookupActivity').output.value[0].schema}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "schema":"dbo",
                "table":"Table1"
            },
            {
                "Id": "2",
                "schema":"dbo",
                "table":"Table2"
            }
        ]
    } 
    ```

## <a name="example"></a>Příklad

V tomto příkladu kanál obsahuje dvě aktivity: **vyhledávání** a **kopírování**. Aktivita kopírování kopíruje data z tabulky SQL ve vaší instanci Azure SQL Database do úložiště objektů BLOB v Azure. Název tabulky SQL je uložený v souboru JSON v úložišti objektů BLOB. Aktivita vyhledávání vyhledá název tabulky za běhu. Formát JSON se dynamicky upravuje pomocí tohoto přístupu. Nemusíte znovu nasazovat kanály ani datové sady. 

Tento příklad ukazuje vyhledávání pouze pro první řádek. Chcete-li vyhledat všechny řádky a zřetězit výsledky pomocí aktivity ForEach, přečtěte si ukázky v [hromadném kopírování více tabulek pomocí Azure Data Factory](tutorial-bulk-copy.md).


### <a name="pipeline"></a>Kanál

- Aktivita vyhledávání je nakonfigurovaná tak, aby používala **LookupDataset**, která odkazuje na umístění v úložišti objektů BLOB v Azure. Aktivita vyhledávání přečte název tabulky SQL ze souboru JSON v tomto umístění. 
- Aktivita kopírování používá výstup aktivity vyhledávání, což je název tabulky SQL. Vlastnost **TableName** v **SourceDataset** je nakonfigurována tak, aby používala výstup z aktivity vyhledávání. Aktivita kopírování kopíruje data z tabulky SQL do umístění v úložišti objektů BLOB v Azure. Umístění je určeno vlastností **SinkDataset** . 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "dependsOn": [],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "JsonSource",
                        "storeSettings": {
                            "type": "AzureBlobStorageReadSettings",
                            "recursive": true
                        },
                        "formatSettings": {
                            "type": "JsonReadSettings"
                        }
                    },
                    "dataset": {
                        "referenceName": "LookupDataset",
                        "type": "DatasetReference"
                    },
                    "firstRowOnly": true
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "dependsOn": [
                    {
                        "activity": "LookupActivity",
                        "dependencyConditions": [
                            "Succeeded"
                        ]
                    }
                ],
                "policy": {
                    "timeout": "7.00:00:00",
                    "retry": 0,
                    "retryIntervalInSeconds": 30,
                    "secureOutput": false,
                    "secureInput": false
                },
                "userProperties": [],
                "typeProperties": {
                    "source": {
                        "type": "AzureSqlSource",
                        "sqlReaderQuery": {
                            "value": "select * from [@{activity('LookupActivity').output.firstRow.schema}].[@{activity('LookupActivity').output.firstRow.table}]",
                            "type": "Expression"
                        },
                        "queryTimeout": "02:00:00",
                        "partitionOption": "None"
                    },
                    "sink": {
                        "type": "DelimitedTextSink",
                        "storeSettings": {
                            "type": "AzureBlobStorageWriteSettings"
                        },
                        "formatSettings": {
                            "type": "DelimitedTextWriteSettings",
                            "quoteAllText": true,
                            "fileExtension": ".txt"
                        }
                    },
                    "enableStaging": false,
                    "translator": {
                        "type": "TabularTranslator",
                        "typeConversion": true,
                        "typeConversionSettings": {
                            "allowDataTruncation": true,
                            "treatBooleanAsNumber": false
                        }
                    }
                },
                "inputs": [
                    {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schemaName": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "tableName": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ],
                "outputs": [
                    {
                        "referenceName": "SinkDataset",
                        "type": "DatasetReference",
                        "parameters": {
                            "schema": {
                                "value": "@activity('LookupActivity').output.firstRow.schema",
                                "type": "Expression"
                            },
                            "table": {
                                "value": "@activity('LookupActivity').output.firstRow.table",
                                "type": "Expression"
                            }
                        }
                    }
                ]
            }
        ],
        "annotations": [],
        "lastPublishTime": "2020-08-17T10:48:25Z"
    }
}
```

### <a name="lookup-dataset"></a>Vyhledávací datová sada

**Vyhledávací** datová sada je **sourcetable.jsv** souboru ve složce pro vyhledávání Azure Storage určené typem **AzureBlobStorageLinkedService** . 

```json
{
    "name": "LookupDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "Json",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "sourcetable.json",
                "container": "lookup"
            }
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Zdrojová** datová sada pro aktivitu kopírování

**Zdrojová** datová sada používá výstup aktivity vyhledávání, což je název tabulky SQL. Aktivita kopírování kopíruje data z této tabulky SQL do umístění v úložišti objektů BLOB v Azure. Umístění je určené datovou sadou **jímky** . 

```json
{
    "name": "SourceDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureSqlDatabase",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schemaName": {
                "type": "string"
            },
            "tableName": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "AzureSqlTable",
        "schema": [],
        "typeProperties": {
            "schema": {
                "value": "@dataset().schemaName",
                "type": "Expression"
            },
            "table": {
                "value": "@dataset().tableName",
                "type": "Expression"
            }
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>Datová sada **jímky** pro aktivitu kopírování

Aktivita kopírování kopíruje data z tabulky SQL do souboru **filebylookup.csv** ve složce **CSV** v Azure Storage. Soubor je určen vlastností **AzureBlobStorageLinkedService** . 

```json
{
    "name": "SinkDataset",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "parameters": {
            "schema": {
                "type": "string"
            },
            "table": {
                "type": "string"
            }
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": {
                    "value": "@{dataset().schema}_@{dataset().table}.csv",
                    "type": "Expression"
                },
                "container": "csv"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.jsna

Pro **sourcetable.jsv** souboru můžete použít následující dva druhy formátů:

#### <a name="set-of-objects"></a>Sada objektů

```json
{
   "Id":"1",
   "schema":"dbo",
   "table":"Table1"
}
{
   "Id":"2",
   "schema":"dbo",
   "table":"Table2"
}
```

#### <a name="array-of-objects"></a>Pole objektů

```json
[ 
    {
        "Id": "1",
        "schema":"dbo",
        "table":"Table1"
    },
    {
        "Id": "2",
        "schema":"dbo",
        "table":"Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Omezení a alternativní řešení

Tady jsou některá omezení aktivity vyhledávání a navrhovaná řešení.

| Omezení | Alternativní řešení |
|---|---|
| Aktivita vyhledávání má maximálně 5 000 řádků a maximální velikost 2 MB. | Navrhněte kanál se dvěma úrovněmi, kde se vnější kanál opakuje přes vnitřní kanál, který načte data, která nepřekračují maximální počet řádků nebo velikost. |
| | |

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita ForEach](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Aktivita webu](control-flow-web-activity.md)
