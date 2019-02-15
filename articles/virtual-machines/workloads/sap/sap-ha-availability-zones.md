---
title: Konfigurace úlohy SAP se zónami dostupnosti Azure | Dokumentace Microsoftu
description: Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver pomocí zóny dostupnosti Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d98a5ab13c2aecd3b3cef590526031f5bdee594
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268307"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úlohy SAP se zónami dostupnosti Azure
[Zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) je jedna z funkcí vysoké dostupnosti, které poskytuje Azure. Použití zón dostupnosti zlepšuje celkovou dostupnost úloh SAP v Azure. Tato funkce je k dispozici v některých [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). V budoucnu bude k dispozici v dalších oblastech.

Tento obrázek ukazuje základní architektury vysoké dostupnosti SAP:

![Konfigurace standardní vysoké dostupnosti](./media/sap-ha-availability-zones/standard-ha-config.png)

Nasazení aplikační vrstvě SAP v Azure jednu [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Pro zajištění vysoké dostupnosti SAP Central Services nasadíte dva virtuální počítače v samostatné skupiny dostupnosti. Použijte Windows Server Failover Clustering nebo Pacemaker (Linux) jako Architektura vysoké dostupnosti s automatické převzetí služeb při selhání v případě infrastruktury nebo softwaru problému. Další informace o těchto nasazení najdete v tématu:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Platí i pro vrstvu DBMS Hybris systémy, S/4HANA nebo SAP NetWeaver podobnou architekturu. Nasazením DBMS vrstvy v režimu aktivní/pasivní vysokou dostupnost pomocí řešení clusteru převzetí služeb při selhání k ochraně při selhání infrastruktury nebo softwaru. Řešení clusteru převzetí služeb při selhání může být specifické pro systém DBMS framework převzetí služeb při selhání, Windows Server Failover Clustering nebo Pacemaker.

K nasazení stejnou architekturu s využitím zón dostupnosti Azure, budete muset udělat nějaké změny architektury popsané výše. Tento článek popisuje tyto změny.

## <a name="considerations-for-deploying-across-availability-zones"></a>Důležité informace týkající se nasazení napříč zónami dostupnosti


Při použití zón dostupnosti, zvažte následující:

- Neexistují žádné záruky týkající se vzdáleností mezi různými zónami dostupnosti v rámci oblasti Azure.
- Zóny dostupnosti nejsou ideální řešení zotavení po Havárii. Přírodními katastrofami mohou způsobit poškození rozšířených ve světě oblastech, včetně těžkých poškození infrastruktur power. Vzdálenost mezi různými oblastmi nemusí být dostatečně velký, aby tvoří správné řešení zotavení po Havárii.
- Latence sítě napříč zónami dostupnosti není stejná ve všech oblastech Azure. V některých případech můžete nasadit a spustit aplikační vrstvě SAP napříč různými zónami, protože je přijatelné latence sítě z jednu zónu na aktivní DBMS virtuálního počítače. Ale v některých oblastech Azure, latence mezi aktivního databázového systému virtuálního počítače a instance aplikace SAP při nasazení v různých oblastech, nemusí být přijatelné pro SAP obchodních procesů. V těchto případech se architektura nasazení musí být odlišný se aktivní/aktivní architekturu pro aplikaci nebo architekturu typu aktivní/pasivní vysokou dostupnost, kde je příliš vysoká latence sítě mezi zónami.
- Při rozhodování, kam k používání zón dostupnosti, základní rozhodnutí o latenci sítě mezi zóny. Latence sítě hraje důležitou roli ve dvou oblastech:
    - Latence mezi dvěma instancemi DBMS, které potřebují mít synchronní replikace. Čím vyšší latence sítě, větší pravděpodobností bude mít vliv na škálovatelnost vašich úloh.
    - Rozdíl v latence sítě mezi virtuálním Počítačem s aktivní instanci databázového systému SAP dialogové okno instance v zóně a podobně jako virtuální počítač v jiné zóně. Jak se zvyšuje tohoto rozdílu, vliv na provozní dobu obchodní procesy a dávkové úlohy také zvyšuje, závisí na, jestli jsou spuštěné v zónami s správce databáze nebo v jiné zóně.

Při nasazení napříč zónami dostupnosti virtuálních počítačů Azure a vytvořit řešení převzetí služeb při selhání v rámci stejné oblasti Azure, platí určitá omezení:

- Je nutné použít [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) při nasazování do zóny dostupnosti Azure. 
- Mapování výčtů zóny pro fyzické zóny vyřešen na základě předplatného Azure. Pokud používáte k nasazení vašich systémů SAP různých předplatných, musíte definovat ideální zóny pro každé předplatné.
- Skupinu dostupnosti Azure v rámci zóny dostupnosti Azure se nedají nasadit. Zvolte jeden z nich jako architektura pro nasazení pro virtuální počítače.
- Nelze použít [Azure Load balancer úrovně Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) k vytvoření clusteru řešení založená na Windows Server Failover Clustering nebo Linux Pacemaker převzetí služeb při selhání. Místo toho budete muset použít [Azure standardní SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Ideální kombinaci zóny dostupnosti
Než se rozhodnete použití zón dostupnosti, je nutné určit:

- Latence sítě mezi tři zóny oblasti Azure. To vám umožní vybrat zóny s nejnižší síťovou latencí mezi zónami provozu v síti.
- Rozdíl mezi virtuálních počítačů VM latence v rámci jedné ze zón, které si vyberete a latence sítě napříč dvě zóny, které si vyberete.
- Určení, jestli se typy virtuálních počítačů, které potřebujete k nasazení jsou dostupné dvě zóny, které jste vybrali. Několik virtuálních počítačů, zejména řady M-Series virtuálních počítačů, může dojít k situacích, ve kterých jsou k dispozici pouze ve dvou tři zóny některých jednotkách SKU.

## <a name="network-latency-between-and-within-zones"></a>Latence sítě mezi a v rámci zóny
Pokud chcete zjistit latence mezi různými zónami, budete muset:

- Nasazení skladovou Položku virtuálního počítače, které chcete použít pro vaši instanci databázového systému ve všech třech zónách. Ujistěte se, že [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolená, když provedete toto měření.
- Když najdete dvě zóny s nejnižší síťovou latencí nasaďte další tři virtuální počítače skladovou Položku virtuálního počítače, který chcete použít jako aplikační vrstvu virtuálních počítačů ve třech zónách dostupnosti. Měření latence sítě na dva virtuální počítače DBMS v dvě zóny DBMS, které jste vybrali. 
- Použití **niping** jako nástroj pro měření. Tento nástroj, od SAP, je popsaná v poznámkách podporu [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy popsané pro měření latence. Protože **ping** nefunguje prostřednictvím cesty kódu akcelerovaných síťových služeb Azure nedoporučujeme její použití.

Na základě měření a dostupnosti vašich skladové položky virtuálních počítačů v zónách dostupnosti, je třeba některá rozhodnutí:

- Definujte ideální zóny pro vrstvu DBMS.
- Zjistěte, zda chcete distribuovat aktivní aplikační vrstvě SAP napříč jedna, dvě nebo všechny tři zóny, podle rozdílů latence v zóny sítě a napříč zónami.
- Určete, jestli chcete nasadit konfiguraci aktivní/pasivní nebo konfiguraci aktivní/aktivní, z hlediska aplikací. (Tyto konfigurace jsou vysvětleny dále v tomto článku).

V rozhodování, rovněž brát v úvahu účet SAP doporučení latence sítě, jak je uvedeno v Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Jsou platné pro předplatné Azure, které jste použili při trvalo měření měření a rozhodnutí, které provedete. Pokud používáte jiného předplatného Azure, musíte opakovat měření. Mapování výčtu zóny můžou být různé pro jiného předplatného Azure.


> [!IMPORTANT]
> Očekává se, že měření bylo popsáno dříve bude poskytovat různé výsledky ve všech oblastech Azure, který podporuje [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). I v případě, že vaše požadavky na latenci sítě jsou stejné, můžete potřebovat přijmout různé strategie nasazení v různých oblastech Azure, protože latence sítě mezi zónami se může lišit. V některých oblastech Azure latence sítě mezi tři různé oblasti, může se výrazně lišit. V jiných oblastech může být latence sítě mezi tři různé oblasti jednotnější. Deklarace identity, který je vždycky latence sítě mezi 1 a 2 ms není správná. Latence sítě napříč zónami dostupnosti v oblasti Azure, které nelze zobecněný.

## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože nasazení vaše aktivní dialogové okno instance SAP napříč zónami dvě nebo tři. Centrální služby SAP instanci, která používá replikaci na zařazení do fronty se nasadí mezi dvě zóny. Totéž platí pro vrstvu DBMS, které budou nasazené ve stejné oblasti jako centrální služby SAP.

Při zvažování tuto konfiguraci, budete muset najít dvě zóny dostupnosti ve vaší oblasti této nabídky mezi zónami sítích s latencí, které je přijatelné pro vaši úlohu a vaše synchronní replikace DBMS. Můžete také chtít nechat rozdílů mezi latence sítě v rámci zóny, kterou jste vybrali a latence sítě napříč zónami není příliš velký. Důvodem je, že už nechcete velké odchylky, v závislosti na tom, jestli úloha běží v zóně serverem DBMS nebo napříč oblasti, ve spuštěné časů vašich firemních procesech nebo dávkových úloh Hive. Některé změny jsou přijatelné, ale ne faktory rozdíl.

Zjednodušené schéma aktivní/aktivní nasazení napříč zónami dvě může vypadat například takto:

![Nasazení zóny aktivní/aktivní](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Je považovat za zóny dostupnosti Azure doménami selhání a aktualizačními doménami pro všechny virtuální počítače, protože skupiny dostupnosti se nedá nasadit zóny dostupnosti Azure.
- Pro nástroje pro vyrovnávání zatížení clustery převzetí služeb při selhání centrální služby SAP a vrstvě DBMS, budete muset použít [skladová položka Azure Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní nástroje pro vyrovnávání zatížení nebude fungovat mezi zónami.
- Virtuální síť Azure, který jste nasadili jako hostitele systému SAP, společně s jeho podsítě je roztažená napříč zónami. Není nutné oddělit virtuálních sítí pro každou zónu.
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení.
- Azure Premium Storage a [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují libovolného typu replikace úložiště mezi zónami. Aplikace (systém DBMS nebo centrální služby SAP) musí replikovat důležitá data.
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo do sdílené složky systému souborů NFS (Linux). Budete muset použít technologii, která se replikují mezi zóny těchto sdílených disků nebo sdílených složek. Podporují se tyto technologie:
    - Pro Windows, clusterové řešení, která používá SIOS DataKeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    V současné době řešení, které používá Microsoft horizontální navýšení kapacity souborového serveru, jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporované napříč zónami.
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux Pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace.
- Jak dosáhnout konzistence běhu pro důležité obchodní procesy, můžete zkusit přímé některé úlohy služby batch a uživatelů do instancí aplikace, které jsou v zóně s aktivní instanci databázového systému SAP pomocí služby batch skupiny serverů, skupin přihlášení nebo RFC skupiny. Ale v případě oblastmi převzetí služeb při selhání, je třeba ručně přesunout tyto skupiny do instance běžící na virtuálních počítačích, které jsou v zóně s virtuálním Počítačem aktivní databáze.  
- Můžete chtít nasadit neaktivní dialogové okno instance ve všech oblastech. Toto je povolit okamžité návrat do kapacity bývalé prostředku, pokud zóna používané části instancí vašich aplikací je mimo provoz.


## <a name="activepassive-deployment"></a>Aktivní/pasivní vysoká dostupnost nasazení
Pokud nemůžete najít přijatelné rozdílů mezi latence sítě v jedné zóně a latence napříč zónami síťový provoz, můžete nasadit architekturu, která obsahuje znak aktivní/pasivní vysokou dostupnost z SAP application layer hlediska. Můžete definovat *aktivní* zóně, což je zóna, kde nasadíte kompletní aplikační vrstvy a při pokusu o spuštění aktivní správce databáze a instance centrální služby SAP. S konfigurací budete muset Ujistěte se, že nemáte extreme běhu odchylky, v závislosti na tom, zda úloha běží v zónami s aktivní instanci databázového systému nebo Ne, v obchodních transakcí a úloh služby batch.

Základní rozložení architektura vypadá takto:

![Aktivní/pasivní zónu nasazení](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Ano v takovém případě máte jednu doménu aktualizace a odolnost pro aplikační vrstvu. To je proto, že je nasazena pouze v jedné zóně. Tato konfigurace je mírně méně žádoucí než referenční architektury, což naznačuje, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Při použití této architektury, budete muset pečlivě monitorovat stav a snažte se zachovat aktivní DBMS a centrální služby SAP instance ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému budete chtít Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejrychleji.
- Pro nástroje pro vyrovnávání zatížení clustery převzetí služeb při selhání centrální služby SAP a vrstvě DBMS, budete muset použít [skladová položka Azure Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní nástroje pro vyrovnávání zatížení nebude fungovat mezi zónami.
- Virtuální síť Azure, který jste nasadili jako hostitele systému SAP, společně s jeho podsítě je roztažená napříč zónami. Není nutné oddělit virtuálních sítí pro každou zónu.
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení.
- Azure Premium Storage a [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují libovolného typu replikace úložiště mezi zónami. Aplikace (systém DBMS nebo centrální služby SAP) musí replikovat důležitá data.
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo do sdílené složky systému souborů NFS (Linux). Budete muset použít technologii, která se replikují mezi zóny těchto sdílených disků nebo sdílených složek. Podporují se tyto technologie:
    - Pro Windows, clusterové řešení, která používá SIOS DataKeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  V současné době řešení, které používá Microsoft horizontální navýšení kapacity souborového serveru, jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporované napříč zónami.
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux Pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace.
- Měli byste nasadit neaktivní virtuální počítače ve skupině pasivní zónu (z DBMS hlediska), abyste je mohli začít prostředků aplikace v případě selhání zóny.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) současné době není schopna replikovat virtuální počítače aktivní na neaktivní virtuální počítače mezi zónami. 
- Investovat ve službě automation, která umožňuje, v případě selhání zóny na automatické spuštění aplikační vrstvě SAP v druhé zóny.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinované vysoké dostupnosti a konfigurace zotavení po havárii
Microsoft nesdílel žádné informace o geografické vzdálenosti mezi prostředky tohoto hostitele různých zón dostupnosti Azure v oblasti Azure. Zákazníci, kteří stále, využívají zóny pro kombinované vysokou dostupnost a zotavení po Havárii konfiguraci, která slibuje plánovaného bodu obnovení (RPO), nula. To znamená, že by neměla ztratit jakékoli transakce potvrzeny databáze i v případě zotavení po havárii. 

> [!NOTE]
> Doporučujeme použít konfiguraci jako to pouze za určitých okolností. Ho můžete použít například, když data nemohou opustit oblast Azure z důvodů zabezpečení nebo dodržování předpisů. 

Tady je jeden příklad toho, jak taková konfigurace může vypadat:

![Kombinované zotavení po Havárii vysokou dostupnost v oblastech](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci, platí následující aspekty:

- Buď jste za předpokladu, že je důležité vzdálenost mezi zařízení, který je hostitelem zóny dostupnosti nebo budete muset zůstat v určité oblasti Azure. Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Ano v takovém případě máte jednu doménu aktualizace a odolnost pro aplikační vrstvu. To je proto, že je nasazena pouze v jedné zóně. Tato konfigurace je mírně méně žádoucí než referenční architektury, což naznačuje, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Při použití této architektury, budete muset pečlivě monitorovat stav a snažte se zachovat aktivní DBMS a centrální služby SAP instance ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému budete chtít Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejrychleji.
- Měli byste mít produkčních instancí aplikace předinstalované ve virtuálních počítačích, na kterých běží aktivní instance aplikace dotazů a odpovědí.
- V případě selhání zóny vypne instance aplikace QA a spouštěli produkční instance. Všimněte si, že budete muset použít virtuální názvů pro instance aplikace provést tuto práci.
- Pro nástroje pro vyrovnávání zatížení clustery převzetí služeb při selhání centrální služby SAP a vrstvě DBMS, budete muset použít [skladová položka Azure Load balanceru úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Základní nástroje pro vyrovnávání zatížení nebude fungovat mezi zónami.
- Virtuální síť Azure, který jste nasadili jako hostitele systému SAP, společně s jeho podsítě je roztažená napříč zónami. Není nutné oddělit virtuálních sítí pro každou zónu.
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení.
- Azure Premium Storage a [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nepodporují libovolného typu replikace úložiště mezi zónami. Aplikace (systém DBMS nebo centrální služby SAP) musí replikovat důležitá data.
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo do sdílené složky systému souborů NFS (Linux). Budete muset použít technologii, která se replikují mezi zóny těchto sdílených disků nebo sdílených složek. Podporují se tyto technologie:
    - Pro Windows, clusterové řešení, která používá SIOS DataKeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  V současné době řešení, které používá Microsoft horizontální navýšení kapacity souborového serveru, jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), není podporované napříč zónami.
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux Pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace.





## <a name="next-steps"></a>Další postup
Tady jsou některé další kroky k nasazení napříč zónami dostupnosti Azure:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Příprava infrastruktury Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






