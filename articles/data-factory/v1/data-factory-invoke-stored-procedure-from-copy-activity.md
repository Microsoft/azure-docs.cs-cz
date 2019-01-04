---
title: Vyvolání uložené procedury z aktivity kopírování objekt pro vytváření dat Azure | Dokumentace Microsoftu
description: Zjistěte, jak vyvolat uloženou proceduru v Azure SQL Database nebo SQL serveru z Azure Data Factory aktivity kopírování.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 35e9347039a7b9939ab4d2719f9738429dec168c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016062"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Vyvolání uložené procedury z aktivity kopírování ve službě Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformovat data pomocí aktivity uložených procedur ve službě Data Factory](../transform-data-using-stored-procedure.md).


Při kopírování dat do [systému SQL Server](data-factory-sqlserver-connector.md) nebo [Azure SQL Database](data-factory-azure-sql-connector.md), můžete nakonfigurovat **SqlSink** v aktivitě kopírování, která se má vyvolat uloženou proceduru. Můžete chtít použít uloženou proceduru provádět žádné další zpracování (sloučení sloupců vyhledávání hodnot, vložení do několika tabulek, atd.) vyžaduje před vložením dat v cílové tabulce. Tato funkce využívá [Table-Valued parametry](https://msdn.microsoft.com/library/bb675163.aspx). 

Následující příklad ukazuje, jak vyvolat uloženou proceduru v databázi SQL serveru z kanálu Data Factory (aktivita kopírování):  

## <a name="output-dataset-json"></a>Výstupní datové sady JSON
Ve vstupní sadě JSON, nastavte **typ** na: **SqlServerTable**. Nastavte ho na **AzureSqlTable** pro použití s Azure SQL database. Hodnota pro **tableName** vlastnost musí odpovídat názvu první parametr uložené procedury.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Část SqlSink v kódu JSON aktivity kopírování
Definovat **SqlSink** části v kódu JSON aktivity kopírování následujícím způsobem. Vyvolat uloženou proceduru při vkládání dat do jímky/cílové databáze, zadejte hodnoty pro oba **SqlWriterStoredProcedureName** a **SqlWriterTableType** vlastnosti. Popisy těchto vlastností naleznete v tématu [v systému SQL Server konektoru části SqlSink](data-factory-sqlserver-connector.md#sqlsink).

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
V databázi, definovat uložená procedura se stejným názvem jako **SqlWriterStoredProcedureName**. Uložená procedura zpracovává vstupní data ze zdrojového úložiště dat a vloží data do tabulky v cílové databázi. Název první parametr uložené procedury musí odpovídat tableName definované v sadě dat JSON (Marketing).

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @stringData varchar(256)
AS
BEGIN
    DELETE FROM [dbo].[Marketing] where ProfileID = @stringData
    INSERT [dbo].[Marketing](ProfileID, State)
    SELECT * FROM @Marketing
END
```

## <a name="table-type-definition"></a>Definice typu tabulky
V databázi, definujte typ tabulky se stejným názvem jako **SqlWriterTableType**. Schéma typu tabulky musí odpovídat schématu vstupní datové sady.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Další postup
Kontrola konektoru následující články, které kompletní příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
