---
title: Zajištění kontinuity provozu & zotavení po havárii pomocí spárovaných oblastí Azure
description: Zajištění odolnosti aplikace pomocí místního párování Azure
author: jpconnock
manager: angrobe
ms.service: multiple
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: jeconnoc
ms.openlocfilehash: 778943dad9a04632797d5d9165b6f1f9a3eb9850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248252"
---
# <a name="business-continuity-and-disaster-recovery-bcdr-azure-paired-regions"></a>Provozní kontinuita a zotavení po havárii (BCDR): Spárované oblasti Azure

## <a name="what-are-paired-regions"></a>Co jsou spárované oblasti?

Oblast Azure se skládá ze sady datových center nasazených v rámci obvodu definovaného latencí a připojených prostřednictvím vyhrazené sítě s nízkou latencí.  Tím zajistíte, že služby Azure v rámci oblasti Azure nabízejí nejlepší možný výkon a zabezpečení.  

Azure geografie definuje oblast světa obsahující alespoň jednu oblast Azure. Zeměpisné oblasti definují diskrétní trh, obvykle obsahující dvě nebo více oblastí, které zachovávají hranice rezidence dat a dodržování předpisů.  Další informace o globální infrastruktuře Azure [najdete tady](https://azure.microsoft.com/global-infrastructure/regions/)

Regionální pár se skládá ze dvou oblastí v rámci stejné zeměpisné oblasti. Azure serializuje aktualizace platformy (plánovanou údržbu) napříč regionálními páry a zajišťuje, že se v každé dvojici aktualizuje pouze jedna oblast najednou. Pokud výpadek ovlivňuje více oblastí, bude mít prioritu alespoň jedna oblast v každé dvojici pro obnovení.

![AzureGeography](./media/best-practices-availability-paired-regions/GeoRegionDataCenter.png)

Některé služby Azure využívají spárované oblasti k zajištění kontinuity podnikání a ochraně před ztrátou dat.  Azure poskytuje několik [řešení úložiště,](./storage/common/storage-redundancy.md#redundancy-in-a-secondary-region) která využívají spárované oblasti k zajištění dostupnosti dat. Například [Azure Geo redundantní úložiště](./storage/common/storage-redundancy.md#geo-redundant-storage) (GRS) replikuje data do sekundární oblasti automaticky, zajištění, že data jsou trvalé i v případě, že primární oblast není obnovitelná. 

Všimněte si, že ne všechny služby Azure automaticky replikovat data, ani všechny služby Azure automaticky fall-back z oblasti se nezdařilo na jeho pár.  V takových případech musí být obnovení a replikace nakonfigurovány zákazníkem.

## <a name="can-i-select-my-regional-pairs"></a>Mohu si vybrat regionální páry?

Ne. Některé služby Azure spoléhají na místní páry, jako je [například redundantní úložiště](./storage/common/storage-redundancy.md)Azure . Tyto služby neumožňují vytvářet nové regionální párování.  Podobně vzhledem k tomu, že Azure řídí plánované plánování údržby a obnovení priorit pro místní dvojice, nelze definovat vlastní místní páry využít těchto služeb. Můžete však vytvořit vlastní řešení zotavení po havárii tím, že vytvoříte služby v libovolném počtu oblastí a využijete služby Azure k jejich spárování. 

Například můžete použít služby Azure, jako je [AzCopy](./storage/common/storage-use-azcopy-v10.md) naplánovat zálohování dat do účtu úložiště v jiné oblasti.  Pomocí [Azure DNS a Azure Traffic Manager](./networking/disaster-recovery-dns-traffic-manager.md), zákazníci mohou navrhnout odolnou architekturu pro své aplikace, které přežijí ztrátu primární oblasti.

## <a name="am-i-limited-to-using-services-within-my-regional-pairs"></a>Jsem omezen na využívání služeb v rámci svých regionálních párů?

Ne. Zatímco daná služba Azure se může spoléhat na místní dvojici, můžete hostovat další služby v jakékoli oblasti, která splňuje vaše obchodní potřeby.  Řešení úložiště Azure GRS může spárovat data v Aplikaci Canada Central s druhou stranou v Kanadě – východ při použití výpočetních prostředků umístěných ve východním USA.  

## <a name="must-i-use-azure-regional-pairs"></a>Musím používat místní páry Azure?

Ne. Zákazníci můžou využít služby Azure k navržení odolné služby, aniž by se spoléhali na místní páry Azure.  Doporučujeme však nakonfigurovat obnovení zotavení po havárii kontinuity provozu (BCDR) napříč regionálními dvojicemi, abyste mohli využívat [izolaci](./security/fundamentals/isolation-choices.md) a zlepšovat [dostupnost](./availability-zones/az-overview.md). U aplikací, které podporují více aktivních oblastí, doporučujeme vždy, když je to možné, použít obě oblasti z dvojice oblastí. To zajišťuje optimální dostupnost pro aplikace a minimální dobu obnovení v případě havárie. Kdykoli je to možné, navrhněte [aplikaci](https://docs.microsoft.com/azure/architecture/framework/resiliency/overview) pro maximální odolnost proti chybám a snadné zotavení po [havárii](https://docs.microsoft.com/azure/architecture/framework/resiliency/backup-and-recovery).

## <a name="azure-regional-pairs"></a>Místní páry Azure

| Geography | Regionální pár A | Regionální pár B  |
|:--- |:--- |:--- |
| Asie a Tichomoří |Východní Asie (Hongkong) | Jihovýchodní Asie (Singapur) |
| Austrálie |Austrálie – východ |Austrálie – jihovýchod |
| Austrálie |Austrálie – střed |Austrálie – střed 2 |
| Brazílie |Brazílie – jih |USA – středojih |
| Kanada |Střední Kanada |Kanada – východ |
| Čína |Čína – sever |Čína – východ|
| Čína |Čína Sever 2 |Čína východ 2|
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
| Severní Amerika |USA – západ 2 |USA – středozápad |
| Norsko | Norsko východ | Norsko Západ |
| Jižní Afrika | Jižní Afrika – sever |Jižní Afrika – západ |
| Švýcarsko | Švýcarsko Sever |Švýcarsko Západ |
| UK |Spojené království – západ |Spojené království – jih |
| Spojené arabské emiráty | SAE Sever | SAE Centrální
| Ministerstvo obrany USA |US DoD – východ |US DoD – střed |
| Vláda USA |USA (Gov) – Arizona |USA (Gov) – Texas |
| Vláda USA |US Gov – Iowa |USA (Gov) – Virginia |
| Vláda USA |USA (Gov) – Virginia |USA (Gov) – Texas |

> [!Important]
> - Západní Indie je spárována pouze v jednom směru. Sekundárním regionem západní Indie je jižní Indie, ale sekundární masiv jižní Indie je střední Indie.
> - Brazílie Jih je jedinečný, protože je spárován s oblastí mimo jeho geografie. Sekundární oblast Brazílie Jih je jižní střední USA. Sekundární masiv USA na jihu USA není Brazílie – jih.


## <a name="an-example-of-paired-regions"></a>Příklad spárovaných oblastí
Obrázek níže ilustruje hypotetickou aplikaci, která používá regionální pár pro zotavení po havárii. Zelená čísla zvýrazňují aktivity mezi oblastmi tří služeb Azure (výpočetní prostředky Azure, úložiště a databáze) a způsob jejich konfigurace pro replikaci napříč oblastmi. Jedinečné výhody nasazení napříč spárovanými oblastmi jsou zvýrazněny oranžovými čísly.

![Přehled výhod spárované oblasti](./media/best-practices-availability-paired-regions/PairedRegionsOverview2.png)

Obrázek 2 – Hypotetický regionální pár Azure

## <a name="cross-region-activities"></a>Aktivity mezi oblastmi
Jak je uvedeno na obrázku 2.

1. **Azure Compute (IaaS)** – je nutné zřídit další výpočetní prostředky předem zajistit prostředky jsou k dispozici v jiné oblasti během havárie. Další informace naleznete v [technických příručkách k odolnosti azure](https://github.com/uglide/azure-content/blob/master/articles/resiliency/resiliency-technical-guidance.md). 

2. **Azure Storage** – Pokud používáte spravované disky, získejte informace o [zálohování mezi oblastmi](https://docs.microsoft.com/azure/architecture/resiliency/recovery-loss-azure-region#virtual-machines) pomocí Azure Backup a [replikaci virtuálních počítačů](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-enable-replication) z jedné oblasti do druhé pomocí Azure Site Recovery. Pokud používáte účty úložiště, pak geograficky redundantní úložiště (GRS) je ve výchozím nastavení nakonfigurované při vytvoření účtu Azure Storage. S GRS jsou vaše data automaticky replikována třikrát v rámci primární oblasti a třikrát ve spárované oblasti. Další informace najdete v tématu [Možnosti redundance úložiště Azure](storage/common/storage-redundancy.md).

3. **Azure SQL Database** – s geografickou replikací Azure SQL Database můžete nakonfigurovat asynchronní replikaci transakcí do libovolné oblasti na světě; Doporučujeme však nasadit tyto prostředky ve spárované oblasti pro většinu scénářů zotavení po havárii. Další informace najdete [v tématu Geografická replikace v Azure SQL Database](sql-database/sql-database-geo-replication-overview.md).

4. **Azure Resource Manager** – Resource Manager poskytuje logickou izolaci komponent v jednotlivých oblastech. To znamená, že logické selhání v jedné oblasti mají menší pravděpodobnost, že ovlivní jinou.

## <a name="benefits-of-paired-regions"></a>Výhody spárovaných oblastí

5. **Fyzická izolace** – Pokud je to možné, Azure preferuje alespoň 300 mil oddělení mezi datovými centry v regionální majež, i když to není praktické nebo možné ve všech zeměpisných oblastech. Oddělení fyzického datového centra snižuje pravděpodobnost přírodních katastrof, občanských nepokojů, výpadků proudu nebo výpadků fyzické sítě, které postihují oba regiony najednou. Izolace podléhá omezením v rámci zeměpisné polohy (geografická velikost, dostupnost elektrické/síťové infrastruktury, předpisy atd.).  

6. **Replikace poskytovaná platformou** – některé služby, jako je geograficky redundantní úložiště, poskytují automatickou replikaci do spárované oblasti.

7. **Obnovení pořadí** oblasti – v případě rozsáhlévýpadku obnovení jedné oblasti je prioritou z každého páru. U aplikací, které jsou nasazené ve spárovaných oblastech, je tak zaručeno, že mají k dispozici prioritně obnovenou oblast. Pokud je aplikace nasazena napříč oblastmi, které nejsou spárovány, obnovení může být zpožděno – v nejhorším případě mohou být vybrané oblasti poslední dvě, které mají být obnoveny.

8. **Sekvenční aktualizace** – plánované aktualizace systému Azure jsou zaváděny do spárovaných oblastí postupně (ne současně) minimalizovat prostoje, efekt chyb a logické selhání ve výjimečných případech chybné aktualizace.

9. **Rezidence dat** – Oblast se nachází ve stejné zeměpisné oblasti jako její dvojice (s výjimkou brazílie – jih) ke splnění požadavků na rezidenci dat pro účely jurisdikce pro daňové účely a účely jurisdikce donucovacích orgánů.
