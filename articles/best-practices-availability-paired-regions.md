---
title: 'Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): spárované oblasti Azure | Dokumentace Microsoftu'
description: Další informace o Azure oblastní párování, zajistěte, aby aplikace byly odolné při výpadku datového centra.
author: rayne-wiselman
ms.service: multiple
ms.topic: article
ms.date: 07/03/2018
ms.author: raynew
ms.openlocfilehash: 983a551da26e08797b2a65f609cff17954a52828
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954806"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Obchodní kontinuity podnikových procesů a zotavení po havárii (BCDR): spárované oblasti Azure

## <a name="what-are-paired-regions"></a>Co jsou spárované oblasti?

Azure funguje v různých geografických oblastech po celém světě. Zeměpisná oblast Azure je definované oblasti na světě, který obsahuje alespoň jedné oblasti Azure. Oblast Azure je oblast v rámci zeměpisné oblasti, který obsahuje jeden nebo více datových centrech.

Každá oblast Azure je spárovaná s jinou oblastí ve stejné zeměpisné oblasti společně vytváření páru oblastí. Výjimkou je Brazílie – Jih, což je spárovaná s oblastí mimo jeho zeměpisné oblasti. Napříč párování oblastí, které bude Azure serializovat aktualizace platformy (plánované údržby), takže dané pouze jeden spárované oblasti aktualizují najednou. Kromě toho v případě výpadku by to ovlivnilo více oblastí aspoň jednu oblast v každém páru bude priorita pro obnovení.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Obrázek 1 – Azure oblastních párech

| Zeměpisné oblasti | Spárované oblasti |  |
|:--- |:--- |:--- |
| Asie |Východní Asie |Jihovýchodní Asie |
| Austrálie |Austrálie – východ |Austrálie – jihovýchod |
| Austrálie |Austrálie – střed |Austrálie – střed 2 |
| Brazílie |Brazílie – jih 2 |Střed USA – jih |
| Kanada |Kanada – střed |Kanada – východ |
| Čína |Čína – sever |Čína – východ|
| Čína |Čína – sever 2 |Čína – východ 2|
| Evropa |Severní Evropa |Západní Evropa |
| Francie |Francie – střed|Francie – jih|
| Německo |Německo – střed |Německo – severovýchod |
| Indie |Střed Indie |Indie – jih |
| Indie |Indie – západ (1) |Indie – jih |
| Japonsko |Japonsko – východ |Japonsko – západ |
| Jižní Korea |Korea – střed |Jižní Korea – jih |
| Severní Amerika |USA – východ |Západní USA |
| Severní Amerika |Východní USA 2 |USA – střed |
| Severní Amerika |Střed USA – sever |Střed USA – jih |
| Severní Amerika |Západní USA 2 |Západní střed USA 
| Spojené království |Spojené království – západ |Velká Británie – jih |
| ministerstvo obrany USA |US DoD – východ |US DoD – střed |
| US Government |USA (Gov) – Arizona |USA (Gov) – Texas |
| US Government |US Gov Iowa (3) |USA (Gov) – Virginia |
| US Government |US Gov Virginie (4) |USA (Gov) – Texas |

Tabulka 1 - mapování Azure oblastních párech

- (1) Indie – západ se liší, protože je spárovaná s jinou oblastí jenom v jednom směru. Indie – jih je sekundární oblasti Indie – Západ, ale sekundární oblasti Indie – jih je střed Indie.
- (2) Brazílie – jih je jedinečný, protože je spárovaná s oblastí mimo svůj vlastní zeměpisné oblasti. Je sekundární oblasti Brazílie – Jih, střed USA – Jih, ale není sekundární oblasti střed USA – jih pro oblast Brazílie – jih.
- (3) US Gov Iowa sekundární oblasti je Virginie státní správy USA, ale sekundární oblast USA (gov) – Virginia není Iowa státní správy USA.
- (4) US Gov Virginie sekundární oblasti je US Gov Texas, ale sekundární oblast US Gov Texas není Virginie státní správy USA.


Doporučujeme, abyste nakonfigurovali obchodní kontinuity podnikových procesů zotavení po havárii (BCDR) napříč oblastních párech těžit ze zásad izolace a dostupnosti Azure. Pro aplikace, které podporují více aktivní oblasti doporučujeme použít obě oblasti v páru oblastí, kde je to možné. Tím se zajistí optimální dostupnost pro aplikace a čas minimalizované obnovení v případě havárie. 

## <a name="an-example-of-paired-regions"></a>Příklad spárovaných oblastí
Obrázek 2 níže ukazuje hypotetické aplikaci, která používá páru oblastí pro zotavení po havárii. Zelená čísla zvýrazněte aktivity mezi různými oblastmi tři služeb Azure (Azure compute, storage a databáze) a jak jsou nakonfigurované pro replikaci mezi oblastmi. Jedinečné výhody nasazení ve spárovaných oblastech jsou zvýrazněny oranžové čísel.

![Přehled výhod spárované oblasti](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Obrázek 2 – hypotetické páru oblastí Azure

## <a name="cross-region-activities"></a>Aktivity mezi oblastmi
Jak je uvedeno na obrázku 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure Compute (IaaS)** – je třeba zřídit další výpočetní prostředky předem zajistit prostředky jsou k dispozici v jiné oblasti při havárii. Další informace najdete v tématu [technické pokyny k odolnosti Azure](resiliency/resiliency-technical-guidance.md).

![Úložiště](./media/best-practices-availability-paired-regions/2Green.png) **služby Azure Storage** – geograficky redundantní úložiště (GRS) je ve výchozím nastavení nakonfigurované, když se vytvoří účet služby Azure Storage. S GRS data automaticky replikují na třech místech v rámci primární oblasti a třikrát v párované oblasti. Další informace najdete v tématu [možnosti redundance Azure Storage](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – s Azure SQL Database geografickou replikaci, můžete nakonfigurovat asynchronní replikace transakcí do libovolné oblasti na světě; doporučujeme však nasazování těchto prostředků v Spárované oblasti pro většinu scénářů zotavení po havárii. Další informace najdete v tématu [geografické replikace ve službě Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Resource Manager](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manageru** -Resource Manageru ze své podstaty poskytuje logickou izolaci komponentami pro správu služby napříč oblastmi. To znamená, že jsou méně pravděpodobné, že ovlivnit jiné logické chyby v jedné oblasti.

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
