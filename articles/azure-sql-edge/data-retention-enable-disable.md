---
title: Povolit a zakázat zásady uchovávání dat – Azure SQL Edge
description: Přečtěte si, jak povolit a zakázat zásady uchovávání dat v Azure SQL Edge.
keywords: SQL Edge, uchovávání dat
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: 9408aaa8fd5b677f012392ef4bd51c8826650eee
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93395151"
---
# <a name="enable-and-disable-data-retention-policies"></a>Povolit a zakázat zásady uchovávání dat

Toto téma popisuje, jak povolit a zakázat zásady uchovávání dat pro databázi a tabulku. 

## <a name="enable-data-retention-for-a-database"></a>Povolení uchovávání dat pro databázi

Následující příklad ukazuje, jak povolit uchovávání dat pomocí [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options).

```sql
ALTER DATABASE [<DatabaseName>] SET DATA_RETENTION  ON;
```

## <a name="check-if-data-retention-is-enabled-for-a-database"></a>Zkontroluje, jestli je pro databázi povolené uchovávání dat.

Pomocí následujícího příkazu můžete ověřit, jestli je pro databázi povolené uchovávání dat.
```sql
SELECT is_data_retention_enabled, name
FROM sys.databases;
```

## <a name="enable-data-retention-for-a-table"></a>Povolení uchovávání dat pro tabulku

Pro každou tabulku, pro kterou chcete automaticky vyprázdnit data, musí být povoleno uchovávání dat. Pokud je v databázi a tabulce povolené uchovávání dat, úloha systému na pozadí bude pravidelně kontrolovat tabulku, aby identifikovala a odstranila všechny zastaralé (zastaralé) řádky. Uchovávání dat lze v tabulce povolit buď [při vytvoření tabulky, nebo pomocí](/sql/t-sql/statements/create-table-transact-sql) [příkazu ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).

Následující příklad ukazuje, jak povolit uchovávání dat pro tabulku pomocí funkce [vytvořit tabulku](/sql/t-sql/statements/create-table-transact-sql). 

```sql
CREATE TABLE [dbo].[data_retention_table] 
(
[dbdatetime2] datetime2(7), 
[product_code] int, 
[value] char(10),  
CONSTRAINT [pk_current_data_retention_table] PRIMARY KEY CLUSTERED ([product_code])
) WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )
```

`WITH (DATA_DELETION = ON ( FILTER_COLUMN = [dbdatetime2], RETENTION_PERIOD = 1 day ) )`Část příkazu CREATE TABLE nastaví uchovávání dat v tabulce. Příkaz používá následující požadované parametry. 

- DATA_DELETION – určuje, zda je uchovávání dat ZAPNUTo nebo vypnuto.
- FILTER_COLUMN – název sloupce v tabulce, který se použije ke zjištění, jestli jsou řádky zastaralé nebo ne. Sloupec filtru může být pouze sloupec s těmito datovými typy. 
    - Datum
    - SmallDateTime
    - DateTime
    - DateTime2
    - DateTimeOffset
- RETENTION_PERIOD – celočíselná hodnota následovaná popisovačem jednotky. Povolené jednotky jsou den, dny, týden, týdny, měsíc, měsíce, rok a roky.

Následující příklad ukazuje, jak povolit uchovávání dat pro tabulku pomocí [příkazu ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql).  

```sql
Alter Table [dbo].[data_retention_table]
SET (DATA_DELETION = On (FILTER_COLUMN = [timestamp], RETENTION_PERIOD = 1 day))
```

## <a name="check-if-data-retention-is-enabled-for-a-table"></a>Zkontroluje, jestli je pro tabulku povolené uchovávání dat.

Následující příkaz slouží ke kontrole tabulek, pro které je povoleno uchovávání dat.

```sql
select name, data_retention_period, data_retention_period_unit from sys.tables
```

Hodnota data_retention_period =-1 a data_retention_period_unit jako nekonečno znamená, že pro tabulku není nastavena doba uchovávání dat.

Následující dotaz slouží k identifikaci sloupce používaného jako filter_column pro uchovávání dat. 

```sql
Select name from sys.columns
where is_data_deletion_filter_column =1 
and object_id = object_id(N'dbo.data_retention_table', N'U')
```

## <a name="corelating-db-and-table-data-retention-settings"></a>Nastavení uchovávání dat v DATABÁZÍch a tabulkách

Nastavení uchovávání dat v databázi a tabulce se používají ve spojení s cílem určit, jestli se má v tabulkách spustit autocleanup pro staré řádky. 

|Možnost databáze | Možnost tabulky | Chování |
|----------------|--------------|----------|
| OFF | OFF | Zásady uchovávání dat jsou zakázané a je zakázané automatické i ruční vyčištění starých záznamů.|
| OFF | ON  | Zásady uchovávání dat jsou pro tabulku povolené. Automatické čištění zastaralých záznamů je zakázané, ale k vyčištění zastaralých záznamů se dá použít manuální metoda vyčištění. |
| ON | OFF | Zásady uchovávání dat jsou povolené na úrovni databáze. Vzhledem k tomu, že je tato možnost zakázána na úrovni tabulky, neexistuje žádné vyčištění na základě uchování starých řádků.|
| ON | ON | Zásady uchovávání dat jsou povolené pro databáze i tabulky. Automatické čištění zastaralých záznamů je povoleno. |

## <a name="disable-data-retention-on-a-table"></a>Zakázat uchovávání dat v tabulce 

Uchovávání dat lze v tabulce zakázat pomocí [příkazu ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql). Následující příkaz lze použít k zakázání uchovávání dat v tabulce.

```sql
Alter Table [dbo].[data_retention_table]
Set (DATA_DELETION = OFF)
```

## <a name="disable-data-retention-on-a-database"></a>Zakázání uchovávání dat v databázi

Uchovávání dat lze v tabulce zakázat pomocí [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql-set-options). Následující příkaz lze použít k zakázání uchovávání dat v databázi.

```sql
ALTER DATABASE <DatabaseName> SET DATA_RETENTION  OFF;
```

## <a name="next-steps"></a>Další kroky
- [Uchovávání dat a automatické vyprazdňování dat](data-retention-overview.md)
- [Správa historických dat pomocí zásad uchovávání informací](data-retention-cleanup.md)