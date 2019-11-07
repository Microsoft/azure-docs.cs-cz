---
title: Ladění výkonu s využitím mezipaměti sady výsledků
description: Přehled funkcí ukládání do mezipaměti sady výsledků pro Azure SQL Data Warehouse
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: seo-lt-2019
ms.openlocfilehash: 461320b9c3ed48176fb60fe695704c582edcd552
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692955"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ladění výkonu s využitím mezipaměti sady výsledků  
Pokud je povoleno ukládání sady výsledků do mezipaměti, Azure SQL Data Warehouse automaticky ukládá do mezipaměti výsledky dotazu v uživatelské databázi pro opakované použití.  To umožňuje následným provedením dotazů získat výsledky přímo z trvalé mezipaměti, aby se ještě nevyžadovalo jejich recompute.   Ukládání sady výsledků do mezipaměti vylepšuje výkon dotazů a snižuje využití prostředků v výpočetním prostředí.  Dotazy, které používají sadu výsledků uložených v mezipaměti, nepoužívají žádné přihrádky souběžnosti, a proto se nepočítají proti stávajícím limitům souběžnosti. Z důvodu zabezpečení mají uživatelé přístup k výsledkům uloženým v mezipaměti pouze v případě, že mají stejná oprávnění k přístupu k datům, jako uživatelé, kteří vytvářejí výsledky v mezipaměti.  

## <a name="key-commands"></a>Klíčové příkazy
[Zapnout nebo vypnout ukládání sady výsledků do mezipaměti pro uživatelskou databázi](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Zapnout nebo vypnout ukládání sady výsledků do mezipaměti pro relaci](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Ověřte velikost sady výsledků v mezipaměti.](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Vyčištění mezipaměti](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co není uloženo v mezipaměti  

Když je pro databázi zapnuté ukládání výsledků do mezipaměti, výsledky se ukládají do mezipaměti pro všechny dotazy, dokud nebude mezipaměť zaplněna, s výjimkou těchto dotazů:
- Dotazování pomocí nedeterministických funkcí, jako je DateTime. Now ()
- Dotazy využívající uživatelsky definované funkce
- Dotazy, které používají tabulky se zabezpečením na úrovni řádků nebo na úrovni sloupce povolené
- Dotazy vracející data s velikostí řádku větší než 64 KB

> [!IMPORTANT]
> Operace pro vytvoření mezipaměti sady výsledků a načtení dat z mezipaměti se vyskytují v uzlu Control instance datového skladu. Když je zapnuté ukládání sady výsledků do mezipaměti, spouštění dotazů, které vracejí velkou sadu výsledků (například > 1 milion řádků), může způsobit vysoké využití procesoru v uzlu Control a zpomalit celkovou odpověď na instanci.  Tyto dotazy se běžně používají při zkoumání dat nebo při operacích ETL. Aby nedošlo k přerušení řídicího uzlu a mohl by dojít k problémům s výkonem, měli by uživatelé před spuštěním těchto typů dotazů vypnout ukládání sady výsledků do mezipaměti v databázi.  

Spustit tento dotaz pro dobu, kterou zadalo operace ukládání sady výsledků do mezipaměti pro dotaz:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Tady je příklad výstupu pro dotaz, který se spustil s povoleným ukládáním sady výsledků dotazu.

![Dotaz-kroky-with-RSC – disabled](media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Tady je příklad výstupu dotazu spuštěného s povoleným ukládáním sady výsledků.

![Dotaz-kroky-with-RSC – povoleno](media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Když se použijí výsledky uložené v mezipaměti

Sada výsledků dotazu v mezipaměti se znovu použije pro dotaz, pokud jsou splněné všechny následující požadavky:
- Uživatel, který spouští dotaz, má přístup ke všem tabulkám, na které je odkazováno v dotazu.
- Existuje přesná shoda mezi novým dotazem a předchozím dotazem, který vygeneroval mezipaměť sady výsledků dotazu.
- V tabulkách, ve kterých byla sada výsledků dotazu z mezipaměti vygenerována, nejsou žádná data ani změny schématu.

Spusťte tento příkaz a ověřte, zda byl dotaz proveden s výsledkem nebo neúspěšným výsledkem mezipaměti výsledků. Pokud dojde k mezipaměti, vrátí result_cache_hit hodnotu 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Správa výsledků v mezipaměti 

Maximální velikost mezipaměti sady výsledků je 1 TB na databázi.  Dojde-li ke změně podkladových dat dotazu, dojde k automatickému zrušení platnosti výsledků v mezipaměti.  

Vyřazení mezipaměti je spravováno nástrojem Azure SQL Data Warehouse automaticky podle tohoto plánu: 
- Každých 48 hodin, pokud se sada výsledků nepoužila nebo byla zrušena její platnost. 
- Když mezipaměť sady výsledků blíží maximální velikost.

Uživatelé mohou ručně Vyprázdnit celou mezipaměť sady výsledků dotazu pomocí jedné z následujících možností: 
- Vypnutí funkce mezipaměti sady výsledků pro databázi 
- Spustit DBCC DROPRESULTSETCACHE při připojení k databázi

Pozastavení databáze nevyprázdní sadu výsledků v mezipaměti.  

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v části [Přehled vývoje SQL Data Warehouse](sql-data-warehouse-overview-develop.md). 
