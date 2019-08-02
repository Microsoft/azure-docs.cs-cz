---
title: Provozní úložiště dotazů v Azure SQL Database
description: Naučte se pracovat s úložištěm dotazů v Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
ms.date: 12/19/2018
ms.openlocfilehash: b4f999818fe3b3517ee3fb48c22e616ee50f2d88
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567151"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Provozování úložiště dotazů v Azure SQL Database

Úložiště dotazů v Azure je plně spravovaná funkce databáze, která průběžně shromažďuje a prezentuje podrobné historické informace o všech dotazech. Úložiště dotazů si můžete představit podobně jako Zapisovač letových dat v letadle, který významně zjednodušuje řešení potíží s výkonem u cloudových i místních zákazníků. Tento článek vysvětluje konkrétní aspekty provozního úložiště dotazů v Azure. Pomocí těchto předem shromážděných dat dotazů můžete rychle diagnostikovat a vyřešit problémy s výkonem a tím se zaměříte na své podnikání více času. 

Úložiště dotazů bylo [globálně k dispozici](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) v Azure SQL Database od listopadu 2015. Úložiště dotazů je základem pro funkce analýzy a optimalizace výkonu, jako je například [SQL Database Advisor a řídicí panel výkonu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). V okamžiku publikování tohoto článku je úložiště dotazů spuštěné ve více než 200 000 uživatelských databází v Azure a shromažďují informace související s dotazy po dobu několika měsíců bez přerušení.

> [!IMPORTANT]
> Microsoft v procesu aktivuje úložiště dotazů pro všechny databáze SQL Azure (existující a nové). 

## <a name="optimal-query-store-configuration"></a>Optimální konfigurace úložiště dotazů

Tato část popisuje optimální výchozí nastavení konfigurace, která jsou navržená tak, aby zajistila spolehlivé fungování úložiště dotazů a závislých funkcí, jako je například [SQL Database Advisor a řídicí panel výkonu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Výchozí konfigurace je optimalizovaná pro kontinuální shromažďování dat, což je minimální čas strávený ve stavu vypnuto/READ_ONLY.

| Konfiguraci | Popis | Výchozí | Komentář |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Určuje limit pro datový prostor, který může úložiště dotazů převzít v rámci databáze zákazníka. |100 |Vynutilo pro nové databáze |
| INTERVAL_LENGTH_MINUTES |Definuje velikost časového okna, během kterého se shromažďují a uchovávají shromážděné statistiky za běhu pro plány dotazů. Každý aktivní plán dotazu má po dobu definovanou v této konfiguraci maximálně jeden řádek. |60 |Vynutilo pro nové databáze |
| STALE_QUERY_THRESHOLD_DAYS |Zásady čištění založené na čase, které řídí dobu uchování trvalých statistik za běhu a neaktivní dotazy |30 |Vynutilo pro nové databáze a databáze s předchozí výchozí (367) |
| SIZE_BASED_CLEANUP_MODE |Určuje, jestli se má automaticky vyčistit data, když se blíží limitu velikosti dat úložiště dotazů. |AUTO |Vynutilo pro všechny databáze |
| QUERY_CAPTURE_MODE |Určuje, zda jsou sledovány všechny dotazy nebo pouze podmnožina dotazů. |AUTO |Vynutilo pro všechny databáze |
| FLUSH_INTERVAL_SECONDS |Určuje maximální dobu, během které jsou zachycená běhová Statistika udržována v paměti, než se vyprázdní na disk. |900 |Vynutilo pro nové databáze |
|  | | | |

> [!IMPORTANT]
> Tato výchozí nastavení se automaticky aplikují v konečné fázi aktivace úložiště dotazů ve všech databázích SQL Azure (viz předchozí důležitá Poznámka). Po tomto osvětlení Azure SQL Database nemění hodnoty konfigurace nastavené zákazníky, pokud nemají negativní vliv na primární úlohy nebo spolehlivé operace úložiště dotazů.

Pokud chcete zachovat vlastní nastavení, použijte [příkaz ALTER DATABASE s možností úložiště dotazů](https://msdn.microsoft.com/library/bb522682.aspx) a obnovte konfiguraci do předchozího stavu. Podívejte se na [osvědčené postupy s úložištěm dotazů](https://msdn.microsoft.com/library/mt604821.aspx) , abyste se seznámili s tím, jak si vybrala nejlepší parametry konfigurace.

## <a name="next-steps"></a>Další kroky

[Přehled výkonu SQL Database](sql-database-performance.md)

## <a name="additional-resources"></a>Další zdroje

Další informace najdete v následujících článcích:

- [Záznam letových dat pro vaši databázi](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Monitorování výkonu pomocí úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx)
- [Scénáře použití úložiště dotazů](https://msdn.microsoft.com/library/mt614796.aspx)
