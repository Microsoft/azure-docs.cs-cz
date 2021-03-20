---
title: Sledování osvědčených postupů – Azure Database for MySQL
description: Tento článek popisuje osvědčené postupy pro monitorování Azure Database for MySQL.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: 59301e26f4d42056322ba5a7cdaff1555c531096
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96354956"
---
# <a name="best-practices-for-monitoring-azure-database-for-mysql--single-server"></a>Osvědčené postupy pro monitorování Azure Database for MySQL – jeden server

Seznamte se s osvědčenými postupy, které je možné použít k monitorování databázových operací a zajištění, že výkon při zvětšování velikosti dat není ohrožen. Jakmile na platformu přidáváme nové možnosti, budeme v této části dále upřesňovat osvědčené postupy popsané v této části.

## <a name="layout-of-the-current-monitoring-toolkit"></a>Rozložení aktuální sady nástrojů pro monitorování

Azure Database for MySQL poskytuje nástroje a metody, pomocí kterých můžete snadno monitorovat využití, přidávat nebo odebírat prostředky (například procesor, paměť nebo vstupně-výstupní operace), řešit potenciální problémy a pomáhat vylepšit výkon databáze. V pravidelných intervalech můžete [monitorovat metriky výkonu](concepts-monitoring.md#metrics) , abyste viděli průměrnou, maximální a minimální hodnotu pro celou řadu časových rozsahů.

Můžete [nastavit výstrahy](howto-alert-on-metric.md#create-an-alert-rule-on-a-metric-from-the-azure-portal) pro prahovou hodnotu metriky, takže budete informováni, zda server dosáhl těchto limitů, a provést příslušné akce.  

Monitorujte databázový server a ujistěte se, že prostředky přiřazené k databázi aplikace mohou zpracovat úlohu aplikace. Pokud je databáze na omezení prostředků, zvažte následující:
    * Identifikace a optimalizace nejdůležitějších dotazů náročných na prostředky. 
    * Přidání dalších prostředků upgradem úrovně služby.

### <a name="cpu-utilization"></a>Využití procesoru
Monitorujte využití CPU a pokud databáze vyčerpá prostředky CPU. Pokud je využití procesoru 90% nebo více než, měli byste škálovat výpočetní výkon zvýšením počtu virtuální jádra nebo škálovat na další cenovou úroveň.  Zajistěte, aby propustnost nebo souběžnost byly očekávané při horizontálním navýšení kapacity procesoru. 

### <a name="memory"></a>Memory (Paměť) 
Velikost paměti, která je k dispozici pro databázový server, je úměrná [počtu virtuální jádra](concepts-pricing-tiers.md). Ujistěte se, že je paměť pro úlohu dostačující. Zátěžový test aplikace pro ověření, zda je paměť dostatečná pro operace čtení a zápisu. Pokud se spotřeba paměti databáze často zvětšuje nad rámec definované prahové hodnoty, znamená to, že byste měli upgradovat instanci tím, že zvýšíte virtuální jádra nebo vyšší úroveň výkonu. Použijte [úložiště dotazů](concepts-query-store.md), proveďte [dotaz na doporučení výkonu](concepts-performance-recommendations.md) a Identifikujte dotazy s nejdelší dobou trvání, který je spuštěný. Prozkoumejte příležitosti k optimalizaci. 

### <a name="storage"></a>Storage 
[Velikost úložiště](howto-create-manage-server-portal.md#scale-compute-and-storage) zřízená pro server MySQL určuje IOPS serveru. Úložiště, které používá služba, zahrnuje soubory databáze, protokoly transakcí, protokoly serveru a záložní snímky. Zajistěte, aby se spotřebované místo na disku trvale nepřekročilo nad 85 procentem celkového množství zajištěného místa na disku. V takovém případě je nutné odstranit nebo archivovat data z databázového serveru, abyste uvolnili nějaké místo. 

### <a name="network-traffic"></a>Síťový provoz 

**Propustnost příjmu sítě, propustnost síťového přenosu** – rychlost síťového provozu do a z instance MySQL v megabajtech za sekundu. Je nutné vyhodnotit požadavek na propustnost pro server a nepřetržitě monitorovat provoz, pokud je propustnost nižší, než bylo očekáváno. 

### <a name="database-connections"></a>Databázová připojení 
**Databázová připojení** – počet relací klienta, které jsou připojené k Azure Database for MySQL musí být zarovnány s [omezeními počtu připojení pro vybranou velikost SKU](concepts-server-parameters.md#max_connections) . 


## <a name="next-steps"></a>Další kroky

- [Osvědčený postup pro výkon Azure Database for MySQL](concept-performance-best-practices.md)
- [Osvědčené postupy při operacích serveru pomocí Azure Database for MySQL](concept-operation-excellence-best-practices.md)
