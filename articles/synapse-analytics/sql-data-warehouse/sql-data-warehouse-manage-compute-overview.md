---
title: Správa výpočetních prostředků pro vyhrazený fond SQL (dřív SQL DW)
description: Přečtěte si o možnostech škálování výkonu vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics. Nahorizontální navýšení kapacity úpravou DWU nebo snížení nákladů díky pozastavení vyhrazeného fondu SQL (dřív SQL DW).
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 20087fbf4d5c37f1501df08cc294a10ddb9118e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103601810"
---
# <a name="manage-compute-for-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Správa výpočetních prostředků pro vyhrazený fond SQL (dřív SQL DW) ve službě Azure synapse Analytics

Přečtěte si o správě výpočetních prostředků vyhrazeného fondu SQL (dřív SQL DW) ve službě Azure synapse Analytics. Snižte náklady tím, že pozastavíte vyhrazený fond SQL, nebo můžete škálovat vyhrazený fond SQL tak, aby splňoval požadavky na výkon.

## <a name="what-is-compute-management"></a>Co je Správa výpočtů?

Architektura vyhrazeného fondu SQL (dříve SQL DW) odděluje úložiště a výpočetní prostředky, což umožňuje nezávisle škálovat jednotlivé služby. V důsledku toho je možné škálovat výpočty tak, aby byly splněny požadavky na výkon nezávisle na úložišti dat. Můžete také pozastavit výpočetní prostředky a obnovit jejich chod. V důsledku přirozeného důsledku této architektury je [fakturace](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) za výpočetní prostředky a úložiště oddělená. Pokud nepotřebujete použít vyhrazený fond SQL (dřív než SQL DW) pro určitou dobu, můžete ušetřit náklady na výpočetní prostředky tím, že pozastavíte výpočetní výkon.

## <a name="scaling-compute"></a>Škálování výpočetních prostředků

Výpočetní prostředky můžete škálovat nebo škálovat zpátky tak, že upravíte nastavení [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) pro vyhrazený fond SQL (dřív SQL DW). Výkon načítání a dotazování se může lineárně zvyšovat, stačí přidávat další jednotky datového skladu.

Postup pro horizontální navýšení kapacity najdete v serychlých startech [Azure Portal](quickstart-scale-compute-portal.md), [PowerShellu](quickstart-scale-compute-powershell.md)nebo [T-SQL](quickstart-scale-compute-tsql.md) . Můžete také provádět operace škálování na více instancí s [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Vyhrazený fond SQL (dříve SQL DW) nejprve zastavuje všechny příchozí dotazy a potom vrátí transakce, aby zajistil konzistentní stav. Ke škálování dojde až po odvolání transakcí. V případě operace škálování systém odpojí vrstvu úložiště od výpočetních uzlů, přidá výpočetní uzly a pak znovu připojí vrstvu úložiště k výpočetní vrstvě. Každý vyhrazený fond SQL (dřív SQL DW) je uložený jako 60 distribuce, které jsou rovnoměrně distribuovány do výpočetních uzlů. Přidání dalších výpočetních uzlů zvyšuje výpočetní výkon. Vzhledem k tomu, že se počet výpočetních uzlů zvyšuje, počet distribucí na výpočetní uzel se sníží a poskytuje pro dotazy efektivnější výpočetní výkon. Podobně snížení jednotek datového skladu snižuje počet výpočetních uzlů, což snižuje výpočetní prostředky pro dotazy.

Následující tabulka ukazuje, jak se mění počet distribucí na výpočetní uzel při změně jednotek datového skladu.  DW30000c poskytuje 60 výpočetních uzlů a dosahuje mnohem vyššího výkonu dotazů než DW100c.

| Jednotky datového skladu  | \# výpočetních uzlů | \# distribucí na uzel |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c.   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |

## <a name="finding-the-right-size-of-data-warehouse-units"></a>Zjištění správné velikosti jednotek datového skladu

Pokud chcete zobrazit výhody výkonu při horizontálním navýšení kapacity, zvláště u větších jednotek datového skladu, budete chtít použít aspoň jednu datovou sadu o velikosti 1 TB. Pokud chcete najít nejlepší počet jednotek datového skladu pro vyhrazený fond SQL (dřív SQL DW), zkuste horizontální navýšení kapacity a zmenšení kapacity. Po načtení dat spusťte několik dotazů s různými počty jednotek datového skladu. Vzhledem k tomu, že škálování je rychlé, můžete vyzkoušet různé úrovně výkonu za hodinu nebo méně.

Doporučení pro vyhledání nejlepšího počtu jednotek datového skladu:

- Pro vyhrazený fond SQL (dřív SQL DW) ve vývoji Začněte výběrem menšího počtu jednotek datového skladu.  Dobrým výchozím bodem je DW400c nebo DW200c.
- Monitorujte výkon aplikace a sledujte počet vybraných jednotek datového skladu v porovnání s výkonem, které sledujete.
- Předpokládejme Lineární škálování a určete, kolik potřebujete zvýšit nebo snížit jednotky datového skladu.
- Pokračujte v provádění úprav, dokud nedosáhnete optimální úrovně výkonu pro vaše podnikové požadavky.

## <a name="when-to-scale-out"></a>Kdy horizontální navýšení kapacity

Horizontální navýšení kapacity jednotek datového skladu má dopad na tyto aspekty výkonu:

- Lineárně vylepšuje výkon systému pro prohledávání, agregace a příkazy CTAS.
- Zvyšuje počet čtenářů a zapisovačů pro načítání dat.
- Maximální počet souběžných dotazů a souběžných slotů.

Doporučení pro horizontální navýšení kapacity datového skladu:

- Než provedete velkou operaci načítání nebo transformace dat, Škálujte, aby byla data rychleji dostupná.
- Během špičky v pracovní době se horizontální navýšení kapacity přizpůsobí většímu počtu souběžných dotazů.

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co když horizontální škálování nezvyšuje výkon?

Přidávají se jednotky datového skladu, které zvyšují paralelismus. Pokud je práce rovnoměrně rozdělena mezi výpočetními uzly, další paralelismus vylepšuje výkon dotazů. Pokud horizontální navýšení kapacity nemění výkon, existují některé důvody, proč k tomu může dojít. Vaše data se můžou v rámci distribucí zkosit nebo dotazy můžou zavádět velký objem přesunu dat. Problémy s výkonem dotazů můžete prozkoumat v tématu [řešení potíží s výkonem](sql-data-warehouse-troubleshoot.md#performance).

## <a name="pausing-and-resuming-compute"></a>Pozastavení a obnovení výpočetních prostředků

Pozastavení výpočetního prostředí způsobí odpojení vrstvy úložiště od výpočetních uzlů. Výpočetní prostředky jsou vydávány z vašeho účtu. Při pozastavení výpočtů se vám neúčtují poplatky za výpočetní výkon. Obnovení služby COMPUTE znovu připojí úložiště k výpočetním uzlům a obnoví poplatky za výpočetní výkon.
Když pozastavíte vyhrazený fond SQL (dříve SQL DW):

- Výpočetní a paměťové prostředky se vrátí do fondu dostupných prostředků v datovém centru.
- Náklady na jednotku datového skladu jsou po dobu trvání pozastavení nulové.
- Úložiště dat není ovlivněno a vaše data zůstanou nedotčena.
- Všechny spuštěné nebo zařazené operace byly zrušeny.

Když obnovíte vyhrazený fond SQL (dřív SQL DW):

- Vyhrazený fond SQL (dříve SQL DW) získává výpočetní a paměťové prostředky pro nastavení jednotek datového skladu.
- Poplatky za výpočetní prostředky pro vaše jednotky datového skladu se obnoví.
- Data budou k dispozici.
- Až bude vyhrazený fond SQL (dřív SQL DW) online, musíte restartovat dotazy na úlohy.

Pokud chcete vždy používat vyhrazený fond SQL (dříve SQL DW), zvažte jeho horizontální zmenšování na nejmenší velikost, nikoli na jeho pozastavení.

Postup pozastavení a obnovení najdete v tématu rychlé starty [Azure Portal](pause-and-resume-compute-portal.md)nebo [PowerShellu](pause-and-resume-compute-powershell.md) . Můžete také použít [pozastavení REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) nebo [obnovení REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vypusťte transakce před pozastavením nebo škálováním

Před zahájením operace pozastavení nebo škálování doporučujeme povolit dokončení stávajících transakcí.

Když pozastavíte nebo zmenšíte svůj vyhrazený fond SQL (dřív SQL DW), vaše dotazy se po zahájení žádosti o pozastavení nebo škálování zruší. Zrušení jednoduchého dotazu SELECT je rychlá operace a nemá téměř žádný vliv na čas potřebný k pozastavení nebo škálování instance.  Nicméně zastavení transakčních dotazů, které upravují data nebo strukturu dat, může trvat mnohem déle. **Transakční dotazy se podle definice musí dokončit v celém rozsahu, nebo musí vrátit zpět provedené změny.** Vracení dokončené práce transakčního dotazu zpět může trvat stejně dlouho nebo dokonce déle, než původní změna, kterou dotaz prováděl. Například pokud zrušíte dotaz, který odstraňoval řádky a už hodinu běžel, může systému hodinu trvat, než odstraněné řádky vloží zpět. Pokud spustíte pozastavení nebo škálování zatímco probíhají transakce, může to vypadat, že vaše pozastavení nebo škálování trvá dlouho, protože pozastavení a škálování musí počkat na dokončení odvolání transakce, než budou moci pokračovat.

Viz také [porozumění transakcím](sql-data-warehouse-develop-transactions.md)a [optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizace výpočetní správy

Informace o automatizaci operací správy výpočtů najdete v tématu [Správa výpočtů pomocí Azure Functions](manage-compute-with-azure-functions.md).

Dokončení operací škálování na více instancí, pozastavení a obnovení může trvat několik minut. Pokud používáte automatické škálování, pozastavení nebo obnovení, doporučujeme, abyste před pokračováním v jiné akci implementovali logiku pro zajištění, že byly dokončeny určité operace. Kontrola vyhrazeného fondu SQL (dříve SQL DW) prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci takových operací.

Pokud chcete kontrolovat vyhrazený fond SQL (dřív SQL DW), přečtěte si téma rychlý Start pro [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) nebo [T-SQL](quickstart-scale-compute-tsql.md#check-dedicated-sql-pool-formerly-sql-dw-state) . Můžete také kontrolovat vyhrazený fond SQL (dříve SQL DW) pomocí [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).

## <a name="permissions"></a>Oprávnění

Škálování vyhrazeného fondu SQL (dříve SQL DW) vyžaduje oprávnění popsaná v [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Pozastavení a obnovení vyžadují oprávnění [přispěvatele databáze SQL](../../role-based-access-control/built-in-roles.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#sql-db-contributor) , konkrétně Microsoft. SQL/servery/databáze/akce.

## <a name="next-steps"></a>Další kroky

Další aspekty správy výpočetních prostředků je přidělením různých výpočetních prostředků pro jednotlivé dotazy, najdete v příručce průvodce [správou výpočetních](manage-compute-with-azure-functions.md) prostředků. Další informace najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
