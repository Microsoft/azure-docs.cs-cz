---
title: Provozní kontinuita & zotavení po havárii – spárované oblasti Azure
description: Přečtěte si o regionálním párování Azure, abyste měli jistotu, že aplikace jsou odolné při selhání datových center.
author: rayne-wiselman
manager: carmon
ms.service: multiple
ms.topic: article
ms.date: 07/01/2019
ms.author: raynew
ms.openlocfilehash: c1e14db9dafc8b03acbeb1c6b97e5ac0e27cb0fd
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78163044"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Provozní kontinuita a zotavení po havárii (BCDR): spárované oblasti Azure

## <a name="what-are-paired-regions"></a>Co jsou spárované oblasti?

Azure pracuje v různých geografických oblastech po celém světě. Zeměpisná oblast Azure je definovaná oblast světa, která obsahuje alespoň jednu oblast Azure. Oblast Azure je oblast v rámci geografické oblasti, která obsahuje jedno nebo více datových center.

Každá oblast Azure je spárovaná s jinou oblastí v rámci stejné geografické oblasti a tvoří tak místní dvojici. Výjimka je Brazílie – jih, která se spáruje s oblastí mimo její zeměpisnou oblast. Napříč dvojicemi oblastí Azure serializovat aktualizace platformy (plánovaná údržba), takže se současně aktualizuje jenom jedna spárovaná oblast. V případě výpadku, který má vliv na více oblastí, bude mít nejméně jedna oblast v každém páru určenou prioritu pro obnovení.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

> [!NOTE]
> Přiřazené místní páry Azure nelze upravovat.

Obrázek 1 – regionální páry Azure

| Geografické | Spárované oblasti |  |
|:--- |:--- |:--- |
| Asie |Východní Asie |Jihovýchodní Asie |
| Austrálie |Austrálie – východ |Austrálie – jihovýchod |
| Austrálie |Austrálie – střed |Austrálie – střed 2 |
| Brazílie |Brazílie – jih |USA – středojih |
| Kanada |Kanada – střed |Východní Kanada |
| Čína |Čína – sever |Čína – východ|
| Čína |Čína – sever 2 |Čína – východ 2|
| Evropa |Severní Evropa (Irsko) |Západní Evropa (Nizozemsko) |
| Francie |Francie – střed|Francie – jih|
| Německo |Německo – střed |Německo – severovýchod |
| Indie |Indie – střed |Indie – jih |
| Indie |Indie – západ |Indie – jih |
| Japonsko |Japonsko – východ |Japonsko – západ |
| Jižní Korea |Jižní Korea – střed |Jižní Korea – jih |
| Severní Amerika |USA – východ |USA – západ |
| Severní Amerika |USA – východ 2 |USA – střed |
| Severní Amerika |USA – středosever |USA – středojih |
| Severní Amerika |USA – západ 2 |USA – středozápad 
| Jižní Afrika | Jižní Afrika – sever | Jižní Afrika – západ
| Spojené království |Spojené království – západ |Velká Británie – jih |
| Spojené arabské emiráty | Spojené arabské emiráty sever | Spojené arabské emiráty – střed
| Ministerstvo obrany USA |US DoD – východ |US DoD – střed |
| Státní správa USA |USA (Gov) – Arizona |USA (Gov) – Texas |
| Státní správa USA |US Gov – Iowa |USA (Gov) – Virginia |
| Státní správa USA |USA (Gov) – Virginia |USA (Gov) – Texas |

Tabulka 1 – mapování místních párů Azure

- Západní Indie se spáruje pouze v jednom směru. Sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie je Střed Indie.
- Brazílie – jih je jedinečná, protože je spárovaná s oblastí mimo svou vlastní zeměpisnou oblast. Sekundární oblast Brazílie – jih je Střed USA – jih. Sekundární oblast Střed USA – jih není Brazílie – jih.
- Sekundární oblast US Gov – Iowa je US Gov – Virginie.
- Sekundární oblast US Gov – Virginie je US Gov – Texas.
- US Gov – Texas ' sekundární oblast je US Gov – Arizona.


Doporučujeme, abyste nakonfigurovali zotavení po havárii pro provozní kontinuitu (BCDR) napříč místními páry a využili jsme zásady izolace a dostupnosti Azure. Pro aplikace, které podporují více aktivních oblastí, doporučujeme použít obě oblasti v páru oblastí, kde je to možné. Tím zajistíte optimální dostupnost pro aplikace a minimalizovanou dobu obnovení v případě havárie. 

## <a name="an-example-of-paired-regions"></a>Příklad spárovaných oblastí
Obrázek 2 níže ukazuje hypotetickou aplikaci, která používá místní dvojici pro zotavení po havárii. Zelená čísla zvýrazňují aktivity různých oblastí se třemi službami Azure (výpočty, úložiště a databáze Azure) a způsob jejich konfigurace pro replikaci napříč oblastmi. Jedinečné výhody nasazení v spárovaných oblastech jsou zvýrazněné oranžovými čísly.

![Přehled výhod spárovaných oblastí](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Obrázek 2 – hypotetické oblastní páry Azure

## <a name="cross-region-activities"></a>Aktivity mezi oblastmi
Jak je uvedeno na obrázku 2.

![IaaS](./media/best-practices-availability-paired-regions/1Green.png) **Azure COMPUTE (IaaS)** – musíte předem zřídit ještě dodatečné výpočetní prostředky, abyste zajistili dostupnost prostředků v jiné oblasti během havárie. Další informace najdete v tématu [technické pokyny k odolnosti Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md).

![úložiště](./media/best-practices-availability-paired-regions/2Green.png) **Azure Storage** – Pokud používáte spravované disky, přečtěte si informace o [zálohování mezi jednotlivými oblastmi](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) Azure Backup a [replikaci virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) z jedné oblasti do druhé s Azure Site Recovery. Pokud používáte účty úložiště, pak je geograficky redundantní úložiště (GRS) nakonfigurované ve výchozím nastavení, když se vytvoří účet Azure Storage. V GRS se vaše data automaticky replikují třikrát v rámci primární oblasti a třikrát se nacházejí v spárované oblasti. Další informace najdete v tématu [Azure Storage možností redundance](storage/common/storage-redundancy.md).

![Azure SQL](./media/best-practices-availability-paired-regions/3Green.png) **Azure SQL Database** – s Azure SQL Database geografickou replikací můžete nakonfigurovat asynchronní replikaci transakcí do libovolné oblasti světa. Nicméně doporučujeme nasadit tyto prostředky do spárované oblasti pro většinu scénářů zotavení po havárii. Další informace najdete v tématu [geografická replikace v Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

![Správce prostředků](./media/best-practices-availability-paired-regions/4Green.png) **Azure Resource Manager** -správce prostředků poskytuje logickou izolaci komponent napříč oblastmi. To znamená, že logická selhání v jedné oblasti mají méně pravděpodobný dopad na jiné.

## <a name="benefits-of-paired-regions"></a>Výhody spárovaných oblastí
Jak je uvedeno na obrázku 2.  

![izolaci](./media/best-practices-availability-paired-regions/5Orange.png)
**fyzickou izolaci** – Pokud je to možné, Azure upřednostňuje oddělení mezi datacentry v rámci regionu alespoň 300 km, i když to není praktické nebo možné ve všech geografických oblastech. Oddělení fyzického datacentra omezuje pravděpodobnost přirozených havárií, neodpočinku, výpadků napájení nebo fyzických výpadků sítě, které omezují obě oblasti najednou. K izolaci se vztahují omezení v rámci geografického umístění (zeměpisná velikost, dostupnost infrastruktury v síti, nařízení atd.).  

replikace ![](./media/best-practices-availability-paired-regions/6Orange.png)
replikace prostřednictvím **platformy** – některé služby, jako je geograficky redundantní úložiště, poskytují automatickou replikaci do spárované oblasti.

**pořadí obnovení** ![obnovení](./media/best-practices-availability-paired-regions/7Orange.png)
oblasti – v případě rozsáhlých výpadků je obnovení jedné oblasti v každé dvojici prioritní. U aplikací, které jsou nasazené v spárovaných oblastech, je zaručeno, že se jedna z oblastí obnovila s prioritou. Pokud je aplikace nasazená v oblastech, které nejsou spárované, obnovení může být zpožděné – v nejhorším případě můžou zvolené oblasti obnovit poslední dvě.

![aktualizace](./media/best-practices-availability-paired-regions/8Orange.png)
**sekvenční aktualizace** – plánované aktualizace systému Azure se nasazují na spárované oblasti postupně (ne ve stejnou dobu), aby se minimalizovaly prostoje, vliv chyb a logické chyby v vzácných událostech chybné aktualizace.

![**data](./media/best-practices-availability-paired-regions/9Orange.png)
zaregistrovaná data –** oblast se nachází ve stejné geografické oblasti jako její dvojice (s výjimkou Brazílie – jih), aby splňovala požadavky na příslušnost k právnímu a daňovému vynucování.
