---
title: Aktivita vyhledávání ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak aktivitu vyhledávání použijte k vyhledání hodnoty z externího zdroje. Tento výstup mohou dále odkazovat následující aktivity.
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
ms.date: 06/15/2018
ms.author: shlo
ms.openlocfilehash: e682b3780c26da9cf2398e93adc32cb107127d9c
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426787"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Aktivita vyhledávání ve službě Azure Data Factory

Aktivita vyhledávání můžete načíst datovou sadu z libovolného zdroje dat podporované služby Azure Data Factory. Použije v následujícím scénáři:
- Dynamické určování objektů, které se má operace provést následující aktivity, místo pevného kódování název objektu. Příklady některých objektů jsou soubory a tabulky.

Aktivita vyhledávání přečte a vrátí obsah konfiguračního souboru nebo tabulky. Také vrátí výsledek provedení dotazu nebo uložené procedury. Výstupem z aktivity vyhledávání lze použít v následných kopírování nebo aktivity transformace, pokud je hodnota typu singleton. Výstup je možné v aktivitě ForEach, pokud je pole atributů.

## <a name="supported-capabilities"></a>Podporované funkce

Následující zdroje dat nejsou podporovány pro aktivitu vyhledávání. Největší počet řádků, které může být vrácen aktivita vyhledávání je 5 000 až 2 MB. V současné době nejdelší dobu platnosti vyhledávací aktivita před vypršením časového limitu je jedna hodina.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores-for-lookup-activity.md)]

## <a name="syntax"></a>Syntaxe

```json
{
    "name": "LookupActivity",
    "type": "Lookup",
    "typeProperties": {
        "source": {
            "type": "<source type>"
            <additional source specific properties (optional)>
        },
        "dataset": { 
            "referenceName": "<source dataset name>",
            "type": "DatasetReference"
        },
        "firstRowOnly": false
    }
}
```

## <a name="type-properties"></a>Typ vlastnosti
Název | Popis | Typ | Povinné?
---- | ----------- | ---- | --------
Datové sady | Poskytuje odkaz na datovou sadu pro vyhledávání. Získat tak podrobné údaje z **vlastnosti datové sady** části každého článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
source | Obsahuje vlastnosti zdroje specifických pro datovou sadu, stejně jako zdroj kopírování. Získat tak podrobné údaje z **vlastnosti aktivity kopírování** části každého článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
firstRowOnly | Určuje, jestli se mají vrátit pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí formát je `true`.

> [!NOTE]

> * Zdrojové sloupce s **ByteArray** typu nejsou podporované.
> * **Struktura** není podporována v definicích datových sad. Pro soubory ve formátu textu použijte k poskytnutí názvu sloupce řádek záhlaví.
> * Pokud soubor JSON, je zdrojem vyhledávání `jsonPathDefinition` nastavení pro měnící tvar objektu JSON se nepodporuje. Celé objekty budou načítat.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Použijte aktivitu výsledek vyhledávání v následné aktivity

Výsledek vyhledávání se vrátí v `output` část výsledky spuštění aktivit.

* **Když `firstRowOnly` je nastavena na `true` (výchozí)**, formát výstupu se, jak je znázorněno v následujícím kódu. Výsledek vyhledávání je pod určitou `firstRow` klíč. Výsledek v následné aktivity, použijte vzor `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Když `firstRowOnly` je nastavena na `false`** , formát výstupu se, jak je znázorněno v následujícím kódu. A `count` pole označuje, kolik záznamů se vrátí. Podrobné hodnoty jsou zobrazeny v části s pevným `value` pole. V takovém případě je následována aktivita vyhledávání [aktivita Foreach](control-flow-for-each-activity.md). Předání `value` pole pro aktivitu ForEach `items` pole, a to pomocí vzor `@activity('MyLookupActivity').output.value`. Přístup k prvkům v `value` pole, použijte následující syntaxi: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Příklad: `@{activity('lookupActivity').output.value[0].tablename}`.

    ```json
    {
        "count": "2",
        "value": [
            {
                "Id": "1",
                "TableName" : "Table1"
            },
            {
                "Id": "2",
                "TableName" : "Table2"
            }
        ]
    } 
    ```

### <a name="copy-activity-example"></a>Příklad aktivity kopírování
V tomto příkladu aktivita kopírování kopíruje data z tabulky SQL ve vaší instanci Azure SQL Database do úložiště objektů Blob v Azure. Název tabulky SQL je uložené v souboru JSON ve službě Blob storage. Aktivita vyhledávání vyhledá název tabulky za běhu. JSON je upravit dynamicky pomocí tohoto přístupu. Není nutné znovu nasadit kanály ani datové sady. 

Tento příklad ukazuje vyhledání pouze na prvním řádku. Vyhledávání pro všechny řádky a zřetězení výsledky s aktivitu ForEach, najdete v ukázkách ve [hromadné kopírování několika tabulek pomocí Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Kanál
Tento kanál obsahuje dvě aktivity: vyhledávání a kopírování. 

- Aktivita vyhledávání je nakonfigurován na použití **LookupDataset**, který odkazuje na umístění v úložišti objektů Blob v Azure. Aktivita vyhledávání načte název tabulky SQL ze souboru JSON v tomto umístění. 
- Aktivita kopírování používá výstup aktivity vyhledávání, což je název tabulky SQL. **TableName** vlastnost **SourceDataset** je nakonfigurován na použití výstupu z aktivity vyhledávání. Zkopírujte aktivita kopíruje data z tabulky SQL do umístění v úložišti objektů Blob v Azure. Umístění je určená **SinkDataset** vlastnost. 

```json
{
    "name": "LookupPipelineDemo",
    "properties": {
        "activities": [
            {
                "name": "LookupActivity",
                "type": "Lookup",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "dataset": { 
                        "referenceName": "LookupDataset", 
                        "type": "DatasetReference" 
                    }
                }
            },
            {
                "name": "CopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": { 
                        "type": "SqlSource", 
                        "sqlReaderQuery": "select * from @{activity('LookupActivity').output.firstRow.tableName}" 
                    },
                    "sink": { 
                        "type": "BlobSink" 
                    }
                },                
                "dependsOn": [ 
                    { 
                        "activity": "LookupActivity", 
                        "dependencyConditions": [ "Succeeded" ] 
                    }
                 ],
                "inputs": [ 
                    { 
                        "referenceName": "SourceDataset", 
                        "type": "DatasetReference" 
                    } 
                ],
                "outputs": [ 
                    { 
                        "referenceName": "SinkDataset", 
                        "type": "DatasetReference" 
                    } 
                ]
            }
        ]
    }
}
```

### <a name="lookup-dataset"></a>Vyhledávání datové sady
**Vyhledávání** datová sada je **sourcetable.json** souboru ve složce vyhledávací služby Azure Storage určené **AzureStorageLinkedService** typu. 

```json
{
    "name": "LookupDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "lookup",
            "fileName": "sourcetable.json",
            "format": {
                "type": "JsonFormat",
                "filePattern": "SetOfObjects"
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="source-dataset-for-copy-activity"></a>**Zdroj** datovou sadu pro aktivitu kopírování
**Zdroj** datová sada používá výstup aktivity vyhledávání, což je název tabulky SQL. Zkopírujte aktivita kopíruje data z této tabulky SQL do umístění v úložišti objektů Blob v Azure. Umístění je určená **jímky** datové sady. 

```json
{
    "name": "SourceDataset",
    "properties": {
        "type": "AzureSqlTable",
        "typeProperties":{
            "tableName": "@{activity('LookupActivity').output.firstRow.tableName}"
        },
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="sink-dataset-for-copy-activity"></a>**Jímka** datovou sadu pro aktivitu kopírování
Aktivita kopírování kopíruje data z tabulky SQL **filebylookup.csv** soubor **sdíleného svazku clusteru** složky ve službě Azure Storage. Soubor je určená **AzureStorageLinkedService** vlastnost. 

```json
{
    "name": "SinkDataset",
    "properties": {
        "type": "AzureBlob",
        "typeProperties": {
            "folderPath": "csv",
            "fileName": "filebylookup.csv",
            "format": {
                "type": "TextFormat"                                                                    
            }
        },
        "linkedServiceName": {
            "referenceName": "AzureStorageLinkedService",
            "type": "LinkedServiceReference"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Propojená služba Azure Storage
Tento účet úložiště obsahuje soubor JSON s názvy tabulek SQL. 

```json
{
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "value": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>",
                "type": "SecureString"
            }
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Tuto instanci Azure SQL Database s daty, které se mají zkopírovat do úložiště objektů Blob. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": {
                "value": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;",
                "type": "SecureString"
            }
        }
    }
}
```

### <a name="sourcetablejson"></a>sourcetable.json

#### <a name="set-of-objects"></a>Sada objektů

```json
{
  "Id": "1",
  "tableName": "Table1"
}
{
   "Id": "2",
  "tableName": "Table2"
}
```

#### <a name="array-of-objects"></a>Pole objektů

```json
[ 
    {
        "Id": "1",
        "tableName": "Table1"
    },
    {
        "Id": "2",
        "tableName": "Table2"
    }
]
```

## <a name="limitations-and-workarounds"></a>Omezení a řešení

Tady jsou některá omezení, aktivita vyhledávání a navrhovaná alternativní řešení.

| Omezení | Alternativní řešení |
|---|---|
| Aktivita vyhledávání má maximálně 5 000 řádků a maximální velikosti 2 MB. | Navrhněte dvouúrovňová kanálu, kde vnější kanálu Iteruje přes vnitřní kanál, který načte data, která nepřekračuje maximální počet řádků nebo velikosti. |
| | |

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita ForEach](control-flow-for-each-activity.md)
- [Aktivita GetMetadata](control-flow-get-metadata-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
