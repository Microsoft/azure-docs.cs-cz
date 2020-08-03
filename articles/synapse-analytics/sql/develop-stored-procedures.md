---
title: Použití uložených procedur
description: Tipy pro implementaci uložených procedur v synapse fondu SQL (datový sklad) pro vývoj řešení
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 2b106a28514179d9be43b3c56ea8030eaf7ba13f
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87494991"
---
# <a name="use-stored-procedures-in-sql-pool"></a>Použití uložených procedur ve fondu SQL

Tipy pro implementaci uložených procedur v synapse fondu SQL (datový sklad) pro vývoj řešení

## <a name="what-to-expect"></a>Co očekávat

Fond SQL podporuje mnoho funkcí T-SQL, které se používají v SQL Server. Důležitější je, že existují určité funkce škálované na více instancí, které můžete použít k maximalizaci výkonu řešení.

Pokud ale chcete zachovat rozsah a výkon fondu SQL, existují i některé funkce a funkce, které mají rozdíly v chování a jiné, které se nepodporují.

## <a name="stored-procedures-in-sql-pool"></a>Uložené procedury ve fondu SQL

Uložené procedury jsou skvělým způsobem, jak zapouzdření kódu SQL; Uložte je blízko do vašich dat v datovém skladu. Uložené procedury mohou vývojářům naplánovat modularizaci svá řešení zapouzdřením kódu do spravovatelných jednotek. usnadnění větší použitelnosti kódu. Každý uložený postup může také přijímat parametry, aby byly ještě flexibilnější.

Fond SQL poskytuje zjednodušenou a zjednodušenou implementaci uložených procedur. Největší rozdíl v porovnání s SQL Server je, že uložená procedura není předem zkompilován kód. V datových skladech je čas kompilace malý v porovnání s časem potřebným ke spouštění dotazů na velké objemy dat. Je důležité zajistit, aby byl kód uložené procedury správně optimalizován pro velké dotazy. Cílem je ušetřit hodiny, minuty a sekundy, ne milisekundy. Je proto vhodnější si představit uložené procedury jako kontejnery pro SQL logiku.

Když fond SQL spouští vaši uloženou proceduru, příkazy SQL jsou analyzovány, přeloženy a optimalizovány za běhu. Během tohoto procesu se každý příkaz převede na distribuované dotazy. Kód SQL, který je proveden na základě dat, je jiný než odeslaný dotaz.

## <a name="nesting-stored-procedures"></a>Vnořování uložených procedur

Když uložené procedury volají jiné uložené procedury nebo spustí dynamický příkaz SQL, pak je vnitřní uložená procedura nebo volání kódu označována jako vnořená.

Fond SQL podporuje maximálně osm úrovní vnoření. To se mírně liší od SQL Server. Úroveň vnoření v SQL Server je 32.

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

> [!NOTE]
> Fond SQL momentálně nepodporuje [@ @NESTLEVEL ](/sql/t-sql/functions/nestlevel-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Je nutné sledovat úroveň vnoření. Je pravděpodobné, že byste překročili osm omezení na úrovni vnoření, ale pokud to uděláte, budete muset znovu pracovat svůj kód, aby odpovídal úrovním vnoření v rámci tohoto limitu.

## <a name="insertexecute"></a>INSERT..EXEROZTOMILÁ

Fond SQL vám neumožňuje zpracovat sadu výsledků uložené procedury pomocí příkazu INSERT. Existuje však alternativní přístup, který můžete použít. Příklad najdete v článku o [dočasných tabulkách](develop-tables-temporary.md).

## <a name="limitations"></a>Omezení

Existují některé aspekty uložených procedur v jazyce Transact-SQL, které nejsou implementovány ve fondu SQL.

Jsou to tyto:

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

Další tipy pro vývoj najdete v tématu [Přehled vývoje](develop-overview.md).
