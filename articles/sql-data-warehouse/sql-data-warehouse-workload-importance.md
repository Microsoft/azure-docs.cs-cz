---
title: Důležitost úloh | Dokumentace Microsoftu
description: Pokyny k nastavení význam pro dotazy ve službě Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 12e7d9bc22eff14bbf302aed50080412d04a40d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61474698"
---
# <a name="sql-data-warehouse-workload-importance-preview"></a>Důležitost úlohy SQL Data Warehouse (Preview)

Tento článek vysvětluje, jak důležité úlohy mohou mít vliv na pořadí provádění pro požadavky na SQL Data Warehouse.

> [!Note]
> Úloha klasifikace je dostupná ve verzi preview na SQL Data Warehouse Gen2. Klasifikace úlohy správy a význam ve verzi preview je pro sestavení s datem vydání verze z 9. dubna 2019 nebo novější.  Uživatelé byste neměli používat sestavení starší než toto datum pro testování úloh správy.  K určení, zda je sestavení umožňující správu úloh, spustit, vyberte @@version při připojení k vaší instanci SQL Data Warehouse.

## <a name="importance"></a>Důležitost

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Obchodní potřeby může vyžadovat bude důležitější než jiné úlohy datových skladů.  Představte si třeba situaci, kde je nejdůležitější prodejní data načíst před fiskálního období zavřete.  Data se načtou u jiných zdrojů, jako je data o počasí nemají striktní smlouvy o úrovni služeb.   Nastavení vysokou důležitostí pro žádost o načtení prodejních dat a s nízkou důležitostí na žádost o načtení, zda data zajišťuje zatížení prodejních dat získá první přístup k prostředkům a rychleji se dokončí.

## <a name="importance-levels"></a>Úrovně důležitosti

Existuje pět úrovní důležitosti: Nízká, below_normal, Normální, above_normal a vysoká.  Požadavky, které nemají nastavený význam jsou přiřazeny výchozí úroveň normální.  Požadavky, které mají stejnou úroveň důležitosti mají stejné chování plánování, která existuje ještě dnes.

## <a name="importance-scenarios"></a>Důležitost scénáře

Nad rámec výše popsaného s prodeje a data o počasí scénáře základní význam jsou další scénáře, kde význam úloh pomáhá plnit zpracování dat a dotazování na ně potřebám.

### <a name="locking"></a>Uzamykání

Přístup na zámků pro čtení a zápisu aktivit je jedné oblasti přírodních kolize.  Aktivity, jako [přepínání oddílů](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) nebo [přejmenovat OBJEKT](/sql/t-sql/statements/rename-transact-sql) vyžadovat uzamčení se zvýšenými oprávněními.  SQL Data Warehouse bez úloh význam, optimalizuje propustnost.  Optimalizace propustnosti znamená, že při spuštění a požadavky ve frontě mají stejné zamykání potřeby a prostředky jsou k dispozici, požadavků zařazených do fronty může obejít požadavky s vyšší vyžaduje uzamčení, které byly přijaty ve frontě dříve.  Po použití pracovního vytížení důležitosti pro požadavky s vyšší uzamčení potřebuje. Požadavek s větší význam se spustí před požadavkem s důležitostí nižší.

Vezměte v úvahu v následujícím příkladu:

Q1 je aktivně spuštěná a výběr dat z SalesFact.
2. čtvrtletí je ve frontě čeká Q1 dokončete.  Byla odeslána v 9: 00 a pokouší se oddíl přepínače nová data do SalesFact.
3. čtvrtletí odeslání v 9:01:00 a chce, aby se k výběru dat z SalesFact.

Pokud stále provádí Q1 F2 a F3 mají stejnou důležitost, 3. čtvrtletí začne provádění. 2. čtvrtletí bude dál čekat na SalesFact pro výhradní zámek.  Pokud Q2 má větší význam než 3. čtvrtletí, 3. čtvrtletí počká, až 2. čtvrtletí dokončení před zahájením provádění.

### <a name="non-uniform-requests"></a>Neuniformní žádosti

Jiný scénář, kde význam pomáhají splňovat požadavky na zjišťování je odeslání žádosti s třídami jiný prostředek.  Protože se už jsme zmínili, v části stejný význam, SQL Data Warehouse optimalizuje propustnost.  Když smíšené velikost požadavků (jako je například smallrc nebo mediumrc) se zařadí do fronty, SQL Data Warehouse zvolí nejdřívější opravovány požadavek, který se vejde do dostupné prostředky.  Pokud jsou použité význam pracovního vytížení, nejvyšší žádost o důležitosti vedle naplánován.
  
Podívejte se na následující příklad na DW500c:

1, 2. čtvrtletí, 3. čtvrtletí a. až 4. běží smallrc dotazy.
5 se odešle s třídou mediumrc prostředků v 9: 00.
6 se odešle s smallrc Třída prostředku v 9:01:00.

Vzhledem k tomu, že 5 je mediumrc, vyžaduje dva slotů souběžnosti.  5 musí počkat pro dva spouštění dotazů na dokončení.  Ale po dokončení jedné spuštěných dotazů (1-4) 6 je naplánováno okamžitě vzhledem k tomu, že prostředky existují k provedení dotazu.  Pokud dotaz č. 5 má větší význam než 6, 6 počká, až 5 běží před zahájením provádění.

## <a name="next-steps"></a>Další postup

Další informace o klasifikaci úlohy SQL Data Warehouse najdete v tématu [SQL Data Warehouse úloh klasifikace](sql-data-warehouse-workload-classification.md) a [vytvořit úlohu třídění](quickstart-create-a-workload-classifier-tsql.md). Zobrazit [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) k zobrazení dotazů a závažnosti přiřazené.
