---
title: Aktivita vyhledávání v Azure Data Factory
description: Naučte se používat aktivitu vyhledávání k vyhledání hodnoty z externího zdroje. Na tento výstup můžete dál odkazovat pomocí úspěšných aktivit.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 06/15/2018
ms.openlocfilehash: 02abdaf46ca2af6c96d3b5e8d4ce5876831bd415
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81417994"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Aktivita vyhledávání v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita vyhledávání může načíst datovou sadu z libovolného zdroje dat podporovaného Azure Data Factory. Použijte ho v následujícím scénáři:
- Dynamicky určit, na které objekty se má pracovat v následné aktivitě místo hardwarového kódování názvu objektu. Některé příklady objektů jsou soubory a tabulky.

Aktivita vyhledávání načte a vrátí obsah konfiguračního souboru nebo tabulky. Vrátí také výsledek provedení dotazu nebo uložené procedury. Výstup aktivity vyhledávání se dá použít v následné aktivitě kopírování nebo transformace, pokud se jedná o hodnotu typu singleton. Výstup lze použít v aktivitě ForEach, pokud se jedná o pole atributů.

## <a name="supported-capabilities"></a>Podporované možnosti

Pro aktivitu vyhledávání jsou podporovány následující zdroje dat. Největší počet řádků, které mohou být vráceny vyhledávací aktivitou, je 5 000, velikost až 2 MB. V současné době je nejdelší doba trvání aktivity vyhledávání před časovým limitem jedna hodina.

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

## <a name="type-properties"></a>Vlastnosti typu

Název | Popis | Typ | Povinné?
---- | ----------- | ---- | --------
integrován | Poskytuje odkaz na datovou sadu pro vyhledávání. Získejte podrobnosti z oddílu **Vlastnosti datové sady** v každém odpovídajícím článku konektoru. | Pár klíč/hodnota | Ano
source | Obsahuje vlastnosti zdroje specifické pro datovou sadu, která je stejná jako zdroj aktivity kopírování. Získejte podrobnosti z části **vlastnosti aktivity kopírování** v každém odpovídajícím článku konektoru. | Pár klíč/hodnota | Ano
firstRowOnly | Označuje, zda má být vrácen pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí formát je `true`.

> [!NOTE]
> 
> * Zdrojové sloupce s typem **ByteArray** se nepodporují.
> * **Struktura** není v definicích datových sad podporována. Pro textové soubory formátu použijte řádek záhlaví k zadání názvu sloupce.
> * Pokud je zdrojem vyhledávání soubor JSON, `jsonPathDefinition` nastavení pro změnu tvaru objektu JSON se nepodporuje. Budou načteny všechny objekty.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Použití aktivity vyhledávání v důsledku následné aktivity

Výsledek vyhledávání se vrátí v `output` části výsledku spuštění aktivity.

* **Pokud `firstRowOnly` je nastaven na `true` (výchozí)**, je výstupní formát, jak je znázorněno v následujícím kódu. Výsledkem hledání je pevný `firstRow` klíč. Chcete-li použít výsledek v následné aktivitě, použijte vzor `@{activity('MyLookupActivity').output.firstRow.TableName}`.

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Pokud `firstRowOnly` je nastaven na `false` **, výstupní formát je znázorněn v následujícím kódu. `count` Pole indikuje, kolik záznamů je vráceno. Podrobné hodnoty se zobrazí pod pevným `value` polem. V takovém případě je aktivita vyhledávání následována [aktivitou foreach](control-flow-for-each-activity.md). Předáte `value` pole aktivity `items` foreach pomocí vzoru. `@activity('MyLookupActivity').output.value` Chcete-li získat přístup `value` k prvkům v poli, použijte `@{activity('lookupActivity').output.value[zero based index].propertyname}`následující syntaxi:. Příklad: `@{activity('lookupActivity').output.value[0].tablename}`.

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
V tomto příkladu aktivita kopírování kopíruje data z tabulky SQL ve vaší instanci Azure SQL Database do úložiště objektů BLOB v Azure. Název tabulky SQL je uložený v souboru JSON v úložišti objektů BLOB. Aktivita vyhledávání vyhledá název tabulky za běhu. Formát JSON se dynamicky upravuje pomocí tohoto přístupu. Nemusíte znovu nasazovat kanály ani datové sady. 

Tento příklad ukazuje vyhledávání pouze pro první řádek. Chcete-li vyhledat všechny řádky a zřetězit výsledky pomocí aktivity ForEach, přečtěte si ukázky v [hromadném kopírování více tabulek pomocí Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Kanál
Tento kanál obsahuje dvě aktivity: vyhledávání a kopírování. 

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

### <a name="lookup-dataset"></a>Vyhledávací datová sada
**Vyhledávací** datová sada je **zdrojový soubor. json** ve složce Azure Storage vyhledávání určené typem **AzureStorageLinkedService** . 

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

### <a name="source-dataset-for-copy-activity"></a>**Zdrojová** datová sada pro aktivitu kopírování
**Zdrojová** datová sada používá výstup aktivity vyhledávání, což je název tabulky SQL. Aktivita kopírování kopíruje data z této tabulky SQL do umístění v úložišti objektů BLOB v Azure. Umístění je určené datovou sadou **jímky** . 

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

### <a name="sink-dataset-for-copy-activity"></a>Datová sada **jímky** pro aktivitu kopírování
Aktivita kopírování kopíruje data z tabulky SQL do souboru **filebylookup. csv** ve složce **CSV** v Azure Storage. Soubor je určen vlastností **AzureStorageLinkedService** . 

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
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<StorageAccountName>;AccountKey=<StorageAccountKey>"
        }
    },
        "name": "AzureStorageLinkedService"
}
```

### <a name="azure-sql-database-linked-service"></a>Propojená služba Azure SQL Database
Tato instance Azure SQL Database obsahuje data, která se mají zkopírovat do úložiště objektů BLOB. 

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "description": "",
        "typeProperties": {
            "connectionString": "Server=<server>;Initial Catalog=<database>;User ID=<user>;Password=<password>;"
        }
    }
}
```

### <a name="sourcetablejson"></a>Source. JSON

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
