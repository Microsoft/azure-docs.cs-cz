---
title: Opakovatelné kopírování ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak se vyhnout duplicity, i když je více než jednou spustit určitý řez, který kopíruje data.
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
ms.openlocfilehash: 450e6a180b104d8f384fd08cc7c4cafcd53b4453
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021335"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Opakovatelné kopírování ve službě Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Opakovatelné čtení z relačních zdrojů
Při kopírování dat z relačních dat ukládá, mějte opakovatelnosti aby se zabránilo neúmyslnému výsledků. Ve službě Azure Data Factory můžete znovu spustit řezu ručně. Zásady opakování pro datovou sadu můžete také nakonfigurovat tak, aby určitý řez se znovu spustí, když dojde k chybě. V obou případech se znovu spustí určitý řez, musíte zajistit, že stejná data je pro čtení bez ohledu na to kolikrát spustit určitý řez.  
 
> [!NOTE]
> Následující ukázky jsou pro Azure SQL, ale platí pro jakékoli úložiště dat, který podporuje obdélníkové datové sady. Možná budete muset upravit **typ** zdroje a **dotazu** vlastnosti (například: dotaz namísto sqlReaderQuery) data ukládat.   

Obvykle při čtení z relačního úložiště, budete chtít číst pouze data odpovídající této řez. Způsob, jak tomu by pomocí WindowStart a WindowEnd systémové proměnné k dispozici ve službě Azure Data Factory. Přečtěte si informace o proměnných a funkcí ve službě Azure Data Factory tady v [Azure Data Factory – funkce a systémové proměnné](data-factory-functions-variables.md) článku. Příklad: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Tento dotaz načítá data, která spadá do rozsahu řez doba trvání (WindowStart -> WindowEnd) z tabulky MyTable. Znovu spustit tento řezu by také vždy ujistěte se, že je pro čtení na stejná data. 

V jiných případech může chtít přečíst celou tabulku a může definovat sqlReaderQuery následujícím způsobem:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Opakovatelné zápis SqlSink
Při kopírování dat do **Azure SQL nebo SQL Server** z jiných úložišť dat, budete muset opakovatelnosti mějte na paměti, aby se zabránilo neúmyslnému výsledků. 

Při kopírování dat do služby Azure SQL nebo SQL Server Database, aktivita kopírování připojí data do tabulky jímky ve výchozím nastavení. Řekněme, se kopírování dat ze souboru CSV (hodnoty oddělené čárkami) obsahující dva záznamy v databázi Azure SQL nebo SQL serveru v následující tabulce. Při spuštění řezu dva záznamy se zkopírují do tabulky SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že byly nalezeny chyby ve zdrojovém souboru a aktualizované množství trubky snížit z 2 až 4. Pokud znovu spustíte datový řez za toto období ručně, najdete dva nové záznamy připojenou k databázi Azure SQL nebo SQL Server. Tento příklad předpokládá, že žádný sloupec v tabulce má omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Chcete-li toto chování vyhnout, musíte zadat UPSERT sémantiku pomocí jedné z následujících dvou mechanismů:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanismus 1: použití sqlWriterCleanupScript
Můžete použít **sqlWriterCleanupScript** vlastnost vyčistit data z tabulky jímky před vložením dat při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Při spuštění určitý řez, je nejprve spustit skript pro vyčištění odstranit data, která odpovídá řez z tabulky SQL. Aktivita kopírování dat poté vloží do tabulky SQL. Pokud řez znovu spustit, množství se aktualizuje podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že záznam pro plochý podložku Odebereme z původní sdílený svazek clusteru. Potom znovu spustit řez byste mohli vytvořit následující výsledek: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Aktivita kopírování spustili skript pro vyčištění odstranit odpovídajících dat pro tento řez. Potom jej číst vstup ze souboru csv (což je obsaženy jenom jednomu záznamu) a vloženy do tabulky. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanismus 2: použití sliceIdentifierColumnName
> [!IMPORTANT]
> V současné době sliceIdentifierColumnName není podporována pro službu Azure SQL Data Warehouse. 

Druhý mechanismus opakovatelnosti dosáhnout spočívá v použití vyhrazené sloupec (sliceIdentifierColumnName) v cílové tabulce. V tomto sloupci se použije službou Azure Data Factory k zajištění, že zdroj a cíl zůstat synchronizováni. Tento přístup funguje, když je flexibilitu při změně nebo definování schématu cílové tabulky SQL. 

Tento sloupec se službou Azure Data Factory používá pro účely opakovatelnost a v procesu služby Azure Data Factory neprovede žádné změny schématu tabulky. Způsob, jak pomocí tohoto postupu:

1. Definovat sloupec typu **binární soubor (32)** v cílové tabulce SQL. Měla by existovat bez omezení na tomto sloupci. V tomto příkladu pojmenujeme v tomto sloupci jako AdfSliceIdentifier.


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

2. Použije v aktivitě kopírování následujícím způsobem:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory naplní v tomto sloupci podle jeho potřeba zajistit, že zdroj a cíl zůstat synchronizováni. Mimo tento kontext by neměl použít hodnoty daného sloupce. 

Podobně jako mechanismus 1, aktivita kopírování automaticky vyčistí data pro danou řezu cílové tabulky SQL. Potom vkládá data ze zdroje do do cílové tabulky. 

## <a name="next-steps"></a>Další postup
Kontrola konektoru následující články, které kompletní příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
