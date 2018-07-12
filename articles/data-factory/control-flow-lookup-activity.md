---
title: Aktivita vyhledávání ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak aktivitu vyhledávání použijte k vyhledání hodnoty z externího zdroje. Na tento výstup mohou dále odkazovat následující aktivity.
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
ms.openlocfilehash: 25ed439674fcf7136e29034eb97e0652ae9ba111
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38237828"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Aktivita vyhledávání ve službě Azure Data Factory

Aktivita vyhledávání slouží k načtení datové sady z libovolného zdroje dat podporované ADF.  Je možné v následujícím scénáři:
- Dynamicky určete, které objekty (soubory, tabulky atd.) se má operace provést následující aktivity, místo pevného kódování název objektu

Aktivita vyhledávání může číst a vrátí obsah konfiguračního souboru, konfigurace tabulky nebo výsledek provedení dotazu nebo uložené procedury.  Výstupem z aktivity vyhledávání můžete použít v následných kopírování nebo aktivity transformace, pokud je hodnota typu singleton nebo použít v aktivitě ForEach, pokud je pole atributů.

## <a name="supported-capabilities"></a>Podporované funkce

Následující zdroje dat nejsou podporovány pro vyhledávání. Maximální počet řádků může být vrácen vyhledávací aktivita **5000**a až **2MB** velikosti. A právě maximální dobu trvání pro aktivitu vyhledávání před vypršením časového limitu je jedna hodina.

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
datové sady | Poskytuje odkaz na datovou sadu pro vyhledávání. Získáte podrobnosti v části "Vlastnosti datové sady" každého článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
source | Obsahuje vlastnosti zdroje specifických pro datovou sadu, stejné jako u zdroje aktivity kopírování. Získáte podrobnosti v části "Vlastnosti aktivity kopírování" každého článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
firstRowOnly | Určuje, jestli se mají vrátit pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí hodnota je `true`.

**Je třeba počítat s následujícím:**

1. Zdrojový sloupec s typem třídy ByteArray se nepodporuje.
2. Struktura se nepodporuje v definici datové sady. Pro soubory ve formátu textu konkrétně, vám pomůže řádek záhlaví zadejte název sloupce.
3. Pokud je zdrojem vyhledávání soubory JSON, `jsonPathDefinition` nastavení znovu tvarování objekt JSON se nepodporuje, celé objekty budou načítat.

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

* **Když `firstRowOnly` je nastavena na `false`** , formát výstupu se, jak je znázorněno v následujícím kódu. A `count` pole označuje, kolik záznamů se vrátí a podrobné hodnoty jsou zobrazeny v části s pevným `value` pole. V takovém případě se aktivita vyhledávání obvykle následuje [aktivita Foreach](control-flow-for-each-activity.md). Můžete předat `value` pole pro aktivitu ForEach `items` pole, a to pomocí vzor `@activity('MyLookupActivity').output.value`. Přístup k prvkům v `value` pole, použijte následující syntaxi: `@{activity('lookupActivity').output.value[zero based index].propertyname}`. Tady je příklad: `@{activity('lookupActivity').output.value[0].tablename}`.

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

## <a name="example"></a>Příklad:
V tomto příkladu aktivita kopírování kopíruje data z tabulky SQL ve vaší instanci Azure SQL Database do úložiště objektů Blob v Azure. Název tabulky SQL je uložené v souboru JSON ve službě Blob storage. Aktivita vyhledávání vyhledá název tabulky za běhu. Tento přístup umožňuje JSON dynamicky měnit bez nutnosti nasazení kanály ani datové sady. 

Tento příklad ukazuje vyhledání pouze na prvním řádku. Vyhledávání pro všechny řádky a zřetězení výsledky s aktivitu ForEach, najdete v ukázkách ve [hromadné kopírování několika tabulek pomocí Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Kanál
Tento kanál obsahuje dvě aktivity: *vyhledávání* a *kopírování*. 

- Aktivita vyhledávání je nakonfigurován pro použití LookupDataset, který odkazuje na umístění v úložišti objektů Blob v Azure. Aktivita vyhledávání načte název tabulky SQL ze souboru JSON v tomto umístění. 
- Aktivita kopírování používá výstup aktivity vyhledávání (název tabulky SQL). Vlastnost tableName v jako zdrojovou datovou sadu (SourceDataset) je nakonfigurován pro použití výstupu z aktivity vyhledávání. Aktivita kopírování kopíruje data z tabulky SQL do umístění ve službě Azure Blob storage, která je zadána vlastnost SinkDataset. 


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
Vyhledávání datová sada odkazuje *sourcetable.json* souboru ve složce vyhledávací služby Azure Storage, který je určený typem AzureStorageLinkedService. 

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

### <a name="source-dataset-for-the-copy-activity"></a>Zdrojová datová sada pro aktivitu kopírování
Zdrojová datová sada používá výstup aktivity vyhledávání, což je název tabulky SQL. Aktivita kopírování kopíruje data z této tabulky SQL do umístění ve službě Azure Blob storage, která je zadána datová sada jímky. 

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

### <a name="sink-dataset-for-the-copy-activity"></a>Datová sada pro aktivitu kopírování jímky
Aktivita kopírování kopíruje data z tabulky SQL *filebylookup.csv* soubor *sdíleného svazku clusteru* složky ve službě Azure storage, která je zadána vlastnost AzureStorageLinkedService. 

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

## <a name="next-steps"></a>Další postup
Zobrazit další aktivity toku řízení podporovaných službou Data Factory: 

- [Aktivita spuštění kanálu](control-flow-execute-pipeline-activity.md)
- [Pro každou aktivitu](control-flow-for-each-activity.md)
- [Získání metadat aktivity](control-flow-get-metadata-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
