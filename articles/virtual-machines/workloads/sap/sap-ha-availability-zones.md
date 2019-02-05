---
title: Konfigurace úlohy SAP se zónami dostupnosti Azure | Dokumentace Microsoftu
description: Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver použití zón dostupnosti Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: juergent
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
ms.author: msjuergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 56c1ffd314a9a8e9440832b9fd92a51cdaf9f228
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55735652"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Konfigurace úlohy SAP se zónami dostupnosti Azure

Jeden z funkce vysoké dostupnosti Azure nabízí pro zlepšení celkovou dostupnost úloh SAP v Azure, jsou [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview). Zóny dostupnosti jsou k dispozici v různých [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/) již. Postupujte podle dalších oblastí. 

Základní architektura vysoké dostupnosti SAP lze popsat, jak se zobrazuje na tomto obrázku:

![Standardní konfigurace s vysokou DOSTUPNOSTÍ](./media/sap-ha-availability-zones/standard-ha-config.png)

Nasazení aplikační vrstvě SAP v Azure jednu [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Pro zajištění vysoké dostupnosti centrální služby SAP nasadíte dva virtuální počítače v samostatné skupiny dostupnosti a k nasazení Architektura vysoké dostupnosti, která umožňuje automatické převzetí služeb při selhání v případě infrastrukturu použít služby clusteru převzetí služeb při selhání Windows nebo Pacemaker (Linux) nebo byly problémy s softwaru. Dokumentace ke službě s podrobnostmi o tento seznam nasazení jako:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Platí i pro vrstvu DBMS Hybris systémy, S/4HANA nebo SAP NetWeaver podobnou architekturu. Nasaďte vrstvu DBMS v režimu aktivní/pasivní vysokou dostupnost pomocí řešení clusteru převzetí služeb při selhání, který používá systém DBMS architektury konkrétní převzetí služeb při selhání, služby clusteru převzetí služeb při selhání Windows nebo Pacemaker k zahájení převzetí služeb při selhání aktivity v případě infrastruktury nebo softwaru došlo k chybě. 

Abyste mohli nasadit stejné architektury použití zón dostupnosti Azure, potřeba provést některé změny architektury popsané. Tyto změny jsou popsány v různých částech tohoto dokumentu.

## <a name="considerations-deploying-across-availability-zones"></a>Důležité informace o nasazení napříč zónami dostupnosti

Použití zón dostupnosti, existují některé věci k uvážení. Důležité informace o seznam jako:

- Zóny dostupnosti Azure nejsou uvedením jakékoli záruky určité vzdálenosti mezi různými zónami v rámci jedné oblasti Azure
- Zóny dostupnosti Azure nejsou ideální řešení zotavení po Havárii. V časy kde způsobují velké přírodními katastrofami široké šíření poškození v některé oblasti na světě, včetně těžkých poškození power infrastruktury, vzdálenost mezi různými zónami může není dostatečně velký, aby se vztahovala jako správné řešení zotavení po Havárii
- Latence sítě napříč zónami dostupnosti různé Azure v různých oblastech Azure liší od oblastmi Azure. Existují případy, kde můžete spouštět aplikační vrstvě SAP nasadit napříč různými zónami, protože je stále přijatelné z obchodní dopad procesu latence sítě z jednu zónu na aktivní DBMS virtuálního počítače. Ale existují scénáře, v některých oblastech Azure, kde může být latence mezi aktivního virtuálního počítače DBMS v jedné zóně a instance aplikace SAP v jiné zóně příliš rušivé a nepřijatelný pro SAP obchodních procesů. Architektura nasazení v důsledku toho musí být odlišný s architekturu aktivní/aktivní pro aplikaci nebo architekturu aktivní/pasivní vysokou dostupnost, kde je příliš vysoké latenci mezi zóny.
- Provedení rozhodnutí v konfiguraci, kterou jste mohli používat zóny dostupnosti Azure pro. Založené na latenci sítě, které jsou měření mezi různými zónami. Latence sítě hraje důležitou roli ve dvou různých oblastech:
    - Latence mezi dvěma instancemi DBMS, které potřebují mít synchronní replikace navázat. Větší je latence sítě, tím větší potenciál dopad škálovatelnost vašich úloh
    - Rozdíl v latence sítě virtuálního počítače, který spouští instanci SAP dialogového okna ve stejné zóně jako aktivní instanci databázového systému a podobně jako virtuální počítač v jiné zóně. Čím vyšší tohoto rozdílu, tím vyšší dopad na dobu spuštění obchodní procesy a úlohy služby batch, závisí na, jestli jsou spuštěné ve stejné zóně s správce databáze nebo v jiné zóně


K používání funkcí Azure platí určitá omezení funkcí, které se dá použít při nasazování virtuálních počítačů Azure mezi zónami a vytváří řešení pro převzetí služeb při selhání v různých zón dostupnosti v rámci stejné oblasti Azure. Tato omezení jsou uvedeny jako:

- Pomocí [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) je povinné pro nasazení do zóny dostupnosti Azure 
- Mapování výčtů zóny pro fyzické zóny vyřešen na základě předplatného Azure. Pokud používáte k nasazení vašich systémů SAP různých předplatných, budete muset definovat samostatně ideální zóny pro každé předplatné
- Skupinu dostupnosti Azure v rámci zóny dostupnosti Azure se nedají nasadit. Zvolte zóně dostupnosti Azure nebo Azure dostupnosti jako architektura pro nasazení pro virtuální počítače.
- Nelze použít [Azure Load balancer úrovně Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) k vytvoření clusteru řešení založená na služby clusteru převzetí služeb při selhání Windows nebo Linuxem Pacemaker převzetí služeb při selhání. Místo toho budete muset použít [Azure standardní SKU nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Kombinace ideální zóny
Při rozhodování o tom, jak můžete využít zóny dostupnosti, budete muset provést šetření, které vám:

- Latence sítě mezi třemi různými zónami oblasti Azure. Ano můžete nastavit zóny, které mají nejnižší latence sítě mezi síťové přenosy zóny
- Rozdíl mezi virtuálních počítačů VM latence v rámci jedné oblasti, kterou jste zvolili a latence sítě napříč dvě zóny, kterou jste zvolili
- Příkaz, který určuje, zda virtuální počítač typy potřebné pro nasazení napříč zónami dostupnosti podle výběru jsou k dispozici dvě zóny podle vašeho výběru. Zejména s virtuálními počítači řady M-Series se chystáte nastat situace, kdy různé skladové položky virtuálních počítačů řady M-Series budou k dispozici ve dvou pouze tři zóny

### <a name="network-latency-between-zones-and-within-zone"></a>Latence sítě mezi zónami a v rámci zóny
Chcete-li zjistit, co je latence mezi různými zónami, budete muset:

- Nasazení skladovou Položku virtuálního počítače, které chcete použít pro vaši instanci databázového systému ve všech třech zónách. Ujistěte se, že, který [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolená, když provádí toto měření
- Jak to můžete znát dvě zóny s nejnižší síťovou latencí, nasaďte další tři virtuální počítače skladovou Položku virtuálního počítače, které chcete použít jako aplikační vrstvu virtuálních počítačů ve třech zónách dostupnosti. Měření latence sítě proti dvěma "DBMS virtuálními počítači' ve dvou různých zónách 'DBMS' podle vašeho výběru. 
- Jakožto nástroj k měření použití **niping**. Nástroje ze SAP, což funguje, jak je popsáno v poznámkách k SAP support [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Zaměřte se na příkazy SAP poznamenat, měření latence. Pomocí **ping** není doporučenému nástroji od **ping** nefunguje prostřednictvím Azure akcelerovaných síťových cest kódu.

Na základě měření a dostupnosti vašich skladové položky virtuálních počítačů v různých oblastech, třeba i další otázky:

- Definovat ideální zóny pro vrstvu DBMS
- Odpověď na otázku, zda se podle rozdílů latence sítě v rámci zóny nebo napříč zónami, jste mohli distribuovat aktivní aplikační vrstvě SAP napříč jednu, dvě nebo všechny tři zóny
- Odpověď na otázku, jestli se má nasadit aktivní/pasivní nebo konfiguraci aktivní/aktivní z hlediska aplikací (viz dále)

Tyto rozhodování, také odvolat SAP doporučení latence sítě, jak je uvedeno v Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Mějte

> [!IMPORTANT]
> Měření a rozhodnutí, které provedete, jsou platné pro předplatné Azure, které jste použili provádění těchto měření. Použít jiné předplatné Azure, budete muset opakovat měření, protože mapování závislé výčet zón vaše předplatné můžete změnit pomocí jiného předplatného


> [!IMPORTANT]
> Očekává se, že měření, jak provést výše se zobrazují různé výsledky ve všech oblastech Azure, který podporuje [zóny dostupnosti](https://docs.microsoft.com/azure/availability-zones/az-overview). I u vašich požadavků na latenci sítě se nemění budete možná muset přizpůsobit různé strategie nasazení v různých oblastech Azure, protože latence sítě mezi zónami se může lišit. Očekává se, že v některých oblastech Azure, latence sítě mezi třemi různými zónami může být značně rozdílné. Vzhledem k tomu v jiných oblastech jsou jednotnější latence sítě mezi třemi různými zónami. Deklarace identity tom, že **vždy** je latence sítě napříč zónami 1 milisekund na 2 milisekund **nesprávné**. Latence sítě napříč zónami dostupnosti v oblasti Azure, které nelze zobecněný.


## <a name="activeactive-deployment"></a>Aktivní/aktivní nasazení
Tato architektura nasazení se nazývá aktivní/aktivní, protože nasazení vaše aktivní dialogové okno instance SAP napříč zónami dvě nebo tři. Centrální služby SAP pomocí zařazení do fronty replikace bude možné nasadit mezi dvě zóny. Totéž platí pro vrstvu DBMS, které se chystáte nasadit ve stejné oblasti jako centrální služby SAP.

Chcete-li tento takovou konfiguraci, najít dvě zóny dostupnosti ve vaší oblasti, které nabízejí latence sítě mezi zóny, které je přijatelné pro vaši úlohu a pro vaše synchronní replikaci DBMS. Kromě toho chcete rozdílů mezi latence sítě v rámci zóny vyberete a v zóně latence sítě není je příliš velký. Důvod k tomu, že už nechcete příliš velké rozdíly v běhu časy vašich obchodních procesů nebo batch zpracovává závislé na, jestli úloha spuštění v zóny se serverem DBMS nebo napříč zónami. Některé změny jsou přijatelné, ale ne faktory rozdíl.

Zjednodušené schéma aktivní/aktivní nasazení napříč zónami dvě může vypadat:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Je považovat za zóny dostupnosti Azure doménami selhání a aktualizačními doménami pro všechny virtuální počítače od dostupnosti nejde nasadit do zón dostupnosti Azure
- Nástroje pro vyrovnávání zatížení Azure použít pro centrální služby SAP, stejně jako vrstva DBMS, clustery převzetí služeb při selhání musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, použijte [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux vytvořené sdílené složky NFS podle postupu uvedeného v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - V tomto okamžiku řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace
- Jak dosáhnout konzistence běhu pro několik důležitých obchodních transakcí a/nebo úlohy. Můžete řídit některé úlohy služby batch a uživatele přímo do instancí konkrétní aplikace, které jsou v zóně s aktivní instanci databázového systému pomocí skupiny přihlášení, RFC skupin a skupin serverů služby Batch SAP. Ale v případě oblastmi převzetí služeb při selhání, budete muset ručně přesunout tyto skupiny do dialogového okna instancí, které jsou ve zbývajících tyto zóny: 
- Rozhodněte, jestli chcete nasadit některé instance neaktivní dialogového okna v každé zóny, které mají být okamžitě získají kapacitu bývalé prostředků v případě, že zóna, které jste nasadili část instancí vašich aplikací, je mimo provoz


## <a name="activepassive-deployment"></a>Aktivní/pasivní vysoká dostupnost nasazení
V případech, kdy nejsou hledání přijatelné rozdílů mezi latence sítě v rámci jedné zóně a různé zóny síťový provoz architektury, které můžete nasadit má znak aktivní/pasivní vysokou dostupnost z SAP application layer hlediska. Můžete definovat "aktivní" zóně, což je zóna nasadit kompletní aplikační vrstvu do a kde se uživatel pokusí spustit aktivní instanci databázového systému a aktivní instanci centrální služby SAP. S konfigurací Ujistěte se, nemáte extreme běhu rozdíl v obchodních transakcí a dávkových úloh, závisí na tom, zda úloha běží ve stejné zóně s aktivní instanci databázového systému nebo ne.

Základní rozložení takové architektury vypadá takto:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Pro tuto konfiguraci, platí následující aspekty:

- Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Proto v tomto případě je jsou končí jednu doménu aktualizace a odolnost pro aplikační vrstvu. Důvodem je, že je pouze nasazené v jedné zóně. Tato konfigurace je mírné setback ve srovnání s referenční architektury, který předpokládá, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Provozování takové architektury, musíte celý proces bedlivě sledujte a snažte se zachovat aktivní instance DBMS a SAP Central services ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému kterou chcete Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejdříve
- Nástroje pro vyrovnávání zatížení Azure použít pro centrální služby SAP, stejně jako vrstva DBMS, clustery převzetí služeb při selhání musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux vytvořené sdílené složky NFS podle postupu uvedeného v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - V tomto okamžiku řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace
- Abyste mohli spustit aplikace prostředků v případě selhání zóny nasazení neaktivní virtuálních počítačů v pasivní zónu (z DBMS hlediska)
    - Nemůžete použít [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) pro replikaci virtuálních počítačů aktivní na neaktivní virtuální počítače mezi zónami. V tomto okamžiku není možné splnit takové funkce Azure Site Recovery
- Investovat do automatizaci, která umožňuje, v případě selhání zóny na automatické spuštění aplikační vrstvě SAP v druhé zóny

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinované vysoké dostupnosti a konfigurace zotavení po havárii
Navzdory tomu, že Microsoft neposkytuje žádné informace o geografické vzdálenosti mezi zařízení, které jsou hostiteli různých Azure zón dostupnosti v určité oblasti Azure, zákazníci, kteří využívají zóny pro kombinované konfigurace vysokou dostupnost a zotavení po Havárii, které slibuje **R**pro obnovení **P**SharePoint **O**bjective (RPO) nula. To znamená, že by neměla ztratí všechny transakce potvrzeny databáze i v případě zotavení po havárii. 

> [!NOTE]
> Konfigurace tímto způsobem se doporučuje pro pouze zvláštní okolnosti. Například případy, kdy data nemohou opustit oblast Azure z důvodů zabezpečení a dodržování předpisů. 

Na tomto obrázku je znázorněn příklad, jak taková konfigurace může vypadat:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Pro tuto konfiguraci, platí následující aspekty:

- Jsou buď za předpokladu, že je důležité vzdálenost mezi zařízení, který je hostitelem zóny dostupnosti. Nebo se musí Zůstaňte s přední určitá oblast Azure. Zóny dostupnosti Azure se nedá nasadit skupiny dostupnosti. Proto v tomto případě je jsou končí jednu doménu aktualizace a odolnost pro aplikační vrstvu. Důvodem je, že je pouze nasazené v jedné zóně. Toto je setback ve srovnání s referenční architektury, který předpokládá, že nasadíte aplikační vrstvu ve skupině dostupnosti Azure.
- Provozování takové architektury, musíte celý proces bedlivě sledujte a snažte se zachovat aktivní instance DBMS a SAP Central services ve stejné zóně jako nasazená aplikační vrstvu. V případě selhání centrální služby SAP nebo instance databázového systému kterou chcete Ujistěte se, že můžete ručně převzít zpět do zóny s aplikační vrstvě SAP nasazené co nejdříve
- Máte produkčních instancí aplikace předinstalované ve virtuálních počítačích, na kterých běží aktivní instance aplikace dotazů a odpovědí.
- V případě selhání oblastmi vypne instance aplikace QA a spouštěli produkční instance. Mějte na paměti, které potřebujete pro práci s virtuální názvy instancí aplikace, aby tuto práci
- Nástroje pro vyrovnávání zatížení Azure použít pro centrální služby SAP, stejně jako vrstva DBMS, clustery převzetí služeb při selhání musí být [nástroje pro vyrovnávání zatížení standardní SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Napříč zónami nebude fungovat load balanceru úrovně basic
- Virtuální síť Azure a její podsítě, které jste nasadili jako hostitele systému SAP jsou roztažená napříč zónami. Můžete **nepotřebujete** samostatné virtuální sítě pro každou zónu
- Pro všechny virtuální počítače nasadíte, budete muset použít [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nespravované disky nejsou podporovány pro zónové nasazení
- Azure Premium storage nebo [Ultra SSD úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nejsou podporovány libovolného typu replikace úložiště mezi zónami. V důsledku zodpovídá aplikace (systém DBMS nebo centrální služby SAP) k replikaci důležitá data
- Totéž platí pro adresář sdílené sapmnt, který je sdílený disk (Windows), sdílené složky CIFS (Windows) nebo sdílených složek NFS (Linux). Budete muset použít technologii, která replikuje sdíleného disku nebo sdílené složky mezi zóny. V tuto chvíli se podporují následující technologie:
    - Pro Windows, clusterové řešení, která používá SIOS Datakeeper, jak je uvedeno v [instanci SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) je podporována mezi zónami
    - SUSE Linux vytvořené sdílené složky NFS podle postupu uvedeného v [vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) je podporováno
    - V tomto okamžiku řešení pomocí služby Windows soubor horizontální navýšení kapacity (SOFS), jak je uvedeno v [infrastruktury Příprava Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **nepodporuje napříč zónami**
- Třetí zóny slouží jako hostitel SBD zařízení v případě, že vytvoříte [operačním systémem SUSE Linux pacemaker clusteru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) nebo instance další aplikace





## <a name="next-steps"></a>Další kroky
Zkontrolujte další kroky k nasazení napříč zónami dostupnosti Azure:

- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Příprava infrastruktury Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






