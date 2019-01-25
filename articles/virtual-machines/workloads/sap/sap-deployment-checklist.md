---
title: SAP úlohy plánování a nasazení kontrolního seznamu | Dokumentace Microsoftu
description: kontrolní seznam pro plánování a nasazení úloh SAP v Azure
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/24/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 67083a8214724659765922047c1f0ccd6da87b9d
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884924"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Úloh SAP v Azure kontrolní seznam plánování a nasazení 

Tento kontrolní seznam je určená pro zákazníky, kteří přecházejí jejich SAP NetWeaver, S/4HANA a aplikací Hybris do Azure infrastruktury jako služby.  Tento kontrolní seznam byste měli zkontrolovat zákazník nebo SAP partner po dobu trvání projektu. Je důležité, že mnoho kontroly jsou prováděny na začátku projektu a ve fázi plánování. Po dokončení nasazení se může stát složité základní změny na nasazenou infrastrukturu Azure nebo vydaných verzí softwaru SAP. Přečtěte si tento kontrolní seznam na klíčové milníky v průběhu projektu.  Malé potíže lze zjistit, než začnou způsobovat problémy velké a existuje dostatek času k znovu analyzovat a testovat všechny potřebné změny. Kontrolní seznam žádné způsoby deklarace na dokončení. Závisí na vaší individuální situaci, může existovat mnoho další kontroly, které je potřeba provést. 

Kontrolní seznam sestavených nezahrnuje úlohy, které jsou nezávislé na platformě Azure.  Příklad: Při přesunu do veřejného cloudu Azure nebo k poskytovateli hostingu SAP změn rozhraní aplikace.    

Tento kontrolní seznam můžete také používá pro již nasazenou systémy. Nové funkce, například akcelerátorem zápisu, zóny dostupnosti a nové typy virtuálních počítačů může byly přidány od jste nasadili.  Je proto užitečné k použití kontrolního seznamu pravidelně zajistit, že si uvědomujete nových funkcí na platformě Azure. 

## <a name="project-preparation-and-planning-phase"></a>Příprava projektu a plánovací fáze
V této fázi je naplánovaná migrace úloh SAP do veřejného cloudu Azure. Minimální sada entit a položek definovaných seznam podobný a popsány:

1. Podrobný návrh dokumentu – tento dokument by měl obsahovat:
    1. Aktuální seznam aplikací a komponenty SAP a cílového inventář aplikací v Azure
    2. Vytváření a práci odpovědnost přiřazení matice (RACI), která definuje odpovědnosti a přiřazení různé zúčastněné strany. Spusťte na vysoké úrovni a pracujeme, abychom čím dál tím víc podrobné úrovně propustnosti první a plánování nasazení
    2. Architektura vysoké úrovně řešení
    3. Rozhodnutí o oblasti Azure, které chcete nasadit do. Seznam oblastí Azure, zkontrolujte, [oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Služby k dispozici ve všech oblastech Azure, najdete v článku [produkty, které jsou k dispozici v jedné oblasti](https://azure.microsoft.com/global-infrastructure/services/)
    4. Síťová architektura pro připojení z místního do Azure. Začátkem sami znát [plán virtuální datové centrum Azure](https://docs.microsoft.com/azure/architecture/vdc/)
    5. Zásady zabezpečení pro spuštění vysoký obchodní dopad dat v Azure. Pro čtení materiálu začínat [dokumentace ke službě Azure Security](https://docs.microsoft.com/azure/security/)
2.  Technický návrh dokument –, který obsahuje:
    1.  Blokový diagram řešení 
    2.  K určení velikosti výpočetní výkon, úložiště a síťové součásti v Azure. Pro SAP velikosti virtuálních počítačů Azure, najdete podporu Poznámka SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Architektura provozní kontinuitu a zotavení po havárii
    4.  Podrobné operačního systému, databáze, jádra a SAP podporují balíčku verze. Nejedná se vzhledem k tomu, že všechny vydané verze operačního systému, který podporuje SAP NetWeaver a S/4HANA je podporována ve virtuálních počítačích Azure. Totéž platí pro verze DBMS. Je povinné, získáte následující zdroje zaškrtnuto, abyste mohli zarovnání a v případě potřeby upgradujte verze SAP DBMS verzí a verzí operačního systému dalo v SAP a Azure nepodporuje okno. Je povinné, že jsou v rámci SAP a podporované kombinace verze a získejte plnou podporu od SAPU a Microsoftu v Azure. V případě potřeby, musíte naplánovat pro upgrade některé ze součástí softwaru. Další informace o podporovaných SAP, operačního systému a systém DBMS softwaru najdete v těchto umístěních:
        1.  Poznámka: podpora SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Tato poznámka definuje minimální verze operačního systému podporována ve virtuálních počítačích Azure. Také definuje minimální databázové vydané verze, vyžaduje se pro většinu bez HANA database. Poznámka také prezentuje velikosti SAP různých typů virtuálních počítačů Azure nepodporuje SAP.
        2.  Poznámka: podpora SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619). Poznámka definuje matice podpory Oracle v Azure. Uvědomte si, že Oracle podporuje pouze Windows a Oracle Linux jako hostovaný operační systém v Azure pro úlohy SAP. Toto prohlášení o odborné pomoci se vztahuje na aplikační vrstvě SAP spuštěné instance SAP také. Oracle, ale nepodporuje vysokou dostupnost pro centrální služby SAP v Oracle Linuxu prostřednictvím Pacemaker. Pokud požadujete vysoké dostupnosti ASCS v Oracle Linuxu, budete muset využít SIOS ochranná sada pro Linux. Podrobná data certifikace SAP, zkontrolujte poznámky SAP support [#1662610 – podrobnosti o podpoře pro SIOS ochranná sada pro Linux](https://launchpad.support.sap.com/#/notes/1662610). Pro Windows SAP pro centrální služby SAP se podporuje ve spojení se společností Oracle jako vrstva DBMS nepodporuje řešení Windows převzetí služeb při selhání clusteru převzetí služeb při selhání. 
        3.  Poznámka: podpora SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) získat podporu pro SAP HANA na různých operačních systémech verze
        4.  SAP HANA nepodporuje virtuální počítače Azure a [velkých instancích HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) patří [zde](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)
        5.  [Přehled dostupnosti SAP produktu](https://support.sap.com/en/)
        6.  Další poznámky SAP pro konkrétní produkty SAP  
    5.  Doporučujeme, abyste striktní 3vrstvé návrhy pro SAP produkčními systémy. Kombinování ASCS + aplikace servery na stejný virtuální počítač se nedoporučuje.  Používání konfigurace clusteru s několika SID pro centrální služby SAP se podporuje s Windows jako hostovaný operační systém v Azure. Zatímco operačních systémů Linux v Azure nepodporuje konfigurace clusteru s několika SID centrální služby SAP. Dokumentace ke službě pro případ hostovaného operačního systému Windows najdete v:
        1.  [SAP ASCS/SCS instance s několika SID vysokou dostupnost s možnostmi Windows Server Failover Clustering a sdíleného disku v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS instance s několika SID vysokou dostupnost s možnostmi Windows Server Failover Clustering a sdílení souborů v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Vysoká dostupnost a obnovení architektury
        1.  Definujte na základě RTO a RPO vysokou dostupnost a obnovení architektura vyžaduje, aby vypadala jako
        2.  Pro zajištění vysoké dostupnosti v rámci stejné zóny zkontrolujte požadované DBMS se nabízí v Azure. Většina DBMS nabízejí synchronních metod synchronní aktivní pohotovostní režim, který doporučujeme pro produkční systémy. Také kontrola systému SAP související dokumentaci pro různé databáze počínaje [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a související dokumenty
            1.  Pomocí služby clusteru převzetí služeb při selhání Windows se sdíleným diskem konfigurací pro vrstvu DBMS, jako je například pro SQL Server [tady](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) je **není** podporována. Místo toho řešení, jako je:
                1.  [Technologie AlwaysOn systému SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Systémové replikace HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  Pro zotavení po havárii v různých oblastech Azure zkontrolujte, jaké možnosti jsou nabízeny od různých dodavatelů DBMS. Většina z nich podporuje asynchronní replikaci nebo přesouvání protokolu
        4.  Pro aplikační vrstvě SAP definujte, jestli jste by běžet vaše obchodní regrese testovací systémy, které jsou v ideálním případě repliky nasazení produkčního prostředí, ve stejné oblasti Azure nebo v oblasti zotavení po Havárii. V druhém případě je možné cílit na daný systém regrese firmy jako cíl obnovení po Havárii pro vaše produkční prostředí
        5.  Pokud se rozhodnete umístit neprodukční systémy v lokalitě zotavení po Havárii, pohlížet jako přijatelné metody replikace aplikační vrstvě SAP do oblasti zotavení po Havárii Azure do Azure Site Recovery. Viz také [nastavit zotavení po havárii pro nasazení vícevrstvé aplikace SAP NetWeaver](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Pokud se rozhodnete použít kombinované konfiguraci HA/DR, využití [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) stát zkušenosti s oblastí Azure zóny dostupnosti jsou k dispozici a omezení, které mohou být způsobeny latence sítě mezi dvě zóny dostupnosti  
3.  Zákazník nebo Partner měli vytvořit seznam všech rozhraní SAP (SAP a mimo systém SAP). 
4.  Návrh Foundation Services – návrh – tento návrh zahrnuje různé věci, třeba
    1.  Návrh služby Active Directory a DNS
    2.  Topologie sítě v rámci Azure a přiřazení různých systémů SAP
    3.  [Přístup na základě role](https://docs.microsoft.com/azure/role-based-access-control/overview) strukturu pro různé týmy, které spravují infrastrukturu a aplikace SAP v Azure
    3.  Skupina prostředků topologie 
    4.  [Označování strategie](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Pojmenování vytváření názvů pro virtuální počítače a dalších komponent infrastruktury a/nebo logické názvy
5.  Smlouva o podpoře Microsoft Premier – Identifikujte MS účtu správce (TECHNICAL Account Manager). Pro podporu požadavků na SAP, přečtěte si Poznámka SAP support [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Určete počet kvótu jader v různých předplatných a předplatných Azure. [Otevření žádosti o podporu o navýšení kvóty předplatných Azure,](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) podle potřeby 
7.  Redukce dat a migraci dat naplánujte migraci dat SAP do Azure. Systémů SAP NetWeaver SAP má pokyny o tom, jak zachovat objem velké množství dat, které jsou omezené. SAP publikované [příručku velký](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) o správě dat v SAP ERP systémy. Však nějaký obsah nevztahuje k systémům NetWeaver a S/4HANA obecně.
8.  Definujte a rozhodnout postup automatického nasazení. Cílem služby automation za infrastrukturu nasazení v Azure je nasazení deterministické způsobem a deterministické výsledky. Mnozí uživatelé používají Power Shell nebo skripty rozhraní příkazového řádku založené. Existují různé opensourcových technologií, které lze použít k nasazení infrastruktury Azure pro SAP a dokonce i instalaci softwaru SAP. Příklady lze nalézt v githubu:
    1.  [Nasazení automatické SAP v cloudu Azure](https://github.com/Azure/sap-hana)
    2.  [Instalace SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definování regulárního návrh a zkontrolujte pravidelnost mezi vámi jako zákazník, systémoví integrátoři, Microsoft a jiných součástí strany

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotní fáze (doporučeno)
 
Pilotní nasazení můžete spustit před nebo v paralelní k plánování a příprava projektu. Fáze slouží také k testování přístupy a usnadnění návrhu s dobrou ve fázi plánování a příprava. Pilotní fázi můžete roztažen tak, aby skutečné testování konceptů. Doporučuje se nastavit a ověřit úplné HA/DR řešení a také návrh zabezpečení během pilotního nasazení. V některých případech zákazníka škálovatelnost testů také mohou být prováděna v této fázi. Další zákazníků používá jako pilotní fázi nasazení systémů SAP izolovaného prostoru. Takže předpokládáme, identifikovat systému, který chcete migrovat do Azure za účelem spouštění pilotní nasazení.

1.  Optimalizujte přenosy dat do Azure. Vysoce závislé na přenos případů zákazníka prostřednictvím [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z místního byl nejrychlejší, pokud má dostatečnou šířku pásma okruhu Express. S ostatními zákazníky prostřednictvím Internetu naplánujete bude rychlejší
2.  V případě SAP migrace heterogenní platformy, která zahrnuje exportu a importu dat z databáze, otestovat a optimalizovat export a import fází. Pro velké migrace zahrnující systému SQL Server jako cílové platformy, můžete najít doporučení [tady](https://blogs.msdn.microsoft.com/saponsqlserver/2017/05/08/sap-osdb-migration-to-sql-server-faq-v6-2-april-2017/). Můžete provést migraci monitorování/SWPM přístup v případě, že není nutné kombinované vydání upgradu nebo [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) při kombinování migrace se upgrade verze SAP a splnění určitých zdrojová a cílová platforma DBMS kombinace popsáno, například v [možnost migrace databáze (DMO) ze SOUČTU 2.0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
    1.  Exportovat do zdroje, nahrání souboru exportu do Azure a Import výkon.  Maximalizovat překrývají exportu a importu
    2.  Vyhodnocení objemu databáze mezi cíle a cílové platformy, aby bylo zřejmé ve velikosti infrastruktury    
    3.  Ověřte a optimalizujte časování 
3.  Technické ověření 
    1.  Typy virtuálních počítačů
        1.  Ověření prostředky na podporu poznámky SAP, SAP HANA hardwaru adresáře a SAP PAM znovu a ujistěte se, že se žádné změny v podporovaných virtuálních počítačích Azure, podporované verze operačního systému pro tyto typy virtuálních počítačů a podporované verze SAP a DBMS
        2.  Velikost vaší aplikace a infrastrukturu, kterou můžete nasadit v Azure znovu ověřte. V případě přesunutí stávající aplikace, můžete často odvodit nezbytné přístupové body z infrastruktury můžete použít a [SAP srovnávací test webové stránce](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) a porovnejte je s protokoly SAP čísla uvedená v poznámce podpory SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Také zachovat [v tomto článku](https://blogs.msdn.microsoft.com/saponsqlserver/2018/11/04/saps-ratings-on-azure-vms-where-to-look-and-where-you-can-get-confused/) v paměti
        3.  Vyhodnocování a testování velikosti virtuálních počítačů Azure týkajících se propustnosti maximální velikost úložiště a propustnost sítě pro různé typy virtuálních počítačů, kterou jste zvolili ve fázi plánování. Data můžete najít v:
            1.  [Velikosti virtuálních počítačů Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Je důležité vzít v úvahu **maximální počet mezipamětí propustností** k určení velikosti
            2.  [Velikosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) je důležité vzít v úvahu **maximální počet mezipamětí propustností** k určení velikosti
    2.  Storage
        1.  Použití Azure Premium Storage pro virtuální počítače databáze
        2.  Použití [Azure managed disks](https://azure.microsoft.com/services/managed-disks/)
        3.  Akcelerátor zápisu Azure použijte pro disky protokolu DBMS s M-Series. Mějte na paměti omezení akcelerátor zápisu a využití, jak je uvedeno v [akcelerátorem zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
        4.  Pro různé typy DBMS, zkontrolujte [týkající se obecné SAP DBMS dokumentaci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a konkrétní dokumentace DBMS obecného dokumentu odkazuje, vám
        5.  Pro SAP HANA, další podrobnosti jsou popsány v [konfigurace infrastruktury SAP HANA a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
        6.  Nikdy připojení Azure datové disky na virtuálním počítači Azure s Linuxem s použitím ID zařízení. Místo toho použijte univerzálně jedinečným identifikátorem (UUID). Buďte opatrní při použití grafické nástroje pro připojení Azure datových disků, např. Zkontrolujte položky v/etc/fstab, abyste měli jistotu, že jsou připojené disky pomocí identifikátoru UUID
            1.  Další podrobnosti najdete [zde](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
    3.  Sítě
        1.  Testování a hodnocení infrastruktury virtuální sítě a distribuce aplikace SAP napříč nebo mezi různými virtuálními sítěmi Azure
            1.  Vyhodnocení přístupu centra a architekturu virtuální sítě paprsků nebo microsegmentation v rámci jedné virtuální síť Azure na základě
                1.  Náklady na kvůli výměnu dat mezi [partnerský vztah virtuálních sítí Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Náklady na zkontrolujte [ceny služby Virtual Network](https://azure.microsoft.com/pricing/details/virtual-network/)
                2.  Odpojit výhod rychlé vytvoření partnerského vztahu mezi virtuálními sítěmi Azure porovnání změn skupiny NSG k izolování podsítí v rámci virtuální sítě pro případy, kdy virtuální počítače hostované v podsíti virtuální sítě nebo aplikace stala ohrožení zabezpečení
                3.  Centrální protokolování a auditování síťový provoz mezi virtuální datové centrum, které jste vytvořili v Azure, místní a okolním světem
            2.  Vyhodnocování a testování cestu k datům mezi aplikační vrstvě SAP a SAP DBMS vrstvy. 
                1.  Libovolné umístění [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační trasa mezi aplikací SAP a vrstvě databázového systému SAP NetWeaver, Hybris nebo S/4HANA systémů SAP není podporována na všech
                2.  Umístění aplikační vrstvě SAP a SAP DBMS v různých virtuálních sítí Azure, které nejsou v partnerském vztahu se nepodporuje.
                3.  [Pravidla ASG a skupiny zabezpečení sítě Azure](https://docs.microsoft.com/azure/virtual-network/security-overview) jsou podporovány pro definování tras mezi aplikační vrstvě SAP a SAP DBMS vrstvy
            3.  Ujistěte se, že [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolená na virtuální počítače použité v aplikační vrstvě SAP a SAP DBMS vrstvy. Mějte na paměti, že různých úrovních operačního systému jsou potřeba k podpoře Akcelerovanými síťovými službami v Azure:
                1.  Windows Server 2012 R2 nebo novější verze
                2.  SUSE Linux 12 SP3 nebo novější verze
                3.  RHEL 7.4 nebo novější verze
                4.  Oracle Linux 7.5. Používání jádra RHCKL, musí být 3.10.0-862.13.1.el7 vydání. Použití Oracle UEK jádra verze 5 je povinné
            4.   Testování a vyhodnocení latence sítě mezi aplikační vrstvě SAP virtuálních počítačů a virtuálních počítačů databázového systému podle Poznámka SAP support [#500235](https://launchpad.support.sap.com/#/notes/500235) a podporu Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnocení výsledků na základě pokynů latence sítě ze Poznámka SAP support [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě musí být v rozsahu střední a funkční. Výjimky se vztahují na provoz mezi virtuálními počítači a velké Instance HANA jednotky, jak je uvedeno [zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
            5.   Ujistěte se, že jsou nastavené ILB nasazení použít přímou odpověď serveru. Toto nastavení se sníží latence v případech, kde jsou Azure ILBs použít pro konfigurace s vysokou dostupností na vrstvu DBMS
    4.   Vysoká dostupnost a nasazení pro obnovení. 
        1.   Pokud nasadíte aplikační vrstvě SAP bez definování konkrétní zóně dostupnosti Azure, ujistěte se, že jsou všechny virtuální počítače spuštěné instance SAP dialogové okno nebo middleware instance na jednom systému SAP v nasazené [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
            1.   V případě nevyžadují vysokou dostupnost pro centrální služby SAP a DBMS, tyto virtuální počítače je možné nasadit do stejné sadě dostupnosti jako aplikační vrstvě SAP
        2.   Pokud chráníte centrální služby SAP a vrstvu DBMS pro vysokou dostupnost s pasivním repliky, máte dva uzly pro centrální služby SAP v jedné samostatné skupiny dostupnosti a dvěma uzly DBMS v jiné sady dostupnosti
        3.   Pokud provádíte nasazení do zóny dostupnosti Azure, nemůžou využívat skupiny dostupnosti. Musíte ale ujistěte se, že budete nasazovat aktivními a pasivními uzly centrální služby do dvou různých zón dostupnosti, které zobrazují nejmenší latenci mezi zónami.
            1.   Pamatujte, že budete muset použít [Azure Load balancer úrovně Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) pro případ zřízení Pacemaker clustery převzetí služeb při selhání nebo Windows pro vrstvu DBMS a centrální služby SAP napříč zónami dostupnosti. [Load balancer úrovně Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) nelze použít pro zónové nasazení 
    5.   Nastavení časového limitu
        1.   Zkontrolovat trasování pro vývojáře SAP NetWeaver jinou instancí SAP a ujistěte se, že jsou uvedené žádné konce připojení mezi serverem pro zařazení do fronty a pracovní postupy SAP. Tyto konce připojení se lze vyvarovat nastavením tyto dvě registru parametry:
            1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – viz také [v tomto článku](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
            2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – viz také [v tomto článku](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
        2.   Pokud se chcete vyhnout grafickým uživatelským rozhraním časové limity mezi místním jeden nasazené rozhraní grafického uživatelského rozhraní SAP a SAP vrstvy aplikace nasazené v Azure, zkontrolujte, zda tyto parametry jsou nastaveny v default.pfl nebo instance profilu:
            1.   rdisp/keepalive_timeout = 3600
            2.   rdisp/keepalive = 20
        3.   Pokud používáte konfiguraci clusteru převzetí služeb při selhání Windows, ujistěte se, že jsou správně nastavena času reagovat na jako nereagující uzly pro Azure. Článku znalostní báze Microsoft [ladění prahy sítě clusteru převzetí služeb při selhání](https://blogs.msdn.microsoft.com/clustering/2012/11/21/tuning-failover-cluster-network-thresholds/) jsou uvedeny parametry a jak ty ovlivnit citlivost převzetí služeb při selhání. Tyto dva parametry uvedených parametrů by měl být nastaven s hodnotami:
            1.   SameSubNetDelay = 2
            2.   SameSubNetThreshold = 15
4.   Testování vysokou dostupnost a obnovení procedury programu
    1.   Situace převzetí služeb při selhání simulujte vypíná virtuální počítače (hostovaného operačního systému Windows) nebo uvedení operační systémy v režimu tísňový (hostovaného operačního systému Linux), aby bylo možné zjistit, jestli vaše konfigurace převzetí služeb při selhání fungovat tak, jak navrženo. 
    2.   Změřte vaše dobu potřebnou ke spuštění převzetí služeb při selhání. Pokud dobu trvá příliš dlouho, vezměte v úvahu:
        1.   SUSE Linux použijte SBD zařízení místo agenta oplocení Azure ke zrychlení převzetí služeb při selhání
        2.   Pro SAP HANA Pokud opětovné načtení dat trvá příliš dlouho zvažte zřídit větší šířku pásma úložiště
    3.   Vyzkoušejte zálohování a obnovení pořadí a načasování a ladit v případě potřeby. Ujistěte se, že časy pouze zálohování jsou dostatečné. Také testovací obnovení a provádět s nimi načasování obnovení činnosti. Ujistěte se, že časů obnovení jsou v rámci vašich smlouvách SLA RTO Pokud stanovíte RTO závisí na databázi nebo procesu obnovení virtuálního počítače
    4.   Testování v oblasti zotavení po Havárii funkcí a architektury
5.  Kontroly zabezpečení
    1.  Test platnosti roli Azure na základě architektura přístupu (RBAC), které jste implementovali. Cílem je omezit přístup a oprávnění z různých týmů a oddělit. Jako příklad členové týmu SAP základ měl k nasazení virtuálních počítačů a přiřazení disky ze služby Azure storage do dané virtuální sítě Azure. Ale SAP základ týmu by neměl moct vytvářet vlastní virtuální sítě nebo změnit nastavení existující virtuální sítě. Na druhé straně nesmí být schopni nasadit virtuální počítače do virtuální sítě, kde aplikace SAP DBMS virtuální počítače běží a členové týmu pro sítě. Ani měli členové týmu pro sítě být schopni změnit atributy virtuálních počítačů nebo dokonce odstranit virtuální počítače nebo disky.  
    2.  Ověřte [NSG a ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) pravidla fungují podle očekávání a spusťte operaci ochrany chráněné prostředky
    3.  Ujistěte se, že všechny prostředky, které je potřeba šifrovat zašifrují. Definování a provádění procesy pro zálohování certifikáty, ukládat a přístup k těchto certifikátů a obnovení šifrovaných entity. 
    4.  Použití [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) a/nebo disky operačního systému, kde možných odesílatelů operačního systému podporují pohledu
    5.  Zkontrolujte, že se používají příliš mnoho vrstvy šifrování. To dává smysl omezené použití Azure Disk encryption a na prvních jedné z metod DBMS transparentnímu šifrování dat
6.  Testování výkonnosti
    1.  V systému SAP na základě SAP trasování a měření porovnejte nejvyšší 10 online sestavy do aktuální implementace, kde je to možné 
    2.  V SAP na základě SAP trasování a měření porovnejte prvních 10 dávkových úloh aktuální implementace, kde je to možné 
    3.  V SAPU na základě SAP trasování a měření porovnejte přenosy dat prostřednictvím rozhraní do systému SAP. Zaměřte se na rozhraních, kdy víte, teď bude přenos mezi různými umístěními jako z místního Azure 


## <a name="non-production-phase"></a>Li se o Neprodukční fáze 
V této fázi předpokládáme, že po úspěšném pilotním projektu nebo testování konceptu, při spuštění nasazení neprodukční systémy SAP do Azure. Poznatky a prostředí mimo testování konceptů, by měla být přizpůsobena takovémto nasazení. V tomto typu nasazení také použít kritéria a kroků uvedených v testování konceptu. V této fázi se obvykle nasazení vývoj systémů, systémů testů jednotek a obchodní regresní testy systémy do Azure. Doporučujeme tuto alespoň jeden mimo produkční systém v jednom řádku aplikace SAP má konfiguraci úplné vysoké dostupnosti budete budoucí produkční systém. Další kroky, které je třeba zvážit v průběhu této fáze jsou:  

1.  Před přesunutím shromažďovat data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a vstupně-výstupních operací datové systémy od starého platformy do Azure. Zejména z DBMS jednotky, vrstvy, ale také z jednotky vrstvy aplikace. Také měření latence sítě a úložiště.
2.  Zaznamenejte vzory používání času dostupnosti systémů. Cílem je zjistit, zda-li se o neprodukční systémy musí být 7 × 24 k dispozici, nebo jestli existují-li se o neprodukční systémy, které může být vypnut během určité fáze týden nebo měsíc
3.  Testování a definujte, jestli chcete vytvořit vlastní Image operačního systému pro virtuální počítače v Azure nebo zda chcete použít image z Galerie Imagí Azure. Pokud používáte image z Galerie Azure, ujistěte se, že zvolíte správné bitové kopie, která zohledňuje, smlouvu o podpoře s vaším dodavatelem operačního systému. Pro někteří dodavatelé operačního systému Galerie Azure nabízí chcete použít vlastní licenci. Pro ostatní bitové kopie operačního systému, podpora je zahrnutá v ceně v uvozovkách Azure. Pokud se rozhodnete vytvořit vlastní Image operačního systému, můžete najít dokumentaci v těchto článcích:
    1.  Můžete vytvářet generalizované image virtuálního počítače s Windows nasazené v Azure na základě [této dokumentaci](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    2.  Můžete vytvářet generalizované image systému Linux virtuálního počítače nasazeného v Azure na základě [této dokumentaci](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Pokud používáte Image operačního systému SUSE a Red Hat Linux z Galerie virtuálních počítačů Azure, budete muset použít obrázky pro SAP poskytovaných dodavateli Linux v galerii virtuálních počítačů Azure
4.  Ujistěte se, že splňují požadavky na podporu, které SAP, které se týkají smlouvy o podpoře Microsoft. Informace najdete v poznámce podpory SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Pro velké instance HANA, naleznete v dokumentu [požadavků na připojení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Ujistěte se, že lidé získat [oznámení plánované údržby](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), tedy v čase můžete zvolit výpadek a restartování virtuálních počítačů
5.  Neustálé přečtěte dokumentaci k Azure z Microsoft prezentace na kanálů, jako jsou [Channel9](https://channel9.msdn.com/) pro nové funkce, které se mohou vztahovat na vaše nasazení
6.  Související poznámky k vrácení SAP do Azure, jako je podpora Poznámka [#1928533](https://launchpad.support.sap.com/#/notes/1928533) pro nové skladové položky virtuálních počítačů nebo nově podporované verze operačního systému a systém DBMS. Porovnání nové typy virtuálních počítačů na virtuálním počítači starší typy, které do ceny, tak, že budete moct nasadit virtuální počítače s nejlepší poměr cena/výkon
7.  Ověření prostředky na podporu poznámky SAP, SAP HANA hardwaru adresáře a znovu PAM SAP chcete mít jistotu, že nebyly provedeny žádné změny v podporovaných virtuálních počítačích Azure, uvolní podporovaný operační systém v těchto virtuálních počítačů a podporovaných SAP a DBMS vydané verze
8.  Zkontrolujte [tady](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro nové HANA certifikované skladová jednotka v Azure a porovnat ceny na základě těch plánované a nakonec změnit na jednotky s lepší výkon deklaraci cena 
9.  Upravit skripty nasazení využít nové typy virtuálních počítačů a začlenit nových funkcí služby Azure, kterou chcete použít
10. Po nasazení infrastruktury, testování a vyhodnocení latence sítě mezi aplikační vrstvě SAP virtuálních počítačů a virtuálních počítačů databázového systému podle Poznámka SAP support [#500235](https://launchpad.support.sap.com/#/notes/500235) a podporu Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnocení výsledků na základě pokynů latence sítě ze Poznámka SAP support [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě musí být v rozsahu střední a funkční. Výjimky se vztahují na provoz mezi virtuálními počítači a velké Instance HANA jednotky, jak je uvedeno [tady](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Ujistěte se, že žádná omezení uvedených v [aspekty pro nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) a [konfigurace infrastruktury SAP HANA a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) platí pro vaše nasazení
11. Provést všechny kontroly, jak je uvedeno ve fázi testování koncepce před použitím úlohy
12. Jako úlohy platí, zaznamenejte spotřeba prostředků těchto systémů v Azure a porovnejte s záznamy, které jste získali z původní platformě. Pokud zjistíte, že máte větší rozdíly, upravte virtuálního počítače k určení velikosti budoucí nasazení. Mějte na paměti, že v případě downsizing, úložiště a šířky pásma sítě virtuálního počítače, také snižuje:
    1.  [Velikosti virtuálních počítačů Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Velikosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Práce na systémové funkce kopírování a procesy. Cílem je, aby byl snadno zkopírovat vývojového systému nebo v testovacím systému tak, která projekt týmy můžou novými systémy velmi rychle získat. Vezměte v úvahu [SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) jako nástroj pro provádění takových úloh.
14. Optimalizace a Rozviňte váš tým přístup na základě rolí Azure, oprávnění a procesy Pokud chcete mít jistotu, že máte na jedné straně oddělení provozu. Na druhé straně budete chtít mít všechny týmy provádět úkoly v infrastruktuře Azure.
15. Cvičení, testování a dokumentu a po havárii s vysokou dostupností obnovení postupy, které umožní pracovníky k provedení těchto úloh. Identifikujte nedostatky a přizpůsobit nové funkce Azure, kterou integrujete do svých nasazení

 
## <a name="production-preparation-phase"></a>Fáze přípravy v ostrém provozu 
V této fázi budete chtít shromáždit všechna prostředí a poznatky o neprodukční nasazení a použít je v budoucnu nasazení v produkčním prostředí. Kromě toho k jednotlivým fázím dříve, budete také muset připravit pracovní přenos dat mezi aktuálním umístění pro hostování a Azure. 

1.  Seznámení se základními nezbytné upgradu verze SAP produkční systémy před přesunem do Azure
2.  Souhlasím s vlastníkům obchodních na funkční a obchodní testy, které je potřeba provést po migraci produkčního systému.
    1.  Ujistěte se, že tyto testy jsou spouštěny s zdrojových systémů v aktuálním umístění pro hostování. Chcete se vyhnout zkouškách poprvé po systém bude přesunuta do Azure
2.  Otestujte výrobní proces migrace do Azure. V případě přesun všech systémů v produkčním prostředí do Azure není ve stejném časovém rámci, vytvořte skupiny produkční systémy, které musí být ve stejném umístění pro hostování. Migrace dat výkonu a testování. Seznam běžných metod jako:
    1.  Pomocí metody DBMS, jako je zálohování a obnovení v kombinaci se SQL Server AlwaysOn, HANA System Replication nebo protokol přenosů počáteční hodnoty a synchronizujte databázi obsahu v Azure
    2.  Pomocí zálohování a obnovení pro menší databáze
    3.  Použití SAP monitorování migrace implementována do nástroje SAP SWPM provádět heterogenní migrace
    4.  Použití [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) zpracování, pokud je potřeba v kombinaci s upgrade verze SAP. Uvědomte si, že ne všechny kombinace mezi zdrojem a cílem DBMS jsou podporovány. Další informace najdete v konkrétních podporuje poznámky SAP pro dvě různé verze DMO. Například [možnost migrace databáze (DMO) ze SOUČTU 2.0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Otestujte, zda přenos dat přes internet nebo prostřednictvím ExpressRoute je lepší propustnost pro případ, budete potřebovat přesunout zálohy nebo SAP exportovat soubory. Pro případ přesouvá data prostřednictvím Internetu může být nutné změnit některá pravidla NSG a ASG zabezpečení, které je potřeba mít na místě pro budoucí produkční systémy.
3.  Před přesunutím shromažďovat data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a vstupně-výstupních operací datové systémy od starého platformy do Azure. Zejména z DBMS jednotky, vrstvy, ale také z jednotky vrstvy aplikace. Také měření latence sítě a úložiště.
4.  Ověření prostředky na podporu poznámky SAP, SAP HANA hardwaru adresáře a znovu PAM SAP chcete mít jistotu, že nebyly provedeny žádné změny v podporovaných virtuálních počítačích Azure, uvolní podporovaný operační systém v těchto virtuálních počítačů a podporovaných SAP a DBMS vydané verze 
4.  Upravit skripty nasazení pro nejnovější změny, které jste se rozhodli pro typy virtuálních počítačů a funkce Azure
5.  Po nasazení infrastruktury a aplikací, projděte si sérii kontrol v pořadí pro ověření:
    1.  Správné typy virtuálních počítačů máte nasazené správné atributy a velikosti úložiště
    2.  Zkontrolujte, zda virtuální počítače na správné a požadovanou verzí operačního systému a oprav a jsou jednotné
    3.  Zkontrolujte, zda jsou jako povinné a jednotné posílení zabezpečení virtuálních počítačů
    4.  Zkontrolujte, zda opravy a správné aplikace je teď nainstalovaný a nasazený
    5.  Virtuální počítače je teď nasadit do skupin dostupnosti Azure podle plánu
    6.  Azure Premium Storage se použil pro citlivé disky latence nebo kde [jeden virtuální počítač poskytuje se smlouva SLA 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) je povinný
    7.  Zkontrolujte správné nasazení akcelerátor zápisu Azure
        1.  Ujistěte se, že na virtuálním počítači a prostory úložiště, nebo na discích, které vyžadují podporu akcelerátor zápisu Azure je teď správně sestavena prokládané sady
            1.  Zkontrolujte [konfigurace softwarového pole RAID v Linuxu](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Zkontrolujte [konfigurace LVM na virtuální počítač s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) byly použity výhradně
    9.  Virtuální počítače je teď nasadit do skupiny dostupnosti správný a zóny dostupnosti
    10. Ujistěte se, že [akcelerovaných síťových služeb Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je na virtuální počítače použité v aplikační vrstvě SAP a SAP DBMS vrstvu povolená
    11. Žádná umístění [Azure síťových virtuálních zařízení](https://azure.microsoft.com/solutions/network-appliances/) v komunikační trasa mezi aplikací SAP a úroveň databázového systému SAP NetWeaver, Hybris nebo S/4HANA na systémů SAP
    12. ASG a skupiny zabezpečení sítě pravidla umožňují komunikaci jako požadovaný a plánované a blokovat komunikaci vyžadováno
    13. Nastavení časového limitu jak bylo popsáno dříve byly nastaveny správně
    14. Testování a vyhodnocení latence sítě mezi aplikační vrstvě SAP virtuálních počítačů a virtuálních počítačů databázového systému podle Poznámka SAP support [#500235](https://launchpad.support.sap.com/#/notes/500235) a podporu Poznámka SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnocení výsledků na základě pokynů latence sítě ze Poznámka SAP support [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě musí být v rozsahu střední a funkční. Výjimky se vztahují na provoz mezi virtuálními počítači a velké Instance HANA jednotky, jak je uvedeno [zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)
    15. Zkontrolujte, zda je teď v případě potřeby a metodou šifrování nezbytné nasadit šifrování
    16. Zkontrolujte, zda rozhraní a další aplikace můžete připojit nově nasazená infrastruktura
6.  Vytvořte playbook pro reakce na plánovaná údržba Azure. Definovat pořadí systémy a virtuálními počítači až po restartování v případě plánované údržby
    

## <a name="go-live-phase"></a>Přejděte živé fáze
Fáze uvedení do provozu budete muset Nezapomeňte postupovat podle vaší playbooky, které jste vytvořili v předchozích fází. Proveďte kroky, které jste otestovali a školení. Nepřijímají poslední minutu změn konfigurace a proces. Kromě, která se vztahují následující míry:

1. Ověřte, zda jsou funkční monitorování Azure portal a další monitorovací nástroje.  Jsou doporučené nástroje Perfmon (Windows) nebo – zvláštní administrativní oblast (Linux): 
    1.  Čítače CPU 
        1.  Průměrná doba využití procesoru – celkem (všechny procesoru)
        2.  Průměrná doba využití procesoru – každé jednotlivé procesoru (tedy 128 procesorů na virtuálním počítači m128)
        3.  Čas jádra CPU – každé jednotlivé procesoru
        4.  Procesor nový uživatel – každé jednotlivé procesoru
    5.  Memory (Paměť) 
        1.  Volná paměť
        2.  Paměťové stránky za sekundu
        3.  Paměťové stránky na více instancí za sekundu
    4.  Disk 
        1.  Čtení z disku kb/s – na jednotlivých disků 
        2.  Čtení disku/s – na jednotlivých disků
        3.  Čtení z disku ms/čtení – na jednotlivých disků
        4.  Kb zápis disku/s – na jednotlivých disků 
        5.  Zápis disku/s – na jednotlivých disků
        6.  Ms zápis disku/čtení – na jednotlivých disků
    5.  Síť 
        1.  Síťových paketů za sekundu
        2.  Síťových paketů na více instancí za sekundu
        3.  Síť kb za sekundu
        4.  Síť kb na více instancí za sekundu 
2.  Po dokončení migrace dat proveďte všemi ověřovacími testy, které dohodnutých s vlastníky firmy. Oprávnění přijměte jen výsledky ověřovacího testu, kde jsou výsledky pro původní zdrojových systémů
3.  Zkontrolujte, jestli fungují rozhraní a určuje, zda jiné aplikace můžou komunikovat s nově nasazené produkčními systémy
4.  Zkontrolujte přenosu a oprava systému prostřednictvím transakce SAP moduly STM
5.  Proveďte zálohování databáze po vydání systému pro produkční prostředí
6.  Zálohování virtuálních počítačů pro aplikační vrstvě SAP virtuálních počítačů po vydání systému pro produkční prostředí
7.  Pro SAP systémy, které nejsou součástí aktuální ostrou fáze, ale, že jste přesunuli do Azure v této fázi uvedení do provozu, budete potřebovat resetovat vyrovnávací paměť názvu hostitele v SM51 komunikovat se systémy SAP. Tento krok se zbavit staré uložené v mezipaměti IP adresy přidružené názvy instancí aplikace, které jste přesunuli do Azure  


## <a name="post-production"></a>Produkční příspěvku
V této fázi všechno se točí kolem monitorování, provozování a správě systému. Z SAP pohledu použít běžné úkoly, které se vyžadují k provedení s původní umístění pro hostování. Jsou konkrétní úkoly v Azure, kterou chcete provést:

1. Analýza Azure faktury pro vysokou zpoplatnění systémy
2. Zajištění optimální efektivity ceny a výkonu na straně úložiště a virtuálních počítačů
3. Optimalizace času, které systémy můžete vypnout.  


## <a name="next-steps"></a>Další kroky
Informace naleznete v dokumentaci:

- [Azure Virtual Machines, plánování a implementace SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

