---
title: "Načtení dat do SQL Data Warehouse pomocí nástroje příkazového řádku bcp | Dokumentace Microsoftu"
description: "Zjistěte, co je bcp a jak tento nástroj používat pro scénáře datových skladů."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 146c6fdada651551c05b2cbcadc3e1248a40b613
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2018
---
# <a name="load-data-with-bcp"></a>Načtení dat pomocí bcp

**[bcp](/sql/tools/bcp-utility.md)** je nástroj příkazového řádku pro hromadné načítání, který umožňuje kopírovat data mezi SQL Serverem, datovými soubory a SQL Data Warehouse. Pomocí bcp můžete importovat velké počty řádků do tabulek SQL Data Warehouse nebo exportovat data z tabulek SQL Serveru do datových souborů. S výjimkou případu, kdy se nástroj bcp používá s parametrem queryout, nepotřebujete k použití nástroje bcp žádné znalosti jazyka Transact-SQL.

Nástroj bcp představuje rychlý a snadný způsob, jak přesunout menší datové sady z databáze SQL Data Warehouse nebo do ní. Přesné množství dat, které se doporučuje načítat/extrahovat přes bcp, závisí na síťovém připojení k Azure.  Malé tabulky dimenzí se pomocí bcp načítají a extrahují snadno. Pro načítání a extrahování velkých objemů dat se však namísto bcp doporučuje použít nástroj Polybase. Nástroj PolyBase je určený pro architekturu se značným objemem paralelního zpracování v SQL Data Warehouse.

Pomocí nástroje bcp můžete:

* Načítat data do SQL Data Warehouse pomocí nástroje příkazového řádku
* Extrahovat data z SQL Data Warehouse pomocí nástroje příkazového řádku

V tomto kurzu:

* Proběhne import dat do tabulky pomocí příkazu bcp in.
* Proběhne export dat z tabulky pomocí příkazu bcp out.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Požadavky
Pro jednotlivé kroky v tomto kurzu budete potřebovat:

* Databázi SQL Data Warehouse
* Nainstalované nástroje příkazového řádku bcp a sqlcmd Můžete si je stáhnout z webu [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Data ve formátu ASCII nebo UTF-16
Pokud pro tento kurz používáte svoje vlastní data, musí vaše data používat kódování ASCII nebo UTF-16, protože bcp nepodporuje kódování UTF-8. 

PolyBase podporuje UTF-8, ale zatím nepodporuje UTF-16. Pokud chcete používat bcp pro exportování dat a potom PolyBase pro načítání dat, je třeba, abyste data po vyexportování z SQL Serveru převedli do kódování UTF-8. 

## <a name="import-data-into-sql-data-warehouse"></a>Import dat do SQL Data Warehouse
V tomto kurzu vytvoříte tabulku v Azure SQL Data Warehouse a naimportujete do ní data.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Krok 1: Vytvoření tabulky v Azure SQL Data Warehouse
Pomocí příkazu sqlcmd na příkazovém řádku spusťte následující dotaz, který vytvoří tabulku ve vaší instanci:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

Další informace o vytvoření tabulky najdete v článku [Přehled tabulek](sql-data-warehouse-tables-overview.md) nebo v informacích o syntaxi příkazu [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

### <a name="step-2-create-a-source-data-file"></a>Krok 2: Vytvoření zdrojového datového souboru
Otevřete Poznámkový blok a zkopírujte následující řádky dat do nového textového souboru. Pak tento soubor uložte do místního dočasného adresáře C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Je důležité pamatovat na to, že nástroj bcp.exe nepodporuje kódování souborů UTF-8. V případě použití nástroje bcp.exe použijte soubory v kódování ASCII nebo UTF-16.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Krok 3: Připojení a import dat
Při použití nástroje bcp můžete připojit a importovat data pomocí následujícího příkazu (hodnoty podle potřeby nahraďte svými):

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

To, jestli byla data načtena, můžete ověřit spuštěním následujícího dotazu pomocí sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Dotaz by měl vrátit následující výsledky:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Krok 4: Vytvoření statistiky pro vaše nově načtená data
Posledním krokem po načtení dat je vytvoření nebo aktualizace statistiky. Tímto způsobem je možné zvýšit výkon při zpracování dotazů. Další informace najdete v článku [Statistika](sql-data-warehouse-tables-statistics.md). Následující příklad použití příkazu sqlcmd vytvoří statistiku pro tabulku, která obsahuje nově načtená data.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Export dat z SQL Data Warehouse
V tomto kurzu bude vytvořen datový soubor z tabulky v SQL Data Warehouse. Exportují se data, která jste naimportovali v předchozí části. Výsledky se uloží do souboru DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Krok 1: Export dat
Při použití nástroje bcp můžete připojit a vyexportovat data pomocí následujícího příkazu (hodnoty podle potřeby nahraďte svými):

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
To, že se data vyexportovala správně, můžete ověřit tak, že nový soubor otevřete. Data v souboru by měla odpovídat následujícímu textu:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Vzhledem k povaze distribuovaných systémů nemusí být pořadí dat napříč všemi databázemi SQL Data Warehouse stejné. Další možností je použít funkci **queryout** nástroje bcp, která místo vyexportování celé tabulky umožňuje zapsat extrakci dotazu.
> 
> 

## <a name="next-steps"></a>Další kroky
Informace o návrhu vlastního procesu načítání najdete v tématu [Přehled načítání](sql-data-warehouse-design-elt-data-loading).  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
