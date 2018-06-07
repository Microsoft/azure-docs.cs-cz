---
title: Mapování sloupce datové sady v Azure Data Factory | Microsoft Docs
description: Naučte se namapovat zdrojové sloupce cílového sloupce.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1c8ea748297b8fe4052b60f08b8f00b1e41275fb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34620353"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapování zdrojových datovou sadu sloupců na sloupce cílové sady dat
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). 

Mapování sloupce slouží k určení, jak sloupce zadané v "strukturu" zdrojové tabulky mapy na sloupce zadané v "struktura" tabulky jímky. **ColumnMapping** vlastnost je k dispozici v **rámci typeProperties** části aktivitě kopírování.

Mapování sloupců podporuje následující scénáře:

* Všechny sloupce ve struktuře datové sady zdroje jsou namapované na všechny sloupce ve struktuře datové sady jímky.
* Podmnožinu sloupců ve struktuře datové sady zdroje je namapována na všechny sloupce ve struktuře datové sady jímky.

Tady jsou chybové stavy, které mít za následek výjimku:

* Méně sloupců nebo více sloupců ve "struktuře" tabulky jímky než zadaná v mapování.
* Duplicitní mapování.
* Výsledek dotazu SQL neobsahuje název sloupce, který je zadán v mapování.

> [!NOTE]
> Následující ukázky jsou pro Azure SQL a objektů Blob v Azure, ale platí pro všechny datové úložiště, které podporuje obdélníková datové sady. Upravte datovou sadu a propojené služby definice v příkladech tak, aby odkazoval na data ve zdroji relevantní data.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Ukázka 1 – mapování z Azure SQL do objektu blob Azure sloupců
V této ukázce vstupní tabulka obsahuje strukturu a odkazuje na tabulku SQL v databázi Azure SQL.

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

V této ukázce výstupní tabulka obsahuje strukturu a odkazuje na objekt blob v Azure blob storage.

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

Následující kód JSON určuje aktivitu kopírování v kanálu. Sloupce ze zdroje mapovat na sloupce ve podřízený (**columnMappings**) pomocí **překladač** vlastnost.

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

![Sloupec mapování toku](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Ukázka 2 – mapování pomocí dotazu SQL z Azure SQL do objektu blob Azure sloupců
V této ukázce se používá dotaz SQL extrahovat data z Azure SQL místo jednoduše zadání názvu tabulky a názvy sloupců v části "struktura". 

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
V takovém případě výsledky dotazu jsou nejprve namapované na sloupce zadané v "struktura" zdroje. V dalším kroku sloupců ze zdroje "struktura", jsou namapované na sloupce v podřízený "struktura" s pravidly zadaná ve vlastnosti columnMappings.  Předpokládejme, že dotaz vrátí 5 sloupců, než procesory zadané v "struktura" zdroje další dva sloupce.

**Sloupec mapování toku**

![Mapování sloupce toku-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Další postup
Kurz týkající se použití aktivitě kopírování najdete v článku: 

- [Kopírování dat z úložiště objektů Blob do databáze SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
