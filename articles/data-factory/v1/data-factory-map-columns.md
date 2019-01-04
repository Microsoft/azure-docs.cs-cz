---
title: Mapování sloupců v datové sadě ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak mapovat sloupce zdrojového do cílového sloupce.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1b009ac2ca42e9804b88989b55b2e73524732550
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017459"
---
# <a name="map-source-dataset-columns-to-destination-dataset-columns"></a>Mapování sloupců zdrojové datové sady na cílové sloupce datové sady
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. 

Mapování sloupce lze použít k určení, jak se sloupce zadané v "struktura" mapování tabulky zdroje na sloupce zadané v "struktura" tabulky jímky. **ColumnMapping** vlastnost je k dispozici v **typeProperties** části aktivity kopírování.

Mapování sloupců podporuje následující scénáře:

* Všechny sloupce ve struktuře datové sady zdroje se mapují na všechny sloupce ve struktuře datové sady jímky.
* Podmnožinu sloupců ve struktuře datové sady zdroje je namapována na všechny sloupce ve struktuře datové sady jímky.

Následují chybových podmínek, za následek výjimku:

* Méně sloupců nebo více sloupců v "struktura" tabulky jímky než zadán v mapování.
* Duplicitní mapování.
* Výsledek dotazu SQL nemá název sloupce, který je zadán v mapování.

> [!NOTE]
> Následující ukázky jsou pro Azure SQL a objektů Blob v Azure, ale platí pro jakékoli úložiště dat, který podporuje obdélníkové datové sady. Upravte datovou sadu a definice propojené služby v příkladech tak, aby odkazoval na data ve zdroji dat relevantní.

## <a name="sample-1--column-mapping-from-azure-sql-to-azure-blob"></a>Příklad 1 – sloupec mapování z Azure SQL do objektu blob Azure
V tomto příkladu vstupní tabulka má strukturu a odkazuje na tabulku SQL ve službě Azure SQL database.

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

V této ukázce výstupní tabulce obsahuje strukturu a odkazuje na objekt blob ve službě Azure blob storage.

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

Následující kód JSON určuje aktivitu kopírování v kanálu. Sloupce z namapované na sloupce v jímky zdroje (**columnMappings**) s použitím **Translator** vlastnost.

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

![Tok mapování sloupce](./media/data-factory-map-columns/column-mapping-flow.png)

## <a name="sample-2--column-mapping-with-sql-query-from-azure-sql-to-azure-blob"></a>Příklad 2 – sloupec mapování pomocí dotazu SQL z Azure SQL do objektu blob Azure
V této ukázce se používá jazyka SQL namísto stačí zadat název tabulky a názvů sloupců v oddílu "struktura" extrahovat data z Azure SQL. 

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
Výsledky dotazu se v tomto případě nejprve mapují na sloupce zadané v "struktura" zdroje. V dalším kroku mapování sloupce ze zdroje "struktura" na sloupce Sink "struktura" pomocí pravidel specifikovaných v columnMappings.  Předpokládejme, že dotaz vrací 5 sloupců, než procesory zadané v "struktura" zdroje další dva sloupce.

**Tok mapování sloupce**

![Mapování sloupce flow-2](./media/data-factory-map-columns/column-mapping-flow-2.png)

## <a name="next-steps"></a>Další postup
Kurz týkající se použití aktivity kopírování najdete v článku: 

- [Kopírování dat z úložiště objektů Blob do služby SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
