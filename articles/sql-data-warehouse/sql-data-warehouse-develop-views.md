---
title: Pomocí zobrazení jazyka T-SQL ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Tipy pro používání zobrazení jazyka T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: dba6d49590cc4064155e58784a166d3abbb19b6f
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468423"
---
# <a name="views-in-azure-sql-data-warehouse"></a>Zobrazení ve službě Azure SQL Data Warehouse
Tipy pro používání zobrazení jazyka T-SQL ve službě Azure SQL Data Warehouse pro vývoj řešení. 

## <a name="why-use-views"></a>Proč používat zobrazení?
Zobrazení lze použít různými způsoby a zlepšovat tak kvalitu vašeho řešení.  Tento článek se soustředí na několik příkladů toho, jak rozšířit vaše řešení s zobrazení, jakož i omezení, které je potřeba považovat za.

> [!NOTE]
> Syntaxe pro příkaz CREATE VIEW není popsané v tomto článku. Další informace najdete v tématu [příkaz CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql) dokumentaci.
> 
> 

## <a name="architectural-abstraction"></a>Abstrakce architektury
Běžným aplikačním postupem je vytvořit znovu vytvořit TABLE AS SELECT (CTAS) následované objekt přejmenování vzor při načítání dat pomocí tabulek.

Následující příklad přidá nové datum záznamy do dimenze kalendářního data. Všimněte si, jak je nová tabulka DimDate_New, vytvoření a potom přejmenovat k nahrazení původní verze v tabulce.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tento přístup však může vést k tabulkám, zobrazování a zmizení ze zobrazení uživatele, jakož i "tabulka neexistuje" chybové zprávy. Zobrazení je možné uživatelům poskytnout konzistentní prezentační vrstvy, zatímco objekty jsou přejmenovány. Poskytuje přístup k datům prostřednictvím zobrazení a uživatelé nepotřebují viditelnost na základní tabulky. Tato vrstva poskytuje konzistentní uživatelské prostředí, zatímco služba zajistí, že datový sklad Návrháři můžete rozvíjet datového modelu. Možnost vyvíjet podkladové tabulky znamená, že návrháře můžete použít CTAS pro zajištění maximálního výkonu během procesu načítání dat.   

## <a name="performance-optimization"></a>Optimalizace výkonu
Zobrazení můžete také využít k vynucení optimalizováno pro výkon spojení mezi tabulkami. Třeba spojovací kritéria pro minimalizaci přesun dat v rámci zobrazení začlenit redundantní distribučního klíče. Další výhodou zobrazení může být k vynucení konkrétní dotaz nebo spojovacího pomocný parametr. Použití zobrazení tímto způsobem zaručuje, že optimální způsobem vyloučení uživatelé pamatovat správné konstrukce pro jejich spojení se vždy provádějí spojení.

## <a name="limitations"></a>Omezení
Zobrazení v SQL Data Warehouse se ukládají jako pouze metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje žádná vazba možnost schématu
* Základní tabulky nelze aktualizovat prostřednictvím zobrazení
* Zobrazení nelze vytvořit přes dočasné tabulky
* Neexistuje žádná podpora pro ROZBALENÍ / NOEXPAND pomocné parametry
* Nejsou žádné indexované zobrazení ve službě SQL Data Warehouse

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


