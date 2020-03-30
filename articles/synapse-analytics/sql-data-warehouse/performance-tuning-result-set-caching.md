---
title: Ladění výkonu s využitím ukládání sad výsledků do mezipaměti
description: Přehled funkcí ukládání výsledků do mezipaměti pro SQL Analytics v Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 10/10/2019
ms.author: xiaoyul
ms.reviewer: nidejaco;
ms.custom: azure-synapse
ms.openlocfilehash: 0c2190c29054301a8e21a9a27eb078802fbc9612
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350859"
---
# <a name="performance-tuning-with-result-set-caching"></a>Ladění výkonu s využitím ukládání sad výsledků do mezipaměti  
Je-li povoleno ukládání do mezipaměti sady výsledků, sql analytics automaticky ukládá výsledky dotazu do mezipaměti v databázi uživatelů pro opakované použití.  To umožňuje následné spuštění dotazu získat výsledky přímo z trvalé mezipaměti, takže recomputation není potřeba.   Ukládání do mezipaměti sady výsledků zlepšuje výkon dotazu a snižuje využití výpočetních prostředků.  Kromě toho dotazy pomocí sady výsledků uložených v mezipaměti nepoužívají žádné sloty souběžnosti a proto se nezapočítávají do existujících limitů souběžnosti. Z bezpečnostních důvodů mají uživatelé přístup k výsledkům uložený v mezipaměti pouze v případě, že mají stejná oprávnění k přístupu k datům jako uživatelé, kteří vytvářejí výsledky uložené v mezipaměti.  

## <a name="key-commands"></a>Klíčové příkazy
[Zapnutí/vypnutí ukládání výsledků do mezipaměti pro databázi uživatelů](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options?view=azure-sqldw-latest)

[Zapnutí/vypnutí nastavení stavu nastavení ukládání výsledků do mezipaměti relace](https://docs.microsoft.com/sql/t-sql/statements/set-result-set-caching-transact-sql?view=azure-sqldw-latest)

[Kontrola velikosti sady výsledků uložené v mezipaměti](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-showresultcachespaceused-transact-sql?view=azure-sqldw-latest)  

[Vyčištění mezipaměti](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-dropresultsetcache-transact-sql?view=azure-sqldw-latest)

## <a name="whats-not-cached"></a>Co není uloženo do mezipaměti  

Po zapnutí ukládání do mezipaměti sady výsledků pro databázi jsou výsledky ukládány do mezipaměti pro všechny dotazy, dokud není mezipaměť plná, s výjimkou těchto dotazů:
- Dotazy používající nedeterministické funkce, například DateTime.Now()
- Dotazy pomocí uživatelem definovaných funkcí
- Dotazy pomocí tabulek s povoleným zabezpečením na úrovni řádků nebo na úrovni sloupců
- Dotazy vracející data s velikostí řádku větší než 64 kB

> [!IMPORTANT]
> Operace k vytvoření mezipaměti sady výsledků a načtení dat z mezipaměti probíhají na řídicím uzlu instance SQL Analytics.
> Pokud je ukládání do mezipaměti sady výsledků zapnuto, může spuštění dotazů, které vrací sadu velkých výsledků (například >1 milion řádků), způsobit vysoké využití procesoru v řídicím uzlu a zpomalit celkovou odpověď na dotaz instance.  Tyto dotazy se běžně používají během zkoumání dat nebo etl operace. Aby se zabránilo zpětí řídicíuzel a způsobit problém s výkonem, uživatelé by měli vypnout sadu výsledků ukládání do mezipaměti v databázi před spuštěním těchto typů dotazů.  

Spusťte tento dotaz po dobu, kterou pro dotaz zaberou operace ukládání do mezipaměti sady výsledků:

```sql
SELECT step_index, operation_type, location_type, status, total_elapsed_time, command 
FROM sys.dm_pdw_request_steps 
WHERE request_id  = <'request_id'>; 
```

Zde je příklad výstupu pro dotaz spuštěný se zakázaným ukládáním do mezipaměti sady výsledků.

![Dotaz-kroky-s-rsc-zakázáno](./media/performance-tuning-result-set-caching/query-steps-with-rsc-disabled.png)

Zde je příklad výstupu pro dotaz spuštěný s povolenou mezipamětí sady výsledků.

![Možnost dotazu s rsc povoleno](./media/performance-tuning-result-set-caching/query-steps-with-rsc-enabled.png)

## <a name="when-cached-results-are-used"></a>Při použití výsledků uložených v mezipaměti

Sada výsledků v mezipaměti je znovu použita pro dotaz, pokud jsou splněny všechny následující požadavky:
- Uživatel, který spouštějí dotaz, má přístup ke všem tabulkám, na které se dotaz odkazuje.
- Existuje přesná shoda mezi novým dotazem a předchozím dotazem, který vygeneroval mezipaměť sady výsledků.
- V tabulkách, ze kterých byla generována sada výsledků uložené v mezipaměti, nejsou žádná data ani změny schématu.

Spuštěním tohoto příkazu zkontrolujte, zda byl dotaz proveden s výsledky mezipaměti přístupů nebo chybět. Pokud dojde k přístupu do mezipaměti, result_cache_hit vrátí 1.

```sql
SELECT request_id, command, result_cache_hit FROM sys.dm_pdw_exec_requests 
WHERE request_id = <'Your_Query_Request_ID'>
```

## <a name="manage-cached-results"></a>Správa výsledků uložených v mezipaměti 

Maximální velikost mezipaměti sady výsledků je 1 TB na databázi.  Výsledky uložené v mezipaměti jsou automaticky zneplatněny při změně podkladových dat dotazu.  

Vyřazovací služba mezipaměti je spravována službou SQL Analytics automaticky podle tohoto plánu: 
- Každých 48 hodin, pokud sada výsledků nebyla použita nebo byla zrušena. 
- Když se mezipaměť sady výsledků blíží maximální velikosti.

Uživatelé mohou ručně vyprázdnit celou mezipaměť sady výsledků pomocí jedné z těchto možností: 
- Vypnutí funkce mezipaměti sady výsledků pro databázi 
- Spuštění dbcc DROPRESULTSETCACHE při připojení k databázi

Pozastavenídatabáze nevyprázdní sadu výsledků v mezipaměti.  

## <a name="next-steps"></a>Další kroky
Další tipy pro vývoj najdete v [tématu přehled vývoje](sql-data-warehouse-overview-develop.md). 
