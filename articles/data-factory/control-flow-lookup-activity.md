---
title: Vyhledávací aktivita v Azure Data Factory
description: Přečtěte si, jak pomocí vyhledávací aktivity vyhledat hodnotu z externího zdroje. Na tento výstup lze dále odkazovat následujícími aktivitami.
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
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417994"
---
# <a name="lookup-activity-in-azure-data-factory"></a>Vyhledávací aktivita v Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktivita vyhledávání můžete načíst datovou sadu z libovolného zdroje dat podporované Azure Data Factory. Použijte jej v následujícím scénáři:
- Dynamicky určete, na kterých objektech se má pracovat v následné aktivitě, namísto pevného kódování názvu objektu. Některé příklady objektů jsou soubory a tabulky.

Vyhledávací aktivita přečte a vrátí obsah konfiguračního souboru nebo tabulky. Vrátí také výsledek provádění dotazu nebo uložené procedury. Výstup z aktivity vyhledávání lze použít v následné kopírování nebo transformace aktivity, pokud je hodnota singleton. Výstup lze použít v ForEach aktivity, pokud je pole atributů.

## <a name="supported-capabilities"></a>Podporované možnosti

Následující zdroje dat jsou podporovány pro aktivitu vyhledávání. Největší počet řádků, které mohou být vráceny aktivitou vyhledávání, je 5 000, až 2 MB. V současné době nejdelší doba trvání aktivity vyhledávání před časovým časem je jedna hodina.

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
Dataset | Poskytuje odkaz na datovou sadu pro vyhledávání. Získejte podrobnosti z části **Vlastnosti datové sady** v každém článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
source | Obsahuje zdrojové vlastnosti specifické pro datovou sadu, stejné jako zdroj Aktivity kopírování. Získejte podrobnosti z části **Kopírovat aktivitu vlastnosti** v každém článku odpovídající konektor. | Dvojice klíč/hodnota | Ano
firstRowOnly | Označuje, zda se má vrátit pouze první řádek nebo všechny řádky. | Logická hodnota | Ne. Výchozí formát je `true`.

> [!NOTE]
> 
> * Zdrojové sloupce s **typem ByteArray** nejsou podporovány.
> * **Struktura** není podporována v definicích datových sad. U souborů ve formátu textu zadejte název sloupce pomocí řádku záhlaví.
> * Pokud je vyhledávacím zdrojem soubor JSON, `jsonPathDefinition` není nastavení pro změnu tvaru objektu JSON podporováno. Najsou načteny celé objekty.

## <a name="use-the-lookup-activity-result-in-a-subsequent-activity"></a>Použít výsledek aktivity vyhledávání v následné aktivitě

Výsledek vyhledávání je vrácen `output` v části výsledek spuštění aktivity.

* **Pokud `firstRowOnly` je `true` nastavena na (výchozí)**, výstupní formát je znázorněno v následujícím kódu. Výsledek vyhledávání je pod `firstRow` pevným klíčem. Chcete-li výsledek použít v následné `@{activity('MyLookupActivity').output.firstRow.TableName}`aktivitě, použijte vzor .

    ```json
    {
        "firstRow":
        {
            "Id": "1",
            "TableName" : "Table1"
        }
    }
    ```

* **Pokud `firstRowOnly` je `false`nastavena na **, výstupní formát je znázorněno v následujícím kódu. Pole `count` označuje, kolik záznamů je vráceno. Podrobné hodnoty jsou zobrazeny `value` pod pevným polem. V takovém případě je aktivita vyhledávání následuje [Foreach aktivity](control-flow-for-each-activity.md). `value` Pole předáte `items` poli Aktivita ForEach pomocí `@activity('MyLookupActivity').output.value`vzoru aplikace . Chcete-li získat `value` přístup k prvkům `@{activity('lookupActivity').output.value[zero based index].propertyname}`v poli, použijte následující syntaxi: . Příklad: `@{activity('lookupActivity').output.value[0].tablename}`.

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

### <a name="copy-activity-example"></a>Příklad kopírovat aktivitu
V tomto příkladu zkopírujte aktivitu zkopíruje data z tabulky SQL v instanci Azure SQL Database do úložiště objektů blob Azure. Název tabulky SQL je uložen v souboru JSON v úložišti objektů Blob. Aktivita vyhledávání vyhledá název tabulky za běhu. JSON je dynamicky modifikována pomocí tohoto přístupu. Není nutné znovu nasadit kanály nebo datové sady. 

Tento příklad ukazuje vyhledávání pouze pro první řádek. Pro vyhledávání pro všechny řádky a zřetězení výsledků s ForEach aktivity, najdete v ukázkách v [Kopírování více tabulek hromadně pomocí Azure Data Factory](tutorial-bulk-copy.md).

### <a name="pipeline"></a>Kanál
Tento kanál obsahuje dvě aktivity: Vyhledávání a Kopírování. 

- Aktivita vyhledávání je nakonfigurovaná pro použití **lookupDataset**, která odkazuje na umístění v úložišti objektů blob Azure. Aktivita vyhledávání přečte název tabulky SQL ze souboru JSON v tomto umístění. 
- Aktivita kopírování používá výstup aktivity vyhledávání, což je název tabulky SQL. Vlastnost **tableName** v **sadě SourceDataset** je nakonfigurována tak, aby používala výstup z aktivity vyhledávání. Kopírování aktivity zkopíruje data z tabulky SQL do umístění v úložišti objektů Blob Azure. Umístění je určeno vlastností **SinkDataset.** 

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

### <a name="lookup-dataset"></a>Datová sada vyhledávání
Vyhledávací **lookup** datová sada je soubor **sourcetable.json** ve vyhledávací složce Azure Storage určené typem **AzureStorageLinkedService.** 

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
**Zdrojová** datová sada používá výstup aktivity vyhledávání, což je název tabulky SQL. Kopírovat aktivitu zkopíruje data z této tabulky SQL do umístění v úložišti objektů Blob Azure. Umístění je určeno datovou sadou **jímky.** 

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

### <a name="sink-dataset-for-copy-activity"></a>**Datová** sada jímky pro aktivitu kopírování
Kopírovat aktivitu zkopíruje data z tabulky SQL do **souboru filebylookup.csv** ve složce **CSV** ve službě Azure Storage. Soubor je určen **vlastností AzureStorageLinkedService.** 

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
Tato instance Azure SQL Database obsahuje data, která se mají zkopírovat do úložiště objektů Blob. 

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

Tady jsou některá omezení aktivity vyhledávání a navrhovaná řešení.

| Omezení | Alternativní řešení |
|---|---|
| Aktivita vyhledávání má maximálně 5 000 řádků a maximální velikost 2 MB. | Navrhněte dvouúrovňový kanál, kde vnější kanál iterates přes vnitřní potrubí, které načítá data, která nepřesahuje maximální řádky nebo velikost. |
| | |

## <a name="next-steps"></a>Další kroky
Podívejte se na další aktivity toku řízení podporované factory: 

- [Spustit aktivitu kanálu](control-flow-execute-pipeline-activity.md)
- [Aktivita ForEach](control-flow-for-each-activity.md)
- [Aktivita getMetadata](control-flow-get-metadata-activity.md)
- [Webová aktivita](control-flow-web-activity.md)
