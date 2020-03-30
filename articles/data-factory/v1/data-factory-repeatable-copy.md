---
title: Opakovatelná kopie ve Službě Azure Data Factory
description: Přečtěte si, jak se vyhnout duplikátům, i když je řez, který kopíruje data, spuštěn vícenež jednou.
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
ms.openlocfilehash: 7188cb5774699fc6e31fc3b8c78068bb33c6f552
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281142"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Opakovatelná kopie ve Službě Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení ze relačních zdrojů
Při kopírování dat z úložišť relačních dat mějte na paměti opakovatelnost, abyste se vyhnuli nezamýšleným výsledkům. V Azure Data Factory můžete znovu spustit řez ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu tak, aby řez je znovu spustit, když dojde k selhání. Při opětovném spuštění řezu v obou směrech je třeba se ujistit, že stejná data jsou čtena bez ohledu na to, kolikrát je řez spuštěn.  
 
> [!NOTE]
> Následující ukázky jsou pro Azure SQL, ale jsou použitelné pro všechna úložiště dat, která podporuje obdélníkové datové sady. Možná budete muset upravit **typ** zdroje a vlastnost **dotazu** (například: dotaz namísto sqlReaderQuery) pro úložiště dat.   

Při čtení z relačních úložišť obvykle chcete číst pouze data odpovídající danému řezu. Způsob, jak to udělat by pomocí WindowStart a WindowEnd systémové proměnné dostupné v Azure Data Factory. Přečtěte si o proměnných a funkcích v Azure Data Factory tady v článku [Azure Data Factory – funkce a systémové proměnné.](data-factory-functions-variables.md) Příklad: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Tento dotaz přečte data, která spadá do rozsahu trvání řezu (WindowStart -> WindowEnd) z tabulky MyTable. Opětovné spuštění tohoto řezu by také vždy zajistit, že jsou čtena stejná data. 

V ostatních případech můžete chtít přečíst celou tabulku a může definovat sqlReaderQuery takto:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Opakovatelný zápis do aplikace SqlSink
Při kopírování dat do **Azure SQL/SQL Server** z jiných úložišť dat, je třeba mít na paměti opakovatelnost, aby se zabránilo nezamýšleným výsledkům. 

Při kopírování dat do databáze Azure SQL/SQL Server, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. Řekněme, že kopírujete data ze souboru CSV (hodnoty oddělené čárkami), který obsahuje dva záznamy do následující tabulky v databázi Azure SQL/SQL Server. Při spuštění řezu jsou tyto dva záznamy zkopírovány do tabulky SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že jste našli chyby ve zdrojovém souboru a aktualizovali množství Down Tube z 2 na 4. Pokud znovu spustíte řez dat pro toto období ručně, najdete dva nové záznamy připojené k Azure SQL/SQL Server Database. Tento příklad předpokládá, že žádný ze sloupců v tabulce nemá omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Chcete-li se tomuto chování vyhnout, je třeba zadat sémantiku UPSERT pomocí jednoho z následujících dvou mechanismů:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanismus 1: použití sqlWriterCleanupScript
Vlastnost **sqlWriterCleanupScript** můžete použít k vyčištění dat z tabulky jímky před vložením dat při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Při spuštění řezu je skript pro vyčištění spuštěn jako první, aby se odstranila data, která odpovídají řezu z tabulky SQL. Aktivita kopírování pak vloží data do tabulky SQL. Pokud je řez znovu spuštěn, množství se aktualizuje podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že záznam ploché podložky bude odebrán z původního csv. Potom opětovné spuštění řezu by vedlo k následujícímu výsledku: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Aktivita kopírování spustila skript vyčištění, aby odstranila odpovídající data pro tento řez. Pak si přečetl vstup z csv (který pak obsahoval pouze jeden záznam) a vložil jej do tabulky. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanismus 2: použití sliceIdentifierColumnName
> [!IMPORTANT]
> V současné době sliceIdentifierColumnName není podporována pro Azure SQL Data Warehouse. 

Druhý mechanismus pro dosažení opakovatelnosti je tím, že vyhrazené sloupec (sliceIdentifierColumnName) v cílové tabulce. Tento sloupec by byl používán Azure Data Factory k zajištění synchronizace zdrojového a cílového zůstatu. Tento přístup funguje, pokud je flexibilita při změně nebo definování cílového schématu tabulky SQL. 

Tento sloupec používá Azure Data Factory pro účely opakovatelnosti a v procesu Azure Data Factory neprovede žádné změny schématu v tabulce. Způsob použití tohoto přístupu:

1. Definujte sloupec typu **binární (32)** v cílové tabulce SQL. V tomto sloupci by neměla být žádná omezení. Pojmenujme tento sloupec jako AdfSliceIdentifier pro tento příklad.


    Zdrojová tabulka:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Cílová tabulka: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Použijte ji v aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory naplní tento sloupec podle potřeby zajistit, aby zdroj a cíl zůstat synchronizovány. Hodnoty tohoto sloupce by neměly být použity mimo tento kontext. 

Podobně jako mechanismus 1, aktivita kopírování automaticky vyčistí data pro daný řez z cílové tabulky SQL. Potom vloží data ze zdroje do cílové tabulky. 

## <a name="next-steps"></a>Další kroky
Prohlédněte si následující články konektoru, které pro úplné příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
