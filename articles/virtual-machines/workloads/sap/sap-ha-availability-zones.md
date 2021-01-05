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
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 12/29/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7418e5578450367e9fa37a87adb6e7036619877b
ms.sourcegitcommit: 42922af070f7edf3639a79b1a60565d90bb801c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97827444"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úloh SAP s využitím služby Zóny dostupnosti Azure
Kromě nasazení různých vrstev architektury SAP v Azure Availability Sets se vám nedávno zavedené [zóny dostupnosti Azure](../../../availability-zones/az-overview.md) taky dají použít i pro nasazení úloh SAP. Zóna dostupnosti Azure je definovaná jako: "jedinečná fyzická umístění v rámci oblasti. Každá zóna se skládá z jednoho nebo více datových center vybavených nezávislým napájením, chlazením a sítěmi. Zóny dostupnosti Azure nejsou k dispozici ve všech oblastech. Pro oblasti Azure, které poskytují Zóny dostupnosti, se podívejte na [mapu oblastí Azure](https://azure.microsoft.com/global-infrastructure/geographies/). Tato mapa vám ukáže, které oblasti poskytují nebo jsou oznámeny k poskytování Zóny dostupnosti. 

Od typické architektury SAP NetWeaver nebo S/4HANA je nutné chránit tři různé vrstvy:

- Aplikační vrstva SAP, která může být jedním až deseti virtuálními počítači. Chcete minimalizovat šance na to, že virtuální počítače se nasazují na stejném hostitelském serveru. Také budete chtít, aby se tyto virtuální počítače v přijatelné blízkosti vrstvy DBMS zachovaly při latenci sítě v přijatelném okně.
- Vrstva SAP ASCS/SCS, která představuje jediný bod selhání v architektuře SAP NetWeaver a S/4HANA. Obvykle se podíváte na dva virtuální počítače, které chcete pokrýt pomocí architektury převzetí služeb při selhání. Proto by se tyto virtuální počítače měly přidělit v různých selháních infrastruktury a aktualizačních doménách.
- Vrstva SAP DBMS, která představuje jediný bod selhání. V obvyklých případech se skládá ze dvou virtuálních počítačů, které jsou pokryté architekturou převzetí služeb při selhání. Proto by se tyto virtuální počítače měly přidělit v různých selháních infrastruktury a aktualizačních doménách. Výjimky jsou SAP HANA nasazení se škálováním na více instancí, kde se dá použít víc než dva virtuální počítače.

Hlavní rozdíly mezi nasazením vašich důležitých virtuálních počítačů prostřednictvím skupin dostupnosti nebo Zóny dostupnosti jsou:

- Nasazení se skupinou dostupnosti vyrovnává virtuální počítače v rámci množiny v jedné zóně nebo datacentru (ať už se týká konkrétní oblasti). V důsledku toho není nasazení prostřednictvím skupiny dostupnosti chráněno pomocí napájení, chlazení nebo síťových problémů, které mají vliv na dataceter (y) zóny jako celku. Na straně plus jsou virtuální počítače zarovnané s doménami aktualizace a selhání v rámci této zóny nebo datacentra. Konkrétně pro vrstvu SAP ASCS nebo DBMS, kde chráníme dva virtuální počítače na skupinu dostupnosti, může zarovnání s doménami selhání a aktualizací zabránit tomu, aby oba virtuální počítače končily na stejný hostitelský hardware. 
- Nasazení virtuálních počítačů prostřednictvím Zóny dostupnosti Azure a výběr různých zón (maximálně ze tří možných) bude nasazovat virtuální počítače napříč různými fyzickými umístěními a přidat další ochranu před problémy napájení, chlazení nebo sítí, které mají vliv na dataceter (y) zóny jako celku. Při nasazení více než jednoho virtuálního počítače stejné rodiny virtuálních počítačů do stejné zóny dostupnosti ale neexistuje žádná ochrana před těmito virtuálními počítači na stejném hostiteli. V důsledku toho je nasazení prostřednictvím Zóny dostupnosti ideální pro vrstvu SAP ASCS a DBMS, kde se obvykle podíváme na dva virtuální počítače. Pro aplikační vrstvu SAP, která může být drasticky více než dva virtuální počítače, může být nutné přejít zpět na jiný model nasazení (viz později).

Motivace pro nasazení napříč Zóny dostupnosti Azure by měla být na pokrytí selhání jednoho kritického virtuálního počítače nebo schopnosti snížit prostoje při opravách softwaru v kritickém stavu, chtít chránit před většími problémy s infrastrukturou, které by mohly ovlivnit dostupnost jednoho nebo více datových center Azure. 

## <a name="considerations-for-deploying-across-availability-zones"></a>Předpoklady pro nasazení v rámci Zóny dostupnosti


Při použití Zóny dostupnosti Vezměte v úvahu následující:

- Neexistují žádné záruky týkající se vzdálenosti mezi různými Zóny dostupnosti v oblasti Azure.
- Zóny dostupnosti není ideální řešení pro zotavení po havárii. Přírodní katastrofy můžou způsobit rozšířenou škodu v oblastech světa, včetně těžkého poškození infrastruktury napájení. Vzdálenosti mezi různými zónami nemusí být dostatečně velká, aby představovaly správné řešení zotavení po havárii.
- Latence sítě v rámci Zóny dostupnosti není stejná ve všech oblastech Azure. V některých případech můžete nasadit a spustit aplikační vrstvu SAP v různých zónách, protože latence sítě z jedné zóny na aktivní virtuální počítač DBMS je přijatelná. V některých oblastech Azure ale nemusí být latence mezi aktivním virtuálním počítačem DBMS a instancí aplikace SAP, pokud je nasazená v různých zónách, nepřijatelná pro obchodní procesy SAP. V těchto případech je potřeba, aby se architektura nasazení lišila s architekturou aktivní/aktivní pro aplikaci nebo aktivní/pasivní architektura, ve které je latence sítě mezi zónami příliš vysoká.
- Při rozhodování, kde použít Zóny dostupnosti, založte rozhodnutí o latenci sítě mezi zónami. Latence sítě hraje důležitou roli ve dvou oblastech:
    - Latence mezi dvěma instancemi systému DBMS, které musí mít synchronní replikaci. Čím vyšší je latence sítě, tím pravděpodobněji bude ovlivněná škálovatelnost vašich úloh.
    - Rozdíl v latenci sítě mezi virtuálním počítačem, na kterém běží instance dialogového okna SAP v zóně s aktivní instancí DBMS a podobným virtuálním počítačem v jiné zóně. Vzhledem k tomu, že se tento rozdíl zvyšuje, zvyšuje se vliv na dobu běhu obchodních procesů a dávkových úloh, záleží na tom, jestli běží v zóně se systémem DBMS nebo v jiné zóně.

Když nasadíte virtuální počítače Azure napříč Zóny dostupnosti a vytvoříte řešení pro převzetí služeb při selhání v rámci stejné oblasti Azure, uplatní se některá omezení:

- Při nasazení do Zóny dostupnosti Azure je nutné použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) . 
- Mapování výčtů zóny na fyzické zóny je vyřešeno na základě předplatného Azure. Pokud k nasazení systémů SAP používáte různá předplatná, musíte pro každé předplatné definovat ideální zóny.
- Skupiny dostupnosti Azure nemůžete nasadit v rámci zóny dostupnosti Azure, pokud nepoužíváte [skupinu umístění s použitím Azure Proximity](../../linux/co-location.md). Způsob nasazení vrstvy SAP DBMS a centrálních služeb v různých zónách a zároveň nasazení vrstvy aplikace SAP pomocí skupin dostupnosti a zajištění bezprostřední blízkosti virtuálních počítačů je popsána v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md). Pokud nepoužíváte skupiny umístění v blízkosti Azure, musíte zvolit jednu nebo druhou jako architekturu nasazení pro virtuální počítače.
- K vytvoření řešení clusteru s podporou převzetí služeb při selhání založeného na clusteringu Windows Server s podporou převzetí služeb při selhání nebo Linux Pacemaker nejde použít [Azure Basic Load Balancer](../../../load-balancer/load-balancer-overview.md) . Místo toho je potřeba použít [SKU Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md).



## <a name="the-ideal-availability-zones-combination"></a>Ideální kombinace Zóny dostupnosti
Pokud chcete v rámci zón nasadit systém SAP NetWeaver nebo S/4HANA, můžete nasadit dvě hlavní architektury:

- Aktivní/aktivní: dvojice virtuálních počítačů se systémem ASCS/SCS a dvojice virtuálních počítačů, na kterých běží vrstva DBMS, se rozdělují mezi dvě zóny. Počet virtuálních počítačů, na kterých běží aplikační vrstva SAP, je nasazený na sudých číslech ve stejných dvou zónách. Pokud virtuální počítač se systémem DBMS nebo ASCS/SCS převezme služby při selhání, můžou se některé otevřené a aktivní transakce vrátit zpátky. Ale uživatelé jsou přihlášení. Nezáleží na tom, v jaké zóně je aktivní virtuální počítač DBMS a instance aplikace spuštěné. Tato architektura je upřednostňovanou architekturou pro nasazení napříč zónami.
- Aktivní/pasivní: dvojice virtuálních počítačů se systémem ASCS/SCS a dvojice virtuálních počítačů, na kterých běží vrstva DBMS, se rozdělují mezi dvě zóny. Počet virtuálních počítačů, na kterých běží aplikační vrstva SAP, se nasadí do jednoho z Zóny dostupnosti. Vrstvu aplikace spustíte ve stejné zóně jako instance Active ASCS/SCS a DBMS. Tuto architekturu nasazení použijete, pokud je latence sítě v různých zónách příliš vysoká pro spuštění aplikační vrstvy distribuované napříč zónami. Místo toho je třeba, aby aplikační vrstva SAP běžela ve stejné zóně jako aktivní instance ASCS/SCS a/nebo DBMS. Pokud dojde k převzetí služeb při selhání virtuálního počítače s ASCS/SCS nebo DBMS do sekundární zóny, může dojít k vyšší latenci sítě a k omezení propustnosti. A pokud se chcete vrátit k předchozím úrovním propustnosti, je potřeba, abyste nastavili předchozí virtuální počítač pro převzetí služeb při selhání. Pokud dojde k výpadku oblasti, musí být aplikační vrstva při převzetí služeb při selhání sekundární zónou. Činnost, kterou uživatelé můžou vyzkoušet jako úplné vypnutí systému. V některých oblastech Azure je tato architektura jedinou životaschopnou architekturou, kterou chcete použít Zóny dostupnosti. Pokud nemůžete přijmout potenciální dopad virtuálních počítačů ASCS/SCS nebo DBMS při selhání do sekundární zóny, možná budete mít lepší přehled o nasazeních skupiny dostupnosti.


Takže před rozhodnutím, jak používat Zóny dostupnosti, je nutné určit:

- Latence sítě mezi třemi zónami oblasti Azure. Znalost sítě mezi zónami v oblasti vám umožní vybrat zóny s minimální latencí sítě v síťových přenosech mezi zónami.
- Rozdíl mezi latencí virtuálních počítačů v rámci jedné z zón, podle vašeho výběru a latencí sítě ve dvou zónách podle vašeho výběru.
- Určení, zda typy virtuálních počítačů, které je třeba nasadit, jsou k dispozici ve dvou zónách, které jste vybrali. U některých virtuálních počítačů, zejména virtuálních počítačů řady M-Series, může dojít k situaci, kdy jsou některé SKU dostupné jenom v obou třech zónách.

## <a name="network-latency-between-and-within-zones"></a>Latence sítě mezi a v zónách
K určení latence mezi různými zónami budete potřebovat:

- Nasaďte položku virtuálního počítače, kterou chcete použít pro instanci systému DBMS ve všech třech zónách. Ujistěte se, že je při provádění tohoto měření povolené [urychlení sítě Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) .
- Když najdete dvě zóny s minimální latencí sítě, nasaďte další tři virtuální počítače skladové položky virtuálního počítače, které chcete použít jako virtuální počítač aplikační vrstvy v rámci tří Zóny dostupnosti. Změřte latenci sítě proti dvěma virtuálním počítačům DBMS v obou zónách DBMS, které jste vybrali. 
- Použijte **`niping`** jako měřicí nástroj. Tento nástroj ze SAP je popsaný v tématu poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy dokumentované pro měření latence. Protože nástroj **příkazového testu** na cestách nefunguje přes službu Azure akcelerované síťové cesty, nedoporučujeme ho používat.

Tyto testy není nutné provádět ručně. Můžete najít [test latence zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedury PowerShellu, který automatizuje popsané testy latence. 

Na základě vašich měření a dostupnosti skladových položek virtuálních počítačů v Zóny dostupnosti musíte udělat některá rozhodnutí:

- Definujte ideální zóny pro vrstvu DBMS.
- Určete, jestli chcete distribuovat aktivní aplikační vrstvu SAP pro jednu, dvě nebo všechny tři zóny na základě rozdílů mezi zónou latence sítě v různých zónách.
- Určete, zda chcete nasadit konfiguraci typu aktivní/pasivní nebo aktivní/aktivní v aplikačním bodě zobrazení. (Tyto konfigurace jsou vysvětleny dále v tomto článku.)

Při rozhodování o těchto rozhodnutích Vezměte v úvahu také doporučení pro latenci sítě SAP, jak je popsáno v SAP Note [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Měření a rozhodnutí, která učiníte, jsou platná pro předplatné Azure, které jste použili, když jste provedli měření. Pokud používáte jiné předplatné Azure, budete muset měření opakovat. Mapování výčtu zón se může pro jiné předplatné Azure lišit.


> [!IMPORTANT]
> Očekává se, že výše popsané míry poskytují různé výsledky v každé oblasti Azure, která podporuje [zóny dostupnosti](https://azure.microsoft.com/global-infrastructure/geographies/). I v případě, že požadavky na latenci sítě jsou stejné, možná budete muset v různých oblastech Azure přijmout různé strategie nasazení, protože latence sítě mezi zónami se může lišit. V některých oblastech Azure může být latence sítě mezi třemi různými zónami výrazně odlišná. V jiných oblastech může být latence sítě mezi třemi různými zónami větší. Deklarace identity, která je vždy latence sítě mezi 1 a 2 milisekundami, není správná. Latence sítě napříč Zóny dostupnosti v oblastech Azure není možné zobecnit.

## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože vaše aktivní aplikační servery SAP nasazujete ve dvou nebo třech zónách. Instance služby SAP Central Services, která využívá replikaci do fronty, bude nasazena mezi dvěma zónami. Totéž platí pro vrstvu DBMS, která bude nasazena ve stejných zónách jako centrální služba SAP. Při zvažování této konfigurace potřebujete najít dvě Zóny dostupnosti ve vaší oblasti, které nabízejí latenci sítě mezi zónami, což je přijatelné pro vaše zatížení a synchronní replikaci DBMS. Také si přejete mít jistotu, že rozdíl mezi latencí sítě v rámci vybraných zón a latencí sítě mezi zónami není moc velký. 

Povaha architektury SAP je taková, pokud ji nenastavíte jinak, uživatelé a dávkové úlohy mohou být spuštěny v různých instancích aplikace. Vedlejším účinkem tohoto faktu s aktivním/aktivním nasazením je, že dávkové úlohy mohou být provedeny všemi instancemi aplikace SAP nezávisle na tom, zda jsou spouštěny ve stejné zóně s aktivním systémem DBMS nebo nikoli. Pokud rozdíl v latenci sítě mezi zónami rozdílů je malý v porovnání s latencí sítě v rámci zóny, rozdíl v době běhu dávkových úloh nemusí být významný. Čím větší rozdíl latence sítě v rámci zóny v porovnání s přenosem sítě v zóně je, může být ovlivněna i doba běhu dávkových úloh, pokud se úloha spustila v zóně, kde není aktivní instance systému DBMS. Je na vás jako zákazník, abyste se rozhodli, jaké jsou přijatelné rozdíly v době běhu. A s tím, co je přípustná latence sítě pro provoz mezi zónami.

Oblasti Azure, ve kterých by takové nasazení aktivní/aktivní mělo být možné bez velkých rozdílů v době běhu a propustnosti v rámci aplikační vrstvy nasazené napříč různými Zóny dostupnosti, jako jsou:

- Západní USA 2 (všechny tři zóny)
- Východní USA 2 (všechny tři zóny)
- Střed USA (všechny tři zóny)
- Severní Evropa (všechny tři zóny)
- Západní Evropa (dvě ze tří zón)
- Východní USA (dvě ze tří zón)
- Střed USA – jih (dvě ze tří zón)
- Velká Británie – jih (dvě ze tří zón)

Oblasti Azure, ve kterých se tato architektura nasazení SAP mezi zónami nedoporučuje:

- Francie – střed 
- Jižní Afrika – sever
- Střední Kanada
- Japan East

V závislosti na tom, co jste ochotni tolerovat za běhu, se liší i jiné oblasti, které zde nejsou uvedené.


Zjednodušené schéma aktivního/aktivního nasazení ve dvou zónách může vypadat takto:

![Nasazení aktivní/aktivní zóny](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci platí následující požadavky:

- Nepoužíváte [skupinu umístění blízkosti Azure](../../linux/co-location.md), považujete zóny dostupnosti Azure jako chybu a aktualizační domény pro všechny virtuální počítače, protože skupiny dostupnosti nejde nasadit v zóny dostupnosti Azure.
- Pokud chcete kombinovat nasazení prostředí pro vrstvu DBMS a centrální služby, ale chcete použít skupiny dostupnosti Azure pro aplikační vrstvu, je nutné použít skupiny Azure Proximity, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage, [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk)nebo ANF nepodporuje žádný typ replikace úložiště napříč zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
  - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
  - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
    V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) a místo agenta Azure oplocení použijete zařízení SBD. Nebo pro další instance aplikace.
- Aby byla zajištěna konzistence za běhu pro kritické obchodní procesy, můžete se pokusit směrovat určité úlohy služby Batch a uživatele do instancí aplikace, které jsou v zóně s aktivní instancí DBMS, pomocí skupin systému SAP Batch, skupin přihlášení SAP nebo skupin RFC. V případě převzetí služeb při selhání však budete muset tyto skupiny ručně přesunout na instance spuštěné na virtuálních počítačích, které jsou v zóně s aktivním virtuálním počítačem databáze.  
- V každé z zón můžete chtít nasadit instance nespících dialogových oken. 

> [!IMPORTANT]
> V tomto aktivním nebo aktivním scénáři se další poplatky za šířku pásma oznamují od společnosti Microsoft od 04/01/2020. Podívejte se na [Podrobnosti o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/)dokumentu. Přenos dat mezi aplikační vrstvou SAP a vrstvou SAP DBMS je poměrně náročný. Scénář aktivní/aktivní může proto přispívat k nákladům poměrně o bit. Pokud chcete získat přesné náklady, zachovejte kontrolu tohoto článku. 


## <a name="activepassive-deployment"></a>Aktivní/pasivní nasazení
Pokud nemůžete najít přijatelný rozdíl mezi latencí sítě v rámci jedné zóny a latencí síťového provozu mezi zónami, můžete nasadit architekturu s aktivním/pasivním znakem z bodu aplikační vrstvy SAP. Definujete *aktivní* zónu, což je zóna, do které nasazujete kompletní vrstvu aplikace a kde se pokusíte spustit jak aktivní systém DBMS, tak i službu SAP Central Services. Tato konfigurace vyžaduje, abyste měli jistotu, že nemáte extrémní dobu běhu, a to v závislosti na tom, jestli úloha běží v zóně s aktivní instancí DBMS, nebo ne, v obchodních transakcích a dávkových úlohách.

Oblasti Azure, ve kterých může být vhodnější tento typ architektury nasazení v různých zónách:

- Southeast Asia
- Austrálie – východ
- Brazílie – jih
- Německo – středozápad
- Jižní Afrika – sever
- Francie – střed 
- Střední Kanada
- Japan East


Základní rozložení architektury vypadá takto:

![Aktivní/pasivní nasazení zóny](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Pro tuto konfiguraci platí následující požadavky:

- Skupiny dostupnosti nejde nasadit v Zóny dostupnosti Azure. Abyste to mohli kompenzovat, můžete použít skupiny umístění v blízkosti Azure, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Když použijete tuto architekturu, musíte monitorovat stav těsně a pokusit se zachovat instance Active DBMS a služby SAP Central Services ve stejné zóně jako nasazená aplikační vrstva. V případě převzetí služeb při selhání centrální služby SAP nebo systému DBMS se ujistěte, že můžete ručně navrátit služby po obnovení do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage, [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk)nebo ANF nepodporuje žádný typ replikace úložiště napříč zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
    - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).
    
  V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) a místo agenta Azure oplocení použijete zařízení SBD. Nebo pro další instance aplikace.
- Měli byste nasadit neaktivní virtuální počítače v pasivní zóně (ze zobrazení bod DBMS), abyste mohli spustit prostředky aplikace pro případ selhání zóny.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) aktuálně nedokáže replikovat aktivní virtuální počítače na neaktivní virtuální počítače mezi zónami. 
- Měli byste investovat do automatizace, která umožňuje automaticky spustit vrstvu aplikace SAP ve druhé zóně, pokud dojde k výpadku oblasti.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinovaná konfigurace vysoké dostupnosti a zotavení po havárii
Společnost Microsoft nesdílí žádné informace o geografických vzdálenostech mezi zařízeními, která jsou hostitelem různých Zóny dostupnosti Azure v oblasti Azure. Přesto někteří zákazníci používají zóny pro kombinovanou konfiguraci HA a zotavení po havárii, která příslibů cíl bodu obnovení (RPO) nula. RPO s hodnotou nula znamená, že byste neměli přijít o žádné potvrzené databázové transakce i v případě zotavení po havárii. 

> [!NOTE]
> Doporučujeme, abyste používali konfiguraci, jako je to jenom za určitých okolností. Můžete ho například použít, když data nemůžou opustit oblast Azure z důvodů zabezpečení nebo dodržování předpisů. 

Tady je příklad toho, jak by tato konfigurace mohla vypadat:

![Kombinovaná vysoká dostupnost DR v zónách](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci platí následující požadavky:

- Za předpokladu, že mezi zařízeními, která hostuje zónu dostupnosti, nebo v určité oblasti Azure máte významnou vzdálenost. Skupiny dostupnosti nejde nasadit v Zóny dostupnosti Azure. Abyste to mohli kompenzovat, můžete použít skupiny umístění v blízkosti Azure, jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Když použijete tuto architekturu, musíte monitorovat stav těsně a pokusit se zachovat instance Active DBMS a služby SAP Central Services ve stejné zóně jako nasazená aplikační vrstva. V případě převzetí služeb při selhání centrální služby SAP nebo systému DBMS se ujistěte, že můžete ručně navrátit služby po obnovení do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Na virtuálních počítačích, na kterých běží aktivní instance aplikace QA, byste měli mít předem nainstalované instance produkčních aplikací.
- V případě selhání v rámečku, vypněte instance aplikace QA a místo toho spusťte instance v produkčním prostředí. K provedení této práce je nutné použít virtuální názvy pro instance aplikace.
- Pro nástroje pro vyrovnávání zatížení clusterů s podporou převzetí služeb při selhání v centrálních službách SAP a ve vrstvě DBMS musíte použít [standardní SKU Azure Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md). Základní Load Balancer nebudou v různých zónách fungovat.
- Virtuální síť Azure, kterou jste nasadili pro hostování systému SAP, spolu s jejími podsítěmi, je roztažena v různých zónách. Pro každou zónu nepotřebujete samostatné virtuální sítě.
- U všech virtuálních počítačů, které nasazujete, je potřeba použít [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporované pro nasazení v prostředích.
- Služba Azure Premium Storage a [úložiště SSD úrovně Ultra](../../disks-types.md#ultra-disk) nepodporují žádný typ replikace úložiště mezi zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená složka CIFS (Windows) nebo sdílená složka systému souborů NFS (Linux). Je potřeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Podporují se tyto technologie:
    - Pro Windows se jedná o Clusterové řešení, které používá s DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md).
    - Pro SUSE Linux se jedná o sdílenou složku NFS, která je popsána jako popsaná ve [vysoké dostupnosti pro systém souborů NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md).

  V současné době se řešení, které používá Microsoft Scale-Out souborový server, popsané v části [Příprava infrastruktury Azure pro SAP high availability pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md), nepodporuje napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](./high-availability-guide-suse-pacemaker.md#create-azure-fence-agent-stonith-device) a místo agenta Azure oplocení použijete zařízení SBD. 





## <a name="next-steps"></a>Další kroky
Tady je několik dalších kroků pro nasazení v rámci Zóny dostupnosti Azure:

- [Vytvoření clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání s Windows pomocí sdíleného disku clusteru v Azure](./sap-high-availability-guide-wsfc-shared-disk.md)
- [Příprava infrastruktury Azure na vysokou dostupnost pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](./sap-high-availability-infrastructure-wsfc-file-share.md)