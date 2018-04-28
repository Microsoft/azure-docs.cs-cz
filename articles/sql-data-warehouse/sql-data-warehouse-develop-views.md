---
title: Použití zobrazení T-SQL v Azure SQL Data Warehouse | Microsoft Docs
description: Tipy pro používání zobrazení T-SQL v Azure SQL Data Warehouse na vývoj řešení.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 28280a067e7008c20361e0a0041c81ba84e7f74c
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="views-in-azure-sql-data-warehouse"></a>Zobrazení v Azure SQL Data Warehouse
Tipy pro používání zobrazení T-SQL v Azure SQL Data Warehouse na vývoj řešení. 

## <a name="why-use-views"></a>Proč používat zobrazení?
Zobrazení lze mnoha různými způsoby ke zlepšení kvality vašeho řešení.  V tomto článku klade důraz několik příkladů, jak zlepšit komunikaci oddělení řešení pomocí zobrazení a také omezení, které je potřeba zvážit.

> [!NOTE]
> Syntaxe pro příkaz CREATE VIEW není popsané v tomto článku. Další informace najdete v tématu [vytvořit zobrazení](/sql/t-sql/statements/create-view-transact-sql) dokumentaci.
> 
> 

## <a name="architectural-abstraction"></a>Abstrakce architektury
Běžný vzor aplikací je znovu vytvořit pomocí vytvoření tabulky AS vyberte funkce CTAS () a potom objektem přejmenování vzor, zatímco načítání dat tabulky.

Následující příklad přidá nové datum záznamy do dimenze kalendářního data. Všimněte si, jak je nové tabulky, DimDate_New, nejprve vytvořit a pak přejmenovat nahradit původní verzi v tabulce.

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

Tento přístup může však způsobit tabulky zobrazování a ztrácejí ze zobrazení uživatele, jakož i "tabulka neexistuje" chybové zprávy. Zobrazení umožňuje uživatelům konzistentní prezentační vrstvou a přitom se základní objekty jsou přejmenovat. Tím, že poskytuje přístup k datům prostřednictvím zobrazení, uživatelé nemusí viditelnost na základní tabulky. Tato vrstva nabízí konzistentní uživatelské prostředí, zatímco zajistíte, že datový sklad Designer můžete rozvíjet datový model. Bude možné vyvíjí základní tabulky znamená, že Designer můžete použít funkce CTAS maximalizovat výkon během procesu načítání data.   

## <a name="performance-optimization"></a>Optimalizace výkonu
Zobrazení, můžete použít také k vynucení výkonu optimalizované spojení mezi tabulkami. Například jako součást kritérií pro spojující minimalizovat přesun dat zobrazení začlenit redundantní distribučního klíče. Další výhodou zobrazení může být vynutit spojující pomocný parametr nebo specifického dotazu. Použití zobrazení tímto způsobem zaručuje, že vždy spojení optimální způsobem vyloučení uživatelé pamatovat správné konstrukce pro jejich spojení.

## <a name="limitations"></a>Omezení
Zobrazení v SQL Data Warehouse se ukládají jako obsahují pouze metadata. V důsledku toho nejsou k dispozici následující možnosti:

* Neexistuje žádná vazba možnost schématu
* Základní tabulky nelze aktualizovat prostřednictvím zobrazení
* Zobrazení nelze vytvořit přes dočasných tabulek
* Neexistuje žádná podpora pro ROZBALENÍ nebo NOEXPAND pomocné parametry
* Nejsou žádná indexované zobrazení v SQL Data Warehouse

## <a name="next-steps"></a>Další postup
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md).


