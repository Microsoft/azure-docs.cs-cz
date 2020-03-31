---
title: Mapování sloupců datové sady ve Službě Azure Data Factory
description: Přečtěte si, jak mapovat zdrojové sloupce na cílové sloupce.
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
ms.openlocfilehash: 6eb7012e28319ee6cc86de5ee56090743d681068
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74923870"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapování sloupců zdrojové datové sady na sloupce cílové datové sady
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Mapování sloupců lze použít k určení, jak se sloupce zadané v "struktuře" zdrojové tabulky mapují na sloupce zadané v "struktuře" tabulky jímek. Vlastnost **columnMapping** je k dispozici v části **typeProperties** aktivity Copy.

Mapování sloupců podporuje následující scénáře:

* Všechny sloupce ve struktuře zdrojové datové sady jsou mapovány na všechny sloupce ve struktuře datové sady jímky.
* Podmnožina sloupců ve struktuře zdrojové datové sady je mapována na všechny sloupce ve struktuře datové sady jímky.

Následují chybové stavy, které vedou k výjimce:

* Buď méně sloupců nebo více sloupců v "struktuře" tabulky jímky, než je uvedeno v mapování.
* Duplicitní mapování.
* Výsledek dotazu SQL nemá název sloupce, který je zadán v mapování.

> [!NOTE]
> Následující ukázky jsou pro Azure SQL a Azure Blob, ale jsou použitelné pro všechny úložiště dat, které podporuje obdélníkové datové sady. Upravte definice datové sady a propojených služeb v příkladech tak, aby ukazovaly na data v příslušném zdroji dat.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ukázka 1 – mapování sloupců z Azure SQL na objekt blob Azure
V této ukázce vstupní tabulka má strukturu a odkazuje na tabulku SQL v databázi Azure SQL.

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

V této ukázce má výstupní tabulka strukturu a odkazuje na objekt blob v úložišti objektů blob Azure.

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

Následující JSON definuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapovány na sloupce v jímce **(columnMappings**) pomocí **Translator** vlastnost.

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

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ukázka 2 – mapování sloupců s dotazem SQL z Azure SQL do objektu blob Azure
V této ukázce se dotaz SQL používá k extrahování dat z Azure SQL namísto jednoduše zadání názvu tabulky a názvy sloupců v části "struktura". 

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
V tomto případě jsou výsledky dotazu nejprve mapovány na sloupce zadané v "struktuře" zdroje. Dále sloupce ze zdrojové "struktury" jsou mapovány na sloupce v jímce "struktura" s pravidly zadanými v columnMappings.  Předpokládejme, že dotaz vrátí 5 sloupců, další dva sloupce než ty, které jsou zadány v "struktuře" zdroje.

**Tok mapování sloupců**

![Tok mapování sloupců-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Další kroky
Podívejte se na článek o návodu k používání aktivity kopírování: 

- [Kopírování dat z úložiště objektů blob do databáze SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
