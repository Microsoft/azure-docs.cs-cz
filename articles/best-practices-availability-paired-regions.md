---
title: Zajištění provozní kontinuity & zotavení po havárii pomocí spárovaných oblastí Azure
description: Zajištění odolnosti aplikací pomocí regionálního párování Azure
author: martinekuan
manager: martinekuan
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: martinek
ms.custom: references_regions
ms.openlocfilehash: 3310d4a7d86db9dee7d5f71fc9410545817886f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97511225"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Provozní kontinuita a zotavení po havárii (BCDR): Spárované oblasti Azure

## <a name="what-are-paired-regions"></a>Co jsou spárované oblasti?

Oblast Azure se skládá ze sady datových center nasazených v hraničním prostředí definovaném latencí a připojených přes vyhrazenou síť s nízkou latencí.  Tím se zajistí, že služby Azure v rámci oblasti Azure nabízejí nejlepší možný výkon a zabezpečení.  

Geografické prostředí Azure definuje oblast světa, která obsahuje alespoň jednu oblast Azure. Geografické oblasti definují diskrétní trh, který obvykle obsahuje dvě nebo více oblastí, které zachovávají zachovávání dat a hranice dodržování předpisů.  Další informace o globální infrastruktuře Azure najdete [tady](https://azure.microsoft.com/global-infrastructure/regions/) .

Regionální pár se skládá ze dvou oblastí ve stejné zeměpisné oblasti. Azure serializovat aktualizace platforem (plánovaná údržba) napříč místními páry, což zajistí, že se v každé dvojici současně aktualizuje jenom jedna oblast. Pokud výpadek ovlivňuje více oblastí, bude mít nejméně jedna oblast v každém páru prioritu pro obnovení.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Některé služby Azure využívají spárované oblasti k zajištění kontinuity obchodních činností a k ochraně před ztrátou dat.  Azure poskytuje několik [řešení úložiště](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) , která využívají spárované oblasti k zajištění dostupnosti dat. Například [geograficky redundantní úložiště Azure](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replikuje data do sekundární oblasti automaticky a zajišťuje tak, že data jsou odolná i v případě, že primární oblast nebude obnovitelné. 

Všimněte si, že ne všechny služby Azure automaticky replikují data, ani nepřejdou všechny služby Azure zpátky z oblasti, která selhala, do její dvojice.  V takovém případě musí být obnovení a replikace konfigurovány zákazníkem.

## <a name="can-i-select-my-regional-pairs"></a>Můžu vybrat své místní páry?

No. Některé služby Azure spoléhají na regionální páry, jako je třeba [redundantní úložiště](./storage/common/storage-redundancy.md)Azure. Tyto služby neumožňují vytváření nových oblastí pro místní párování.  Podobně vzhledem k tomu, že Azure řídí plánovanou údržbu a stanovení priorit obnovení pro regionální páry, nemůžete definovat vlastní regionální páry, které tyto služby budou využívat. Můžete ale vytvořit vlastní řešení pro zotavení po havárii vytvořením služeb v libovolném počtu oblastí a využitím služeb Azure k jejich párování. 

Například můžete použít služby Azure, například [AzCopy](./storage/common/storage-use-azcopy-v10.md) , k plánování zálohování dat do účtu úložiště v jiné oblasti.  Pomocí [Azure DNS a Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md)můžou zákazníci navrhovat odolnou architekturu svých aplikací, které budou zachovány se ztrátou primární oblasti.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Je omezeno na používání služeb v rámci místních párů?

No. Zatímco konkrétní služba Azure může spoléhat na regionální dvojici, můžete své další služby hostovat v libovolné oblasti, která vyhovuje vašim obchodním potřebám.  Řešení úložiště Azure GRS může párovat data v oblasti Kanada – střed s partnerským uzlem v Kanadě – východ při použití výpočetních prostředků umístěných v Východní USA.  

## <a name="must-i-use-azure-regional-pairs"></a>Je nutné použít místní páry Azure?

No. Zákazníci můžou využívat služby Azure k navržení odolné služby, aniž by se museli spoléhat na regionální páry Azure.  Nicméně doporučujeme, abyste nakonfigurovali možnosti zotavení po havárii v provozní kontinuitě (BCDR) napříč místními páry, abyste mohli využít [izolaci](./security/fundamentals/isolation-choices.md) a zlepšit [dostupnost](./availability-zones/az-overview.md). U aplikací, které podporují více aktivních oblastí, doporučujeme vždy, když je to možné, použít obě oblasti z dvojice oblastí. Tím zajistíte optimální dostupnost pro aplikace a minimalizovanou dobu obnovení v případě havárie. Kdykoli je to možné, navrhněte aplikaci pro zajištění [maximální odolnosti](/azure/architecture/framework/resiliency/overview) a snadného [zotavení po havárii](/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Regionální páry Azure

| Geografie | Místní párování A | Oblastní dvojice B  |
|:--- |:--- |:--- |
| Asia-Pacific |Východní Asie (Hongkong) | Jihovýchodní Asie (Singapur) |
| Austrálie |Austrálie – východ |Austrálie – jihovýchod |
| Austrálie |Austrálie – střed |Austrálie – střed 2 |
| Brazílie |Brazílie – jih |Středojižní USA |
| Kanada |Střední Kanada |Kanada – východ |
| Čína |Čína – sever |Čína – východ|
| Čína |Čína – sever 2 |Čína – východ 2|
| Evropa |Severní Evropa (Irsko) |Západní Evropa (Nizozemsko) |
| Francie |Francie – střed|Francie – jih|
| Německo |Německo – střed |Německo – severovýchod |
| Indie |Indie – střed |Indie – jih |
| Indie |Západní Indie |Indie – jih |
| Japonsko |Japonsko – východ |Japonsko – západ |
| Jižní Korea |Jižní Korea – střed |Jižní Korea – jih |
| Severní Amerika |East US |USA – západ |
| Severní Amerika |USA – východ 2 |USA – střed |
| Severní Amerika |USA – středosever |Středojižní USA |
| Severní Amerika |Západní USA 2 |USA – středozápad |
| Norsko | Norsko – východ | Norsko – západ |
| Jižní Afrika | Jižní Afrika – sever |Jižní Afrika – západ |
| Švýcarsko | Švýcarsko – sever |Švýcarsko – západ |
| UK |Spojené království – západ |Spojené království – jih |
| Spojené arabské emiráty | Spojené arabské emiráty sever | Spojené arabské emiráty – střed
| Ministerstvo obrany USA |US DoD – východ |US DoD – střed |
| Vláda USA |USA (Gov) – Arizona |USA (Gov) – Texas |
| Vláda USA |US Gov – Iowa |USA (Gov) – Virginia |
| Vláda USA |USA (Gov) – Virginia |USA (Gov) – Texas |

> [!Important]
> - Západní Indie se spáruje pouze v jednom směru. Sekundární oblast Západní Indie je Jižní Indie, ale sekundární oblast Jižní Indie je Střed Indie.
> - Brazílie – jih je jedinečná, protože se spáruje s oblastí mimo její zeměpisnou oblast. Sekundární oblast Brazílie – jih je Střed USA – jih. Sekundární oblast Střed USA – jih není Brazílie – jih.


## <a name="an-example-of-paired-regions"></a>Příklad spárovaných oblastí
Následující obrázek znázorňuje hypotetickou aplikaci, která používá místní dvojici pro zotavení po havárii. Zelená čísla zvýrazňují aktivity různých oblastí se třemi službami Azure (výpočty, úložiště a databáze Azure) a způsob jejich konfigurace pro replikaci napříč oblastmi. Jedinečné výhody nasazení v spárovaných oblastech jsou zvýrazněné oranžovými čísly.

![Přehled výhod spárovaných oblastí](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Obrázek 2 – hypotetické oblastní páry Azure

## <a name="cross-region-activities"></a>Aktivity mezi oblastmi
Jak je uvedeno na obrázku 2.

1. **Azure COMPUTE (IaaS)** – musíte předem zřídit dodatečné výpočetní prostředky, abyste zajistili dostupnost prostředků v jiné oblasti během havárie. Další informace najdete v tématu [technické pokyny k odolnosti Azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** – Pokud používáte spravované disky, přečtěte si informace o [zálohování mezi oblastmi](/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) pomocí Azure Backup a [replikaci virtuálních počítačů](./site-recovery/azure-to-azure-tutorial-enable-replication.md) z jedné oblasti do druhé s Azure Site Recovery. Pokud používáte účty úložiště, pak je geograficky redundantní úložiště (GRS) nakonfigurované ve výchozím nastavení, když se vytvoří účet Azure Storage. V GRS se vaše data automaticky replikují třikrát v rámci primární oblasti a třikrát se nacházejí v spárované oblasti. Další informace najdete v tématu [Azure Storage možností redundance](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – pomocí Azure SQL Database geografické replikace můžete nakonfigurovat asynchronní replikaci transakcí do libovolné oblasti světa. Nicméně doporučujeme nasadit tyto prostředky do spárované oblasti pro většinu scénářů zotavení po havárii. Další informace najdete v tématu [geografická replikace v Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md).

4. **Azure Resource Manager** – Resource Manager poskytuje logickou izolaci komponent v jednotlivých oblastech. To znamená, že logická selhání v jedné oblasti mají méně pravděpodobný dopad na jiné.

## <a name="benefits-of-paired-regions"></a>Výhody spárovaných oblastí

5. **Fyzickou izolaci** – Pokud je to možné, Azure upřednostňuje oddělení mezi datacentry v rámci geografické dvojice aspoň 300 km, i když to není praktické nebo možné ve všech zeměpisných oblastech. Oddělení fyzického datacentra omezuje pravděpodobnost přirozených havárií, neodpočinku, výpadků napájení nebo fyzických výpadků sítě, které omezují obě oblasti najednou. K izolaci se vztahují omezení v rámci geografického umístění (zeměpisná velikost, dostupnost infrastruktury v síti, nařízení atd.).  

6. **Replikace poskytovaná platformou** – některé služby, jako je například úložiště Geo-Redundant, poskytují automatickou replikaci do spárované oblasti.

7. **Pořadí obnovení oblasti** – v případě rozsáhlých výpadků je obnovení jedné oblasti z každého páru prioritní. U aplikací, které jsou nasazené ve spárovaných oblastech, je tak zaručeno, že mají k dispozici prioritně obnovenou oblast. Pokud je aplikace nasazená v oblastech, které nejsou spárované, obnovení může být zpožděné – v nejhorším případě můžou zvolené oblasti obnovit poslední dvě.

8. **Sekvenční aktualizace** – plánované aktualizace systému Azure se nasazují do spárovaných oblastí postupně (ne ve stejnou dobu), aby se minimalizovaly prostoje, účinek chyb a logické chyby ve vzácných událostech chybné aktualizace.

9. Zaregistrování **dat** – oblast se nachází ve stejné geografické oblasti jako její dvojice (s výjimkou Brazílie – jih), aby splňovala požadavky na příslušnost k právnímu a právnické dani z hlediska platnosti dat.