---
title: Důležitost úloh
description: Pokyny pro nastavení důležitosti pro vyhrazené dotazy fondu SQL ve službě Azure synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 06d1957d182f2cabc336afcfc47a790442a3cb9a
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678402"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Důležitost úloh služby Azure synapse Analytics

Tento článek vysvětluje, jak důležitost úloh může ovlivnit pořadí spouštění vyhrazených požadavků fondu SQL ve službě Azure synapse.

## <a name="importance"></a>Důležitost

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Obchodní potřeby můžou vyžadovat, aby úlohy datových skladů byly důležitější než jiné.  Vezměte v úvahu scénář, při kterém jsou data důležitých prodejů načtena před zavřením fiskálního období.  Načtení dat pro jiné zdroje, například data o počasí, nemá striktní SLA. Nastavení vysoké důležitosti pro požadavek na načtení prodejních dat a nízké důležitost požadavků na načtení dat o počasí zajišťuje, že při načtení dat z prodeje se získá první přístup k prostředkům a dokončí se rychleji.

## <a name="importance-levels"></a>Úrovně důležitosti

Existuje pět úrovní důležitosti: nízká, below_normal, Normal, above_normal a High.  Požadavky, které nemají nastavenou důležitost, jsou přiřazeny výchozí úrovni normální. Žádosti, které mají stejnou úroveň důležitosti, mají stejné chování plánování, které už dnes existuje.

## <a name="importance-scenarios"></a>Scénáře důležitosti

Mimo základní scénář důležitosti, který je popsaný výše s údaji o prodeji a počasí, jsou k dispozici další scénáře, kdy důležité úlohy pomáhají splnit požadavky na zpracování a dotazování dat.

### <a name="locking"></a>Uzamčení

Přístup k zámkům pro aktivitu čtení a zápisu představuje jednu oblast přirozeného sporu. Aktivity, jako je [přepínání oddílů](sql-data-warehouse-tables-partition.md) nebo [přejmenování objektu](/sql/t-sql/statements/rename-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , vyžadují zvýšené zámky.  Bez důležitosti úloh je vyhrazený fond SQL ve službě Azure synapse optimalizován pro propustnost. Optimalizace pro propustnost znamená, že při spuštění a frontě požadavků mají být k dispozici stejné požadavky na uzamykání a prostředky, požadavky ve frontě mohou obejít požadavky s vyššími požadavky na uzamykání, které byly přijaty do fronty požadavků dříve. Po použití důležitosti na požadavky s vyššími nároky na uzamykání. Požadavek s vyšší důležitostí se spustí před vyžádáním s nižší důležitostí.

Uvažujte následující příklad:

- Q1 aktivně běží a vybírá data z SalesFact.
- Q2 je zařazen do fronty čekání na dokončení Q1.  Byl odeslán na 9:00 a pokouší se rozdělit nová data do SalesFact.
- Čtvrtletí se odešle do 9:01am a chce vybrat data z SalesFact.

Pokud má dotaz na hodnotu F2 a Q3 stejnou důležitost a je-li stále spuštěn, bude zahájena otázka Q3. Dotaz Q2 bude nadále čekat na výhradní zámek na SalesFact.  Pokud má dotaz D2 větší důležitost než v rámci třetího čtvrtletí, před zahájením provádění vyprší před tím, než se dokončí spuštění.

### <a name="non-uniform-requests"></a>Neuniformní žádosti

Dalším scénářem, kde důležitost může přispět k splnění požadavků na dotazování, je při odeslání požadavků s různými třídami prostředků.  Jak bylo uvedeno výše, v rámci stejné důležitosti byl vyhrazený fond SQL ve službě Azure synapse optimalizován pro propustnost. Pokud jsou požadavky na smíšenou velikost (například smallrc nebo mediumrc) zařazeny do fronty, bude vyhrazený fond SQL zvolit nejstarší požadavek, který se vejde do dostupných prostředků. Pokud se používá důležitost úloh, naplánuje se další požadavek na důležitost.
  
Vezměte v úvahu následující příklad v DW500c:

- V Q1, Q2, Q3 a Q4 se spouští dotazy smallrc.
- Q5 se odešle s třídou prostředků mediumrc na 9:00.
- Q6 se odešle s třídou prostředků smallrc na 9:01am.

Vzhledem k tomu, že Q5 je mediumrc, vyžaduje dva sloty souběžnosti. Q5 musí počkat na dokončení dvou spuštěných dotazů.  Pokud se ale jeden ze spuštěných dotazů (Q1-Q4) dokončí, Q6 se okamžitě naplánuje, protože prostředky existují ke spuštění dotazu.  Pokud má Q5 vyšší důležitost než Q6, Q6 počká, dokud nebude Q5 spuštěn, než bude moci začít s prováděním.

## <a name="next-steps"></a>Další kroky

- Další informace o vytvoření klasifikátoru najdete v tématu [Vytvoření klasifikátoru úloh (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  
- Další informace o klasifikaci úloh najdete v tématu [klasifikace úloh](sql-data-warehouse-workload-classification.md).  
- Informace o tom, jak vytvořit klasifikátor úloh, najdete v tématu rychlý Start – [Vytvoření klasifikátoru úloh](quickstart-create-a-workload-classifier-tsql.md) .
- V článcích s postupy můžete [nakonfigurovat důležitost úloh](sql-data-warehouse-how-to-configure-workload-importance.md) a [Spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- V tématu [Sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) můžete zobrazit dotazy a přiřazená důležitost.
