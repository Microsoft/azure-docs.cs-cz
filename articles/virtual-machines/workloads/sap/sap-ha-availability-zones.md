---
title: Konfigurace úloh SAP se zónami dostupnosti Azure | Dokumenty společnosti Microsoft
description: Architektura s vysokou dostupností a scénáře pro SAP NetWeaver využívající zóny dostupnosti Azure
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
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675609"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úloh SAP s využitím služby Zóny dostupnosti Azure
[Zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) je jednou z funkcí s vysokou dostupností, které Azure poskytuje. Použití zón dostupnosti zlepšuje celkovou dostupnost úloh SAP v Azure. Tato funkce je už dostupná v [některých oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/). V budoucnu bude k dispozici ve více regionech.

Tato grafika znázorňuje základní architekturu SAP s vysokou dostupností:

![Standardní konfigurace s vysokou dostupností](./media/sap-ha-availability-zones/standard-ha-config.png)

Aplikační vrstva SAP se nasazuje v jedné [sadě dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)Azure . Pro vysokou dostupnost sap centrální služby, můžete nasadit dva virtuální uživatele v samostatné sadě dostupnosti. Použití clusteringu windows server s podporou převzetí služeb při selhání nebo kardiostimulátoru (Linux) jako rámce s vysokou dostupností s automatickým převzetím služeb při selhání v případě problému s infrastrukturou nebo softwarem. Další informace o těchto nasazeních najdete v následujících tématech:

- [Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Podobná architektura platí pro vrstvu DBMS systémů SAP NetWeaver, S/4HANA nebo Hybris. Vrstvu DBMS nasadíte v aktivním/pasivním režimu s řešením clusteru s podporou převzetí služeb při selhání, které chrání před selháním infrastruktury nebo softwaru. Řešení clusteru s podporou převzetí služeb při selhání může být architektura s podporou převzetí služeb při selhání specifická pro dbms, clusterování s podporou převzetí služeb při selhání systému Windows Server nebo kardiostimulátor.

Chcete-li nasadit stejnou architekturu pomocí zón dostupnosti Azure, musíte provést některé změny architektury popsané dříve. Tento článek popisuje tyto změny.

## <a name="considerations-for-deploying-across-availability-zones"></a>Důležité informace pro nasazení napříč zónami dostupnosti


Při použití zón dostupnosti zvažte následující:

- Neexistují žádné záruky týkající se vzdáleností mezi různými zónami dostupnosti v rámci oblasti Azure.
- Zóny dostupnosti nejsou ideálním řešením zotavení po havárii. Přírodní katastrofy mohou způsobit rozsáhlé škody ve světových regionech, včetně těžkých škod na energetické infrastruktuře. Vzdálenosti mezi různými zónami nemusí být dostatečně velké, aby představovaly správné řešení zotavení po havárii.
- Latence sítě napříč zónami dostupnosti není stejná ve všech oblastech Azure. V některých případech můžete nasadit a spustit aplikační vrstvu SAP v různých zónách, protože latence sítě z jedné zóny do aktivního virtuálního virtuálního provozu DBMS je přijatelná. Ale v některých oblastech Azure latence mezi aktivní ms virtuálního počítače DBMS a instance aplikace SAP, při nasazení v různých zónách, nemusí být přijatelné pro obchodní procesy SAP. V těchto případech musí být architektura nasazení odlišná, s aktivní/aktivní architekturou pro aplikaci nebo aktivní/pasivní architekturou, kde je příliš vysoká latence sítě mezi zónami.
- Při rozhodování o tom, kde použít zóny dostupnosti, založit své rozhodnutí na latenci sítě mezi zónami. Latence sítě hraje důležitou roli ve dvou oblastech:
    - Latence mezi dvěma instancemi DBMS, které potřebují mít synchronní replikaci. Čím vyšší je latence sítě, tím je pravděpodobnější, že to ovlivní škálovatelnost úlohy.
    - Rozdíl v latenci sítě mezi virtuálním virtuálním mem, který používá instanci dialogového okna SAP v zóně s aktivní instancí DBMS, a podobným virtuálním virtuálním serverem v jiné zóně. Jak se tento rozdíl zvyšuje, zvyšuje se také vliv na provozní dobu obchodních procesů a dávkových úloh, v závislosti na tom, zda jsou spuštěny v zóně s DBMS nebo v jiné zóně.

Když nasadíte virtuální počítače Azure napříč zónami dostupnosti a navážete řešení pro převzetí služeb při selhání v rámci stejné oblasti Azure, platí určitá omezení:

- Při nasazování do zón dostupnosti Azure je nutné použít [spravované disky](https://azure.microsoft.com/services/managed-disks/) Azure. 
- Mapování výčtů zón na fyzické zóny se opravuje na základě předplatného Azure. Pokud k nasazení systémů SAP používáte různá předplatná, musíte definovat ideální zóny pro každé předplatné.
- V zóně dostupnosti Azure nelze nasadit sady dostupnosti Azure, pokud nepoužíváte [skupinu azure proximity placement](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Způsob nasazení vrstvy SAP DBMS a centrálních služeb napříč zónami a současně nasazení aplikační vrstvy SAP pomocí sad dostupnosti a stále dosažení těsné blízkosti virtuálních počítačů je popsán v článku [Skupiny umístění bez blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md). Pokud nevyužíváte skupiny umístění bezkontaktní komunikace Azure, musíte vybrat jednu nebo druhou jako rámec nasazení pro virtuální počítače.
- Nástroj [Pro vyrovnávání zatížení Azure Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nelze použít k vytvoření clusterových řešení s podporou převzetí služeb při selhání založených na clusteru Windows Server s podporou převzetí služeb při selhání nebo linuxovém kardiostimulátoru. Místo toho je potřeba použít [skladovou položku pro vyrovnávání zatížení Azure Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Ideální kombinace zón dostupnosti
Než se rozhodnete, jak používat zóny dostupnosti, je třeba určit:

- Latence sítě mezi třemi zónami oblasti Azure. To vám umožní zvolit zóny s nejnižší latencí sítě v síťovém provozu mezi zónami.
- Rozdíl mezi latencí virtuálního počítače na virtuální počítač v rámci jedné ze zón podle vašeho výběru a latencí sítě ve dvou zónách podle vašeho výběru.
- Určení, zda typy virtuálních počítačů, které je potřeba nasadit, jsou k dispozici ve dvou zónách, které jste vybrali. U některých virtuálních měn, zejména virtuálních měn řady M, může dojít k situacím, ve kterých jsou některé soustu dostupné pouze ve dvou ze tří zón.

## <a name="network-latency-between-and-within-zones"></a>Latence sítě mezi zónami a uvnitř zón
Chcete-li určit latenci mezi různými zónami, musíte:

- Nasaďte skladovou položku virtuálního počítače, kterou chcete použít pro instanci DBMS ve všech třech zónách. Ujistěte se, že [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolena, když budete mít toto měření.
- Když najdete dvě zóny s nejnižší latencí sítě, nasaďte další tři virtuální virtuální uživatele sku virtuálního připojení, které chcete použít jako virtuální virtuální ms aplikační vrstvy ve třech zónách dostupnosti. Změřte latenci sítě proti dvěma virtuálním mům DBMS ve dvou zónách DBMS, které jste vybrali. 
- Jako měřicí nástroj použijte **niping.** Tento nástroj ze systému SAP je popsán v poznámkách podpory SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy zdokumentované pro měření latence. Vzhledem k tomu, že **ping** nefunguje prostřednictvím cesty kódu Azure Accelerated Networking, nedoporučujeme používat.

Tyto testy není nutné provádět ručně. Můžete najít test [latence zóny dostupnosti](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) procedury prostředí PowerShell, který automatizuje popsané testy latence. 

Na základě vašich měření a dostupnosti virtuálních virtuálních zařízení v zónách dostupnosti je třeba učinit některá rozhodnutí:

- Definujte ideální zóny pro vrstvu DBMS.
- Určete, zda chcete distribuovat aktivní aplikační vrstvu SAP mezi jednu, dvě nebo všechny tři zóny na základě rozdílů latence sítě v zóně a mezi zónami.
- Určete, zda chcete nasadit aktivní/pasivní konfiguraci nebo aktivní/aktivní konfiguraci z hlediska aplikace. (Tyto konfigurace jsou vysvětleny dále v tomto článku.)

Při těchto rozhodnutích také vezměte v úvahu doporučení latence sítě SAP, jak je popsáno v poznámce SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Měření a rozhodnutí, která provedete, platí pro předplatné Azure, které jste použili při měření. Pokud používáte jiné předplatné Azure, musíte měření zopakovat. Mapování výčtových zón se může lišit pro jiné předplatné Azure.


> [!IMPORTANT]
> Očekává se, že měření popsané výše bude poskytovat různé výsledky v každé oblasti Azure, která podporuje [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). I v případě, že vaše požadavky na latenci sítě jsou stejné, možná budete muset přijmout různé strategie nasazení v různých oblastech Azure, protože latence sítě mezi zónami se může lišit. V některých oblastech Azure latence sítě mezi tři různé zóny může být diametrálně odlišné. V jiných oblastech může být latence sítě mezi třemi různými zónami jednotnější. Tvrzení, že je vždy latence sítě mezi 1 a 2 milisekundy není správné. Latenci sítě napříč zónami dostupnosti v oblastech Azure nelze generalizovat.

## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože nasadíte aktivní aplikační servery SAP do dvou nebo tří zón. Instance SAP Central Services, která používá replikaci fronty, bude nasazena mezi dvěma zónami. Totéž platí pro vrstvu DBMS, která bude nasazena ve stejných zónách jako centrální služba SAP.

Při zvažování této konfigurace je třeba najít dvě zóny dostupnosti ve vaší oblasti, které nabízejí latenci sítě mezi zónami, která je přijatelná pro vaše úlohy a synchronní replikaci DBMS. Chcete mít také jistotu, že rozdíl mezi latencí sítě v rámci vybraných zón a latencí sítě mezi zónami není příliš velký. Důvodem je, že nechcete velké varianty, v závislosti na tom, zda úloha běží v zóně se serverem DBMS nebo napříč zónami, v provozních časech obchodních procesů nebo dávkových úloh. Některé varianty jsou přijatelné, ale ne faktory rozdílu.

Zjednodušené schéma aktivního/aktivního nasazení ve dvou zónách může vypadat takto:

![Nasazení aktivní/aktivní zóny](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci platí následující aspekty:

- Nepoužíváte [skupinu umístění bezkontaktní](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)komunikace Azure , považujete zóny dostupnosti Azure za chybové a aktualizační domény pro všechny virtuální počítače, protože sady dostupnosti nelze nasadit v zónách dostupnosti Azure.
- Pokud chcete kombinovat zónová nasazení pro vrstvu DBMS a centrální služby, ale chcete použít sady dostupnosti Azure pro aplikační vrstvu, musíte použít skupiny bezkontaktníkomunikace Azure, jak je popsáno v článku [Skupiny umístění bez blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Pro vykladače zatížení clusterů s podporou převzetí služeb při selhání sap central services a vrstvy DBMS je třeba použít [standardní skladovou položku Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní vyrovnávání zatížení nebude fungovat napříč zónami.
- Virtuální síť Azure, kterou jste nasadili k hostování systému SAP spolu s jeho podsítěmi, se roztáhne napříč zónami. Nepotřebujete samostatné virtuální sítě pro každou zónu.
- Pro všechny virtuální počítače, které nasadíte, je potřeba použít [spravované disky Azure](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro územní nasazení.
- Azure Premium Storage a [úložiště Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují žádný typ replikace úložiště napříč zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená sdílená sada CIFS (Windows) nebo sdílená sada nfs (Linux). Je třeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Tyto technologie jsou podporovány:
  - Pro Windows řešení clusteru, které používá SIOS DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - Pro SUSE Linux, sdílené složky nfs, která je vytvořená jako zdokumentovaná ve [vysoké dostupnosti pro nfs na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    V současné době řešení, které používá Microsoft Scale-Out File Server, jak je popsáno v [Připravit infrastrukturu Azure pro SAP vysokou dostupnost pomocí clusteru windows převzetí služeb při selhání a sdílené složky pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporována napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo další instance aplikací.
- Chcete-li dosáhnout konzistence za běhu pro kritické obchodní procesy, můžete se pokusit nasměrovat určité dávkové úlohy a uživatele na instance aplikací, které jsou v zóně s aktivní instancí DBMS, pomocí skupin dávkových serverů SAP, skupin přihlášení SAP nebo skupin RFC. V případě převzetí služeb při selhání zónby byste však museli tyto skupiny ručně přesunout do instancí spuštěných na virtuálních počítačích, které jsou v zóně s aktivním virtuálním virtuálním počítačem DB.  
- Můžete chtít nasadit spící dialogové okno instance v každé zóně. To to umožňuje okamžitý návrat k bývalé kapacitě prostředků, pokud je zóna používaná částí instancí aplikace mimo provoz.

> [!IMPORTANT]
> V tomto aktivním/aktivním scénáři jsou společností Microsoft vyhlašovány další poplatky za šířku pásma od 04.01.2020. Zkontrolujte [podrobnosti o cenách šířky pásma dokumentu](https://azure.microsoft.com/pricing/details/bandwidth/). Přenos dat mezi aplikační vrstvou SAP a vrstvou SAP DBMS je poměrně intenzivní. Proto aktivní / aktivní scénář může přispět k nákladům docela dost. Mějte kontrolu tohoto článku získat přesné náklady 


## <a name="activepassive-deployment"></a>Aktivní/pasivní nasazení
Pokud nemůžete najít přijatelnou rozdílmezi latencí sítě v rámci jedné zóny a latencí síťového provozu mezi zónami, můžete nasadit architekturu, která má aktivní/pasivní znak z hlediska aplikační vrstvy SAP. Definujete *aktivní* zónu, což je zóna, ve které nasadíte celou aplikační vrstvu a kde se pokusíte spustit aktivní dbms i instanci SAP Central Services. Při takové konfiguraci se musíte ujistit, že nemáte extrémní varianty běhu, v závislosti na tom, zda úloha běží v zóně s aktivní instancí DBMS nebo ne, v obchodních transakcích a dávkových úlohách.

Základní rozložení architektury vypadá takto:

![Aktivní/pasivní zóna nasazení](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Pro tuto konfiguraci platí následující aspekty:

- Skupiny dostupnosti nelze nasadit v zónách dostupnosti Azure. Chcete-li kompenzovat, že můžete použít skupiny umístění bezkontaktní azure, jak je popsáno v článku [Skupiny umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Při použití této architektury je třeba pečlivě sledovat stav a pokusit se zachovat aktivní instance DBMS a SAP Central Services ve stejné zóně jako nasazené aplikační vrstvy. V případě převzetí služeb při selhání služby SAP Central Service nebo instance DBMS se chcete ujistit, že můžete ručně postoupit zpět do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Pro vykladače zatížení clusterů s podporou převzetí služeb při selhání sap central services a vrstvy DBMS je třeba použít [standardní skladovou položku Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní vyrovnávání zatížení nebude fungovat napříč zónami.
- Virtuální síť Azure, kterou jste nasadili k hostování systému SAP spolu s jeho podsítěmi, se roztáhne napříč zónami. Nepotřebujete samostatné virtuální sítě pro každou zónu.
- Pro všechny virtuální počítače, které nasadíte, je potřeba použít [spravované disky Azure](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro územní nasazení.
- Azure Premium Storage a [úložiště Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují žádný typ replikace úložiště napříč zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená sdílená sada CIFS (Windows) nebo sdílená sada nfs (Linux). Je třeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Tyto technologie jsou podporovány:
    - Pro Windows řešení clusteru, které používá SIOS DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Pro SUSE Linux, sdílené složky nfs, která je vytvořená jako zdokumentovaná ve [vysoké dostupnosti pro nfs na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  V současné době řešení, které používá Microsoft Scale-Out File Server, jak je popsáno v [Připravit infrastrukturu Azure pro SAP vysokou dostupnost pomocí clusteru windows převzetí služeb při selhání a sdílené složky pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporována napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo další instance aplikací.
- Měli byste nasadit spící virtuální chod v pasivní zóně (z hlediska DBMS), abyste mohli spustit prostředky aplikace v případě selhání zóny.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) teď nemůže replikovat aktivní virtuální počítače do nečinných virtuálních počítačů mezi zónami. 
- Měli byste investovat do automatizace, která vám umožní v případě selhání zóny automaticky spustit aplikační vrstvu SAP v druhé zóně.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinovaná vysoká dostupnost a konfigurace zotavení po havárii
Microsoft nesdílí žádné informace o geografických vzdálenostech mezi zařízeními, která hostují různé zóny dostupnosti Azure v oblasti Azure. Přesto někteří zákazníci používají zóny pro kombinovanou konfiguraci HA a ZOTAVENÍ, která slibuje cíl bodu obnovení (RPO) nula. To znamená, že byste neměli ztratit žádné potvrzené databázové transakce ani v případě zotavení po havárii. 

> [!NOTE]
> Doporučujeme použít konfiguraci, jako je tato pouze za určitých okolností. Můžete ji například použít, když data nemohou opustit oblast Azure z důvodů zabezpečení nebo dodržování předpisů. 

Zde je jeden příklad, jak by taková konfigurace mohla vypadat:

![Kombinovaná dr. dr. s vysokou dostupností v zónách](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci platí následující aspekty:

- Buď předpokládáte, že mezi zařízeními, která hostuje zónu dostupnosti, je značná vzdálenost, nebo jste nuceni zůstat v určité oblasti Azure. Skupiny dostupnosti nelze nasadit v zónách dostupnosti Azure. Chcete-li kompenzovat, že můžete použít skupiny umístění bezkontaktní azure, jak je popsáno v článku [Skupiny umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
- Při použití této architektury je třeba pečlivě sledovat stav a pokusit se zachovat aktivní instance DBMS a SAP Central Services ve stejné zóně jako nasazené aplikační vrstvy. V případě převzetí služeb při selhání služby SAP Central Service nebo instance DBMS se chcete ujistit, že můžete ručně postoupit zpět do zóny s aplikační vrstvou SAP nasazenou co nejrychleji.
- Měli byste mít instancí produkčních aplikací předinstalovaných ve virtuálních discích, které spouštějí aktivní instance aplikací QA.
- V případě selhání zóny vypněte instance aplikace QA a spusťte produkční instance. Všimněte si, že je třeba použít virtuální názvy pro instance aplikace, aby to fungovalo.
- Pro vykladače zatížení clusterů s podporou převzetí služeb při selhání sap central services a vrstvy DBMS je třeba použít [standardní skladovou položku Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní vyrovnávání zatížení nebude fungovat napříč zónami.
- Virtuální síť Azure, kterou jste nasadili k hostování systému SAP spolu s jeho podsítěmi, se roztáhne napříč zónami. Nepotřebujete samostatné virtuální sítě pro každou zónu.
- Pro všechny virtuální počítače, které nasadíte, je potřeba použít [spravované disky Azure](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro územní nasazení.
- Azure Premium Storage a [úložiště Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují žádný typ replikace úložiště napříč zónami. Aplikace (DBMS nebo SAP Central Services) musí replikovat důležitá data.
- Totéž platí pro sdílený adresář sapmnt, což je sdílený disk (Windows), sdílená sdílená sada CIFS (Windows) nebo sdílená sada nfs (Linux). Je třeba použít technologii, která replikuje tyto sdílené disky nebo sdílené složky mezi zónami. Tyto technologie jsou podporovány:
    - Pro Windows řešení clusteru, které používá SIOS DataKeeper, jak je popsáno v [clusteru instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Pro SUSE Linux, sdílené složky nfs, která je vytvořená jako zdokumentovaná ve [vysoké dostupnosti pro nfs na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  V současné době řešení, které používá Microsoft Scale-Out File Server, jak je popsáno v [Připravit infrastrukturu Azure pro SAP vysokou dostupnost pomocí clusteru windows převzetí služeb při selhání a sdílené složky pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporována napříč zónami.
- Třetí zóna se používá k hostování zařízení SBD v případě, že vytvoříte [cluster SUSE Linux Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo další instance aplikací.





## <a name="next-steps"></a>Další kroky
Tady jsou další kroky pro nasazení v zónách dostupnosti Azure:

- [Cluster instance SAP ASCS/SCS v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Příprava infrastruktury Azure pro vysokou dostupnost SAP pomocí clusteru s podporou převzetí služeb při selhání systému Windows a sdílené složky pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






