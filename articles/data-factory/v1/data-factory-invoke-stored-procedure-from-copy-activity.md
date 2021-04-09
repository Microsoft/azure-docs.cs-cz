---
title: Vyvolat uloženou proceduru z aktivity kopírování Azure Data Factory
description: Naučte se vyvolat uloženou proceduru v Azure SQL Database nebo SQL Server z aktivity Azure Data Factory kopírování.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 6f06b84ac0807a37c7adc603a557894be85a4cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374962"
---
# <a name="invoke-stored-procedure-from-copy-activity-in-azure-data-factory"></a>Vyvolat uloženou proceduru z aktivity kopírování v Azure Data Factory
> [!NOTE]
> Tento článek platí pro Data Factory verze 1. Pokud používáte aktuální verzi služby Data Factory, přečtěte si téma [transformace dat pomocí aktivity uložená procedura v Data Factory](../transform-data-using-stored-procedure.md).


Při kopírování dat do [SQL Server](data-factory-sqlserver-connector.md) nebo [Azure SQL Database](data-factory-azure-sql-connector.md)můžete nakonfigurovat **SqlSink** v aktivitě kopírování a vyvolat uloženou proceduru. Můžete chtít použít uloženou proceduru k provedení dalšího zpracování (sloučení sloupců, vyhledávání hodnot, vložení do několika tabulek atd.) před vložením dat do cílové tabulky. Tato funkce využívá parametry s [hodnotou tabulky](/dotnet/framework/data/adonet/sql/table-valued-parameters). 

Následující příklad ukazuje, jak vyvolat uloženou proceduru v databázi SQL Server z kanálu Data Factory (aktivita kopírování):  

## <a name="output-dataset-json"></a>Formát JSON výstupní datové sady
V souboru JSON výstupní datové sady nastavte **typ** na: **SQLServer**. Nastavte ji na **AzureSqlTable** pro použití s Azure SQL Database. Hodnota vlastnosti **TableName** musí odpovídat názvu prvního parametru uložené procedury.  

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

## <a name="sqlsink-section-in-copy-activity-json"></a>Oddíl SqlSink v kódu JSON aktivity kopírování
V poli JSON aktivity kopírování definujte oddíl **SqlSink** následujícím způsobem. Chcete-li vyvolat uloženou proceduru při vkládání dat do jímky nebo cílové databáze, zadejte hodnoty pro vlastnosti **SqlWriterStoredProcedureName** a **SqlWriterTableType** . Popisy těchto vlastností najdete [v části SqlSink v článku konektoru pro SQL Server](data-factory-sqlserver-connector.md#sqlsink).

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
V databázi definujte uloženou proceduru se stejným názvem jako **SqlWriterStoredProcedureName**. Uložená procedura zpracovává vstupní data ze zdrojového úložiště dat a vkládá data do tabulky v cílové databázi. Název prvního parametru uložené procedury musí odpovídat hodnotě tableName definované v datové sadě JSON (marketing).

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
V databázi Definujte typ tabulky se stejným názvem jako **SqlWriterTableType**. Schéma typu tabulky musí odpovídat schématu vstupní datové sady.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL
)
```

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články konektoru, které jsou kompletními příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)