---
title: Opakující se kopírování v Azure Data Factory
description: Zjistěte, jak se vyhnout duplicitám, i když je řez, který kopíruje data, spuštěn více než jednou.
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
ms.openlocfilehash: 10476544e513b52567eb0ca0182039f2c5f482c3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89441625"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Opakující se kopírování v Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Opakované čtení z relačních zdrojů
Při kopírování dat z relačních úložišť dat mějte na paměti, že se vyhnete nezamýšleným výsledkům. V Azure Data Factory můžete řez znovu spustit ručně. Můžete také nakonfigurovat zásady opakování pro datovou sadu, aby se řez znovu opakoval, když dojde k selhání. Při opětovném spuštění řezu v obou případech je nutné zajistit, že stejná data budou čtena bez ohledu na to, kolikrát je řez spuštěn.  
 
> [!NOTE]
> Následující ukázky jsou pro Azure SQL, ale platí pro všechna úložiště dat, která podporují obdélníkové datové sady. Pro úložiště dat možná budete muset upravit **typ** zdroje a vlastnost **dotazu** (například: Query místo sqlReaderQuery).   

Obvykle při čtení z relačních úložišť chcete číst pouze data, která odpovídají danému řezu. K tomu je potřeba použít systémové proměnné WindowStart a WindowEnd, které jsou k dispozici v Azure Data Factory. Další informace o proměnných a funkcích v Azure Data Factory najdete v článku [Azure Data Factory funkce a systémové proměnné](data-factory-functions-variables.md) . Příklad: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Tento dotaz čte data, která spadají do rozsahu trvání řezu (WindowStart-> WindowEnd) z tabulky MyTable. Po opětovném spuštění tohoto řezu se taky vždycky zabezpečí čtení stejných dat. 

V jiných případech si možná budete chtít přečíst celou tabulku a můžete definovat sqlReaderQuery následujícím způsobem:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Opakovat zápis do SqlSink
Když kopírujete data do **Azure SQL/SQL Server** z jiných úložišť dat, je potřeba mít na paměti, abyste se vyhnuli nezamýšleným výsledkům. 

Když kopírujete data do databáze Azure SQL nebo SQL Server, aktivita kopírování ve výchozím nastavení připojí data do tabulky jímky. Řekněme, že kopírujete data ze souboru CSV (hodnoty oddělené čárkami), který obsahuje dva záznamy do následující tabulky v databázi SQL Azure/SQL Server. Po spuštění řezu se tyto dva záznamy zkopírují do tabulky SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Předpokládejme, že ve zdrojovém souboru byly zjištěny chyby a aktualizované množství z trubky z 2 na 4. Pokud ručně znovu spustíte datový řez pro dané období, najdete dva nové záznamy připojené k databázi Azure SQL/SQL Server. V tomto příkladu se předpokládá, že žádný ze sloupců v tabulce nemá omezení primárního klíče.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Chcete-li se tomuto chování vyhnout, je nutné určit sémantiku UPSERT pomocí jednoho z následujících dvou mechanismů:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mechanismus 1: použití sqlWriterCleanupScript
Vlastnost **sqlWriterCleanupScript** můžete použít k vyčištění dat z tabulky jímky před vložením dat při spuštění řezu. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Při spuštění řezu se nejprve spustí čisticí skript, který odstraní data, která odpovídají řezu v tabulce SQL. Aktivita kopírování pak vloží data do tabulky SQL. V případě opětovného spuštění řezu se množství aktualizuje podle potřeby.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Předpokládejme, že se záznam plochého mycího pračky odebírá z původního sdíleného svazku clusteru. Pak řez znovu spustí následující výsledek: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

Aktivita kopírování spustila skript vyčištění, který odstraní odpovídající data pro daný řez. Pak Přečtěte vstup ze sdíleného svazku clusteru (který pak obsahuje jenom jeden záznam) a vloží ho do tabulky. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mechanismus 2: použití sliceIdentifierColumnName
> [!IMPORTANT]
> V současné době se pro Azure synapse Analytics (dřív SQL Data Warehouse) nepodporuje sliceIdentifierColumnName. 

Druhým mechanismem, který se má dosáhnout opakovatelnosti, je vytvoření vyhrazeného sloupce (sliceIdentifierColumnName) v cílové tabulce. Tento sloupec Azure Data Factory použít k tomu, aby bylo zajištěno, že zůstane synchronizován zdroj a cíl. Tento přístup funguje v případě, že dojde k flexibilitě při změně nebo definování schématu cílové tabulky SQL. 

Tento sloupec se používá Azure Data Factory pro účely opakovatelnosti a v procesu Azure Data Factory neprovede žádné změny schématu v tabulce. Způsob použití tohoto přístupu:

1. Definujte sloupec typu **Binary (32)** v cílové tabulce SQL. Pro tento sloupec by neměla existovat žádná omezení. Pojďme tento sloupec pojmenovat jako AdfSliceIdentifier pro tento příklad.


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

Azure Data Factory naplní tento sloupec podle potřeby, aby se zajistila synchronizace zdroje a cíle. Hodnoty tohoto sloupce by se neměly používat mimo tento kontext. 

Podobně jako mechanismus – aktivita kopírování automaticky vyčistí data pro daný řez z cílové tabulky SQL. Pak vloží data ze zdroje do cílové tabulky. 

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články konektoru, které jsou kompletními příklady JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
