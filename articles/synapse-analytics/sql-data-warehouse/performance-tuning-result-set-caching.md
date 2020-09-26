---
title: Ladění výkonu s využitím ukládání sad výsledků do mezipaměti
description: Přehled funkcí pro ukládání do mezipaměti sady výsledků pro synapse fond SQL ve službě Azure synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: aeeca38afb82e2dcd86e111d1ae5dcb2e7499f42
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362261"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ladění výkonu s využitím ukládání sad výsledků do mezipaměti

Pokud je povoleno ukládání sady výsledků do mezipaměti, synapse SQL automaticky ukládá do mezipaměti výsledky dotazu v uživatelské databázi pro opakované použití.  To umožňuje následným provedením dotazů získat výsledky přímo z trvalé mezipaměti, aby se ještě nevyžadovalo jejich recompute.   Ukládání sady výsledků do mezipaměti vylepšuje výkon dotazů a snižuje využití prostředků v výpočetním prostředí.  Dotazy, které používají sadu výsledků uložených v mezipaměti, nepoužívají žádné přihrádky souběžnosti, a proto se nepočítají proti stávajícím limitům souběžnosti. Z důvodu zabezpečení mají uživatelé přístup k výsledkům uloženým v mezipaměti pouze v případě, že mají stejná oprávnění k přístupu k datům, jako uživatelé, kteří vytvářejí výsledky v mezipaměti.  

## <a name="key-commands"></a>Klíčové příkazy

[Zapnout nebo vypnout ukládání sady výsledků do mezipaměti pro uživatelskou databázi](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Zapnout nebo vypnout ukládání sady výsledků do mezipaměti pro relaci](/sql/t-sql/statements/set-result-set-caching-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

[Ověřte velikost sady výsledků v mezipaměti.](/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  

[Vyčištění mezipaměti](/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co není uloženo v mezipaměti  

Když je pro databázi zapnuté ukládání výsledků do mezipaměti, výsledky se ukládají do mezipaměti pro všechny dotazy, dokud nebude mezipaměť zaplněna, s výjimkou těchto dotazů:

- Dotazování pomocí nedeterministických funkcí, jako je DateTime. Now ()
- Dotazy využívající uživatelsky definované funkce
- Dotazy, které používají tabulky se zabezpečením na úrovni řádků nebo na úrovni sloupce povolené
- Dotazy vracející data s velikostí řádku větší než 64 KB
- Dotazy vracející velká data ve velikosti (>10GB) 

> [!IMPORTANT]
> Operace pro vytvoření mezipaměti sady výsledků a načtení dat z mezipaměti se vyskytují v uzlu Control instance synapse fondu SQL.
> Když je ukládání sady výsledků do mezipaměti zapnuté, spuštění dotazů, které vracejí velkou sadu výsledků (například >GB), může způsobit vysoké omezení na řídicím uzlu a zpomalit celkovou reakci na dotaz na instanci.  Tyto dotazy se běžně používají při zkoumání dat nebo při operacích ETL. Aby nedošlo k přerušení řídicího uzlu a mohl by dojít k problémům s výkonem, měli by uživatelé před spuštěním těchto typů dotazů vypnout ukládání sady výsledků do mezipaměti v databázi.  

Spustit tento dotaz pro dobu, kterou zadalo operace ukládání sady výsledků do mezipaměti pro dotaz:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command
FROM sys.dm_pdw_request_steps
WHERE request_id  = <'request_id'>;
```

Tady je příklad výstupu pro dotaz, který se spustil s povoleným ukládáním sady výsledků dotazu.

![Snímek obrazovky zobrazuje výsledky dotazu, včetně typu umístění a příkazu.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Tady je příklad výstupu dotazu spuštěného s povoleným ukládáním sady výsledků.

![Snímek obrazovky zobrazuje výsledky dotazu s vybraným příkazem * from [D W ResultCache D] tečka d b o.](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Když se použijí výsledky uložené v mezipaměti

Sada výsledků dotazu v mezipaměti se znovu použije pro dotaz, pokud jsou splněné všechny následující požadavky:

- Uživatel, který spouští dotaz, má přístup ke všem tabulkám, na které je odkazováno v dotazu.
- Existuje přesná shoda mezi novým dotazem a předchozím dotazem, který vygeneroval mezipaměť sady výsledků dotazu.
- V tabulkách, ve kterých byla sada výsledků dotazu z mezipaměti vygenerována, nejsou žádná data ani změny schématu.

Spusťte tento příkaz a ověřte, zda byl dotaz proveden s výsledkem nebo neúspěšným výsledkem mezipaměti výsledků. Sloupec result_cache_hit vrátí hodnotu 1 pro spuštění mezipaměti, 0 pro neúspěšný zápis do mezipaměti a záporné hodnoty z důvodů, proč se nepoužilo ukládání sady výsledků do mezipaměti. Podrobnosti najdete v [tabulce sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Správa výsledků v mezipaměti

Maximální velikost mezipaměti sady výsledků je 1 TB na databázi.  Dojde-li ke změně podkladových dat dotazu, dojde k automatickému zrušení platnosti výsledků v mezipaměti.  

Vyřazení mezipaměti je spravováno službou synapse SQL automaticky podle tohoto plánu:

- Každých 48 hodin, pokud se sada výsledků nepoužila nebo byla zrušena její platnost.
- Když mezipaměť sady výsledků blíží maximální velikost.

Uživatelé mohou ručně Vyprázdnit celou mezipaměť sady výsledků dotazu pomocí jedné z následujících možností:

- Vypnutí funkce mezipaměti sady výsledků pro databázi
- Spustit DBCC DROPRESULTSETCACHE při připojení k databázi

Pozastavení databáze nevyprázdní sadu výsledků v mezipaměti.  

## <a name="next-steps"></a>Další kroky

Další tipy pro vývoj najdete v tématu [Přehled vývoje](sql-data-warehouse-overview-develop.md).
