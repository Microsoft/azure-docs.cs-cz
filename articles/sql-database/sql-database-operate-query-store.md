---
title: "Operační úložiště dotazů v databázi Azure SQL"
description: "Naučte se pracovat úložiště dotazů v databázi SQL Azure"
services: sql-database
author: bonova
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 11/08/2016
ms.author: bonova
ms.openlocfilehash: f0c3780f6efe87437742af7c1b8f6a3e6d0ee243
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Operační úložiště dotazů v databázi Azure SQL
Úložiště dotazů v Azure je plně spravovaná databáze funkce, která nepřetržitě shromažďuje a uvede podrobné historické informace o všech dotazů. O úložišti dotazů si můžete představit jako podobná letadle dat záznam výrazně zjednodušuje výkon dotazů řešení potíží pro cloudové a místní zákazníků. Tento článek vysvětluje specifických aspektů operačního úložiště dotazů v Azure. Pomocí této předem shromážděných dotaz na data, můžete rychle diagnostikovat a vyřešit problémy s výkonem a proto věnovat více času, které jsou zaměřené na své firmy. 

Úložiště dotazů byl [globálně dostupnou](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) ve službě Azure SQL Database od listopadu 2015. Úložiště dotazů je základem pro analýzu výkonu a ladění funkcí, jako například [Poradce pro funkci SQL Database a řídicí panel výkonu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Úložiště dotazů je v době publikování tohoto článku, spuštěna ve více než 200 000 uživatelských databází v Azure, shromažďování související s informací o několik měsíců, bez přerušení.

> [!IMPORTANT]
> Společnost Microsoft právě aktivace úložiště dotazů pro všechny databáze Azure SQL (existujícím a novým). 
> 
> 

## <a name="optimal-query-store-configuration"></a>Konfigurace optimální úložiště dotazů
Tato část popisuje výchozí hodnoty optimální konfigurace, které jsou navrženy tak, aby spolehlivé operace úložiště dotazů a závislé součásti, jako například [Poradce pro funkci SQL Database a řídicí panel výkonu](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Výchozí konfigurace je optimalizovaná pro souvislá datová kolekci, ve kterých je minimální čas strávený v OFF/READ_ONLY stavy.

| Konfigurace | Popis | Výchozí | Poznámka |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Určuje tento limit pro datový prostor, který úložiště dotazů můžete provést uvnitř databáze zákazníka |100 |Vynutí nové databáze |
| INTERVAL_LENGTH_MINUTES |Definuje velikost časový interval, během kterého se agregují a trvalé statistiku shromážděných modulu runtime pro plány dotazů. Každý dotaz služby active plán má maximálně jeden řádek pro určitou dobu definované s touto konfigurací |60 |Vynutí nové databáze |
| STALE_QUERY_THRESHOLD_DAYS |Zásady založené na čase čištění, která řídí dobu uchování statistiku trvalou modulu runtime a neaktivní dotazů |30 |Vynucování nové databáze a databáze s předchozí výchozí (367) |
| SIZE_BASED_CLEANUP_MODE |Určuje, zda automatické čištění probíhá při velikost dat úložiště dotazů se blíží limitu |AUTO |Vynutit pro všechny databáze |
| QUERY_CAPTURE_MODE |Určuje, zda jsou sledovány všechny dotazy nebo pouze podmnožinu dotazy |AUTO |Vynutit pro všechny databáze |
| FLUSH_INTERVAL_SECONDS |Určuje maximální dobu, během které zachytit runtime, který statistiky jsou uchovány v paměti, před Vyčištění disku |900 |Vynutí nové databáze |
|  | | | |

> [!IMPORTANT]
> Tato výchozí nastavení budou automaticky použita v konečné fázi aktivace úložiště dotazů v všechny databáze Azure SQL (viz předcházející důležitá poznámka). Po této light až Azure SQL Database nebude mění konfigurace hodnotami nastavenými v zákazníků, pokud budou mít negativní vliv na primární úlohy nebo spolehlivé operace úložiště dotazů.
> 
> 

Pokud chcete, aby zůstali pomocí vlastních nastavení, použijte [ALTER DATABASE s možností úložiště dotazů](https://msdn.microsoft.com/library/bb522682.aspx) vrátit zpátky konfigurace do předchozího stavu. Podívejte se na [osvědčené postupy s úložiště dotazů](https://msdn.microsoft.com/library/mt604821.aspx) Chcete-li další informace, jak horní zvolili optimální konfigurační parametry.

## <a name="next-steps"></a>Další postup
[Informace o výkonu databáze SQL](sql-database-performance.md)

## <a name="additional-resources"></a>Další zdroje informací:
Pro další informace o rezervaci v následujících článcích:

* [Záznam dat pro vaši databázi](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 
* [Monitorování výkonu pomocí úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx)
* [Scénáře použití úložiště dotazů](https://msdn.microsoft.com/library/mt614796.aspx)
* [Monitorování výkonu pomocí úložiště dotazů](https://msdn.microsoft.com/library/dn817826.aspx) 

