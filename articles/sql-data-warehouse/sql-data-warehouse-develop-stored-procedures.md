---
title: Použití uložených procedur ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro provádění uložené procedury pro vývoj řešení ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ckarst
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 4cd8394104c72e8df53fa0c44e60037b4dc05976
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43301609"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Použití uložených procedur ve službě SQL Data Warehouse
Tipy pro provádění uložené procedury pro vývoj řešení ve službě Azure SQL Data Warehouse.

## <a name="what-to-expect"></a>Co můžete očekávat

SQL Data Warehouse podporuje mnoho funkcí T-SQL, které se používají v systému SQL Server. Důležitější je jsou specifické funkce škálování na víc systémů, které vám umožní maximalizovat výkon vašeho řešení.

Zachovat rozsah a výkon služby SQL Data Warehouse existuje ale také některé funkce, které mají behaviorální rozdíly a ostatní, které nejsou podporovány.


## <a name="introducing-stored-procedures"></a>Úvod do uložených procedur
Uložené procedury jsou skvělý způsob pro zapouzdření kódu SQL; uložením blízko vašich dat v datovém skladu. Uložené procedury pomoci vývojářům modularizaci svá řešení zapouzdřením kód do jednotkami; usnadnění větší opětovné použití kódu. Každou uloženou proceduru lze také přijímají parametry tak, aby byly ještě flexibilnější.

SQL Data Warehouse poskytuje zjednodušené a efektivní uložená procedura implementaci. Největší rozdíl oproti serveru SQL Server je, že uložená procedura není předkompilovaný kód. V datových skladech je malá porovnání čas potřebný ke spouštění dotazů na velkých objemů dat čas kompilace. Je důležitější Ujistěte se, že kód uložené procedury je správně optimalizované pro rozsáhlé dotazy. Cílem je ušetřit hodin, minut a sekund, nikoli MS. Proto je užitečnější uložené procedury můžete představit jako kontejnery pro logiku SQL.     

Při spuštění uložené procedury SQL Data Warehouse se příkazy SQL analyzovat, přeložit a optimalizované v době běhu. Během tohoto procesu je každý příkaz převeden na distribuovaných dotazů. Kód SQL, který se spouští na data se liší od odeslání dotazu.

## <a name="nesting-stored-procedures"></a>Vnoření uložené procedury
Při uložené procedury volání dalších uložených procedur, nebo spuštění dynamické SQL, pak vnitřní uloženou proceduru nebo vyvolání kód říká, že je vnořit.

SQL Data Warehouse podporuje maximálně osm úrovní vnoření. Tím se mírně liší na SQL Server. Úroveň vnoření v systému SQL Server je 32.

Volání uložené procedury nejvyšší úrovně odpovídá vnořit úrovně 1.

```sql
EXEC prc_nesting
```
Pokud uložená procedura také provede volání jiného EXEC, zvyšuje se úroveň vnoření do dvou.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Pokud se druhý postup pak provede některé dynamic SQL, zvyšuje se úroveň vnoření na tři.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Mějte na paměti, SQL Data Warehouse v současné době nepodporuje [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Je potřeba sledovat úrovně vnoření. Je nepravděpodobné, že by pro vás překročí limit osmi vnořených úrovní, ale pokud to uděláte, budete muset Přepracujte kódu podle úrovní vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>VLOŽIT... SPUŠTĚNÍ
SQL Data Warehouse neumožňuje využívat sada výsledků v uložené proceduře pomocí příkazu INSERT. Existuje ale alternativní způsob, který můžete použít. Příklad najdete v článku na [dočasné tabulky](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Omezení
Existují některé aspekty jazyka Transact-SQL, uložených procedur, které nejsou implementované ve službě SQL Data Warehouse.

Jsou to tyto:

* dočasně uložených procedur
* číslované uložené procedury
* Rozšířené uložené procedury
* CLR uložené procedury
* Možnost šifrování
* možnost replikace
* Parametry Table-valued
* parametry jen pro čtení
* výchozí parametry
* kontexty provádění
* Return – příkaz

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [přehled vývoje](sql-data-warehouse-overview-develop.md).

