---
title: Použití uložených procedur
description: Tipy pro vývoj řešení implementací uložené procedury v synapse fondu SQL.
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
ms.openlocfilehash: 3283fbeec2226a825625b4e3ede6942a609ae723
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633445"
---
# <a name="using-stored-procedures-in-synapse-sql-pool"></a>Použití uložených procedur v fondu SYNAPse SQL

Tento článek obsahuje tipy pro vývoj řešení fondu SQL implementací uložených procedur.

## <a name="what-to-expect"></a>Co očekávat

Fond SQL podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Ještě důležitější je, že existují specifické funkce škálování, které můžete použít k maximalizaci výkonu vašeho řešení.

Také vám pomohou udržovat škálování a výkon fondu SQL, existují další funkce a funkce, které mají rozdíly v chování.

## <a name="introducing-stored-procedures"></a>Zavedení uložených procedur

Uložené procedury jsou skvělý způsob, jak zapouzdřit kód SQL, který je uložen v blízkosti dat fondu SQL. Uložené procedury také pomáhají vývojářům modularizovat jejich řešení zapouzdřením kódu do spravovatelných jednotek, což usnadňuje větší opětovné použití kódu. Každá uložená procedura může také přijímat parametry, aby byly ještě flexibilnější.

Fond SQL poskytuje zjednodušenou a zjednodušenou implementaci uložené procedury. Největší rozdíl ve srovnání s SQL Server je, že uložená procedura není předem zkompilovaný kód.

Obecně platí pro datové sklady čas kompilace je malý ve srovnání s časpotřebný ke spuštění dotazů na velké objemy dat. Je důležitější zajistit, aby byl kód uložené procedury správně optimalizován pro velké dotazy.

> [!TIP]
> Cílem je ušetřit hodiny, minuty a sekundy, nikoli milisekundy. Takže je užitečné myslet uložené procedury jako kontejnery pro logiku SQL.

Když fond SQL provede uloženou proceduru, příkazy SQL jsou analyzovány, přeloženy a optimalizovány za běhu. Během tohoto procesu je každý příkaz převeden na distribuované dotazy. Kód SQL, který je spuštěn proti datům, se liší od odeslaných dotazů.

## <a name="nesting-stored-procedures"></a>Vnoření uložených procedur

Při uložené procedury volání jiných uložených procedur nebo spustit dynamické SQL, pak vnitřní uložené procedury nebo vyvolání kódu se říká, že je vnořené.

Fond SQL podporuje maximálně osm úrovní vnoření. Naproti tomu úroveň hnízda v SQL Serveru je 32.

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

Fond SQL aktuálně nepodporuje [@NESTLEVEL@](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest). Jako takový je třeba sledovat úroveň hnízda. Je nepravděpodobné, že překročíte limit úrovně osmi hnízd. Ale pokud tak učiníte, je třeba přepracovat kód tak, aby odpovídaly úrovně vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>Vložit.. Provést

Fond SQL neumožňuje využívat sadu výsledků uložené procedury s příkazem INSERT. Existuje však alternativní přístup, který můžete použít. Další informace naleznete v článku o [dočasných tabulkách](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Omezení

Existují některé aspekty Transact-SQL uložené procedury, které nejsou implementovány ve fondu SQL, které jsou následující:

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
