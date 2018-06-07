---
title: Načtení dat ze souboru CSV do Azure SQL Database (bcp) | Microsoft Docs
description: Pro malá množství dat se k importu dat do databáze SQL Azure používá bcp.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.topic: conceptual
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: de25b3318a91a5dbf99a5a9c26af209224177f0f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34649845"
---
# <a name="load-data-from-csv-into-azure-sql-database-flat-files"></a>Načtení dat ze souboru CSV do Azure SQL Database (ploché soubory)
Nástroj příkazového řádku bcp můžete použít k importu dat ze souboru CSV do databáze Azure SQL Database.

## <a name="before-you-begin"></a>Než začnete
### <a name="prerequisites"></a>Požadavky
Chcete-li provést kroky v tomto článku, je třeba:

* Logický server a databáze Azure SQL Database
* Nainstalovaný nástroj příkazového řádku bcp
* Nainstalovaný nástroj příkazového řádku sqlcmd

Nástroje bcp a sqlcmd si můžete stáhnout z webu [Stažení softwaru společnosti Microsoft][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Data ve formátu ASCII nebo UTF-16
Pokud pro tento kurz používáte svoje vlastní data, musí vaše data používat kódování ASCII nebo UTF-16, protože bcp nepodporuje kódování UTF-8. 

## <a name="1-create-a-destination-table"></a>1. Vytvoření cílové tabulky
Definujte tabulku ve službě SQL Database jako cílovou tabulku. Sloupce v tabulce musí odpovídat datům v jednotlivých řádcích vašeho datového souboru.

Pokud chcete vytvořit tabulku, otevřete okno příkazového řádku a pomocí sqlcmd.exe spusťte následující příkaz:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    ;
"
```


## <a name="2-create-a-source-data-file"></a>2. Vytvoření zdrojového datového souboru
Otevřete Poznámkový blok a zkopírujte následující řádky dat do nového textového souboru. Pak tento soubor uložte do místního dočasného adresáře C:\Temp\DimDate2.txt. Tato data jsou ve formátu ASCII.

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

(Volitelné) Pokud chcete z databáze SQL Serveru vyexportovat svoje vlastní data, otevřete příkazový řádek a spusťte následující příkaz. TableName, ServerName, DatabaseName, Username a Password nahraďte svými vlastními informacemi.

```bcp
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t , 
```

## <a name="3-load-the-data"></a>3. Načtení dat
Pokud chcete načíst data, otevřete příkazový řádek a spusťte následující příkaz, přičemž hodnoty parametrů Server Name (Název serveru), Database name (Název databáze), Username (Uživatelské jméno) a Password (Heslo) nahraďte svými vlastními informacemi.

```bcp
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ,
```

Pomocí tohoto příkazu ověřte, že se data načetla správně.

```bcp
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Výsledky by měly vypadat takto:

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

## <a name="next-steps"></a>Další postup
Postup migrace databáze serveru SQL Server naleznete v části [Migrace databáze serveru SQL Server](sql-database-cloud-migrate.md).

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
