---
title: Konfigurace úlohy SAP se zónami dostupnosti Azure | Dokumentace Microsoftu
description: Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver použití zón dostupnosti Azure
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
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858801"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úlohy SAP se zónami dostupnosti Azure

Mezi funkce vysoké dostupnosti Azure nabízí, je [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Použití zón dostupnosti zlepšuje celkovou dostupnost úloh SAP v Azure. Zóny dostupnosti jsou nabízeny v několika [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) již. Postupujte podle dalších oblastí. 

Základní architektura vysoké dostupnosti SAP lze popsat, jak se zobrazuje na tomto obrázku:

![Standardní konfigurace s vysokou DOSTUPNOSTÍ](./media/sap-ha-availability-zones/standard-ha-config.png)

Nasazení aplikační vrstvě SAP v Azure jednu [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Pro zajištění vysoké dostupnosti SAP Central Services nasadíte dva virtuální počítače v samostatné skupiny dostupnosti. Použití služby clusteru převzetí služeb při selhání Windows nebo Pacemaker (Linux) jako Architektura vysoké dostupnosti s automatické převzetí služeb při selhání v případě problému infrastruktury nebo softwaru. Tady jsou uvedené dokumenty s podrobnostmi o těchto nasazení:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Platí i pro vrstvu DBMS Hybris systémy, S/4HANA nebo SAP NetWeaver podobnou architekturu. Nasazením DBMS vrstvy v režimu aktivní/pasivní vysokou dostupnost pomocí řešení clusteru převzetí služeb při selhání k ochraně při selhání infrastruktury nebo softwaru. Řešení clusteru převzetí služeb při selhání může být buď DBMS konkrétní převzetí služeb při selhání framework služby clusteru převzetí služeb při selhání Windows nebo Pacemaker. 

Abyste mohli nasadit stejné architektury použití zón dostupnosti Azure, jsou potřeba některé změny architektury uvedených výše. Tyto změny jsou popsány v tomto dokumentu.

## <a name="considerations-deploying-across-availability-zones"></a>Důležité informace o nasazení napříč zónami dostupnosti

Při použití zón dostupnosti, existují některé věci k uvážení:

- Zóny dostupnosti Azure nezaručují určité vzdálenosti mezi různými zónami v rámci jedné oblasti Azure.
- Zóny dostupnosti Azure nejsou ideální řešení zotavení po Havárii. Přírodními katastrofami může způsobit široké šíří poškození v některých oblastech světě, včetně těžkých poškození infrastruktura power. Vzdálenost mezi různými zónami může být dostatečně velký, aby se vztahovala jako správné řešení zotavení po Havárii.
- Latence sítě napříč zónami dostupnosti se liší v různých oblastech Azure. Existují případy, ve kterém můžete nasazovat a spouštět aplikační vrstvě SAP napříč různými zónami, jako je latence sítě z jednu zónu na aktivního virtuálního počítače DBMS přijatelné z obchodní dopad procesu. Ale v některých oblastech Azure latence mezi aktivního databázového systému virtuálního počítače a instance aplikace SAP, nasazené v různých oblastech, může být příliš rušivé a nepřijatelné pro SAP obchodních procesů. Architektura nasazení v důsledku toho musí být odlišný s architekturu aktivní/aktivní pro aplikaci nebo architekturu aktivní/pasivní vysokou dostupnost, kde je příliš vysoké latenci mezi zóny.
- Rozhodněte, v konfiguraci, kterou jste mohli používat zóny dostupnosti Azure, na základě latence sítě, které budete vyhodnocovat mezi různými zónami. Latence sítě hraje důležitou roli ve dvou oblastech:
    - Latence mezi dvěma instancemi DBMS, které potřebují mít synchronní replikace navázat. Větší je latence sítě, tím větší potenciál dopad škálovatelnost vašich úloh
    - Rozdíl v latence sítě mezi virtuálním Počítačem s aktivní instanci databázového systému SAP dialogové okno instance v zóně a podobně jako virtuální počítač v jiné zóně. Čím to rozdíl, tím větší dopad na dobu zpracování obchodních procesů a dávkových úloh, závisí na, jestli jsou spuštěné v zónami s správce databáze nebo v jiné zóně.


Platí určitá omezení, při nasazování virtuálních počítačů Azure napříč zónami dostupnosti a poskytování řešení převzetí služeb při selhání v rámci stejné oblasti Azure. Tato omezení jsou popsané v následujícím seznamu:

- Pomocí [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) je povinné pro nasazení do zóny dostupnosti Azure 
- Mapování výčtů zóny pro fyzické zóny vyřešen na základě předplatného Azure. Pokud nasazení vašich systémů SAP pomocí různých předplatných, musíte definovat ideální zóny pro každé předplatné.
- Skupinu dostupnosti Azure v rámci zóny dostupnosti Azure se nedají nasadit. Zvolte zóně dostupnosti Azure nebo Azure dostupnosti jako architektura pro nasazení pro virtuální počítače.
- Nelze použít [Azure Load balancer úrovně Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) k vytvoření clusteru řešení založená na služby clusteru převzetí služeb při selhání Windows nebo Linuxem Pacemaker převzetí služeb při selhání. Místo toho budete muset použít [Azure standardní SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-availability-zones-combination"></a>Zóny dostupnosti ideální kombinaci
Než se rozhodnete, jak využít zóny dostupnosti, budete muset provést šetření, které vám:

- Latence sítě mezi třemi různými zónami oblasti Azure. Proto, že můžete nastavit zóny s nejnižší síťovou latencí v různé síťové přenosy zóny
- Rozdíl mezi virtuálních počítačů VM latence v rámci jedné oblasti, kterou jste zvolili a latence sítě napříč dvě zóny, kterou jste zvolili
- Příkaz, který určuje, zda virtuální počítač typy, které potřebujete pro nasazení jsou k dispozici dvě zóny podle vašeho výběru. Zejména s virtuálními počítači řady M-Series se chystáte nastat situace, kdy různé skladové položky virtuálních počítačů řady M-Series budou k dispozici pouze ve dvou tři zóny

### <a name="network-latency-between-zones-and-within-zone"></a>Latence sítě mezi zónami a v rámci zóny
Chcete-li zjistit, co je latence mezi různými zónami, budete muset:

- Nasazení skladovou Položku virtuálního počítače, které chcete použít pro vaši instanci databázového systému ve všech třech zónách. Ujistěte se, že [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povoleno při provádění tohoto měření.
- Jak to můžete znát dvě zóny s nejnižší síťovou latencí, nasaďte další tři virtuální počítače skladovou Položku virtuálního počítače, které chcete použít jako aplikační vrstvu virtuálních počítačů ve třech zónách dostupnosti. Měření latence sítě proti dvěma "DBMS virtuálními počítači' ve dvou různých zónách 'DBMS' podle vašeho výběru. 
- Jakožto nástroj k měření použití **niping**. Nástroje ze SAP, což funguje, jak je popsáno v poznámkách k SAP support [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy SAP poznamenat, měření latence. Pomocí **ping** se nedoporučuje, protože **ping** nefunguje prostřednictvím Azure akcelerovaných síťových cest kódu.

Na základě měření a dostupnosti vašich skladové položky virtuálních počítačů v zónách dostupnosti, je třeba dělat rozhodnutí:

- Definovat ideální zóny pro vrstvu DBMS
- Odpověď na otázku Pokud chcete distribuovat aktivní aplikační vrstvě SAP napříč jednu, dvě nebo všechny tři zóny, v závislosti na rozdíly sítě latence v zónami vs. napříč zónami
- Odpověď na otázku, pokud chcete nasadit aktivní/pasivní nebo konfiguraci aktivní/aktivní z hlediska aplikací (viz dále)

Tyto rozhodování, také odvolat SAP doporučení latence sítě, jak je uvedeno v Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Mějte

> [!IMPORTANT]
> Měření a rozhodnutí, které provedete, jsou platné pro předplatné Azure, které jste použili provádění těchto měření. Pokud používáte jiného předplatného Azure, musíte opakovat měření, protože mapování výčtu zóny se může lišit pro jiné předplatné Azure.


> [!IMPORTANT]
> Očekává se, že měření, jak provést výše se zobrazují různé výsledky ve všech oblastech Azure, který podporuje [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). I v případě, že vaše požadavky na latenci sítě jsou stejné, můžete potřebovat přijmout různé strategie nasazení v různých oblastech Azure, protože latence sítě mezi zónami se může lišit. Očekává se, že v některých oblastech Azure, latence sítě mezi třemi různými zónami může být značně rozdílné. Vzhledem k tomu v jiných oblastech je latence sítě mezi třemi různými zónami jednotnější. Deklarace identity tom, že **vždy** je latence sítě napříč zónami 1 milisekund na 2 milisekund **nesprávné**. Latence sítě napříč zónami dostupnosti v oblasti Azure, které nelze zobecněný.


## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože nasazení vaše aktivní dialogové okno instance SAP napříč zónami dvě nebo tři. Centrální služby SAP pomocí zařazení do fronty replikace bude možné nasadit mezi dvě zóny. Totéž platí pro vrstvu DBMS, které se chystáte nasadit ve stejné oblasti jako centrální služby SAP.

Chcete-li tento takovou konfiguraci, najít dvě zóny dostupnosti ve vaší oblasti, které nabízejí latence sítě mezi zóny, které je přijatelné pro vaši úlohu a pro vaše synchronní replikaci DBMS. Kromě toho chcete rozdílů mezi latence sítě v rámci zóny vyberete a v zóně latence sítě není je příliš velký. Důvod k tomu, je, že nechtějí příliš velké rozdíly v běhu časy vašich obchodních procesů nebo dávkové úlohy, závisí na, jestli úloha spuštění v zóny se serverem DBMS nebo napříč zónami. Některé změny jsou přijatelné, ale ne faktory rozdíl.

Zjednodušené schéma aktivní/aktivní nasazení napříč zónami dvě může vypadat:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Je považovat za zóny dostupnosti Azure doménami selhání a aktualizačními doménami pro všechny virtuální počítače od dostupnosti nejde nasadit do zón dostupnosti Azure
- Nástroje pro vyrovnávání zatížení Azure použít pro převzetí služeb při selhání clustery s centrální služby SAP a vrstvě DBMS musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, použijte [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - Aktuálně řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace
- Jak dosáhnout konzistence běhu pro důležité obchodní procesy, můžete zkusit přímé některé úlohy služby batch a uživatelů do instancí aplikace, které jsou v zóně s aktivní instanci pomocí skupiny přihlášení, RFC skupin a skupin serverů služby Batch SAP DBMS. Ale v případě oblastmi převzetí služeb při selhání budete muset ručně přesunout tyto skupiny instancí běžících na virtuálních počítačích v zóny pomocí aktivního virtuálního počítače databáze.  
- Rozhodněte, pokud chcete nasadit některé instance neaktivní dialogového okna v každé zóny, abyste mohli okamžitě získat kapacity bývalé prostředku, pokud zóna používané části instancí vašich aplikací je mimo provoz


## <a name="activepassive-deployment"></a>Aktivní/pasivní vysoká dostupnost nasazení
Pokud nemůžete najít přijatelné rozdílů mezi latence sítě v rámci jedné zóně a různé zóny síťový provoz, architektury, které můžete nasadit má znak aktivní/pasivní vysokou dostupnost z SAP application layer hlediska. Můžete definovat "aktivní" zóně, což je zóna nasazovaným kompletní aplikační vrstvu a kde se uživatel pokusí spustit active DBMS a centrální služby SAP instance. S konfigurací můžete zajistit nemáte extreme běhu odchylky v obchodních transakcí a dávkových úloh, závisí na tom, zda úloha běží v zónami s aktivní instanci databázového systému nebo ne.

Základní rozložení takové architektury vypadá takto:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Proto v tomto případě máte jednu doménu aktualizace a odolnost pro aplikační vrstvu. Důvodem je, že je pouze nasazené v jedné zóně. Tato konfigurace je mírné setback, ve srovnání s referenční architektury, který předpokládá, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Provozování takové architektury, musíte celý proces bedlivě sledujte a snažte se zachovat aktivní instance DBMS a SAP Central services ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému kterou chcete Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejdříve
- Nástroje pro vyrovnávání zatížení Azure použít pro převzetí služeb při selhání clustery s centrální služby SAP a vrstvě DBMS musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - Aktuálně řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace
- Abyste mohli spustit aplikace prostředků v případě selhání zóny nasazení neaktivní virtuálních počítačů v pasivní zónu (z DBMS hlediska)
    - Nemůžete použít [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) pro replikaci virtuálních počítačů aktivní na neaktivní virtuální počítače mezi zónami. V tomto okamžiku není možné splnit takové funkce Azure Site Recovery
- Investovat do automatizaci, která umožňuje, v případě selhání zóny na automatické spuštění aplikační vrstvě SAP v druhé zóny

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinované vysoké dostupnosti a konfigurace zotavení po havárii
Společnost Microsoft neposkytuje žádné informace o geografické vzdálenosti mezi prostředky tohoto hostitele různých Azure zón dostupnosti v oblasti Azure. Bez ohledu na tuto skutečnost zákazníci, kteří využívají zóny pro kombinované vysokou dostupnost a zotavení po Havárii konfiguraci, která slibuje **R**pro obnovení **P**SharePoint **O**bjective (RPO) nula. To znamená, že by neměla ztratí všechny transakce potvrzeny databáze i v případě zotavení po havárii. 

> [!NOTE]
> Konfigurace tímto způsobem se doporučuje pro pouze zvláštní okolnosti. Například případy, kdy data nemohou opustit oblast Azure z důvodů zabezpečení a dodržování předpisů. 

Na tomto obrázku je znázorněn příklad, jak taková konfigurace může vypadat:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci, platí následující aspekty:

- Jsou buď za předpokladu, že je důležité vzdálenost mezi zařízení, který je hostitelem zóny dostupnosti. Nebo se musí Zůstaňte s přední určitá oblast Azure. Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Proto v tomto případě je jsou končí jednu doménu aktualizace a odolnost pro aplikační vrstvu. Důvodem je, že je pouze nasazené v jedné zóně. Toto je setback ve srovnání s referenční architektury, který předpokládá, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Provozování takové architektury, musíte celý proces bedlivě sledujte a snažte se zachovat aktivní instance DBMS a SAP Central services ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému kterou chcete Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejdříve
- Máte produkčních instancí aplikace předinstalované ve virtuálních počítačích, na kterých běží aktivní instance aplikace dotazů a odpovědí.
- V případě selhání oblastmi vypne instance aplikace QA a spouštěli produkční instance. Mějte na paměti, které potřebujete pro práci s virtuální názvy instancí aplikace, aby tuto práci
- Nástroje pro vyrovnávání zatížení Azure použít pro převzetí služeb při selhání clustery s centrální služby SAP a vrstvě DBMS musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux sdílet systému souborů NFS, které tvoříme tak, jak je uvedeno v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - V tomto okamžiku řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace





## <a name="next-steps"></a>Další kroky
Zkontrolujte další kroky k nasazení napříč zónami dostupnosti Azure:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Příprava infrastruktury Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






