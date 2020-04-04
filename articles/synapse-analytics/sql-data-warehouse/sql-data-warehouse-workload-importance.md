---
title: Důležitost úloh
description: Pokyny pro nastavení důležitosti pro dotazy fondu SYNApse SQL v Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 2c8617cffaa81da6423011a494b8dbc82c42d218
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632457"
---
# <a name="azure-synapse-analytics-workload-importance"></a>Důležitost pracovního vytížení Azure Synapse Analytics

Tento článek vysvětluje, jak důležitost úlohy může ovlivnit pořadí provádění požadavků fondu SYNApse SQL v Azure Synapse.

## <a name="importance"></a>Důležitost

> [!Video https://www.youtube.com/embed/_2rLMljOjw8]

Obchodní potřeby mohou vyžadovat, aby úlohy ukládání dat byly důležitější než jiné.  Zvažte scénář, kdy jsou důležitá data o prodeji načtena před uzavřením fiskálního období.  Načítání dat pro jiné zdroje, jako jsou data o počasí, nemají přísné sla. Nastavení vysoké důležitosti požadavku na načtení dat o prodeji a nízké důležitosti požadavku na načtení dat o počasí zajistí, že zatížení dat o prodeji získá první přístup k prostředkům a dokončí se rychleji.

## <a name="importance-levels"></a>Úrovně důležitosti

Existuje pět úrovní důležitosti: nízká, below_normal, normální, above_normal a vysoká.  Požadavkům, které nenastavují důležitost, je přiřazena výchozí úroveň normálu. Požadavky, které mají stejnou úroveň důležitosti, mají stejné chování plánování, které existuje dnes.

## <a name="importance-scenarios"></a>Scénáře důležitosti

Kromě scénáře základní důležitosti popsaného výše s daty o prodeji a počasí existují další scénáře, kde důležitost úlohy pomáhá plnit požadavky na zpracování dat a dotazování.

### <a name="locking"></a>Uzamčení

Přístup ke zámkům pro čtení a zápis aktivity je jednou z oblastí přirozené tvrzení. Aktivity, jako je [přepínání oddílů](/azure/sql-data-warehouse/sql-data-warehouse-tables-partition) nebo [přejmenování objektu](/sql/t-sql/statements/rename-transact-sql?view=azure-sqldw-latest) vyžadují zvýšené zámky.  Bez důležitosti úlohy optimalizuje fond Synapse SQL v Azure Synapse propropustnost. Optimalizace pro propustnost znamená, že při spuštění a fronty požadavky mají stejné potřeby uzamčení a prostředky jsou k dispozici, požadavky ve frontě můžete obejít požadavky s vyšší potřeby uzamčení, které byly doručeny do fronty požadavků dříve. Jakmile je důležitost pracovního vytížení použita na požadavky s vyššími potřebami uzamčení. Požadavek s vyšší důležitostí bude spuštěn před požadavkem s nižší důležitostí.

Uvažujte následující příklad:

- Q1 je aktivně spuštěna a výběr dat z SalesFact.
- Q2 je ve frontě čeká na dokončení Q1.  Byl odeslán v 9 hodin ráno a pokouší se rozdělit nová data do SalesFact.
- Q3 je odeslána v 9:01 a chce vybrat data z SalesFact.

Pokud Q2 a Q3 mají stejný význam a Q1 je stále spuštěna, Q3 začne provádění. Q2 bude i nadále čekat na výhradní zámek na SalesFact.  Pokud Q2 má vyšší význam než Q3, Q3 bude čekat, až Q2 je dokončena před zahájením provádění.

### <a name="non-uniform-requests"></a>Nejednotné žádosti

Dalším scénářem, kde důležitost může pomoci splnit požadavky na dotazování, je při odeslání požadavků s různými třídami prostředků.  Jak již bylo zmíněno, podle stejného významu, Synapse SQL fond v Azure Synapse optimalizuje pro propustnost. Při smíšené velikosti požadavky (například smallrc nebo mediumrc) jsou ve frontě, Synapse SQL fond vybere nejbližší příchozí požadavek, který se vejde do dostupných prostředků. Pokud je použita důležitost pracovního vytížení, je naplánovánpožadavek nejvyšší důležitosti.
  
Zvažte následující příklad na DW500c:

- Q1, Q2, Q3 a Q4 jsou spuštěny smallrc dotazy.
- Q5 je odeslána s mediumrc třídy prostředků v 9 hodin ráno.
- Q6 je odeslána s třídou prostředků smallrc v 9:01.

Vzhledem k tomu, že Q5 je mediumrc, vyžaduje dva sloty souběžnosti. Q5 musí čekat na dokončení dvou spuštěných dotazů.  Však po dokončení jednoho z běžících dotazů (Q1-Q4) Q6 je naplánováno okamžitě, protože existují prostředky pro spuštění dotazu.  Pokud Q5 má vyšší význam než Q6, Q6 čeká, dokud Q5 je spuštěna před zahájením provádění.

## <a name="next-steps"></a>Další kroky

- Další informace o vytvoření třídění naleznete [v tématu CREATE WORKLOAD CLASSIFIER (Transact-SQL).](/sql/t-sql/statements/create-workload-classifier-transact-sql)  
- Další informace o klasifikaci pracovního vytížení naleznete v [tématu Klasifikace pracovního vytížení](sql-data-warehouse-workload-classification.md).  
- Postup vytvoření třídění pracovního vytížení najdete v tématu Třídění úloh vytvoření rychlého [startu.](quickstart-create-a-workload-classifier-tsql.md)
- Podívejte se na články s postupy [pro konfiguraci důležitosti pracovního vytížení](sql-data-warehouse-how-to-configure-workload-importance.md) a [jak spravovat a monitorovat správu úloh](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
- Viz [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?view=azure-sqldw-latest) pro zobrazení dotazů a přiřazený význam.
