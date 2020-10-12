---
title: Konfigurace úloh SAP pomocí Zóny dostupnosti Azure | Microsoft Docs
description: Architektura a scénáře s vysokou dostupností pro SAP NetWeaver pomocí Zóny dostupnosti Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8265d328a23e871dc25692f22138a7bb648a8323
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653592"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úloh SAP s využitím služby Zóny dostupnosti Azure
[Zóny dostupnosti Azure](../../../availability-zones/az-overview.md) je jednou z funkcí s vysokou dostupností, které poskytuje Azure. Použití Zóny dostupnosti zlepšuje celkovou dostupnost úloh SAP v Azure. Tato funkce je už v některých [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/)dostupná. V budoucnu bude k dispozici ve více oblastech.

Tento obrázek znázorňuje základní architekturu vysoké dostupnosti SAP:

![Standardní konfigurace vysoké dostupnosti](./media/sap-ha-availability-zones/standard-ha-config.png)

Aplikační vrstva SAP je nasazená v rámci jedné [skupiny dostupnosti](../../windows/manage-availability.md)Azure. Pro zajištění vysoké dostupnosti centrálních služeb SAP můžete nasadit dva virtuální počítače v samostatné skupině dostupnosti. Používejte Clustering s podporou převzetí služeb při selhání Windows serveru nebo Pacemaker (Linux) jako architekturu s vysokou dostupností s automatickým převzetím služeb při selhání v případě infrastruktury nebo softwaru. Další informace o těchto nasazeních najdete v těchto tématech:

- [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky](./sap-high-availability-guide-wsfc-file-share.md)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md)
- [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver v Red Hat Enterprise Linux](./high-availability-guide-rhel.md)

Podobná architektura se vztahuje na vrstvu DBMS systémů SAP NetWeaver, S/4HANA nebo Hybris. Vrstvu DBMS nasadíte v aktivním nebo pasivním režimu s řešením clusteru s podporou převzetí služeb při selhání za účelem ochrany před infrastrukturou nebo selháním softwaru. Řešení clusteru s podporou převzetí služeb při selhání může být architektura převzetí služeb při selhání specifická pro DBMS, clustering s podporou převzetí služeb při selhání Windows serveru

Chcete-li nasadit stejnou architekturu pomocí Zóny dostupnosti Azure, je nutné provést některé změny v architektuře, která byla dříve naznačena. Tento článek popisuje tyto změny.

## <a name="considerations-for-deploying-across-availability-zones"></a>Předpoklady pro nasazení v rámci Zóny dostupnosti


Při použití Zóny dostupnosti Vezměte v úvahu následující:

- Neexistují žádné záruky týkající se vzdálenosti mezi různými Zóny dostupnosti v oblasti Azure.
- Zóny dostupnosti není ideální řešení pro zotavení po havárii. Přírodní katastrofy můžou způsobit rozšířenou škodu v oblastech světa, včetně těžkého poškození infrastruktury napájení. Vzdálenosti mezi různými zónami nemusí být dostatečně velká, aby představovaly správné řešení zotavení po havárii.
- Latence sítě v rámci Zóny dostupnosti není stejná ve všech oblastech Azure. V některých případech můžete nasadit a spustit aplikační vrstvu SAP v různých zónách, protože latence sítě z jedné zóny na aktivní virtuální počítač DBMS je přijatelná. V některých oblastech Azure ale nemusí být latence mezi aktivním virtuálním počítačem DBMS a instancí aplikace SAP, pokud je nasazená v různých zónách, nepřijatelná pro obchodní procesy SAP. V těchto případech je potřeba, aby se architektura nasazení lišila s architekturou aktivní/aktivní pro aplikaci nebo aktivní/pasivní architekturou, ve které je latence sítě mezi zónami příliš vysoká.
- Při rozhodování, kde použít Zóny dostupnosti, založte rozhodnutí o latenci sítě mezi zónami. Latence sítě hraje důležitou roli ve dvou oblastech:
    - Latence mezi dvěma instancemi systému DBMS, které musí mít synchronní replikaci. Čím vyšší je latence sítě, tím pravděpodobněji bude ovlivněná škálovatelnost vašich úloh.
    - Rozdíl v latenci sítě mezi virtuálním počítačem, na kterém běží instance dialogového okna SAP v zóně s aktivní instancí DBMS a podobným virtuálním počítačem v jiné zóně. Vzhledem k tomu, že se tento rozdíl zvyšuje, zvyšuje se vliv na dobu běhu obchodních procesů a dávkových úloh, záleží na tom, jestli běží v zóně se systémem DBMS nebo v jiné zóně.

Když nasadíte virtuální počítače Azure napříč Zóny dostupnosti a vytvoříte řešení pro převzetí služeb při selhání v rámci stejné oblasti Azure, uplatní se některá omezení:

- Při nasazení do Zóny dostupnosti Azure je nutné použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) . 
- Mapování výčtů zóny na fyzické zóny je vyřešeno na základě předplatného Azure. Pokud k nasazení systémů SAP používáte různá předplatná, musíte pro každé předplatné definovat ideální zóny.
- Skupiny dostupnosti Azure nemůžete nasadit v rámci zóny dostupnosti Azure, pokud nepoužíváte [skupinu umístění s použitím Azure Proximity](../../linux/co-location.md). Způsob nasazení vrstvy SAP DBMS a centrálních služeb v různých zónách a zároveň nasazení vrstvy aplikace SAP pomocí skupin dostupnosti a zajištění bezprostřední blízkosti virtuálních počítačů je popsána v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md). Pokud nepoužíváte skupiny umístění blízkosti Azure, musíte zvolit jednu nebo druhou jako architekturu nasazení pro virtuální počítače.
- K vytvoření řešení clusteru s podporou převzetí služeb při selhání založeného na clusteringu Windows Server s podporou převzetí služeb při selhání nebo Linux Pacemaker nejde použít [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) . Místo toho je potřeba použít [SKU Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Ideální kombinace Zóny dostupnosti
Než se rozhodnete, jak používat Zóny dostupnosti, je nutné určit:

- Latence sítě mezi třemi zónami oblasti Azure. To vám umožní vybrat zóny s minimální latencí sítě v síťových přenosech mezi zónami.
- Rozdíl mezi latencí virtuálních počítačů v rámci jedné z zón, podle vašeho výběru a latencí sítě ve dvou zónách podle vašeho výběru.
- Určení, zda typy virtuálních počítačů, které je třeba nasadit, jsou k dispozici ve dvou zónách, které jste vybrali. U některých virtuálních počítačů, zejména virtuálních počítačů řady M-Series, může dojít k situaci, kdy jsou některé SKU dostupné jenom v obou třech zónách.

## <a name="network-latency-between-and-within-zones"></a>Latence sítě mezi a v zónách
K určení latence mezi různými zónami budete potřebovat:

- Nasaďte položku virtuálního počítače, kterou chcete použít pro instanci systému DBMS ve všech třech zónách. Ujistěte se, že je při provádění tohoto měření povolené [urychlení sítě Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) .
- Když najdete dvě zóny s minimální latencí sítě, nasaďte další tři virtuální počítače skladové položky virtuálního počítače, které chcete použít jako virtuální počítač aplikační vrstvy v rámci tří Zóny dostupnosti. Změřte latenci sítě proti dvěma virtuálním počítačům DBMS v obou zónách DBMS, které jste vybrali. 
- Použijte **niping** jako měřicí nástroj. Tento nástroj ze SAP je popsaný v tématu poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy dokumentované pro měření latence. Protože nástroj **příkazového testu** na cestách nefunguje přes službu Azure akcelerované síťové cesty, nedoporučujeme ho používat.

Tyto testy není nutné provádět ručně. Můžete najít [test latence zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedury PowerShellu, který automatizuje popsané testy latence. 

Na základě vašich měření a dostupnosti skladových položek virtuálních počítačů v Zóny dostupnosti musíte udělat některá rozhodnutí:

- Definujte ideální zóny pro vrstvu DBMS.
- Určete, jestli chcete distribuovat aktivní aplikační vrstvu SAP pro jednu, dvě nebo všechny tři zóny na základě rozdílů mezi zónou latence sítě v různých zónách.
- Určete, zda chcete nasadit konfiguraci typu aktivní/pasivní nebo aktivní/aktivní v aplikačním bodě zobrazení. (Tyto konfigurace jsou vysvětleny dále v tomto článku.)

Při rozhodování o těchto rozhodnutích Vezměte v úvahu také doporučení pro latenci sítě SAP, jak je popsáno v SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Měření a rozhodnutí, která učiníte, jsou platná pro předplatné Azure, které jste použili, když jste provedli měření. Pokud používáte jiné předplatné Azure, budete muset měření opakovat. Mapování výčtu zón se může pro jiné předplatné Azure lišit.


> [!IMPORTANT]
> Očekává se, že výše popsané míry poskytují různé výsledky v každé oblasti Azure, která podporuje [zóny dostupnosti](../../../availability-zones/az-overview.md). I v případě, že požadavky na latenci sítě jsou stejné, možná budete muset v různých oblastech Azure přijmout různé strategie nasazení, protože latence sítě mezi zónami se může lišit. V některých oblastech Azure může být latence sítě mezi třemi různými zónami výrazně odlišná. V jiných oblastech může být latence sítě mezi třemi různými zónami větší. Deklarace identity, která je vždy latence sítě mezi 1 a 2 milisekundami, není správná. Latence sítě napříč Zóny dostupnosti v oblastech Azure není možné zobecnit.

## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože vaše aktivní aplikační servery SAP nasazujete ve dvou nebo třech zónách. Instance služby SAP Central Services, která využívá replikaci do fronty, bude nasazena mezi dvěma zónami. Totéž platí pro vrstvu DBMS, která bude nasazena ve stejných zónách jako centrální služba SAP.

Při zvažování této konfigurace potřebujete najít dvě Zóny dostupnosti ve vaší oblasti, které nabízejí latenci sítě mezi zónami, což je přijatelné pro vaše zatížení a synchronní replikaci DBMS. Také si přejete mít jistotu, že rozdíl mezi latencí sítě v rámci vybraných zón a latencí sítě mezi zónami není moc velký. Důvodem je to, že nechcete velké odchylky v závislosti na tom, jestli úloha běží v zóně se serverem DBMS nebo v různých zónách, a to v době spuštění vašich obchodních procesů nebo dávkových úloh. Některé varianty jsou přijatelné, ale ne faktory rozdílu.

Zjednodušené schéma aktivního/aktivního nasazení ve dvou zónách může vypadat takto:

![Nasazení aktivní/aktivní zóny](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci platí následující požadavky:

- Nepoužíváte [skupinu umístění blízkosti Azure](../../linux/co-location.md), považujete zóny dostupnosti Azure jako chybu a aktualizační domény pro všechny virtuální počítače, protože skupiny dostupnosti nejde nasadit v zóny dostupnosti Azure.
- Pokud chcete kombinovat nasazení prostředí pro vrstvu DBMS a centrální služby, ale chcete použít skupiny dostupnosti Azure pro aplikační vrstvu, je nutné použít skupiny Azure Proximity, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage a [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk) nepodporují žádný typ replikace úložiště mezi zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
  - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD pro případ, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) nebo další instance aplikace.
- Aby byla zajištěna konzistence za běhu pro kritické obchodní procesy, můžete se pokusit směrovat určité úlohy služby Batch a uživatele do instancí aplikace, které jsou v zóně s aktivní instancí DBMS, pomocí skupin systému SAP Batch, skupin přihlášení SAP nebo skupin RFC. V případě převzetí služeb při selhání však budete muset tyto skupiny ručně přesunout na instance spuštěné na virtuálních počítačích, které jsou v zóně s aktivním virtuálním počítačem databáze.  
- V každé z zón můžete chtít nasadit instance nespících dialogových oken. Je tak umožněn okamžitý návrat k dřívější kapacitě prostředků, pokud je zóna, kterou používá část instancí vaší aplikace, mimo provoz.

> [!IMPORTANT]
> V tomto aktivním nebo aktivním scénáři se další poplatky za šířku pásma oznamují od společnosti Microsoft od 04/01/2020. Podívejte se na [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/)dokumentu. Přenos dat mezi aplikační vrstvou SAP a vrstvou SAP DBMS je poměrně náročný. Scénář aktivní/aktivní může proto přispívat k nákladům poměrně o bit. Pokud chcete získat přesné náklady, zachovejte kontrolu tohoto článku. 


## <a name="activepassive-deployment"></a>Aktivní/pasivní nasazení
Pokud nemůžete najít přijatelný rozdíl mezi latencí sítě v rámci jedné zóny a latencí síťového provozu mezi zónami, můžete nasadit architekturu s aktivním/pasivním znakem z bodu aplikační vrstvy SAP. Definujete *aktivní* zónu, což je zóna, do které nasazujete kompletní vrstvu aplikace a kde se pokusíte spustit jak aktivní systém DBMS, tak i službu SAP Central Services. Tato konfigurace vyžaduje, abyste měli jistotu, že nemáte extrémní dobu běhu, a to v závislosti na tom, jestli úloha běží v zóně s aktivní instancí DBMS, nebo ne, v obchodních transakcích a dávkových úlohách.

Základní rozložení architektury vypadá takto:

![Aktivní/pasivní nasazení zóny](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Pro tuto konfiguraci platí následující požadavky:

- Skupiny dostupnosti nejde nasadit v Zóny dostupnosti Azure. Abyste to mohli kompenzovat, můžete použít skupiny umístění v blízkosti Azure, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Když použijete tuto architekturu, musíte monitorovat stav těsně a pokusit se zachovat instance Active DBMS a služby SAP Central Services ve stejné zóně jako nasazená aplikační vrstva. V případě převzetí služeb při selhání centrální služby SAP nebo systému DBMS se ujistěte, že můžete ručně navrátit služby po obnovení do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage a [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk) nepodporují žádný typ replikace úložiště mezi zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
    - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD pro případ, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) nebo další instance aplikace.
- Měli byste nasadit neaktivní virtuální počítače v pasivní zóně (ze zobrazení bod DBMS), abyste mohli v případě selhání zóny spustit prostředky aplikace.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) aktuálně nedokáže replikovat aktivní virtuální počítače na neaktivní virtuální počítače mezi zónami. 
- Měli byste investovat do automatizace, která vám v případě selhání zóny umožní automaticky spustit vrstvu aplikace SAP ve druhé zóně.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinovaná konfigurace vysoké dostupnosti a zotavení po havárii
Společnost Microsoft nesdílí žádné informace o geografických vzdálenostech mezi zařízeními, která jsou hostitelem různých Zóny dostupnosti Azure v oblasti Azure. Přesto někteří zákazníci používají zóny pro kombinovanou konfiguraci HA a zotavení po havárii, která příslibů cíl bodu obnovení (RPO) nula. To znamená, že byste neměli přijít na jakékoli potvrzené databázové transakce i v případě zotavení po havárii. 

> [!NOTE]
> Doporučujeme, abyste používali konfiguraci, jako je to jenom za určitých okolností. Můžete ho například použít, když data nemůžou opustit oblast Azure z důvodů zabezpečení nebo dodržování předpisů. 

Tady je příklad toho, jak by tato konfigurace mohla vypadat:

![Kombinovaná vysoká dostupnost DR v zónách](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci platí následující požadavky:

- Za předpokladu, že mezi zařízeními, která hostuje zónu dostupnosti, nebo v určité oblasti Azure máte významnou vzdálenost. Skupiny dostupnosti nejde nasadit v Zóny dostupnosti Azure. Abyste to mohli kompenzovat, můžete použít skupiny umístění v blízkosti Azure, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Když použijete tuto architekturu, musíte monitorovat stav těsně a pokusit se zachovat instance Active DBMS a služby SAP Central Services ve stejné zóně jako nasazená aplikační vrstva. V případě převzetí služeb při selhání centrální služby SAP nebo systému DBMS se ujistěte, že můžete ručně navrátit služby po obnovení do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Na virtuálních počítačích, na kterých běží aktivní instance aplikace QA, byste měli mít předem nainstalované instance produkčních aplikací.
- V případě selhání zóny vypněte instance aplikace QA a místo toho spusťte instance v produkčním prostředí. Všimněte si, že k této práci musíte použít virtuální názvy pro instance aplikace.
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage a [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk) nepodporují žádný typ replikace úložiště mezi zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
    - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD pro případ, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) nebo další instance aplikace.





## <a name="next-steps"></a>Další kroky
Tady je několik dalších kroků pro nasazení v rámci Zóny dostupnosti Azure:

- [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Příprava infrastruktury Azure na vysokou dostupnost pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)
