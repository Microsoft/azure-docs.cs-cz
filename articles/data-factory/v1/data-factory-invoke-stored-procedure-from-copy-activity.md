---
title: Vyvolání uložené procedury z aktivity kopírování datové továrny Azure
description: Zjistěte, jak vyvolat uloženou proceduru v Azure SQL Database nebo SQL Server z aktivity kopírování Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: d05c2b03a0c498144f37c9b6205053120a596b09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74924088"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Vyvolání uložené procedury z aktivity kopírování v Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte [si téma transformace dat pomocí uložené aktivity procedury v datové továrně](../transform-data-using-stored-procedure.md).


Při kopírování dat do [SQL Server](data-factory-sqlserver-connector.md) nebo Azure [SQL Database](data-factory-azure-sql-connector.md), můžete nakonfigurovat **SqlSink** v aktivitě kopírování vyvolat uloženou proceduru. Můžete chtít použít uloženou proceduru k provedení dalšího zpracování (slučování sloupců, vyhledání hodnot, vložení do více tabulek atd.) je vyžadováno před vložením dat do cílové tabulky. Tato funkce využívá [parametry s hodnotou tabulky](https://msdn.microsoft.com/library/bb675163.aspx). 

Následující ukázka ukazuje, jak vyvolat uloženou proceduru v databázi serveru SQL Server z kanálu datové továrny (aktivita kopírování):  

## <a name="output-dataset-json"></a>Výstupní datová sada JSON
Ve výstupní datové sadě JSON nastavte **typ** **na: SqlServerTable**. Nastavte ji na **AzureSqlTable** pro použití s databází Azure SQL. Hodnota vlastnosti **tableName** se musí shodovat s názvem prvního parametru uložené procedury.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Oddíl SqlSink v aktivitě kopírování JSON
Definujte část **SqlSink** v aktivitě kopírování JSON následujícím způsobem. Chcete-li vyvolat uloženou proceduru při vkládání dat do databáze jímky/cílové databáze, zadejte hodnoty pro vlastnosti **SqlWriterStoredProcedureName** a **SqlWriterTableType.** Popis těchto vlastností naleznete [v části SqlSink v článku konektoru serveru SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
V databázi definujte uloženou proceduru se stejným názvem jako **SqlWriterStoredProcedureName**. Uložená procedura zpracovává vstupní data ze zdrojového úložiště dat a vloží data do tabulky v cílové databázi. Název prvního parametru uložené procedury se musí shodovat s názvem tableName definovaným v datové sadě JSON (Marketing).

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
V databázi definujte typ tabulky se stejným názvem jako **SqlWriterTableType**. Schéma typu tabulky se musí shodovat se schématem vstupní datové sady.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Další kroky
Prohlédněte si následující články konektoru, které pro úplné příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
