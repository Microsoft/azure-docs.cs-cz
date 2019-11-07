---
title: Správa výpočetních prostředků
description: Přečtěte si o možnostech škálování výkonu v Azure SQL Data Warehouse. Horizontální navýšení kapacity úpravou DWU nebo snížení nákladů díky pozastavení datového skladu.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: f93996d834ab805f2228543a833c4ce601042dc4
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692630"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Správa výpočetních prostředků v Azure SQL Data Warehouse
Přečtěte si o správě výpočetních prostředků v Azure SQL Data Warehouse. Snižte náklady tím, že pozastavíte datový sklad nebo Škálujte datový sklad, abyste splnili nároky na výkon. 

## <a name="what-is-compute-management"></a>Co je Správa výpočtů?
Architektura SQL Data Warehouse odděluje úložiště a výpočetní výkon, což umožňuje nezávisle škálovat jednotlivé služby. V důsledku toho můžete škálovat výpočetní prostředky tak, aby splňovaly požadavky na výkon nezávisle na úložišti dat. Můžete také pozastavit a obnovit výpočetní prostředky. V důsledku přirozeného důsledku této architektury je [fakturace](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) za výpočetní prostředky a úložiště oddělená. Pokud pro určitou dobu nepotřebujete datový sklad, můžete ušetřit výpočetní náklady tím, že pozastavíte výpočetní výkon. 

## <a name="scaling-compute"></a>Škálování výpočetních prostředků
Můžete škálovat nebo škálovat zpátky výpočetní výkon úpravou nastavení [jednotek datového](what-is-a-data-warehouse-unit-dwu-cdwu.md) skladu pro datový sklad. Načítání a dotazování výkonu se může při přidávání dalších jednotek datového skladu zvýšit lineárně. 

Postup pro horizontální navýšení kapacity najdete v serychlých startech [Azure Portal](quickstart-scale-compute-portal.md), [PowerShellu](quickstart-scale-compute-powershell.md)nebo [T-SQL](quickstart-scale-compute-tsql.md) . Můžete také provádět operace škálování na více instancí s [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Chcete-li provést operaci škálování, SQL Data Warehouse nejprve zajistěte, aby všechny příchozí dotazy a pak transakce vrátily do konzistentního stavu. K škálování dochází pouze v případě, že je vrácení transakce dokončeno. V případě operace škálování systém odpojí vrstvu úložiště od výpočetních uzlů, přidá výpočetní uzly a pak znovu připojí vrstvu úložiště k výpočetní vrstvě. Každý datový sklad je uložený jako 60 distribucí, které jsou rovnoměrně distribuovány do výpočetních uzlů. Přidání dalších výpočetních uzlů zvyšuje výpočetní výkon. Vzhledem k tomu, že se počet výpočetních uzlů zvyšuje, počet distribucí na výpočetní uzel se sníží a poskytuje pro dotazy efektivnější výpočetní výkon. Podobně snížení jednotek datového skladu snižuje počet výpočetních uzlů, což snižuje výpočetní prostředky pro dotazy.

Následující tabulka ukazuje, jak se mění počet distribucí na výpočetní uzel při změně jednotek datového skladu.  DWU6000 poskytuje 60 výpočetních uzlů a dosahuje mnohem vyššího výkonu dotazů než DWU100. 

| Jednotky datového skladu  | \# výpočetních uzlů | \# distribucí na uzel |
| ---- | ------------------ | ---------------------------- |
| 100  | 1                  | 60                           |
| 200  | 2                  | 30                           |
| 300  | 3                  | 20                           |
| 400  | 4                  | 15                           |
| 500  | 5                  | 12                           |
| 600  | 6                  | 10                           |
| 1 000 | 10                 | 6                            |
| 1200 | 12                 | 5                            |
| 1 500 | 15                 | 4                            |
| 2000 | 20                 | 3                            |
| 3000 | 30                 | 2                            |
| 6000 | 60                 | 1                            |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Zjištění správné velikosti jednotek datového skladu

Pokud chcete zobrazit výhody výkonu při horizontálním navýšení kapacity, zvláště u větších jednotek datového skladu, budete chtít použít aspoň jednu datovou sadu o velikosti 1 TB. Pokud chcete najít nejlepší počet jednotek datového skladu pro datový sklad, zkuste horizontální navýšení kapacity a zmenšení kapacity. Po načtení dat spusťte několik dotazů s různými počty jednotek datového skladu. Vzhledem k tomu, že škálování je rychlé, můžete vyzkoušet různé úrovně výkonu za hodinu nebo méně. 

Doporučení pro vyhledání nejlepšího počtu jednotek datového skladu:

- Pro datový sklad ve vývoji Začněte výběrem menšího počtu jednotek datového skladu.  Dobrým výchozím bodem je DW400 nebo DW200.
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

## <a name="pausing-and-resuming-compute"></a>Pozastavování a obnovování výpočetních prostředků
Pozastavení výpočetního prostředí způsobí odpojení vrstvy úložiště od výpočetních uzlů. Výpočetní prostředky jsou vydávány z vašeho účtu. Při pozastavení výpočtů se vám neúčtují poplatky za výpočetní výkon. Obnovení služby COMPUTE znovu připojí úložiště k výpočetním uzlům a obnoví poplatky za výpočetní výkon. Když pozastavíte datový sklad:

* Výpočetní a paměťové prostředky se vrátí do fondu dostupných prostředků v datovém centru.
* Náklady na jednotku datového skladu jsou po dobu trvání pozastavení nulové.
* Úložiště dat není ovlivněno a vaše data zůstanou nedotčena. 
* SQL Data Warehouse zruší všechny spuštěné nebo zařazené operace ve frontě.

Po obnovení datového skladu:

* SQL Data Warehouse získá výpočetní a paměťové prostředky pro nastavení jednotek datového skladu.
* Poplatky za výpočetní prostředky pro vaše jednotky datového skladu se obnoví.
* Data budou k dispozici.
* Jakmile je datový sklad online, budete muset restartovat dotazy na úlohy.

Pokud chcete, aby byl datový sklad vždy přístupný, zvažte jeho horizontální zmenšování na nejmenší velikost, nikoli na jeho pozastavení. 

Postup pozastavení a obnovení najdete v tématu rychlé starty [Azure Portal](pause-and-resume-compute-portal.md)nebo [PowerShellu](pause-and-resume-compute-powershell.md) . Můžete také použít [pozastavení REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) nebo [obnovení REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vypusťte transakce před pozastavením nebo škálováním
Před zahájením operace pozastavení nebo škálování doporučujeme povolit dokončení stávajících transakcí.

Když službu SQL Data Warehouse pozastavíte nebo škálujete, při vytvoření požadavku na pozastavení nebo škálování se na pozadí zruší vaše dotazy.  Zrušení jednoduchého dotazu SELECT je rychlá operace a nemá téměř žádný vliv na čas potřebný k pozastavení nebo škálování instance.  Nicméně zastavení transakčních dotazů, které upravují data nebo strukturu dat, může trvat mnohem déle.  **Transakční dotazy se podle definice musí dokončit v celém rozsahu, nebo musí vrátit zpět provedené změny.**  Vracení dokončené práce transakčního dotazu zpět může trvat stejně dlouho nebo dokonce déle, než původní změna, kterou dotaz prováděl.  Například pokud zrušíte dotaz, který odstraňoval řádky a už hodinu běžel, může systému hodinu trvat, než odstraněné řádky vloží zpět.  Pokud spustíte pozastavení nebo škálování zatímco probíhají transakce, může to vypadat, že vaše pozastavení nebo škálování trvá dlouho, protože pozastavení a škálování musí počkat na dokončení odvolání transakce, než budou moci pokračovat.

Viz také [porozumění transakcím](sql-data-warehouse-develop-transactions.md)a [optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizace výpočetní správy
Informace o automatizaci operací správy výpočtů najdete v tématu [Správa výpočtů pomocí Azure Functions](manage-compute-with-azure-functions.md).

Dokončení operací škálování na více instancí, pozastavení a obnovení může trvat několik minut. Pokud používáte automatické škálování, pozastavení nebo obnovení, doporučujeme, abyste před pokračováním v jiné akci implementovali logiku pro zajištění, že byly dokončeny určité operace. Kontrola stavu datového skladu prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci takových operací. 

Pokud chcete zjistit stav datového skladu, přečtěte si rychlý Start pro [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) nebo [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) . Stav datového skladu můžete také ověřit pomocí [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Oprávnění

Škálování datového skladu vyžaduje oprávnění popsaná v [příkazu ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pozastavení a obnovení vyžadují oprávnění [přispěvatele databáze SQL](../role-based-access-control/built-in-roles.md#sql-db-contributor) , konkrétně Microsoft. SQL/servery/databáze/akce.


## <a name="next-steps"></a>Další kroky
Další aspekty správy výpočetních prostředků je přidělením různých výpočetních prostředků pro jednotlivé dotazy, najdete v příručce průvodce [správou výpočetních](manage-compute-with-azure-functions.md) prostředků. Další informace najdete v tématu [třídy prostředků pro správu úloh](resource-classes-for-workload-management.md).
