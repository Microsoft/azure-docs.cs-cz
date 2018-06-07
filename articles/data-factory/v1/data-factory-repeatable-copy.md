---
title: Opakovatelných kopie v Azure Data Factory | Microsoft Docs
description: Jak se vyhnout duplikáty, i když je více než jednou spustit řez, který kopíruje data.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 8b1d1cf62dad94ca6141ce33783c0b16b50c931c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34622631"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Opakovatelných kopie v Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelných číst z relační zdrojů
Při kopírování dat z relačních dat ukládá, uvědomte si, aby se zabránilo neúmyslnému výstupy opakovatelnosti. V Azure Data Factory může řez znovu ručně. Zásady opakovaných pokusů pro datovou sadu můžete také nakonfigurovat tak, aby řez se znovu spustí, když dojde k chybě. Řez se znovu spustí, buď způsobem, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát řez je spustit.  
 
> [!NOTE]
> Následující ukázky jsou pro Azure SQL, ale platí pro všechny datové úložiště, které podporuje obdélníková datové sady. Možná budete muset upravit **typ** zdroje a **dotazu** vlastnosti (například: dotazu místo sqlReaderQuery) pro data uložit.   

Obvykle při čtení z relační úložiště, kterou chcete číst pouze data odpovídající této řez. Způsob, jak tomu by pomocí WindowStart a WindowEnd systémové proměnné dostupné v Azure Data Factory. Přečtěte si informace o proměnných a funkce v Azure Data Factory zde v [Azure Data Factory – funkce a systémové proměnné](data-factory-functions-variables.md) článku. Příklad: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Tento dotaz načte data, která spadá do rozsahu řez doba trvání (WindowStart -> WindowEnd) z tabulky MyTable. Spusťte tento řez by také vždy zajistěte, že je stejná data pro čtení. 

V jiných případech může chtít přečíst celou tabulku a může sqlReaderQuery definujte takto:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Opakovatelných při zápisu do SqlSink
Při kopírování dat **Azure SQL nebo SQL Server** z jiným úložištím dat, budete muset opakovatelnosti mějte na paměti, aby se zabránilo neúmyslnému výstupy. 

Při kopírování dat do databáze serveru SQL/SQL Azure, připojí aktivitě kopírování dat do tabulky jímky ve výchozím nastavení. Řekněme, jsou kopírování dat ze souboru CSV (hodnoty oddělené čárkami) obsahující dva záznamy v databázi Azure SQL nebo SQL Server v následující tabulce. Při spuštění se řez, dva záznamy se zkopírují do tabulky SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že byly nalezeny chyby ve zdrojovém souboru a aktualizované množství dolů trubice od 2 do 4. Pokud datový řez pro toto období ručně, najdete v ní dva nové záznamy připojenou k databázi Azure SQL nebo SQL Server. Tento příklad předpokládá, že žádný sloupců v tabulce nemá omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Chcete-li tomu zabránit, budete muset zadat UPSERT sémantiku pomocí jedné z následujících dvou mechanismů:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanismus 1: použití sqlWriterCleanupScript
Můžete použít **sqlWriterCleanupScript** vlastnost vyčistit data z tabulky jímky před vložením dat při spuštění řez. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Při spuštění se řez, je nejprve spustit skript vyčištění odstranit data, která odpovídá řez z tabulky SQL. Aktivitě kopírování vkládá data do tabulky SQL. Pokud je řez je znovu spustit, množství se aktualizuje podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že odebrání záznamu ploché podložku z původní sdílený svazek clusteru. Pak znovu spustit řez vznikna následující výsledek: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Aktivita kopírování spustili čištění skript, který chcete odstranit data odpovídající této řez. Pak jej číst vstupní ze souboru csv (což je obsaženy jenom jeden záznam) a vložit do tabulky. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanismus 2: pomocí sliceIdentifierColumnName
> [!IMPORTANT]
> V současné době sliceIdentifierColumnName není podporována pro Azure SQL Data Warehouse. 

Druhý mechanismus k dosažení opakovatelnosti spočívá v použití vyhrazených sloupce (sliceIdentifierColumnName) v cílové tabulce. V tomto sloupci se použije službou Azure Data Factory pro Ujistěte se, že zdrojový a cílový zůstane synchronizovaná. Tento přístup funguje, když je flexibilitu při změně nebo definování schématu cílové tabulky SQL. 

V tomto sloupci se používá službou Azure Data Factory pro účely opakovatelnost a v procesu Azure Data Factory nebyly provedeny žádné změny schématu tabulky. Způsob použití tohoto přístupu:

1. Definování sloupec typu **binárního souboru (32)** v cílové tabulce SQL. Měla by existovat žádné omezení na tomto sloupci. Název v tomto sloupci jako AdfSliceIdentifier budeme v tomto příkladu.


    Zdrojová tabulka:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Cílové tabulky: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

2. Ho použijte k aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory naplní tento sloupec podle jeho potřeba zajistit, že zdrojový a cílový zůstane synchronizovaná. Mimo tento kontext není vhodné používat hodnoty daného sloupce. 

Podobně jako mechanismus 1, aktivity kopírování automaticky vyčistí data pro danou řez z cílového tabulky SQL. Potom vloží data ze zdroje v do cílové tabulky. 

## <a name="next-steps"></a>Další postup
Zkontrolujte konektor následující články, dokončení příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
