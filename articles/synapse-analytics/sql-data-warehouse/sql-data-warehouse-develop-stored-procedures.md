---
title: Použití uložených procedur
description: Tipy pro implementaci uložených procedur v Azure SQL Data Warehouse pro vývoj řešení.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/02/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 83c3187c580bda33df8780a0e36f0fb9f2a4f484
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351563"
---
# <a name="using-stored-procedures-in-sql-data-warehouse"></a>Použití uložených procedur v datovém skladu SQL
Tipy pro implementaci uložených procedur v Azure SQL Data Warehouse pro vývoj řešení.

## <a name="what-to-expect"></a>Co očekávat

SQL Data Warehouse podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Ještě důležitější je, že existují specifické funkce škálování, které můžete použít k maximalizaci výkonu vašeho řešení.

Chcete-li však zachovat škálování a výkon datového skladu SQL, existují také některé funkce a funkce, které mají rozdíly v chování a další, které nejsou podporovány.


## <a name="introducing-stored-procedures"></a>Zavedení uložených procedur
Uložené procedury jsou skvělý způsob, jak zapouzdřit kód SQL; ukládání v blízkosti dat v datovém skladu. Uložené procedury pomáhají vývojářům modularizovat jejich řešení zapouzdřením kódu do spravovatelných jednotek; usnadnění větší opětovné použitelnosti kódu. Každá uložená procedura může také přijímat parametry, aby byly ještě flexibilnější.

SQL Data Warehouse poskytuje zjednodušenou a zjednodušenou implementaci uložené procedury. Největší rozdíl ve srovnání s SQL Server je, že uložená procedura není předem zkompilovaný kód. V datových skladech je čas kompilace malý ve srovnání s časem potřebným ke spuštění dotazů na velkých objemech dat. Je důležitější zajistit, aby byl kód uložené procedury správně optimalizován pro velké dotazy. Cílem je ušetřit hodiny, minuty a sekundy, nikoli milisekundy. Je proto užitečnější myslet uložené procedury jako kontejnery pro logiku SQL.     

Když SQL Data Warehouse provede uloženou proceduru, příkazy SQL jsou analyzovány, přeloženy a optimalizovány za běhu. Během tohoto procesu je každý příkaz převeden na distribuované dotazy. Kód SQL, který je spuštěn proti datům, se liší od odeslaných dotazů.

## <a name="nesting-stored-procedures"></a>Vnoření uložených procedur
Při uložené procedury volání jiných uložených procedur nebo spustit dynamické SQL, pak vnitřní uložené procedury nebo vyvolání kódu se říká, že je vnořené.

SQL Data Warehouse podporuje maximálně osm úrovní vnoření. To se mírně liší od SQL Server. Úroveň hnízda v SQL Serveru je 32.

Volání uložené procedury nejvyšší úrovně se rovná úrovni vnoření 1.

```sql
EXEC prc_nesting
```
Pokud uložená procedura také provede další volání EXEC, úroveň vnoření se zvýší na dvě.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Pokud druhý postup pak provede některé dynamické SQL, úroveň hnízda se zvýší na tři.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Poznámka: SQL Data Warehouse aktuálně nepodporuje [@@NESTLEVEL](/sql/t-sql/functions/nestlevel-transact-sql). Musíte sledovat úroveň hnízda. Je nepravděpodobné, že překročíte limit úrovně osmi vnoření, ale pokud tak učiníte, budete muset přepracovat kód tak, aby odpovídal úrovním vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>Vložit.. Provést
SQL Data Warehouse neumožňuje využívat sadu výsledků uložené procedury s příkazem INSERT. Existuje však alternativní přístup, který můžete použít. Další informace naleznete v článku o [dočasných tabulkách](sql-data-warehouse-tables-temporary.md). 

## <a name="limitations"></a>Omezení
Existují některé aspekty Transact-SQL uložené procedury, které nejsou implementovány v databázi DAT SQL.

Jsou to tyto:

* dočasně uložené procedury
* číslované uložené procedury
* rozšířené uložené procedury
* Postupy uložené v CLR
* možnost šifrování
* možnost replikace
* parametry s hodnotou tabulky
* Parametry jen pro čtení
* výchozí parametry
* kontexty spuštění
* return – příkaz

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md).

