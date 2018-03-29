---
title: Aktivita vyhledávání v Azure Data Factory | Microsoft Docs
description: Zjistěte, jak pomocí aktivity vyhledávání najít hodnotu z externího zdroje. Na tento výstup mohou dále odkazovat následující aktivity.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: shlo
ms.openlocfilehash: 7d6abb72fca71c213f9810784581a9af2dafb3a2
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2018
---
# <a name="lookup-activity-in-azure-data-factory"></a>Aktivita vyhledávání v Azure Data Factory
Vyhledávání aktivity slouží ke čtení nebo vyhledat záznam, název tabulky nebo hodnota z externího zdroje. Na tento výstup mohou dále odkazovat následující aktivity. 

Aktivita vyhledávání je užitečné, pokud chcete dynamicky načíst seznam souborů, záznamy nebo tabulky z konfiguračního souboru nebo zdroj dat. Výstup z aktivity další lze ostatní aktivity k provedení určité zpracování na jen ty položky.

> [!NOTE]
> Tento článek se týká verze 2 služby Azure Data Factory, která je aktuálně ve verzi Preview. Pokud používáte verzi 1 služby Data Factory, který je všeobecně dostupná (GA), prostudujte si [dokumentaci služby Data Factory verze 1](v1/data-factory-introduction.md).

## <a name="supported-capabilities"></a>Podporované možnosti

Následující zdroje dat jsou aktuálně podporovány pro vyhledávání:

- Amazon Redshift
- Azure Blob Storage
- Azure Cosmos DB
- Azure Data Lake Store
- Úložiště Azure File
- Azure SQL Database
- Azure SQL Data Warehouse
- Azure Table Storage
- Dynamics 365
- Dynamics CRM
- Systém souborů
- PostgreSQL
- Salesforce
- Cloud služeb Salesforce
- SFTP
- SQL Server

Maximální počet řádků vrácených vyhledávání aktivity je **5000**a až do **10MB** velikost.

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

## <a name="type-properties"></a>Vlastnosti typu
Jméno | Popis | Typ | Požadováno?
---- | ----------- | ---- | --------
dataset | Poskytuje odkaz na datovou sadu pro vyhledávání. Získáte podrobnosti o z části "Vlastnosti datové sady" v jednotlivých odpovídající konektor článků. | Dvojice klíč/hodnota | Ano
zdroj | Obsahuje vlastnosti specifické pro datové sady zdroje, stejný jako zdroj kopie aktivity. Získáte podrobnosti o z části "Zkopírovat vlastnosti aktivity" v jednotlivých odpovídající konektor článků. | Dvojice klíč/hodnota | Ano
firstRowOnly | Určuje, jestli se mají vracet pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí hodnota je `true`.

Je třeba počítat s následujícím:

1. Zdrojový sloupec s typem ByteArray není podporován.
2. Struktura není podporována v definici datové sady. Pro soubory ve formátu textu konkrétně můžete řádek záhlaví zadejte název sloupce.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Výsledek vyhledávání aktivity použít následné aktivity

Výsledek vyhledávání je vrácený v `output` části aktivity při spuštění výsledek.

* **Když `firstRowOnly` je nastaven na `true` (výchozí)**, formát výstupu se, jak je znázorněno v následujícím kódu. Výsledek vyhledávání je pod pevná `firstRow` klíč. Pokud chcete použít výsledek následné aktivity, použijte vzor `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Když `firstRowOnly` je nastaven na `false`** , formát výstupu se, jak je znázorněno v následujícím kódu. A `count` pole určuje počet záznamů, jsou vráceny a podrobné hodnoty jsou zobrazeny v části pevná `value` pole. V takovém případě je vyhledávání aktivity obvykle následuje [Foreach aktivity](control-flow-for-each-activity.md). Můžete předat `value` pole na aktivitu ForEach `items` pole pomocí vzor `@activity('MyLookupActivity').output.value`. Přístup k elementům ve `value` pole, použijte následující syntaxi: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Tady je příklad: `@{activity('lookupActivity').output.value[0].tablename}`.

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

## <a name="example"></a>Příklad
V tomto příkladu aktivity kopírování kopíruje data z tabulky SQL ve vaší instanci databáze SQL Azure do Azure Blob storage. Název tabulky SQL je uložené v souboru JSON v úložišti objektů Blob. Aktivita vyhledávání vyhledá název tabulky za běhu. Tento přístup umožňuje JSON má být změněn dynamicky bez nutnosti znovu nasaďte kanály ani datové sady. 

Tento příklad ukazuje vyhledávání pouze první řádek. Vyhledávání pro všechny řádky a zřetězit výsledky pomocí příkazu ForEach aktivity, najdete v části Ukázky [kopírovat více tabulek hromadné pomocí Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Kanál
Tento kanál obsahuje dvě aktivity: *vyhledávání* a *kopie*. 

- Aktivita vyhledávání je nakonfigurovaný na použití LookupDataset, který odkazuje na umístění v Azure Blob storage. Aktivita vyhledávání načte název tabulky SQL ze souboru JSON v tomto umístění. 
- Aktivita kopírování používá výstup aktivity vyhledávání (název tabulky SQL). Vlastnost tableName v datové sadě zdroje (SourceDataset) je nakonfigurován pro použití výstupu z vyhledávání aktivity. Aktivita kopírování kopíruje data z tabulky SQL do umístění, do úložiště objektů Blob v Azure, která je zadána vlastnost SinkDataset. 


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
Vyhledávání datová sada odkazuje *sourcetable.json* soubor ve složce vyhledávání Azure Storage, který je určen podle typu AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Datové sady zdroje pro aktivitu kopírování
Datové sady zdroje používá výstup aktivity vyhledávání, což je název tabulky SQL. Aktivita kopírování kopíruje data z této tabulky SQL do umístění v Azure Blob storage, která je zadána datová sada podřízený. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Jímky datovou sadu pro aktivitu kopírování
Aktivitě kopírování kopíruje data z tabulky SQL pro *filebylookup.csv* v soubor *csv* složku v úložišti Azure, která je zadána vlastnost AzureStorageLinkedService. 

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
Tato instance databáze SQL Azure obsahuje data se zkopírují do úložiště objektů Blob. 

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
  "tableName": "Table1",
}
{
   "Id": "2",
  "tableName": "Table2",
}
```

#### <a name="array-of-objects"></a>Pole objektů

```json
[ 
    {
        "Id": "1",
          "tableName": "Table1",
    }
    {
        "Id": "2",
        "tableName": "Table2",
    }
]
```

## <a name="next-steps"></a>Další postup
Najdete v části Další aktivity toku řízení, které jsou podporovány službou Data Factory: 

- [Provádění aktivity kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Získání metadat aktivity](control-flow-get-metadata-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
