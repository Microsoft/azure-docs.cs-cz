---
title: Kontrolní seznam plánování úloh SAP a nasazení | Dokumenty společnosti Microsoft
description: Kontrolní seznam pro plánování nasazení úloh SAP do Azure a nasazení úloh
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060069"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Úlohy SAP v Azure: kontrolní seznam plánování a nasazení

Tento kontrolní seznam je určen pro zákazníky, kteří přesouvají aplikace SAP NetWeaver, S/4HANA a Hybris do infrastruktury Azure jako služby. Po celou dobu trvání projektu by měl zákazník a/nebo partner SAP zkontrolovat kontrolní seznam. Je důležité si uvědomit, že mnoho kontrol je dokončeno na začátku projektu a během fáze plánování. Po dokončení nasazení se můžou jednoduché změny nasazené infrastruktuře Azure nebo vydání softwaru SAP stát složitými.

Zkontrolujte kontrolní seznam na klíčových milníkech během projektu. To vám umožní zjistit malé problémy dříve, než se stanou velkými problémy. Budete mít také dostatek času na předělat a otestovat všechny potřebné změny. Nepovažujte tento kontrolní seznam za úplný. V závislosti na vaší situaci může být nutné provést mnohem více kontrol.

Kontrolní seznam neobsahuje úkoly, které jsou nezávislé na Azure. Například rozhraní aplikace SAP změnit během přechodu na platformu Azure nebo poskytovatele hostingu.

Tento kontrolní seznam lze také použít pro systémy, které jsou již nasazeny. Od nasazení mohly být přidány nové funkce, jako je akcelerátor zápisu a zóny dostupnosti a nové typy virtuálních počítačů. Proto je užitečné pravidelně kontrolovat kontrolní seznam, abyste se ujistili, že jste si vědomi nových funkcí v platformě Azure.

## <a name="project-preparation-and-planning-phase"></a>Fáze přípravy a plánování projektu
Během této fáze plánujete migraci úlohy SAP na platformu Azure. Minimálně během této fáze je třeba vytvořit následující dokumenty a definovat a diskutovat o následujících prvcích migrace:

1. Dokument návrhu na vysoké úrovni. Tento dokument by měl obsahovat:
    - Aktuální inventář komponent a aplikací SAP a inventář cílových aplikací pro Azure.
    - Matice přiřazení odpovědnosti (RACI), která definuje odpovědnosti a přiřazení zúčastněných stran. Začněte na vysoké úrovni a během plánování a prvních nasazení pracujte na podrobnějších úrovních.
    - Architektura řešení na vysoké úrovni.
    - Rozhodnutí o tom, do kterých oblastí Azure se má nasadit. Podívejte se na [seznam oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Informace o tom, které služby jsou v jednotlivých oblastech dostupné, naleznete v [tématu Produkty dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/).
    - Síťová architektura pro připojení z místního prostředí k Azure. Začněte se seznamovat s [podrobným plánem virtuálního datového centra pro Azure](https://docs.microsoft.com/azure/architecture/vdc/).
    - Zásady zabezpečení pro spouštění podnikových dat s velkým dopadem v Azure. Pokud se chcete dozvědět o zabezpečení dat, začněte s [dokumentací k zabezpečení Azure](https://docs.microsoft.com/azure/security/).
2.  Technický návrh dokumentu. Tento dokument by měl obsahovat:
    - Blokový diagram pro řešení.
    - Velikost výpočetních, úložných a síťových komponent v Azure. Velikost SAP virtuálních počítačů Azure najdete v tématu [poznámka podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533).
    - Architektura kontinuity provozu a zotavení po havárii.
    - Podrobné informace o verzích sady OS, DB, jádra a balíčku podpory SAP. Není nutně pravda, že každá verze operačního systému podporovaná SAP NetWeaver nebo S/4HANA je podporovaná na virtuálních počítačích Azure. Totéž platí pro verze DBMS. V následujících zdrojích zarovnejte a v případě potřeby upgradujte verze SAP, verze DBMS a verze operačního systému, abyste zajistili podporu SAP a Azure. Chcete-li získat plnou podporu od SAP a Microsoftu, musíte mít kombinace verzí podporované sapem a Azure. V případě potřeby je třeba naplánovat inovaci některých softwarových součástí. Další podrobnosti o podporovaném softwaru SAP, OS a DBMS jsou popsány zde:
        - [Poznámka podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tato poznámka definuje minimální verze operačního systému podporované na virtuálních počítačích Azure. Definuje také minimální verze databáze požadované pro většinu databází bez HANA. Nakonec poskytuje velikost SAP pro sap podporované typy virtuálních počítačů Azure.
        - [Poznámka podpory SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Tato poznámka definuje zásady podpory týkající se úložiště Azure a vztahu podpory potřebného s Microsoftem.
        - [Poznámka podpory SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Tato poznámka definuje matici podpory Oracle pro Azure. Oracle podporuje pouze Windows a Oracle Linux jako hostované operační systémy v azure pro úlohy SAP. Toto prohlášení podpory platí také pro aplikační vrstvu SAP, která spouští instance SAP. Společnost Oracle však nepodporuje vysokou dostupnost služeb SAP Central Services v systému Oracle Linux prostřednictvím pacemakeru. Pokud potřebujete vysokou dostupnost pro ASCS na Oracle Linux, musíte použít SIOS Protection Suite pro Linux. Podrobné údaje o certifikaci SAP najdete v tématu poznámka podpory SAP [#1662610 – podrobnosti o podpoře sady SIOS Protection Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610). V systému Windows je podporováno řešení clusteringu s podporou windows serveru pro převzetí služeb převzetí služeb při selhání pro sap central services ve spojení s vrstvou DBMS oracle.
        - [Poznámka podpory SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Tato poznámka obsahuje matici podpory pro SAP HANA v různých verzích operačního systému.
        - Sap HANA podporované virtuální počítače Azure a [velké instance HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jsou uvedeny na [webu SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Matice dostupnosti produktu SAP](https://support.sap.com/en/).
        - [Poznámka podpory SAP #2555629 – DYNAMICKÉ vrstvení SAP HANA 2.0 – hypervisor ová a cloudová podpora](https://launchpad.support.sap.com/#/notes/2555629)
        - [Poznámka podpory SAP #1662610 - Podrobnosti o podpoře pro SADU SIOS Protection Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - POZNÁMKY SAP pro jiné produkty specifické pro SAP.     
    - Pro výrobní systémy SAP doporučujeme přísné třístupňové návrhy. Nedoporučujeme kombinovat ASCS a/nebo DBMS a/nebo aplikační servery na jednom virtuálním počítači. V hostovaných operačních systémech Windows v Azure je podporováno použití konfigurací clusteru s více SID pro služby SAP Central Services. Ale tato konfigurace není podporována pro sap centrální služby v operačních systémech Linux v Azure. Dokumentaci k scénáři hostovaného operačního systému Windows najdete v těchto článcích:
        - [Sap ASCS/SCS instance multi-SID vysoká dostupnost s Windows Server převzetí služeb při selhání clustering a sdílený disk v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Sap ASCS/SCS instance multi-SID vysoká dostupnost s Windows Server převzetí služeb při selhání clustering a sdílení souborů v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Vysoká dostupnost a architektura zotavení po havárii.
        - Na základě RTO a RPO definujte, jak musí vypadat architektura vysoké dostupnosti a zotavení po havárii.
        - Pokud hledáte vysokou dostupnost v rámci zóny, zkontrolujte, co požadovaný systém DBMS nabízí v Azure. Většina balíčků DBMS nabízí synchronní metody synchronního pohotovostního režimu za tepla, který doporučujeme pro produkční systémy. Zkontrolujte také dokumentaci týkající se SAP pro různé databáze, počínaje [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a související dokumenty.
           Použití clusteringu s podporou převzetí služeb při selhání systému Windows Server s konfigurací sdíleného disku pro vrstvu DBMS, například [popsané pro SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017), není podporováno. Místo toho použijte řešení, jako jsou:
           - [AlwaysOn SQL Serveru](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [Replikace systému HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Pokud hledáte zotavení po havárii v různých oblastech Azure, projděte si řešení nabízená různými dodavateli DBMS. Většina z nich podporuje asynchronní replikaci nebo přesouvání protokolu.
        - Pro aplikační vrstvu SAP určete, jestli budete spouštět vaše obchodní regresní testovací systémy, které jsou v ideálním případě replikami vašich produkčních nasazení, ve stejné oblasti Azure nebo v oblasti zotavení po havárii. V druhém případě můžete cílit na tento obchodní regresní systém jako cíl zotavení po Havárii pro vaše produkční nasazení.
        - Pokud se rozhodnete neumisťovat neprodukční systémy v lokalitě zotavení po Havárii, podívejte se do Azure Site Recovery jako metodu pro replikaci aplikační vrstvy SAP do oblasti Azure ZOTAVENÍ. Další informace naleznete v tématu [Nastavení zotavení po havárii pro vícevrstvé nasazení aplikace SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap).
        - Pokud se rozhodnete použít kombinovanou konfiguraci HADR pomocí [zón dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview), seznamte se s oblastmi Azure, kde jsou k dispozici zóny dostupnosti. Také vzít v úvahu omezení, která mohou být zavedeny zvýšené latence sítě mezi dvěma zónami dostupnosti.  
3.  Soupis všech rozhraní SAP (SAP a non-SAP).
4.  Návrh nadačních služeb. Tento návrh by měl obsahovat následující položky:
    - Služba Active Directory a návrh DNS.
    - Topologie sítě v rámci Azure a přiřazení různých systémů SAP.
    - Struktura [přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) pro týmy, které spravují infrastrukturu a aplikace SAP v Azure.
    - Topologie skupiny prostředků.
    - [Strategie označování](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - Konvence pojmenování pro virtuální servery a další součásti infrastruktury a logické názvy.
5.  Smlouva o podpoře Microsoft Premier. Identifikujte svého technického správce účtu Microsoft (TAM). Požadavky na podporu SAP najdete v [tématu poznámka podpory SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Počet předplatných Azure a základní kvóta pro předplatná. [Otevřete žádosti o podporu a podle potřeby zvyšte kvóty předplatných Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request)
7.  Plán pro redukci dat a migraci dat pro migraci dat SAP do Azure. Pro systémy SAP NetWeaver má SAP pokyny, jak omezit objem velkého množství dat. Podívejte se na [tento průvodce SAP](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) o správě dat v systémech SAP ERP. Část obsahu se vztahuje také na systémy NetWeaver a S/4HANA obecně.
8.  Automatizovaný přístup k nasazení. Cílem automatizace nasazení infrastruktury v Azure je nasadit deterministickým způsobem a získat deterministické výsledky. Mnoho zákazníků používá skripty založené na powershellu nebo cli. Existují však různé technologie s otevřeným zdrojovým kódem, které můžete použít k nasazení infrastruktury Azure pro SAP a dokonce k instalaci softwaru SAP. Příklady na Jdete na GitHubu:
    - [Automatizovaná nasazení SAP v Azure Cloudu](https://github.com/Azure/sap-hana)
    - [Instalace SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definujte pravidelnou kadenci kontrol návrhu a nasazení mezi vámi jako zákazníkem, systémovým integrátorem, společností Microsoft a dalšími zúčastněnými stranami.

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotní fáze (důrazně doporučujeme)
 
Pilotní projekt můžete spustit před plánováním a přípravou projektu nebo během jeho přípravy. Pilotní fázi můžete také použít k testování přístupů a návrhů provedených během fáze plánování a přípravy. A můžete rozšířit pilotní fázi, aby to skutečný důkaz koncepce.

Doporučujeme nastavit a ověřit úplné řešení HADR a návrh zabezpečení během pilotního nasazení. Někteří zákazníci provádět testy škálovatelnosti během této fáze. Ostatní zákazníci používají nasazení systémů sap sandbox jako pilotní fáze. Předpokládáme, že jste již identifikovali systém, který chcete pro pilota migrovat do Azure.

1. Optimalizujte přenos dat do Azure. Optimální volba je vysoce závislá na konkrétním scénáři. Přenos z místního prostředí přes [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) je nejrychlejší, pokud má okruh ExpressRoute dostatečnou šířku pásma. V jiných scénářích je přenos přes internet rychlejší.
2. Pro heterogenní migraci platformy SAP, která zahrnuje export a import dat, otestujte a optimalizujte fáze exportu a importu. Pro velké migrace, ve kterých SQL Server je cílová platforma, můžete najít [doporučení](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Pokud nepotřebujete kombinovaný upgrade verze, můžete použít nástroj Sledování migrace/SWPM. Proces SAP [DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) můžete použít při kombinování migrace s upgradem verze SAP. Chcete-li tak učinit, musíte splnit určité požadavky pro kombinaci zdrojové a cílové platformy DBMS. Tento proces je dokumentován v [možnosti migrace databáze (DMO) sumy 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportujte do zdroje, exportujte nahrávání souborů do Azure a importujte výkon. Maximalizujte překrytí mezi exportem a importem.
   2.  Vyhodnoťte objem databáze na cílové a cílové platformy pro účely velikosti infrastruktury.
   3.  Ověřte a optimalizujte časování.
1. Technická validace.
   1. Typy virtuálních počítačů.
        - Zkontrolujte prostředky v poznámkách podpory SAP, v adresáři hardwaru SAP HANA a v SAP PAM znovu. Ujistěte se, že neexistují žádné změny podporovaných virtuálních počítačů pro Azure, podporované verze operačního systému pro tyto typy virtuálních počítačů a podporované verze SAP a DBMS.
        - Ověřte znovu velikost vaší aplikace a infrastruktury, kterou nasadíte v Azure. Pokud přesouváte stávající aplikace, můžete často odvodit potřebný SAPS z infrastruktury, kterou používáte, a [srovnávací webové stránky SAP](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) a porovnat je s čísly SAPS uvedenými v [poznámce podpory SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Mějte také na paměti [tento článek o hodnocení SAPS.](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)
        - Vyhodnoťte a otestujte velikost virtuálních počítačů Azure s ohledem na maximální propustnost úložiště a propustnost sítě typů virtuálních počítačů, které jste zvolili během fáze plánování. Údaje naleznete zde:
           -  [Velikosti pro virtuální počítače s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Je důležité vzít v úvahu *maximální propustnost disku bez mezipaměti* pro velikost.
           -  [Velikosti pro virtuální počítače s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Je důležité vzít v úvahu *maximální propustnost disku bez mezipaměti* pro velikost.
   2. Úložiště.
        - Minimálně použijte [azure standard ssd úložiště](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) pro virtuální počítače, které představují vrstvy aplikací SAP a pro nasazení DBMS, které nejsou citlivé na výkon.
        - Obecně nedoporučujeme používat [disky Azure Standard HDD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd).
        - Azure [Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) použijte pro všechny virtuální počítače DBMS, které jsou vzdálené na výkonu.
        - Používejte [disky spravované azure](https://azure.microsoft.com/services/managed-disks/).
        - Použijte Azure Write Accelerator pro jednotky protokolu DBMS s m-series. Uvědomte si omezení a využití akcelerátoru zápisu, jak je popsáno v [akcelerátoru zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator).
        - Pro různé typy DBMS zkontrolujte [obecnou dokumentaci SAP související s DBMS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a dokumentaci specifickou pro DBMS, na kterou odkazuje obecný dokument.
        - Další informace o SAP HANA najdete v [tématu KONFIGURACE A Provoz infrastruktury SAP HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations).
        - Nikdy nepřipojujte datové disky Azure na virtuální počítač Azure Linux pomocí ID zařízení. Místo toho použijte univerzálně jedinečný identifikátor (UUID). Buďte opatrní při použití grafických nástrojů pro připojení datových disků Azure, například. Zkontrolujte položky v /etc/fstab a ujistěte se, že UUID se používá k připojení disků. Další podrobnosti naleznete v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Sítí.
        - Otestujte a vyhodnoťte infrastrukturu virtuálních sítí a distribuci aplikací SAP napříč různými virtuálními sítěmi Azure nebo v jejich rámci.
        -  Vyhodnoťte přístup architektury virtuální sítě hub a spoke nebo přístup mikrosegmentace v rámci jedné virtuální sítě Azure. Toto hodnocení založíme na:
               1. Náklady na výměnu dat mezi [partnerskými virtuálními sítěmi Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Informace o nákladech naleznete v [tématu Ceny virtuální sítě](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Výhody rychlého odpojení partnerského vztahu mezi virtuálními sítěmi Azure na rozdíl od změny skupiny zabezpečení sítě izolovat podsítě v rámci virtuální sítě. Toto vyhodnocení je pro případy, kdy se aplikace nebo virtuální počítače hostované v podsíti virtuální sítě staly bezpečnostním rizikem.
                3. Centrální protokolování a auditování síťového provozu mezi místním, vnějším světem a virtuálním datovým centrem, které jste vytvořili v Azure.
        - Vyhodnoťte a otestujte datovou cestu mezi aplikační vrstvou SAP a vrstvou SAP DBMS.
            -  Umístění [virtuálních zařízení sítě Azure](https://azure.microsoft.com/solutions/network-appliances/) v komunikační cestě mezi aplikací SAP a vrstvou DBMS systémů SAP založených na SAP NetWeaver, Hybris nebo S/4HANA není podporováno.
            -  Umístění aplikační vrstvy SAP a SAP DBMS v různých virtuálních sítích Azure, které nejsou peered není podporována.
            -  Pomocí [pravidel skupiny zabezpečení aplikací a skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview) můžete definovat trasy mezi aplikační vrstvou SAP a vrstvou SAP DBMS.
        - Ujistěte se, že [azure accelerated networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolená na virtuálních počítačích používaných v aplikační vrstvě SAP a vrstvě SAP DBMS. Nezapomeňte, že pro podporu akcelerovaných sítí v Azure jsou potřeba různé úrovně operačního režimu:
            - Windows Server 2012 R2 nebo novější
            - SUSE Linux 12 SP3 nebo novější.
            - RHEL 7.4 nebo novější.
            - Oracle Linux 7.5. Pokud používáte jádro RHCKL, je vyžadována verze 3.10.0-862.13.1.el7. Pokud používáte jádro Oracle UEK, je vyžadována verze 5.
        - Otestujte a vyhodnoťte latenci sítě mezi virtuálními servery aplikační vrstvy SAP a virtuálními servery DBMS podle poznámek podpory SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky podle pokynů k latenci sítě v [poznámce podpory SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky platí pro provoz mezi virtuálními aplikacemi a jednotkami velké instance HANA, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
        - Ujistěte se, že nasazení ILB jsou nastavena na použití direct server return. Toto nastavení sníží latenci při azure ilbs se používají pro konfiguraci s vysokou dostupností na vrstvě DBMS.
        - Pokud používáte Azure Load Balancer společně s linuxovými hostovacími operačními systémy, zkontrolujte, jestli je parametr sítě linuxové **net.ipv4.tcp_timestamps** nastavený na **0**. Toto doporučení je v konfliktu s doporučeními ve starších verzích [poznámky SAP #2382421](https://launchpad.support.sap.com/#/notes/2382421). Poznámka SAP je nyní aktualizována, aby bylo uvedeno, že tento parametr musí být nastaven na **hodnotu 0** pro práci s vykladači zatížení Azure.
        - Zvažte použití [skupin umístění bezkontaktní komunikace Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) k získání optimální latence sítě. Další informace najdete v [tématu skupiny umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
   4. Vysoká dostupnost a nasazení zotavení po havárii.
        - Pokud nasadíte aplikační vrstvu SAP bez definování konkrétní zóny dostupnosti Azure, ujistěte se, že všechny virtuální počítače, které používají instance dialogových oken SAP nebo instance middlewaru jednoho systému SAP, jsou nasazeny v [sadě dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability).
        - Pokud nepotřebujete vysokou dostupnost pro centrální služby SAP a DBMS, můžete tyto virtuální uživatele nasadit do stejné sady dostupnosti jako aplikační vrstva SAP.
        - Pokud chráníte centrální služby SAP a vrstvu DBMS pro vysokou dostupnost pomocí pasivní replikace, umístěte dva uzly pro centrální služby SAP do jedné samostatné skupiny dostupnosti a dva uzly DBMS do jiné skupiny dostupnosti.
        - Pokud nasadíte do zón dostupnosti Azure, nemůžete používat sady dostupnosti. Ale musíte se ujistit, že nasazení aktivní a pasivní uzly centrální služby do dvou různých zón dostupnosti. Použijte zóny dostupnosti, které mají nejnižší latenci mezi nimi.
          Mějte na paměti, že musíte použít [Azure Standard Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) pro případ použití vytvoření Windows nebo Pacemaker clustery s podporou převzetí služeb při selhání pro vrstvu DBMS a SAP centrální služby napříč zónami dostupnosti. Základní [vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nelze použít pro zonální nasazení.
   5. Nastavení časového času.
        - Zkontrolujte, zda sledování vývojáře SAP NetWeaver instancemi SAP a ujistěte se, že neexistují žádné přerušení připojení mezi serverem fronty a pracovními procesy SAP. Těmto přerušením připojení se můžete vyhnout nastavením těchto dvou parametrů registru:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Další informace naleznete v tématu [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Další informace naleznete v tématu [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Chcete-li se vyhnout časovým limitům grafického uživatelského rozhraní mezi místními rozhraními SAP GUI a aplikačními vrstvami SAP nasazenými v Azure, zkontrolujte, jestli jsou tyto parametry nastaveny ve default.pfl nebo v profilu instance:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Chcete-li zabránit narušení navázána připojení mezi procesem fronty SAP a pracovní procesy SAP, je třeba nastavit **enque/encni/set_so_keepalive** parametr **true**. Viz také [poznámka SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Pokud používáte konfiguraci clusteru s podporou převzetí služeb při selhání systému Windows, ujistěte se, že čas reagovat na uzlech, které nereagují, je správně nastaven pro Azure. V článku [Optimalizace prahových hodnot sítě clusteru s podporou převzetí služeb při selhání](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) uvádí parametry a jejich vliv na citlivost převzetí služeb při selhání. Za předpokladu, že uzly clusteru jsou ve stejné podsíti, měli byste změnit tyto parametry:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - Délka historie směrování = 30
    6. Nastavení operačního ses nebo záplaty
        - Pro spuštění HANA na SAP, přečtěte si tyto poznámky a dokumentace:
            -   [Poznámka podpory SAP #2814271 – zálohování SAP HANA se nezdaří v Azure s chybou kontrolního součtu](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Poznámka podpory SAP #2753418 - potenciální snížení výkonu z důvodu záložního časovače](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Poznámka k podpoře SAP #2791572 – snížení výkonu z důvodu chybějící podpory VDSO pro technologie Hyper-V v Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Poznámka podpory SAP #2382421 – optimalizace konfigurace sítě na úrovni HANA a OS](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Poznámka podpory SAP #2694118 – doplněk Red Hat Enterprise Linux HA HA v Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Poznámka podpory SAP #1984787 - SUSE LINUX Enterprise Server 12: Poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Poznámka podpory SAP #2002167 – Red Hat Enterprise Linux 7.x: Instalace a upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Poznámka podpory SAP #2292690 - SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Poznámka podpory SAP #2772999 - Red Hat Enterprise Linux 8.x: Instalace a konfigurace](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Poznámka podpory SAP #2777782 - SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Poznámka podpory SAP #2578899 - SUSE Linux Enterprise Server 15: Poznámka k instalaci](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Poznámka podpory SAP #2455582 - Linux: Spuštění sap aplikací sestavených s GCC 6.x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Poznámka podpory SAP #2729475 – HWCCT se nezdařil o chybu "Hypervisor není podporován" na virtuálních počítačích Azure certifikovaných pro SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Otestujte vysokou dostupnost a postupy zotavení po havárii.
   1. Simulujte situace převzetí služeb při selhání vypnutím virtuálních počítačů (hostovací operační systémy Windows) nebo uvedením operačních systémů do nouzového režimu (linuxové hostované operační systémy). Tento krok vám pomůže zjistit, zda konfigurace převzetí služeb při selhání fungují tak, jak byly navrženy.
   1. Změřte, jak dlouho trvá provedení převzetí služeb při selhání. Pokud jsou časy příliš dlouhé, zvažte:
        - Pro SUSE Linux použijte zařízení SBD namísto agenta Azure Fence k urychlení převzetí služeb při selhání.
        - Pro SAP HANA, pokud opětovné načtení dat trvá příliš dlouho, zvažte zřizování větší šířku pásma úložiště.
   3. Otestujte posloupnost zálohování/obnovení a časování a proveďte opravy, pokud potřebujete. Ujistěte se, že jsou dostatečné doby zálohování. Je také třeba otestovat aktivity obnovení a obnovení času. Ujistěte se, že časy obnovení jsou v rámci rto sla všude tam, kde vaše RTO spoléhá na databázi nebo proces obnovení virtuálního počítače.
   4. Otestujte funkce a architekturu zotavení po havárii mezi oblastmi.
1. Bezpečnostní kontroly.
   1. Otestujte platnost architektury řízení přístupu na základě rolí Azure (RBAC). Cílem je oddělit a omezit přístup a oprávnění různých týmů. Například členové týmu SAP Basis by měli být schopni nasadit virtuální počítače a přiřadit disky z Azure Storage do dané virtuální sítě Azure. Ale sap základ tým by neměl být schopen vytvořit vlastní virtuální sítě nebo změnit nastavení stávajících virtuálních sítí. Členové síťového týmu by neměli být schopni nasazovat virtuální počítače do virtuálních sítí, ve kterých jsou spuštěny aplikace SAP a virtuální počítače DBMS. Ani členové tohoto týmu by měli být schopni změnit atributy virtuálních počítačů nebo dokonce odstranit virtuální počítače nebo disky.  
   1.  Ověřte, zda skupina zabezpečení sítě a pravidla [asc](https://docs.microsoft.com/azure/virtual-network/security-overview) fungují podle očekávání a chrání chráněné prostředky.
   1.  Ujistěte se, že všechny prostředky, které je třeba zašifrovat, jsou šifrovány. Definujte a implementujte procesy pro zálohování certifikátů, ukládání a přístup k těmto certifikátům a obnovení šifrovaných entit.
   1.  Pokud je to možné, použijte [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) pro disky operačního systému, kde je to možné z hlediska podpory operačního systému.
   1.  Ujistěte se, že nepoužíváte příliš mnoho vrstev šifrování. V některých případech má smysl používat Azure Disk Encryption spolu s jednou z metod DBMS Transparentní šifrování dat k ochraně různých disků nebo součástí na stejném serveru.  Například na serveru SAP DBMS lze povoleno šifrování disku Azure (ADE) na spouštěcí diskese operačního systému (pokud operační systém podporuje ADE) a na těchto datových diskech, které nejsou používány soubory trvalost dat DBMS.  Příkladem je použití ade na disku, který je držitelem šifrovacích klíčů DBMS TDE.
1. Testování výkonu. V SAP, na základě sap trasování a měření, provést tato porovnání:
   - Pokud je to možné, porovnejte 10 nejlepších online reportů s aktuální implementací.
   - Pokud je to možné, porovnejte prvních 10 dávkových úloh s aktuální implementací.
   - Porovnejte přenosy dat prostřednictvím rozhraní do systému SAP. Zaměřte se na rozhraní, kde víte, že přenos probíhá mezi různými umístěními, jako je místní do Azure.


## <a name="non-production-phase"></a>Nevýrobní fáze 
V této fázi předpokládáme, že po úspěšném pilotním projektu nebo dokladu koncepce (POC) začínáte nasazovat neprodukční systémy SAP do Azure. Do tohoto nasazení začleňte vše, co jste se naučili a zažili během poc. Pro toto nasazení platí také všechna kritéria a kroky uvedené pro poc.

Během této fáze obvykle nasazujete vývojové systémy, systémy testování částí a systémy testování obchodní regrese do Azure. Doporučujeme, aby alespoň jeden neprodukční systém v jedné aplikační lince SAP měl úplnou konfiguraci s vysokou dostupností, kterou bude mít budoucí produkční systém. Zde jsou některé další kroky, které je třeba provést během této fáze:  

1.  Než přesunete systémy ze staré platformy do Azure, shromažďujte data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a data VOPS. Zejména shromažďovat tato data z jednotek vrstvy DBMS, ale také shromažďovat z jednotek aplikační vrstvy. Měřte také latenci sítě a úložiště.
2.  Zaznamenejte vzorce dostupnosti vašich systémů. Cílem je zjistit, zda nevýrobní systémy musí být k dispozici po celý den, každý den, nebo zda existují nevýrobní systémy, které lze vypnout během určitých fází v týdnu nebo měsíci.
3.  Otestujte a zjistěte, jestli chcete vytvořit vlastní image operačního systému pro virtuální počítače v Azure nebo jestli chcete použít image z Galerie sdílených bitových kopií Azure. Pokud používáte obrázek z Galerie sdílených obrázků, nezapomeňte použít obrázek, který odráží smlouvu o podpoře s dodavatelem operačního systému. U některých dodavatelů operačního systému umožňuje galerie sdílených obrázků přinést si vlastní licenční obrázky. U ostatních ibi operačního systému je podpora zahrnuta v ceně citované Azure. Pokud se rozhodnete vytvořit vlastní obrázky operačního systému, najdete dokumentaci v těchto článcích:
    -   [Vytvoření zobecněné image virtuálního počítače s Windows nasazeného v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Vytvoření zobecněné image virtuálního počítače s Linuxem nasazeného v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Pokud používáte obrázky SUSE a Red Hat Linux z Galerie sdílených obrázků, musíte použít obrázky pro SAP poskytované dodavateli Linuxu v galerii sdílených obrázků.
4.  Ujistěte se, že splňujete požadavky na podporu SAP pro smlouvy podpory společnosti Microsoft. Viz [poznámka podpory SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Velké instance HANA naleznete v tématu [Požadavky na připojení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements).
4.  Ujistěte se, že ti praví lidé dostanou [oznámení o plánované údržbě,](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) abyste si mohli vybrat ty nejlepší prostoje.
5.  Často kontrolujte, zda prezentace Azure na kanálech, jako [je Channel 9,](https://channel9.msdn.com/) hledají nové funkce, které se mohou vztahovat na vaše nasazení.
6.  Zkontrolujte poznámky SAP týkající se Azure, jako [je poznámka podpory #1928533](https://launchpad.support.sap.com/#/notes/1928533), pro nové virtuální počítače a nově podporované verze operačního systému a DBMS. Porovnejte ceny nových typů virtuálních počítačů oproti starším typům virtuálních počítačů, takže můžete nasadit virtuální počítače s nejlepším poměrem cena/výkon.
7.  Znovu zkontrolujte poznámky podpory SAP, hardwarový adresář SAP HANA a SAP PAM. Ujistěte se, že nedošlo k žádným změnám ve podporovaných virtuálních počítačích pro Azure, podporovaných verzích operačního systému na těchto virtuálních počítačích a podporovaných verzísap a DBMS.
8.  Na [webu SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) najdete nové skum s certifikací HANA v Azure. Porovnejte ceny nových skusy s těmi, které jste plánovali použít. Nakonec proveďte nezbytné změny, abyste mohli použít ty, které mají nejlepší poměr cena / výkon.
9.  Přizpůsobte si skripty nasazení tak, aby používaly nové typy virtuálních počítačů, a zahrnujte nové funkce Azure, které chcete použít.
10. Po nasazení infrastruktury otestujte a vyhodnoťte latenci sítě mezi virtuálními počítačemi aplikační vrstvy SAP a virtuálními servery DBMS podle poznámek podpory SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky podle pokynů k latenci sítě v [poznámce podpory SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky platí pro provoz mezi virtuálními aplikacemi a jednotkami velké instance HANA, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Ujistěte se, že žádné z omezení uvedených v [aspekty pro nasazení AZURE virtuálnípočítače DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) a [SAP HANA konfigurace infrastruktury a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se vztahují na vaše nasazení.
11. Ujistěte se, že vaše virtuální počítače jsou nasazené do správné [skupiny umístění azure blízkosti](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), jak je popsáno ve [skupinách umístění bezkontaktní azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
11. Před použitím úlohy proveďte všechny ostatní kontroly uvedené pro fázi ověření koncepce.
12. Jak se použije zatížení, zaznamenejte spotřebu prostředků systémů v Azure. Porovnejte tuto spotřebu se záznamy ze staré platformy. Upravte velikost virtuálních počítače budoucích nasazení, pokud zjistíte, že máte velké rozdíly. Mějte na paměti, že když zmenšíte velikost, úložiště a šířku pásma sítě virtuálních počítače se také sníží.
    - [Velikosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Velikosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimentujte s funkcemi a procesy kopírování systému. Cílem je usnadnit kopírování vývojového systému nebo testovacího systému, aby projektové týmy mohly rychle získat nové systémy. Zvažte použití [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) pro tyto úkoly.
14. Optimalizujte a zdokonalujte přístup, oprávnění a procesy založené na rolích Azure vašeho týmu, abyste měli jistotu, že máte oddělení povinností. Současně se ujistěte, že všechny týmy můžou provádět své úkoly v infrastruktuře Azure.
15. Cvičení, testování a dokumentování postupů vysoké dostupnosti a zotavení po havárii, které vašim zaměstnancům umožní tyto úkoly provádět. Identifikujte nedostatky a přizpůsobte nové funkce Azure, které integrujete do svých nasazení.

 
## <a name="production-preparation-phase"></a>Fáze přípravy výroby 
V této fázi shromážděte, co jste zažili a naučili během neprodukčních nasazení, a použijte je pro budoucí produkční nasazení. Je také potřeba připravit práci přenosu dat mezi aktuální umístění hostingu a Azure.

1.  Před přechodem do Azure dokončete nezbytné upgrady verzí SAP ve vašich produkčních systémech.
1.  Dohodněte se s vlastníky podniků na funkčních a obchodních testech, které je třeba provést po migraci výrobního systému.
1.  Ujistěte se, že tyto testy jsou dokončeny se zdrojovými systémy v aktuálním umístění hostování. Vyhněte se provádění testů poprvé po přesunutí systému do Azure.
1.  Otestujte proces migrace produkčních systémů do Azure. Pokud nepřesouváte všechny produkční systémy do Azure ve stejném časovém rámci, vytvořte skupiny produkčních systémů, které musí být ve stejném hostitelském umístění. Testování migrace dat. Zde jsou některé běžné metody:
    - Používejte metody DBMS, jako je zálohování a obnovení v kombinaci s SQL Server always on, HANA system replication nebo log shipping na osiva a synchronizaci obsahu databáze v Azure.
    - Zálohování a obnovení použijte pro menší databáze.
    - Pomocí programu SAP Migration Monitor, který je integrován do SAP SWPM, můžete provádět heterogenní migrace.
    - Pokud potřebujete zkombinovat migraci s upgradem verze SAP, použijte proces [SAP DMO.](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) Mějte na paměti, že nejsou podporovány všechny kombinace zdrojového systému DBMS a cílového systému DBMS. Další informace naleznete v konkrétních poznámkách podpory SAP pro různé verze DMO. Například [možnost migrace databáze (DMO) sumy 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Otestujte, zda je propustnost přenosu dat lepší přes internet nebo expressroute, v případě, že potřebujete přesunout zálohy nebo exportní soubory SAP. Pokud přesouváte data přes internet, možná budete muset změnit některá pravidla skupiny zabezpečení sítě nebo skupiny zabezpečení aplikací, která budete muset mít pro budoucí produkční systémy.
1.  Než přesunete systémy ze staré platformy do Azure, shromažďujte data o spotřebě prostředků. Užitečná data zahrnují využití procesoru, propustnost úložiště a data VOPS. Zejména shromažďovat tato data z jednotek vrstvy DBMS, ale také shromažďovat z jednotek aplikační vrstvy. Měřte také latenci sítě a úložiště.
1.  Znovu zkontrolujte poznámky podpory SAP a požadovaná nastavení operačního systému, hardwarový adresář SAP HANA a SAP PAM. Ujistěte se, že nedošlo k žádným změnám ve podporovaných virtuálních počítačích pro Azure, podporovaných verzích operačního systému v těchto virtuálních počítačích a podporovaných verzísap a DBMS.
1.  Aktualizujte skripty nasazení tak, aby zohledňovala nejnovější rozhodnutí, která jste udělali u typů virtuálních počítačů a funkcí Azure.
1.  Po nasazení infrastruktury a aplikací ověřte, že:
    - Byly nasazeny správné typy virtuálních počítačů se správnými atributy a velikostmi úložiště.
    - Virtuální počítač je na správné a požadované verze operačního systému a záplaty a jsou jednotné.
    - Virtuální ho disponizuje podle potřeby a jednotným způsobem.
    - Byly nainstalovány a nasazeny správné verze aplikací a oprav.
    - Virtuální počítače byly nasazené do sad dostupnosti Azure podle plánu.
    - Azure Premium Storage se používá pro disky citlivé na latenci nebo kde je [vyžadována sla s jedním virtuálním počítačem ve výši 99,9 %.](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)
    - Akcelerátor zápisu Azure je nasazený správně.
        - Ujistěte se, že v rámci virtuálních počítačů, prostory úložiště nebo prokládané sady byly sestaveny správně na discích, které potřebují akcelerátor zápisu.
        - Zkontrolujte [konfiguraci softwaru RAID na Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid).
        - Zkontrolujte [konfiguraci LVM na virtuálních počítačích S IP v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm).
    - [Spravované disky Azure](https://azure.microsoft.com/services/managed-disks/) se používají výhradně.
    - Virtuální virtuální měna byla nasazená do správných sad dostupnosti a zón dostupnosti.
    - [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolený na virtuálních počítačích používaných v aplikační vrstvě SAP a vrstvě SAP DBMS.
    - Žádná [virtuální zařízení sítě Azure](https://azure.microsoft.com/solutions/network-appliances/) nejsou v komunikační cestě mezi aplikací SAP a vrstvou DBMS systémů SAP založených na SAP NetWeaver, Hybris nebo S/4HANA.
    - Pravidla skupiny zabezpečení aplikací a skupiny zabezpečení sítě umožňují požadovanou a plánovanou komunikaci a v případě potřeby blokuje komunikaci.
    - Nastavení časového času jsou nastaveny správně, jak je popsáno výše.
    - Virtuální počítače se nasazují do správné [skupiny umístění bezkontaktní komunikace Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), jak je popsáno ve [skupinách umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
    - Latence sítě mezi virtuálními servery aplikační vrstvy SAP a virtuálními virtuálními servery DBMS se testuje a ověřuje, jak je popsáno v poznámkách podpory SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky podle pokynů k latenci sítě v [poznámce podpory SAP #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky platí pro provoz mezi virtuálními aplikacemi a jednotkami velké instance HANA, jak je popsáno v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance).
    - Šifrování bylo implementováno tam, kde je to nutné, a s příslušnou metodou šifrování.
    - Rozhraní a další aplikace mohou připojit nově nasazenou infrastrukturu.
1.  Vytvořte playbook pro reakci na plánovanou údržbu Azure. Určete pořadí, ve kterém by měly být restartovány systémy a virtuální počítače pro plánovanou údržbu.
    

## <a name="go-live-phase"></a>Fáze go-live
Během fáze živého vysílání se ujistěte, že jste sledovali playbooky, které jste vyvinuli v dřívějších fázích. Proveďte kroky, které jste testovali a cvičili. Nepřijímejte změny v konfiguracích a procesech na poslední chvíli. Proveďte také tyto kroky:

1. Ověřte, že monitorování portálu Azure a další nástroje pro monitorování fungují. Doporučujeme Windows Performance Monitor (perfmon) pro Windows a SAR pro Linux.
    - Čítače procesoru.
        - Průměrný čas procesoru, celkový (všechny procesory)
        - Průměrná doba procesoru, každý jednotlivý procesor (128 procesorů na virtuálních počítačích M128)
        - Čas jádra CPU, každý jednotlivý procesor
        - Čas uživatele procesoru, každý jednotlivý procesor
    - Memory:
        - Volná paměť
        - Paměťová stránka za sekundu
        - Paměťová stránka out/second
    - Disku.
        - Čtení disku v kb/s na jednotlivý disk
        - Čtení disku za sekundu na jednotlivý disk
        - Čtení disku v mikrosekundách/čtení, na jednotlivý disk
        - Zápis disku v kb/s na jednotlivý disk
        - Zápis disku za sekundu na jednotlivý disk
        - Zápis disku v mikrosekundách/čtení, na jednotlivý disk
    - Síťové.
        - Síťové pakety za sekundu
        - Síťové pakety out/second
        - Síť KB za sekundu
        - Síť KB ven/sekunda
1.  Po migraci dat proveďte všechny ověřovací testy, na kterých jste se dohodli s vlastníky firem. Přijměte výsledky ověřovacích testů pouze v případě, že máte výsledky pro původní zdrojové systémy.
1.  Zkontrolujte, zda rozhraní fungují a zda ostatní aplikace mohou komunikovat s nově nasazenými produkčními systémy.
1.  Zkontrolujte systém přenosu a opravy prostřednictvím SAP transakce STMS.
1.  Po uvolnění systému pro produkční prostředí proveďte zálohování databáze.
1.  Po uvolnění systému pro produkční prostředí proveďte zálohování virtuálních počítače pro virtuální počítače aplikační vrstvy SAP.
1.  Pro systémy SAP, které nebyly součástí aktuální fáze go-live, ale které komunikují se systémy SAP, které jste přesunuli do Azure během této fáze go-live, je třeba obnovit vyrovnávací paměť názvu hostitele v SM51. Tím odeberete staré ip adresy uložené v mezipaměti přidružené k názvům instancí aplikace, které jste přesunuli do Azure.  


## <a name="post-production"></a>Postprodukce
Tato fáze je o monitorování, provozu a správě systému. Z hlediska SAP platí obvyklé úkoly, které jste museli dokončit ve starém hostitelském umístění. Dokončete také tyto úkoly specifické pro Azure:

1. Zkontrolujte faktury Azure pro systémy s vysokými poplatky.
2. Optimalizujte efektivitu ceny a výkonu na straně virtuálního počítači a na straně úložiště.
3. Optimalizujte časy, kdy můžete vypnout systémy.  


## <a name="next-steps"></a>Další kroky
Přečtěte si tyto články:

- [Plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Důležité informace o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

