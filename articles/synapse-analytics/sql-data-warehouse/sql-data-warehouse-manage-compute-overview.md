---
title: Správa výpočetního prostředku pro fond SQL
description: Další informace o možnostech škálování výkonu ve fondu SQL Azure Synapse Analytics. Horizontální navýšení kapacity úpravou DWU nebo nižší náklady pozastavením datového skladu.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/12/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 4c6c4d97282387fbcee1d7e8b55b95c01e3dded5
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351603"
---
# <a name="manage-compute-in-azure-synapse-analytics-data-warehouse"></a>Správa výpočetních prostředků v datovém skladu Azure Synapse Analytics

Přečtěte si o správě výpočetních prostředků ve fondu SQL Azure Synapse Analytics. Snižte náklady pozastavením fondu SQL nebo škálování datového skladu tak, aby splňoval požadavky na výkon. 

## <a name="what-is-compute-management"></a>Co je správa výpočetních prostředků?

Architektura datového skladu odděluje úložiště a výpočetní prostředky, což umožňuje každému škálovat nezávisle. V důsledku toho je možné škálovat výpočty tak, aby byly splněny požadavky na výkon nezávisle na úložišti dat. Můžete také pozastavit výpočetní prostředky a obnovit jejich chod. Přirozeným důsledkem této architektury je, že [fakturace](https://azure.microsoft.com/pricing/details/sql-data-warehouse/) pro výpočetní prostředky a úložiště je samostatná. Pokud nebudete datový sklad nějakou dobu potřebovat, můžete ušetřit náklady na výpočetní výkon pozastavením výpočetních prostředků. 

## <a name="scaling-compute"></a>Škálování výpočetních prostředků

Výpočetní výkon můžete škálovat nebo škálovat zpět úpravou nastavení [jednotek datového skladu](what-is-a-data-warehouse-unit-dwu-cdwu.md) pro fond SQL. Výkon načítání a dotazování se může lineárně zvyšovat, stačí přidávat další jednotky datového skladu. 

Kroky horizontálního navýšení kapacity najdete na [webu Azure Portal](quickstart-scale-compute-portal.md), [PowerShell](quickstart-scale-compute-powershell.md)nebo [T-SQL](quickstart-scale-compute-tsql.md) rychlé starty. Můžete také provádět operace horizontálního navýšení kapacity pomocí [rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute).

Chcete-li provést operaci škálování, fond SQL nejprve zabije všechny příchozí dotazy a potom vrátí transakce, aby byl zajištěn konzistentní stav. Ke škálování dojde až po odvolání transakcí. Pro operaci škálování systém odpojí vrstvu úložiště od výpočetních uzlů, přidá výpočetní uzly a pak znovu připojí vrstvu úložiště k výpočetní vrstvě. Každý fond SQL je uložen jako 60 distribucí, které jsou rovnoměrně distribuovány do výpočetních uzlů. Přidání dalších výpočetních uzlů zvyšuje výpočetní výkon. S přirůstá počet výpočetních uzlů se snižuje počet distribucí na výpočetní uzel a poskytuje větší výpočetní výkon pro vaše dotazy. Podobně snížení jednotek datového skladu snižuje počet výpočetních uzlů, což snižuje výpočetní prostředky pro dotazy.

Následující tabulka ukazuje, jak se mění počet distribucí na výpočetní uzel při změně jednotek datového skladu.  DW30000c poskytuje 60 výpočetních uzlů a dosahuje mnohem vyšší výkon dotazu než DW100c. 

| Jednotky datového skladu  | \#výpočetních uzlů | \#rozdělení na uzel |
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


## <a name="finding-the-right-size-of-data-warehouse-units"></a>Nalezení správné velikosti jednotek datového skladu

Chcete-li zobrazit výhody výkonu horizontálnínavýšení kapacity, zejména pro větší jednotky datového skladu, chcete použít alespoň 1 TB datové sady. Chcete-li najít nejlepší počet jednotek datového skladu pro váš fond SQL, zkuste škálování nahoru a dolů. Spusťte několik dotazů s různým počtem jednotek datového skladu po načtení dat. Vzhledem k tomu, že škálování je rychlé, můžete vyzkoušet různé úrovně výkonu za hodinu nebo méně. 

Doporučení pro nalezení nejlepšího počtu jednotek datového skladu:

- Pro fond SQL ve vývoji začněte výběrem menšího počtu jednotek datového skladu.  Dobrým výchozím bodem je DW400c nebo DW200c.
- Sledujte výkon aplikace a sledujte počet vybraných jednotek datového skladu ve srovnání s výkonem, který sledujete.
- Předpokládejme lineární měřítko a určete, kolik je potřeba zvýšit nebo snížit jednotky datového skladu. 
- Pokračujte v úpravách, dokud nedosáhnete optimální úrovně výkonu pro vaše obchodní požadavky.

## <a name="when-to-scale-out"></a>Kdy horizontální navýšení kapacity

Škálování jednotek datového skladu má vliv na tyto aspekty výkonu:

- Lineárně zlepšuje výkon systému pro prohledává, agregace a CTAS příkazy.
- Zvyšuje počet čteček a zapisovačů pro načítání dat.
- Maximální počet souběžných dotazů a slotů souběžnosti.

Doporučení, kdy horizontální navýšení kapacity jednotek datového skladu:

- Před provedením operace načítání nebo transformace těžkých dat můžete vertikálně využít kapacitu, aby byla data k dispozici rychleji.
- Během pracovní doby ve špičce můžete škálovat tak, aby vyhovovaly většímu počtu souběžných dotazů. 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>Co když horizontální navýšení kapacity nezlepší výkon?

Přidání jednotek datového skladu zvyšuje paralelismus. Pokud je práce rovnoměrně rozdělena mezi výpočetní uzly, další paralelismus zlepšuje výkon dotazu. Pokud horizontální navýšení kapacity nezmění výkon, existují některé důvody, proč k tomu může dojít. Vaše data mohou být zkosená napříč distribucemi nebo dotazy mohou zavádět velké množství přesunu dat. Chcete-li zjistit problémy s výkonem [dotazu, přečtěte si článek Řešení potíží s výkonem](sql-data-warehouse-troubleshoot.md#performance). 

## <a name="pausing-and-resuming-compute"></a>Pozastavení a obnovení výpočetních prostředků

Pozastavení výpočetních prostředků způsobí, že vrstva úložiště se odpojí od výpočetních uzlů. Výpočetní prostředky se uvolní z vašeho účtu. Při pozastavení výpočetních prostředků se vám neúčtují poplatky za výpočetní prostředky. Obnovení výpočetní znovu připojit úložiště výpočetníu uzly a obnoví poplatky pro výpočetní výkon. Když pozastavíte fond SQL:

* Výpočetní a paměťové prostředky jsou vráceny do fondu dostupných prostředků v datovém centru
* Jednotkové náklady datového skladu jsou po dobu pozastavení nulové.
* Ukládání dat není ovlivněno a vaše data zůstanou nedotčena. 
* Všechny spuštěné nebo zařazené operace jsou zrušeny.

Při obnovení fondu SQL:

* Fond SQL získává výpočetní a paměťové prostředky pro nastavení jednotek datového skladu.
* Výpočetní poplatky za jednotky datového skladu se obnoví.
* Vaše data budou k dispozici.
* Po sql fond je online, je nutné restartovat dotazy na pracovní vytížení.

Pokud chcete, aby váš fond SQL byl přístupný, zvažte jeho zmenšení na nejmenší velikost, nikoli pozastavení. 

Kroky pozastavení a obnovení najdete na [webu Azure Portal](pause-and-resume-compute-portal.md)nebo na rychlých startech [PowerShellu.](pause-and-resume-compute-powershell.md) Můžete také použít [pozastavit rozhraní REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) nebo [renom REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute).

## <a name="drain-transactions-before-pausing-or-scaling"></a>Vypusťte transakce před pozastavením nebo škálováním

Doporučujeme povolit dokončení existujících transakcí před zahájením operace pozastavení nebo škálování.

Při pozastavení nebo škálování fondu SQL, na pozadí vaše dotazy jsou zrušeny při zahájení pozastavení nebo škálování požadavku. Zrušení jednoduchého dotazu SELECT je rychlá operace a nemá téměř žádný vliv na čas potřebný k pozastavení nebo škálování instance.  Nicméně zastavení transakčních dotazů, které upravují data nebo strukturu dat, může trvat mnohem déle. **Transakční dotazy se podle definice musí dokončit v celém rozsahu, nebo musí vrátit zpět provedené změny.** Vracení dokončené práce transakčního dotazu zpět může trvat stejně dlouho nebo dokonce déle, než původní změna, kterou dotaz prováděl. Například pokud zrušíte dotaz, který odstraňoval řádky a už hodinu běžel, může systému hodinu trvat, než odstraněné řádky vloží zpět. Pokud spustíte pozastavení nebo škálování zatímco probíhají transakce, může to vypadat, že vaše pozastavení nebo škálování trvá dlouho, protože pozastavení a škálování musí počkat na dokončení odvolání transakce, než budou moci pokračovat.

Viz také [Principy transakcí](sql-data-warehouse-develop-transactions.md)a [Optimalizace transakcí](sql-data-warehouse-develop-best-practices-transactions.md).

## <a name="automating-compute-management"></a>Automatizace správy výpočetních prostředků

Pokud chcete automatizovat operace správy výpočetních prostředků, přečtěte si část [Správa výpočetních prostředků pomocí funkcí Azure](manage-compute-with-azure-functions.md).

Dokončení každé operace horizontálního navýšení kapacity, pozastavení a obnovení může trvat několik minut. Pokud automaticky měníte měřítko, zastavujete nebo obnovujete, doporučujeme implementovat logiku, která zajistí, že některé operace byly dokončeny před pokračováním v jiné akci. Kontrola stavu fondu SQL prostřednictvím různých koncových bodů umožňuje správně implementovat automatizaci těchto operací. 

Chcete-li zkontrolovat stav fondu SQL, podívejte se na [powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) nebo [t-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) rychlý start. Můžete také zkontrolovat stav fondu SQL s rozhraním [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state).


## <a name="permissions"></a>Oprávnění

Škálování fondu SQL vyžaduje oprávnění popsaná v [databázi ALTER](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse).  Pozastavení a pokračování vyžadují oprávnění [přispěvatele SQL DB,](../../role-based-access-control/built-in-roles.md#sql-db-contributor) konkrétně Microsoft.Sql/servers/databases/action.


## <a name="next-steps"></a>Další kroky
Podívejte se na návod, jak řídit [výpočetní prostředky](manage-compute-with-azure-functions.md) Dalším aspektem správy výpočetních prostředků je přidělování různých výpočetních prostředků pro jednotlivé dotazy. Další informace naleznete v [tématu Třídy prostředků pro správu pracovního vytížení](resource-classes-for-workload-management.md).
