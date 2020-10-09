---
title: Mapování sloupců datové sady v Azure Data Factory
description: Přečtěte si, jak namapovat zdrojové sloupce na cílové sloupce.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: af7a1e40f21b6c9af490abe6f58edcaf798818b4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85318871"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapování sloupců zdrojové datové sady na cílové sloupce datové sady
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Mapování sloupce lze použít k určení způsobu, jakým jsou sloupce zadané v "struktuře" zdrojové tabulky namapovány na sloupce zadané v "struktuře" tabulky jímky. Vlastnost **columnMapping** je k dispozici v části **typeProperties** aktivity kopírování.

Mapování sloupce podporuje následující scénáře:

* Všechny sloupce ve struktuře zdrojové datové sady jsou namapovány na všechny sloupce ve struktuře datové sady jímky.
* Podmnožina sloupců ve struktuře zdrojové datové sady je namapována na všechny sloupce ve struktuře datové sady jímky.

Následující jsou chybové stavy, jejichž výsledkem je výjimka:

* Buď méně sloupců, nebo více sloupců v "struktuře" tabulky jímky, než je uvedeno v mapování.
* Duplicitní mapování
* Výsledek dotazu SQL neobsahuje název sloupce, který je uveden v mapování.

> [!NOTE]
> Následující ukázky jsou pro Azure SQL a Azure Blob, ale platí pro jakékoliv úložiště dat, které podporuje hranaté datové sady. Upravte definice datové sady a propojené služby v příkladech tak, aby odkazovaly na data v příslušném zdroji dat.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ukázka 1 – mapování sloupce z Azure SQL na Azure Blob
V této ukázce má vstupní tabulka strukturu a odkazuje na tabulku SQL v Azure SQL Database.

```json
{
    "name": "AzureSQLInput",
    "properties": {
        "structure": 
         [
           { "name": "userid"},
           { "name": "name"},
           { "name": "group"}
         ],
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

V této ukázce má výstupní tabulka strukturu a odkazuje na objekt BLOB v úložišti objektů BLOB v Azure.

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
         "structure": 
          [
                { "name": "myuserid"},
                { "name": "myname" },
                { "name": "mygroup"}
          ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder",
            "fileName":"myfile.csv",
            "format":
            {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Následující JSON definuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapované na sloupce v jímky (**ColumnMappings**) pomocí vlastnosti **Translator** .

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "Copy",
    "inputs":  [ { "name": "AzureSQLInput"  } ],
    "outputs":  [ { "name": "AzureBlobOutput" } ],
    "typeProperties":    {
        "source":
        {
            "type": "SqlSource"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
        }
    },
   "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
**Tok mapování sloupců:**

![Tok mapování sloupců](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ukázka 2 – mapování sloupce s dotazem SQL z Azure SQL do Azure Blob
V této ukázce se k extrakci dat ze služby Azure SQL používá dotaz SQL místo pouhého zadání názvu tabulky a názvů sloupců v části Structure. 

```json
{
    "name": "CopyActivity",
    "description": "description", 
    "type": "CopyActivity",
    "inputs":  [ { "name": " AzureSQLInput"  } ],
    "outputs":  [ { "name": " AzureBlobOutput" } ],
    "typeProperties":
    {
        "source":
        {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
        },
        "sink":
        {
            "type": "BlobSink"
        },
        "Translator": 
        {
            "type": "TabularTranslator",
            "ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
        }
    },
    "scheduler": {
          "frequency": "Hour",
          "interval": 1
        }
}
```
V tomto případě jsou výsledky dotazu nejprve namapovány na sloupce zadané v "struktuře" zdroje. V dalším kroku jsou sloupce ze zdrojové struktury mapovány na sloupce v jímky "Structure" s pravidly určenými v parametrech columnMappings.  Předpokládejme, že dotaz vrátí 5 sloupců, dva další sloupce než ty, které jsou zadány v "struktuře" zdroje.

**Tok mapování sloupců**

![Tok mapování sloupců – 2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Další kroky
Návod k používání aktivity kopírování najdete v článku. 

- [Kopírovat data z Blob Storage do SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
