---
title: Použití uložených procedur v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro implementaci uložené procedury v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 5659e8f29d87c48c447a5cb81c836b0be9dabd45
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Použití uložených procedur v SQL Data Warehouse
Tipy pro implementaci uložené procedury v Azure SQL Data Warehouse na vývoj řešení.

## <a name="what-to-expect"></a>Co můžete očekávat

SQL Data Warehouse podporuje mnoho funkcí T-SQL, které se používají v systému SQL Server. Je důležité existují Škálováním na více systémů specifické funkce, které můžete maximalizovat výkon vašeho řešení.

Zachování škálování a výkonu, služby SQL Data Warehouse jsou však také některé funkce a funkce, které mají rozdíly v chování a ostatní uživatele, které nejsou podporovány.


## <a name="introducing-stored-procedures"></a>Představení uložené procedury
Uložené procedury jsou vhodné pro zapouzdření kódu SQL; ukládání blízko data v datovém skladu. Uložené procedury pomoci vývojářům rozčlenění moduly svá řešení zapouzdřením kód do spravovatelných jednotek; usnadnění větší – opětovné použití kódu. Každý uložené procedury mohou také přijímat parametry tak, aby byly i flexibilnější.

SQL Data Warehouse poskytuje zjednodušenou a efektivní uložené procedury implementaci. Největší rozdíl oproti systému SQL Server je, že uložená procedura není předem zkompilovaný kód. V datových skladů je malá oproti době potřebné ke spouštění dotazů na velkých objemů dat dobu kompilace. Je důležité zajistit, že kód uložené procedury je správně optimalizovaná pro rozsáhlé dotazy. Cílem je uložit hodin, minut a sekund, není milisekundách. Je proto více vhodné zamyslet nad uložené procedury jako kontejnery pro logiku SQL.     

Když SQL Data Warehouse provede uložené procedury, jsou příkazy SQL analyzovat, přeložit a optimalizované za běhu. Během tohoto procesu je každý příkaz převeden na distribuované dotazy. Kód SQL, který je y u dat spustit, se liší od odeslání dotazu.

## <a name="nesting-stored-procedures"></a>Vnoření uložené procedury
Pokud uložené procedury volat jiné uložené procedury nebo provést dynamické SQL, pak vnitřní uložené procedury nebo volání kódu se označuje jako vnořený.

SQL Data Warehouse podporuje maximálně osm vnořených úrovní. To se mírně liší k systému SQL Server. Úroveň vnoření v systému SQL Server je 32.

Volání uložené procedury nejvyšší úrovně se rovná vnořit úroveň 1.

```sql
EXEC prc_nesting
```
Pokud uložená procedura také provede volání jiného EXEC, zvyšuje úroveň vnoření ke dvěma.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Pokud se druhý postup pak provede některé dynamické SQL, zvyšuje úroveň vnoření na tři.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Všimněte si, že SQL Data Warehouse v současné době nepodporuje [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Je třeba sledovat úroveň vnoření. Není pravděpodobné, abyste překračují limit úrovně vnoření osm, ale pokud to uděláte, budete muset přepracování kódu podle úrovně vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>PŘÍKAZ INSERT... SPUŠTĚNÍ
SQL Data Warehouse nepovoluje využívat sady výsledků dotazu uložené proceduře pomocí příkazu INSERT. Je však alternativní způsob, který můžete použít. Příklad najdete v článku na [dočasných tabulek](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Omezení
Existují některé aspekty Transact-SQL uložené procedury, které nejsou implementované v SQL Data Warehouse.

Jsou to tyto:

* dočasně uložených procedur
* číslované uložené procedury
* Rozšířené uložené procedury
* CLR uložené procedury
* 
* možnost šifrování
* možnost replikace
* Parametry s hodnotou tabulky
* parametry jen pro čtení
* výchozí parametry
* kontexty provádění
* Return – příkaz

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj, najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

