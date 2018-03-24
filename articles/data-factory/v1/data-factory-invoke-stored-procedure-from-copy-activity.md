---
title: Vyvolat uloženou proceduru z kopie aktivita služby Azure Data Factory | Microsoft Docs
description: Zjistěte, jak vyvolat uloženou proceduru v Azure SQL Database nebo SQL Server z aktivity kopírování Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 96fedb97b16a71b0f09733a01227d38e4d10b0fd
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Vyvolat uloženou proceduru z aktivity kopírování v Azure Data Factory
> [!NOTE]
> Tento článek se týká verze 1 služby Data Factory, která je obecně dostupná (GA). Pokud používáte verze 2 služby Data Factory, který je ve verzi preview, najdete v části [transformace dat pomocí aktivity uložené procedury v datové továrně verze 2](../transform-data-using-stored-procedure.md).


Při kopírování dat do [systému SQL Server](data-factory-sqlserver-connector.md) nebo [Azure SQL Database](data-factory-azure-sql-connector.md), můžete nakonfigurovat **SqlSink** v aktivitě kopírování vyvolat uloženou proceduru. Můžete chtít použít uložené procedury provést žádné další zpracování (slučování sloupců, vyhledávání hodnot, vložení do více tabulek, atd.) je vyžadována před vkládání dat v cílové tabulky. Tato funkce využívá [zavolat parametry](https://msdn.microsoft.com/library/bb675163.aspx). 

Následující příklad ukazuje, jak vyvolat uloženou proceduru v databázi systému SQL Server z objektu pro vytváření dat kanál (aktivita kopírování):  

## <a name="output-dataset-json"></a>Výstupní datovou sadu JSON
V výstupní datovou sadu JSON, nastavte **typ** k: **SqlServerTable**. Nastavte ji na **AzureSqlTable** pro použití s Azure SQL database. Hodnota **tableName** vlastnost musí shodovat s názvem první parametr uložené procedury.  

```json
{
  "name": "SqlOutput",
  "properties": {
    "type": "SqlServerTable",
    "linkedServiceName": "SqlLinkedService",
    "typeProperties": {
      "tableName": "Marketing"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

## <a name="sqlsink-section-in-copy-activity-json"></a>Část SqlSink v aktivitě kopírování JSON
Definování **SqlSink** část v aktivitě kopírování JSON následujícím způsobem. Vyvolat uloženou proceduru při vložení dat do podřízený nebo cílové databázi, zadejte hodnoty pro obě **SqlWriterStoredProcedureName** a **SqlWriterTableType** vlastnosti. Popis těchto vlastností najdete v tématu [SqlSink část v systému SQL Server konektoru článku](data-factory-sqlserver-connector.md#sqlsink).

```json
"sink":
{
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing", 
    "storedProcedureParameters":
            {
                "stringData": 
                {
                    "value": "str1"     
                }
            }
}
```

## <a name="stored-procedure-definition"></a>Definice uložené procedury 
V databázi, zadejte uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Uložená procedura zpracovává vstupní data ze zdrojového úložiště dat a vloží data do tabulky v cílové databázi. Název prvního parametru uložené procedury musí odpovídat tableName definované v sadě dat JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Typ definice tabulky
V databázi, zadejte typ tabulky se stejným názvem jako **SqlWriterTableType**. Schéma typu tabulky musí odpovídat schématu vstupní datové sady.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Další postup
Zkontrolujte konektor následující články, dokončení příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
