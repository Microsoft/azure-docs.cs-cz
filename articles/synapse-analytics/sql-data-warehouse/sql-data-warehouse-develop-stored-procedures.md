---
title: Používejte uložené procedury.
description: Tipy pro vývoj řešení implementací uložených procedur pro vyhrazené fondy SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/02/2019
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: e28eeac131c737d673cac947a3fda30239180a62
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98673582"
---
# <a name="using-stored-procedures-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Použití uložených procedur pro vyhrazené fondy SQL ve službě Azure synapse Analytics

Tento článek popisuje tipy pro vývoj vyhrazených řešení fondu SQL implementací uložených procedur.

## <a name="what-to-expect"></a>Co očekávat

Vyhrazený fond SQL podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Důležitější je, že existují určité funkce škálované na více instancí, které můžete použít k maximalizaci výkonu řešení.

Kromě toho, abyste mohli udržovat škálování a výkon vyhrazeného fondu SQL, existují další funkce a funkce, které mají rozdíly v chování.

## <a name="introducing-stored-procedures"></a>Představení uložených procedur

Uložené procedury jsou skvělým způsobem, jak zapouzdřit kód SQL, který je uložen blízko pro vaše vyhrazená data fondu SQL. Uložené procedury také usnadňují vývojářům naplánovat modularizaci jejich řešení zapouzdřením kódu do spravovatelných jednotek, což usnadňuje větší použitelnost kódu. Každý uložený postup může také přijímat parametry, aby byly ještě flexibilnější.

Vyhrazený fond SQL poskytuje zjednodušenou a zjednodušenou implementaci uložených procedur. Největší rozdíl v porovnání s SQL Server je, že uložená procedura není předem kompilovaný kód.

Obecně platí, že pro datové sklady je čas kompilace malý v porovnání s časem potřebným ke spouštění dotazů na velké objemy dat. Je důležitější, abyste zajistili, že se kód uložené procedury správně optimalizuje u velkých dotazů.

> [!TIP]
> Cílem je ušetřit hodiny, minuty a sekundy, ne milisekundy. Je proto vhodné si představit uložené procedury jako kontejnery pro SQL logiku.

Když vyhrazený fond SQL spustí uloženou proceduru, příkazy SQL jsou analyzovány, přeloženy a optimalizovány v době běhu. Během tohoto procesu se každý příkaz převede na distribuované dotazy. Kód SQL, který je proveden na základě dat, je jiný než odeslaný dotaz.

## <a name="nesting-stored-procedures"></a>Vnořování uložených procedur

Když uložené procedury volají jiné uložené procedury nebo spustí dynamický příkaz SQL, pak je vnitřní uložená procedura nebo volání kódu označována jako vnořená.

Vyhrazený fond SQL podporuje maximálně osm úrovní vnoření. Na rozdíl od je úroveň vnoření v SQL Server 32.

Volání uložené procedury na nejvyšší úrovni je rovno vnořené úrovni 1.

```sql
EXEC prc_nesting
```

Pokud uložená procedura také provede další volání EXEC, úroveň vnořování se zvýší na 2.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```

Pokud druhý postup následně provede nějaký dynamický SQL, úroveň vnořování se zvýší na tři.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Vyhrazený fond SQL momentálně nepodporuje [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). V takovém případě je nutné sledovat úroveň vnoření. Je pravděpodobné, že byste překročili osm omezení na úrovni vnoření. Ale pokud to uděláte, musíte znovu pracovat s kódem, aby odpovídal úrovním vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>INSERT..EXEROZTOMILÁ

Vyhrazený fond SQL neumožňuje využití sady výsledků uložených procedur pomocí příkazu INSERT. Existuje však alternativní přístup, který můžete použít. Příklad najdete v článku o [dočasných tabulkách](sql-data-warehouse-tables-temporary.md).

## <a name="limitations"></a>Omezení

Existují některé aspekty uložených procedur jazyka Transact-SQL, které nejsou implementovány ve vyhrazeném fondu SQL, což je následující:

* dočasné uložené procedury
* číslované uložené procedury
* rozšířené uložené procedury
* Uložené procedury CLR
* možnost šifrování
* možnost replikace
* parametry s hodnotou tabulky
* parametry jen pro čtení
* výchozí parametry
* kontexty spuštění
* return – příkaz

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
