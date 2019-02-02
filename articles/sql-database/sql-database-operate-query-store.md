---
title: Provozní Query Store ve službě Azure SQL Database
description: Zjistěte, jak provozovat Query Store ve službě Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 3ceb8569d952f2947870ce7314f869623b2d87f9
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562942"
---
# <a name="operating-the-query-store-in-azure-sql-database"></a>Provozování Query Store ve službě Azure SQL Database

Query Store v Azure je plně spravované databázové funkce, která průběžně shromažďuje a uvede podrobné historické údaje o všech dotazech. Query Store se dá chápat jako podobný zapisovač letových údajů letadlo, který výrazně zjednodušuje výkon dotazů, řešení potíží pro cloudové a místní zákazníci. Tento článek popisuje konkrétní aspekty provozování Query Store v Azure. Na základě těchto dat předem shromážděná dotazu můžete rychle diagnostikovat a vyřešit problémy s výkonem a tedy věnovat víc času, zaměřuje se na svoje podnikání. 

Query Store bylo [globálně dostupné](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) ve službě Azure SQL Database od listopadu 2015. Query Store je základem pro analýzu výkonu a ladění funkcí, jako například [služby SQL Database Advisor a řídicí panel Výkon](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Query Store je v době publikování tohoto článku, spuštěný ve více než 200 000 uživatelských databází v Azure, shromažďují se informace související s dotazy na několik měsíců, bez přerušení.

> [!IMPORTANT]
> Microsoft se právě aktivace služby Query Store pro všechny databáze Azure SQL (existující i ty nové). 

## <a name="optimal-query-store-configuration"></a>Konfigurace optimální Query Store

Tato část popisuje výchozí hodnoty optimální konfiguraci, které jsou určeny k zaručit spolehlivý provoz Query Store a závislé součásti, jako například [služby SQL Database Advisor a řídicí panel Výkon](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Výchozí konfigurace je optimalizovaná pro souvislá datová kolekci, ve kterých je minimum času stráveného ve stavu vypnuto/READ_ONLY.

| Konfigurace | Popis | Výchozí | Poznámka |
| --- | --- | --- | --- |
| MAX_STORAGE_SIZE_MB |Určuje mez pro data místo Query Store můžete provést uvnitř databáze zákazníků |100 |Vynutit pro nové databáze |
| INTERVAL_LENGTH_MINUTES |Definuje velikost časový interval, během kterého se agregují a trvalé statistické údaje o shromážděných pro plány dotazů. Každý dotaz služby active plán bude mít maximálně jeden řádek pro určitou dobu definované s touto konfigurací |60 |Vynutit pro nové databáze |
| STALE_QUERY_THRESHOLD_DAYS |Čištění podle času zásad, která určuje doba uchování statistické údaje o trvalé a neaktivní dotazy |30 |Vynutit pro nové databáze a databáze s předchozím výchozí (367) |
| SIZE_BASED_CLEANUP_MODE |Určuje, zda automatické čištění probíhá při velikost dat Query Store blíží limitu |AUTO |Vynutit pro všechny databáze |
| QUERY_CAPTURE_MODE |Určuje, zda jsou sledovány všechny dotazy nebo jenom určité podmnožiny dotazy |AUTO |Vynutit pro všechny databáze |
| FLUSH_INTERVAL_SECONDS |Určuje maximální dobu, během které zachycené runtime, které se statistiky jsou uloženy v paměti, než vyprázdnění na disk |900 |Vynutit pro nové databáze |
|  | | | |

> [!IMPORTANT]
> Tyto výchozí hodnoty se automaticky použijí v konečné fázi aktivace Query Store ve všech databázích Azure SQL (viz předchozí důležitá Poznámka:). Po této světlo nahoru Azure SQL Database nebudou měnit konfigurační hodnoty nastavit zákazníky, pokud jejich mít negativní vliv na primární úlohy nebo spolehlivé operace Query Store.

Pokud chcete Zůstaňte s přední vlastní nastavení, použijte [ALTER DATABASE s možnostmi dotazu Store](https://msdn.microsoft.com/library/bb522682.aspx) se vrátit do předchozího stavu konfigurace. Podívejte se na [osvědčených postupů pomocí Query Store](https://msdn.microsoft.com/library/mt604821.aspx) další jak horní zvolili optimální parametry.

## <a name="next-steps"></a>Další postup

[SQL Database Performance Insight](sql-database-performance.md)

## <a name="additional-resources"></a>Další materiály

Pro další kontrolu informace v následujících článcích:

- [Zapisovač letových údajů pro vaši databázi](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database)
- [Monitorování výkonu pomocí Query Store](https://msdn.microsoft.com/library/dn817826.aspx)
- [Scénáře použití úložiště dotazů](https://msdn.microsoft.com/library/mt614796.aspx)
