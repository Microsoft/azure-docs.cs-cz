---
title: Správu výpočetních prostředků v Azure SQL Data Warehouse | Microsoft Docs
description: Další informace o výkonu s více instancemi možnosti v Azure SQL Data Warehouse. Horizontální navýšení kapacity úpravou Dwu nebo nižší poplatky za ponecháte datového skladu.
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: ''
ms.assetid: e13a82b0-abfe-429f-ac3c-f2b6789a70c6
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 02/20/2018
ms.author: elbutter
ms.openlocfilehash: c34e37f0c6393c65d4b60705012769608bb7395b
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Správa výpočtů v Azure SQL Data Warehouse
Další informace o správě výpočetních prostředků v Azure SQL Data Warehouse. Nižší náklady ponecháte datového skladu nebo změně velikosti datového skladu k splňovat požadavky na výkon. 

## <a name="what-is-compute-management"></a>Co je výpočetní management?
Architektura služby SQL Data Warehouse odděluje úložiště a výpočty, což nezávislé škálování. V důsledku toho je možné škálovat výpočetní splňovat požadavky na výkon nezávislé datové úložiště. Můžete taky pozastavit a obnovit výpočetní prostředky. Přirozený důsledek této architektury je, že [fakturace](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) pro výpočetní prostředí a úložiště jsou oddělené. Pokud nemusíte použít datový sklad nějakou dobu, můžete ušetřit náklady na výpočetní pozastavení výpočty. 

## <a name="scaling-compute"></a>Škálování výpočetní
Můžete škálovat nebo úpravou škálovat výpočetní zpět [datového skladu jednotky](what-is-a-data-warehouse-unit-dwu-cdwu.md) nastavení pro datový sklad. Načítání a dotazování výkonu může zvýšit lineárně, jak přidat další jednotky datového skladu. SQL Data Warehouse nabízí [služby úrovně](performance-tiers.md#service-levels) pro datového skladu jednotky, které zajišťují výraznou změnu ve výkonu při změně měřítka out nebo zpět. 

Postup Škálováním na více systémů najdete v tématu [portál Azure](quickstart-scale-compute-portal.md), [prostředí PowerShell](quickstart-scale-compute-powershell.md), nebo [T-SQL](quickstart-scale-compute-tsql.md) – elementy quickstart. Můžete také provádět operace Škálováním na více systémů s [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

SQL Data Warehouse k provedení operace škálování, nejprve ukončí všechny příchozí dotazy a vrátí zpět transakce zajistit konzistentní stav. Škálování dochází pouze po dokončení vrácení transakce. U operace škálování systém odpojí vrstvy úložiště z výpočetních uzlů, přidá výpočetní uzly a poté reattaches vrstvy úložiště pro výpočetní vrstvy. Každý datový sklad je uloženo jako 60 distribuce, které jsou rovnoměrně rozloženy na výpočetní uzly. Přidání další výpočetní uzly přidá že více výpočetního výkonu. Jako hodnota se zvyšuje počet výpočetních uzlů, snižuje počet distribuce na výpočetním uzlu, přičemž bude poskytovat další výpočetní výkon vašich dotazů. Snížení jednotky datového skladu, snižuje počet výpočetních uzlů, což snižuje výpočetní prostředky pro dotazy.

Následující tabulka ukazuje, jak změnit počet distribuce za výpočetní uzel změny jako jednotky datového skladu.  DWU6000 poskytuje 60 výpočetních uzlů a dosahuje mnohem vyšší výkon než DWU100 dotazu. 

| Jednotky datového skladu  | \# výpočetních uzlů | \# distribucí na uzel |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1 500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Hledání správnou velikost jednotky datového skladu

Zobrazíte výkonu výhody škálování, hlavně pro větší jednotky datového skladu, budete chtít použít alespoň 1 TB datové sady. Chcete-li najít nejlepší počet jednotky datového skladu pro datový sklad, zkuste škálování nahoru a dolů. Po načtení dat spusťte pár dotazů s různými počty jednotky datového skladu. Škálování je rychlé, můžete zkusit různé úrovně výkonu za hodinu nebo méně. 

Doporučení pro hledání nejlepší počet datového skladu jednotky:

- Pro datový sklad v vývoj Začněte výběrem menší počet jednotky datového skladu.  Vhodná výchozí hodnota je DW400 nebo DW200.
- Monitorujte výkon aplikací, sledování počet vybrané jednotky datového skladu ve srovnání s, které můžete sledovat výkon.
- Předpokládat lineární stupnice a určit, kolik je potřeba zvýšit nebo snížit jednotky datového skladu. 
- Pokračujte v provádění úprav, dokud se nedostanete na úroveň optimálního výkonu pro vaše podnikové požadavky.

## <a name="when-to-scale-out"></a>Když chcete škálovat.
Škálování jednotky datového skladu má dopad na tyto aspekty výkonu:

- Lineárně zvyšuje výkon systému pro funkce CTAS příkazy, agregace a kontroly.
- Načítání dat se zvyšuje počet čtení a zápis.
- Maximální počet souběžných dotazů a sloty souběžnosti.

Doporučení pro kdy horizontální navýšení kapacity datového skladu jednotky:

- Před provedením operace načítání nebo transformace dat těžká, horizontální navýšení kapacity Pokud chcete zpřístupnit data rychleji.
- Během pracovní dobu ve špičce škálovat. aby bylo možné ošetřit velký počet souběžných dotazů. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co když škálování nezvyšuje výkon?

Přidání zvýšení paralelismus jednotky datového skladu. Pokud je práce rovnoměrně rozdělit mezi výpočetní uzly, další paralelismus zlepšuje výkon dotazů. Pokud výkon není změnit horizontální navýšení kapacity, nejsou některé z důvodů, proč k tomu může dojít. Vaše data mohou být nesouměrně rozdělí na distribucí nebo dotazy může být představení velké množství přesun dat. Chcete-li prozkoumat problémy s výkonem dotazu, přečtěte si téma [řešení potíží s výkonem](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pozastavení a obnovení výpočetní
Pozastavení výpočetní způsobí, že vrstvy úložiště odpojit z výpočetních uzlů. Výpočetní prostředky jsou vydávány z vašeho účtu. Vám není účtován výpočetní při výpočetní je pozastavena. Obnovení výpočetní reattaches úložiště na výpočetní uzly a obnoví poplatky pro výpočet. Při přesunutí ukazatele myši datového skladu:

* Paměťovou a výpočetní prostředky se vrátí do fondu dostupné prostředky v datovém centru
* Náklady na jednotky datového skladu jsou nula po dobu trvání pozastavení.
* Úložiště dat není ovlivněná a vaše data zůstává beze změn. 
* SQL Data Warehouse zruší všechny spuštěné nebo zařazené ve frontě operace.

Při obnovení datového skladu:

* SQL Data Warehouse získá paměťovou a výpočetní prostředky pro vaše nastavení jednotky datového skladu.
* Výpočetní poplatky pro obnovení jednotky vašeho datového skladu.
* Data k dispozici.
* Po datového skladu je online, musíte restartovat své dotazy zatížení.

Pokud chcete, aby váš datový sklad přístupný, zvažte škálování dolů na nejmenší velikost než pozastavení. 

Pro pozastavení a obnovení kroky naleznete [portál Azure](pause-and-resume-compute-portal.md), nebo [prostředí PowerShell](pause-and-resume-compute-powershell.md) – elementy quickstart. Můžete také [pozastavit REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) nebo [obnovit REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vypusťte transakce před pozastavením nebo škálováním
Doporučujeme vám umožní na Dokončit před spuštěním operace pozastavení nebo měřítka vrácení stávajících transakcí.

Když službu SQL Data Warehouse pozastavíte nebo škálujete, při vytvoření požadavku na pozastavení nebo škálování se na pozadí zruší vaše dotazy.  Zrušení jednoduchého dotazu SELECT je rychlá operace a nemá téměř žádný vliv na čas potřebný k pozastavení nebo škálování instance.  Nicméně zastavení transakčních dotazů, které upravují data nebo strukturu dat, může trvat mnohem déle.  **Transakční dotazy se podle definice musí dokončit v celém rozsahu, nebo musí vrátit zpět provedené změny.**  Vracení dokončené práce transakčního dotazu zpět může trvat stejně dlouho nebo dokonce déle, než původní změna, kterou dotaz prováděl.  Například pokud zrušíte dotaz, který odstraňoval řádky a už hodinu běžel, může systému hodinu trvat, než odstraněné řádky vloží zpět.  Pokud spustíte pozastavení nebo škálování zatímco probíhají transakce, může to vypadat, že vaše pozastavení nebo škálování trvá dlouho, protože pozastavení a škálování musí počkat na dokončení odvolání transakce, než budou moci pokračovat.

Viz také [transakce](sql-data-warehouse-develop-transactions.md)a [Optimalizace transakce][optimalizace transakce](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizace správy výpočetní
K automatizaci operací správy výpočetní, najdete v části [spravovat výpočetní s Azure functions](manage-compute-with-azure-functions.md).

Každý Škálováním na více systémů, pozastavení a obnovení operace může trvat několik minut na dokončení. Pokud jste se škálování, pozastavení, nebo obnovení automaticky, doporučujeme, abyste implementace logiku a ujistěte se, že některé operace dokončili před pokračováním další akci. Kontroluje stav datového skladu pomocí různých koncové body umožňuje správně implementovat automatizace těchto operací. 

Chcete-li zkontrolovat stav datového skladu, se [prostředí PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) nebo [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rychlý start. Můžete také zkontrolovat stav datového skladu s [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Oprávnění

Změna měřítka datového skladu vyžaduje oprávnění popsaná v [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse.md).  Pozastavení a obnovení vyžadují [Přispěvatel databází SQL](../active-directory/role-based-access-built-in-roles.md#sql-db-contributor) oprávnění, konkrétně Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Další postup
Další aspekt správu výpočetních prostředků je přidělování různých výpočetních prostředků pro jednotlivé dotazy. Další informace najdete v tématu [třídy prostředků pro úlohy správy](resource-classes-for-workload-management.md).
