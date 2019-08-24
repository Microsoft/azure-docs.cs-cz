---
title: 'Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): Spárované oblasti Azure | Dokumentace Microsoftu'
description: Další informace o Azure oblastní párování, zajistěte, aby aplikace byly odolné při výpadku datového centra.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: 81ba993e6cbe55b45d34325545754bec561ce479
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67514471"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): Spárované oblasti Azure

## <a name="what-are-paired-regions"></a>Co jsou spárované oblasti?

Azure funguje v různých geografických oblastech po celém světě. Zeměpisná oblast Azure je definované oblasti na světě, který obsahuje alespoň jedné oblasti Azure. Oblast Azure je oblast v rámci zeměpisné oblasti, který obsahuje jeden nebo více datových centrech.

Každá oblast Azure je spárovaná s jinou oblastí ve stejné zeměpisné oblasti společně vytváření páru oblastí. Výjimkou je Brazílie – Jih, což je spárovaná s oblastí mimo jeho zeměpisné oblasti. Mezi párování oblastí Azure serializuje aktualizace platformy (plánované údržby), tak, aby pouze jeden spárované oblasti se aktualizuje v čase. V případě výpadku by to ovlivnilo více oblastí bude mít aspoň jednu oblast v každém páru priorita pro obnovení.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Obrázek 1 – Azure oblastních párech

| Geografie | Spárované oblasti |  |
|:--- |:--- |:--- |
| Asie |Východní Asie |Jihovýchodní Asie |
| Austrálie |Austrálie – východ |Austrálie – jihovýchod |
| Austrálie |Austrálie – střed |Austrálie – střed 2 |
| Brazílie |Brazílie – jih |Středojižní USA |
| Kanada |Kanada – střed |Kanada – východ |
| Čína |Čína – sever |Čína – východ|
| Čína |Čína – sever 2 |Čína – východ 2|
| Evropa |Severní Evropa (Irsko) |Západní Evropa (Nizozemsko) |
| Francie |Francie – střed|Francie – jih|
| Německo |Německo – střed |Německo – severovýchod |
| Indie |Střed Indie |Indie – jih |
| Indie |Indie – západ |Indie – jih |
| Japonsko |Japonsko – východ |Japonsko – západ |
| Jižní Korea |Korea – střed |Jižní Korea – jih |
| Severní Amerika |USA – východ |Západní USA |
| Severní Amerika |Východní USA 2 |Střední USA |
| Severní Amerika |Středoseverní USA |Středojižní USA |
| Severní Amerika |Západní USA 2 |Středozápadní USA 
| Jižní Afrika | Jižní Afrika – sever | Jižní Afrika – západ
| Spojené království |Spojené království – západ |Velká Británie – jih |
| Spojené arabské emiráty | Spojené arabské emiráty – sever | Spojené arabské emiráty – střed
| ministerstvo obrany USA |US DoD – východ |US DoD – střed |
| US Government |USA (Gov) – Arizona |USA (Gov) – Texas |
| US Government |US Gov – Iowa |USA (Gov) – Virginia |
| US Government |USA (Gov) – Virginia |USA (Gov) – Texas |

Tabulka 1 - mapování Azure oblastních párech

- Západní Indie je spárovaná jenom v jednom směru. Indie – jih je sekundární oblasti Indie – Západ, ale sekundární oblasti Indie – jih je střed Indie.
- Brazílie – jih je jedinečný, protože je spárovaná s oblastí mimo svůj vlastní zeměpisné oblasti. Je sekundární oblasti Brazílie – Jih, střed USA – jih. Střed USA – jih v sekundární oblasti není Brazílie – jih.
- Sekundární oblast USA (gov) Iowa je Virginie státní správy USA.
- Sekundární oblast USA (gov) Virginie je US Gov Texas.
- Sekundární oblast US Gov Texas je Arizona státní správy USA.


Doporučujeme, abyste nakonfigurovali obchodní kontinuity podnikových procesů zotavení po havárii (BCDR) napříč oblastních párech těžit ze zásad izolace a dostupnosti Azure. Pro aplikace, které podporují více aktivní oblasti doporučujeme použít obě oblasti v páru oblastí, kde je to možné. Tím se zajistí optimální dostupnost pro aplikace a čas minimalizované obnovení v případě havárie. 

## <a name="an-example-of-paired-regions"></a>Příklad spárovaných oblastí
Obrázek 2 níže ukazuje hypotetické aplikaci, která používá páru oblastí pro zotavení po havárii. Zelená čísla zvýrazněte aktivity mezi různými oblastmi tři služeb Azure (Azure compute, storage a databáze) a jak jsou nakonfigurované pro replikaci mezi oblastmi. Jedinečné výhody nasazení ve spárovaných oblastech jsou zvýrazněny oranžové čísel.

![Přehled výhod spárované oblasti](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Obrázek 2 – hypotetické páru oblastí Azure

## <a name="cross-region-activities"></a>Aktivity mezi oblastmi
Jak je uvedeno na obrázku 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – je třeba zřídit další výpočetní prostředky předem zajistit prostředky jsou k dispozici v jiné oblasti při havárii. Další informace najdete v tématu [technické pokyny k odolnosti Azure](resiliency/resiliency-technical-guidance.md).

![Úložiště](./media/best-practices-availability-paired-regions/2Green.png) **služby Azure Storage** – Pokud používáte spravované disky, přečtěte si o [záloh mezi různými oblastmi](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) pomocí služby Azure Backup a [replikaci virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) z jedné oblasti do druhého pomocí Azure Site Recovery. Pokud používáte účty úložiště, je při vytvoření účtu služby Azure Storage geograficky redundantní úložiště (GRS) nakonfigurovaná ve výchozím nastavení. S GRS data automaticky replikují na třech místech v rámci primární oblasti a třikrát v párované oblasti. Další informace najdete v tématu [možnosti redundance Azure Storage](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – s Azure SQL Database geografickou replikaci, můžete nakonfigurovat asynchronní replikace transakcí do libovolné oblasti na světě; doporučujeme však nasazování těchto prostředků v Spárované oblasti pro většinu scénářů zotavení po havárii. Další informace najdete v tématu [geografické replikace ve službě Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manageru** -Resource Manageru ze své podstaty poskytuje logickou izolaci komponent napříč oblastmi. To znamená, že jsou méně pravděpodobné, že ovlivnit jiné logické chyby v jedné oblasti.

## <a name="benefits-of-paired-regions"></a>Výhody spárovaných oblastí
Jak je uvedeno na obrázku 2.  

![Izolace](./media/best-practices-availability-paired-regions/5Orange.png)
**fyzické izolace** – když je možné, Azure dává přednost alespoň 300 mil oddělení mezi datacentry v páru oblastí, i když to není možné nebo praktické ve všech zeměpisných oblastech. Oddělení fyzického datacentra snižuje pravděpodobnost, že přírodní katastrofy, občanské nepokoje, výpadky napájení nebo fyzické výpadky sítě ovlivní obě oblasti současně. Izolace je v souladu s omezeními v rámci zeměpisné oblasti (velikost zeměpisné oblasti, napájení/síťové infrastruktury dostupnosti, nařízení atd.).  

![Replikace](./media/best-practices-availability-paired-regions/6Orange.png)
**poskytovanou platformou replikace** -automatickou replikaci do spárované oblasti poskytují několik služeb, jako je geograficky redundantní úložiště.

![Obnovení](./media/best-practices-availability-paired-regions/7Orange.png)
**oblasti obnovení pořadí** – v případě, že z rozsáhlého výpadku obnovení jedné oblasti je každém páru prioritizováno. Aplikace, které jsou nasazené ve spárovaných oblastech je zaručena mít jednu z oblastí obnovit s prioritou. Pokud je aplikace nasazená v oblasti, které nejsou spárované, obnovení můžou být zpožděné – v nejhorším případě vybrané oblasti je pravděpodobně poslední dva obnovit.

![Aktualizace](./media/best-practices-availability-paired-regions/8Orange.png)
**sekvenčním aktualizacím** – Azure plánované aktualizace systému jsou zavádí do spárovaných oblastí postupně (ne ve stejnou dobu) Chcete-li minimalizovat prostoje, efekt chyb a logických chyb v nepravděpodobném případě chybný aktualizace.

![Data](./media/best-practices-availability-paired-regions/9Orange.png)
**rezidence dat** – oblasti se nachází ve stejné zeměpisné oblasti jako svůj pár (s výjimkou oblasti Brazílie – jih) Chcete-li splnit požadavky na rezidenci dat pro účely jurisdikci vynucení daně a práva.
