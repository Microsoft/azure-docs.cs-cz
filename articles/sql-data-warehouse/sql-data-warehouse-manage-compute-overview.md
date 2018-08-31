---
title: Správa výpočetních prostředků ve službě Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Další informace o škálování výkonu ve službě Azure SQL Data Warehouse seznámili s funkcemi. Horizontální navýšení kapacity úpravou jednotek Dwu nebo nižší náklady pomocí pozastavení datového skladu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 53a801a367e6948c3070224b7ff36a013a1faab3
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300846"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Správa výpočetních služeb v Azure SQL Data Warehouse
Další informace o správě výpočetních prostředků ve službě Azure SQL Data Warehouse. Nižší náklady pomocí pozastavení datového skladu nebo škálovat datový sklad na splňovat požadavky na výkon. 

## <a name="what-is-compute-management"></a>Co je Správa výpočetních?
Architektura služby SQL Data Warehouse odděluje úložiště a výpočetního výkonu, což umožňuje nezávislé škálování. V důsledku toho je možné škálovat výpočetní funkce pro zajištění nezávisle na úložišti dat požadavky na výkon. Můžete taky pozastavit a obnovit výpočetní prostředky. Přirozené důsledkem této architektury je, že [fakturační](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) výpočetních a úložných neběží. Pokud není nutné používat službu data warehouse nějakou dobu, můžete ušetřit náklady na výpočetní výkon když pozastavíte výpočetní prostředky. 

## <a name="scaling-compute"></a>Škálování výpočetních
Můžete horizontálně navýšit kapacitu nebo škálovat výpočetní prostředky zpět úpravou [jednotkách datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) nastavení pro váš datový sklad. Načítání a výkonu dotazování umožňujícímu lineárně zvýšit přidávat další jednotky datového skladu. 

Postup škálování na víc systémů najdete v tématu [webu Azure portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md), nebo [T-SQL](quickstart-scale-compute-tsql.md) šablon rychlý start. Můžete také provádět operace škálování na více instancí s [rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

K provedení operace škálování, SQL Data Warehouse nejprve ukončí všechny příchozí dotazy a vrátí zpět transakce zajistit konzistentní stav. Škálování nastane pouze po dokončení odvolání transakce. Pro operace škálování systém odpojí vrstvy úložiště z výpočetních uzlů, přidá výpočetních uzlů a pak znovu vrstvy úložiště na výpočetní vrstvě. Každý datový sklad se ukládá jako 60 distribucí, které jsou rovnoměrně distribuovány do výpočetních uzlů. Přidání další výpočetní uzly přidá další výpočetní výkon. Jak se zvyšuje počet výpočetních uzlů, sníží počet distribucí na výpočetním uzlu, poskytuje větší výpočetní výkon pro vaše dotazy. Snížení jednotky datového skladu, snižuje počet výpočetních uzlů, což snižuje výpočetní prostředky pro dotazy.

Následující tabulka ukazuje, jak změnit počet distribucí za výpočetní uzel změny jako jednotky datového skladu.  DWU6000 poskytuje 60 výpočetních uzlů a dosahuje mnohem vyšší výkon než DWU100 dotazu. 

| Jednotky datového skladu  | \# výpočetních uzlů | \# distribucí, počtu uzlů |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Vyhledání správné velikosti jednotky datového skladu

Zobrazíte horizontální navýšení kapacity, zejména u větších jednotky datového skladu, přinese zlepšení výkonu, která chcete použít nejméně 1 TB datové sady. Najít nejlepší počet jednotek datového skladu pro váš datový sklad, zkuste škálovat nahoru a dolů. Po načtení dat spustili jste několik dotazů s různým počtem jednotek datového skladu. Škálování je rychlé, můžete zkusit různé úrovně výkonu za hodinu nebo méně. 

Doporučení pro vyhledání nejlepší počet datových skladů jednotky:

- Pro datový sklad ve vývoji začněte tím, že vyberete menší počet jednotek datového skladu.  Dobrým výchozím bodem je DW400 nebo úroveň DW200.
- Monitorování výkonu vaší aplikace, počtu vybraných jednotek datového skladu ve srovnání s výkonem, které můžete sledovat.
- Předpokládejme lineární stupnice a určit, kolik potřebujete zvětšit nebo zmenšit jednotky datového skladu. 
- Pokračujte v provádění úprav, dokud se nedostanete na úrovni optimální výkon pro vaše obchodní požadavky.

## <a name="when-to-scale-out"></a>Když pro horizontální navýšení kapacity
Horizontální navýšení kapacity jednotky datového skladu má vliv na tyto aspekty výkonu:

- Lineárně zlepšuje výkon systému pro kontroly, agregace a příkazů CTAS.
- Načítání dat se zvyšuje počet čtečky a zapisovače.
- Maximální počet souběžných dotazů a slotů souběžnosti.

Doporučení, kdy pro horizontální navýšení kapacity datového skladu jednotky:

- Před provedením velké datové operace načítání nebo transformace, horizontální navýšení kapacity na zpřístupnit data rychleji.
- Během pracovní doby ve špičce horizontálně navýšit kapacitu tak, aby vyhovovaly velký počet souběžných dotazů. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co když horizontální navýšení kapacity nezvyšuje výkon?

Přidání jednotky datového skladu zvýšení paralelismu. Pokud je práce rovnoměrně rozdělit mezi jednotlivými výpočetními uzly, další paralelismu zvyšuje výkon dotazů. Pokud se horizontální navýšení kapacity se mění výkon, existují některé důvody, proč k tomu může dojít. Může být zkosené data prostřednictvím distribuce nebo dotazy může být zavedení velké množství přesun dat. Prozkoumat problémy s výkonem dotazů, najdete v článku [řešení potíží s výkonem](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pozastavení a obnovení výpočetních prostředků
Pozastavení výpočetních způsobí, že vrstvy úložiště se odpojit od výpočetních uzlů. Výpočetní prostředky, které jsou vydávány ze svého účtu. Se vám neúčtovaly výpočetní prostředky během pozastavení výpočetní prostředky. Obnovení výpočetních prostředků znovu úložiště do výpočetních uzlů a obnoví poplatky za výpočetní výkon. Při přesunutí ukazatele myši datového skladu:

* Výpočetní a paměťové prostředky jsou vráceny do fondu dostupných prostředků v datovém centru
* Data warehouse jednotku náklady jsou nulové po dobu trvání pozastavení.
* Úložiště dat není ovlivněn vaše data zůstanou beze změny. 
* SQL Data Warehouse zruší všechny spuštěné nebo zařazené ve frontě operace.

Při obnovení datového skladu:

* SQL Data Warehouse získá výpočetní a paměťové prostředky pro vaše nastavení jednotek datového skladu.
* COMPUTE poplatky za svému životopisu jednotky datového skladu.
* Data k dispozici.
* Po datový sklad je online, budete muset restartovat své dotazy úlohy.

Pokud chcete, aby váš datový sklad přístupné, zvažte vertikální snížení jeho kapacity na nejmenší velikost, místo jeho pozastavení. 

Pro pozastavení a pokračování kroků, najdete v článku [webu Azure portal](pause-and-resume-compute-portal.md), nebo [Powershellu](pause-and-resume-compute-powershell.md) šablon rychlý start. Můžete také použít [pozastavit rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) nebo [obnovit rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vypusťte transakce před pozastavením nebo škálováním
Doporučujeme vám umožní existující transakce dokončeny zahájit operaci pozastavení nebo škálování.

Když službu SQL Data Warehouse pozastavíte nebo škálujete, při vytvoření požadavku na pozastavení nebo škálování se na pozadí zruší vaše dotazy.  Zrušení jednoduchého dotazu SELECT je rychlá operace a nemá téměř žádný vliv na čas potřebný k pozastavení nebo škálování instance.  Nicméně zastavení transakčních dotazů, které upravují data nebo strukturu dat, může trvat mnohem déle.  **Transakční dotazy se podle definice musí dokončit v celém rozsahu, nebo musí vrátit zpět provedené změny.**  Vracení dokončené práce transakčního dotazu zpět může trvat stejně dlouho nebo dokonce déle, než původní změna, kterou dotaz prováděl.  Například pokud zrušíte dotaz, který odstraňoval řádky a už hodinu běžel, může systému hodinu trvat, než odstraněné řádky vloží zpět.  Pokud spustíte pozastavení nebo škálování zatímco probíhají transakce, může to vypadat, že vaše pozastavení nebo škálování trvá dlouho, protože pozastavení a škálování musí počkat na dokončení odvolání transakce, než budou moci pokračovat.

Viz také [vysvětlení transakcí](sql-data-warehouse-develop-transactions.md), a [Optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizace správy výpočetní prostředky
K automatizaci výpočetní operace správy, najdete v článku [spravovat výpočetní prostředky s využitím Azure functions](manage-compute-with-azure-functions.md).

Každý horizontální navýšení kapacity, pozastavení a pokračování operace může trvat několik minut. Pokud jste se škálování, pozastavení, nebo obnovení automaticky, doporučujeme, implementovat logiku a ujistěte se, že jste dokončili určité operace než budete pokračovat s další akci. Kontroluje se stav datového skladu prostřednictvím různých koncových bodů umožňuje správnou implementaci automatizace těchto operací. 

Kontrola stavu datového skladu, najdete v článku [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) nebo [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rychlý start. Můžete také zkontrolovat stav datového skladu s [rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Oprávnění

Škálování datového skladu, vyžaduje oprávnění popsaná v [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pozastavení a obnovení vyžadují [Přispěvatel databází SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) oprávnění, konkrétně Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Další postup
Dalším aspektem správu výpočetních prostředků je přidělování různých výpočetních prostředků pro jednotlivé dotazy. Další informace najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
