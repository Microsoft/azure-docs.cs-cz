---
title: Kontrolní seznam pro plánování a nasazení úloh SAP | Microsoft Docs
description: Kontrolní seznam pro plánování a nasazování úloh SAP v Azure
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2d6170dead4e8577cea6883ffea25b90ebe39b88
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70079011"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>Úlohy SAP v kontrolním seznamu pro plánování a nasazení Azure 

Tento kontrolní seznam je určený pro zákazníky, kteří přesunou své aplikace SAP NetWeaver, S/4HANA a Hybris do infrastruktury Azure jako službu.  Tento kontrolní seznam by měl být po dobu trvání projektu přezkoumán partnerem zákazníka nebo SAP. Je důležité, aby řada kontrol probíhala na začátku projektu a ve fázi plánování. Po dokončení nasazení se základní změny v nasazené infrastruktuře Azure nebo vydání softwaru SAP můžou stát komplexní. Projděte si tento kontrolní seznam na klíčových milníkech v rámci projektu.  Je možné zjistit drobné problémy před tím, než dojde k velkým problémům a dostatek času pro opětovné provedení techniků a testování nezbytných změn. Kontrolní seznam nezpůsobí, aby byly splněné deklarace identity. V závislosti na konkrétní situaci může být nutné provést mnoho dalších kontrol. 

Sestavený kontrolní seznam neobsahuje úlohy nezávislé na Azure.  Příklad: Aplikační rozhraní SAP se během přesunu do veřejného cloudu Azure nebo poskytovatele hostingu mění.    

Tento kontrolní seznam se dá použít i pro už nasazené systémy. Od nasazení se možná přidaly nové funkce, jako jsou Akcelerátor zápisu, Zóny dostupnosti a nové typy virtuálních počítačů.  Je proto vhodné pravidelně kontrolovat kontrolní seznam, abyste se ujistili, že máte na platformě Azure informace o nových funkcích. 

## <a name="project-preparation-and-planning-phase"></a>Fáze přípravy a plánování projektu
V této fázi se plánuje migrace úlohy SAP do veřejného cloudu Azure. Minimální sada entit a popsaných a definovaných položek seznamu jako:

1. Dokument pro návrh na nejvyšší úrovni – tento dokument by měl obsahovat:
    1. Aktuální inventář komponent a aplikací SAP a inventář cílových aplikací v Azure
    2. Vytvořte a pracujte s maticí přiřazení zodpovědnosti (RACI), která definuje odpovědnost a přiřazování různých zúčastněných stran. Zahajte na vysoké úrovni a pracujte na více a podrobnějších úrovních při plánování a prvním nasazení.
    2. Architektura řešení na vysoké úrovni
    3. Rozhodnutí týkající se nasazení oblastí Azure do nástroje. Seznam oblastí Azure najdete v [oblastech Azure](https://azure.microsoft.com/global-infrastructure/regions/). Pro služby dostupné v každé oblasti Azure si projděte článek [Dostupné produkty](https://azure.microsoft.com/global-infrastructure/services/) v jednotlivých oblastech.
    4. Síťová architektura pro připojení z místního prostředí do Azure. Začněte s [plánem Virtual datacentra pro Azure](https://docs.microsoft.com/azure/architecture/vdc/) dobře obeznámeni.
    5. Principy zabezpečení pro provozování dat s vysokým dopadem na firmu v Azure. Materiály pro čtení začínají v [dokumentaci zabezpečení Azure](https://docs.microsoft.com/azure/security/)
2.  Technický návrh dokumentu – který obsahuje:
    1.  Diagram blokování řešení 
    2.  Určení velikosti výpočetních, úložných a síťových komponent v Azure. Informace o určení velikosti virtuálních počítačů Azure v SAP najdete v poznámkách k podpoře SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Provozní kontinuita a architektura zotavení po havárii
    4.  Podrobné verze sady support Pack pro operační systém, databáze, jádro a SAP Nejedná se o konkrétní verzi operačního systému, kterou SAP NetWeaver nebo S/4HANA podporuje ve virtuálních počítačích Azure. Totéž platí pro verze DBMS. Je nutné, aby následující zdroje byly zkontrolovány, aby se daly Zarovnat a v případě potřeby upgradovat verze SAP, verze DBMS nebo vydané verze operačního systému, aby byly v okně podporovaném systémem SAP a Azure. Je povinné, abyste v rámci podporovaných kombinací verzí SAP a Azure získali plnou podporu pro SAP a Microsoft. V případě potřeby musíte naplánovat upgrade některých softwarových komponent. Další podrobnosti o podporovaném softwaru SAP, OS a systému DBMS jsou zdokumentovány v těchto umístěních:
        1.  Poznámka k podpoře SAP [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Tato poznámka definuje minimální verze operačního systému podporované ve virtuálních počítačích Azure. Definuje také minimální verze databází, které jsou požadovány pro většinu databází mimo HANA. Poznámka také uvádí určení velikosti SAP u různých typů virtuálních počítačů Azure podporovaných v SAP.
        2.  Poznámka k podpoře SAP [#2039619](https://launchpad.support.sap.com/#/notes/2039619). Poznámka definuje tabulku podpory Oracle v Azure. Je potřeba si uvědomit, že Oracle podporuje jenom Windows a Oracle Linux jako hostovaný operační systém v Azure pro úlohy SAP. Tento příkaz podpory se vztahuje také na aplikační vrstvu SAP s instancemi SAP. Nicméně společnost Oracle nepodporuje vysokou dostupnost pro centrální služby SAP v Oracle Linux prostřednictvím Pacemaker. Pokud požadujete vysokou dostupnost pro ASCS na Oracle Linux, musíte využít sadu s ochranou Suite pro Linux. Podrobná data o certifikaci SAP najdete v poznámkách k podpoře SAP [#1662610 – podrobnosti o podpoře pro sadu s ochranou Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610). V případě systému Windows podporuje řešení převzetí služeb při selhání clusteru Windows s podporou převzetí služeb při selhání pro služby SAP Central Services ve spojení s Oracle jako s vrstvou DBMS. 
        3.  Poznámka k podpoře SAP [#2235581](https://launchpad.support.sap.com/#/notes/2235581) k získání matrice podpory pro SAP HANA v různých verzích operačního systému
        4.  SAP HANA podporované virtuální počítače Azure a [velké instance Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) jsou uvedené [tady](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) .
        5.  [Matice dostupnosti produktu SAP](https://support.sap.com/en/)
        6.  Další poznámky ke SAP pro jiné produkty specifické pro SAP  
    5.  Pro produkční systémy SAP doporučujeme použít přísné návrhy na 3 vrstvy. Nedoporučuje se kombinovat ASCS + aplikační servery na stejný virtuální počítač.  Použití konfigurace clusteru s více identifikátory SID pro služby SAP Central Services je podporované v systému Windows jako hostovaný operační systém v Azure. Vzhledem k tomu, že konfigurace clusterů s více identifikátory SID služby SAP Central Services není podporována pro operační systémy Linux v Azure. Dokumentaci pro případ operačního systému Windows hosta najdete v části:
        1.  [Vysoká dostupnost ASCS/SCS instance SAP pomocí clusteringu s podporou převzetí služeb při selhání Windows serveru a sdíleného disku v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [Vysoká dostupnost ASCS/SCS instance SAP s Clustering s podporou převzetí služeb při selhání Windows serveru a sdílenou složkou v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Architektura vysoké dostupnosti a zotavení po havárii
        1.  Definujte na základě RTO a RPO, co je potřeba, aby architektura vysoké dostupnosti a zotavení po havárii vypadala jako
        2.  V případě vysoké dostupnosti v rámci stejné zóny si přečtěte, co má požadovaný systém DBMS nabízet v Azure. Většina DBMS nabízí synchronní metody synchronního horkého úsporného režimu, které doporučujeme pro produkční systémy. Také si projděte dokumentaci týkající se SAP pro různé databáze počínaje [požadavky na nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) a související dokumenty.
            1.  Použití služby cluster s podporou převzetí služeb při selhání systému Windows s konfigurací sdíleného disku pro vrstvu DBMS, například popsané pro SQL Server [zde](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) , **není** podporováno. Místo toho řešení jako:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [Replikace systému HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  V případě zotavení po havárii napříč různými oblastmi Azure si můžete prohlédnout, jaké možnosti nabízí různí dodavatelé systému DBMS. Většina z nich podporuje asynchronní replikaci nebo přesouvání protokolů.
        4.  Pro vrstvu aplikace SAP definujte, jestli byste měli provozovat testovací systémy vaší firmy, které jsou v ideálním případě replikou vašich produkčních nasazení, ve stejné oblasti Azure nebo v oblasti zotavení po havárii. V druhém případě je možné cílit na systém pro podnikání jako cíl DR pro vaši produkci.
        5.  Pokud se rozhodnete neumístit systémy, které nejsou v produkčním prostředí, na webu DR, vyhledejte Azure Site Recovery jako životaschopnou metodu replikace aplikační vrstvy SAP do oblasti Azure DR. Další informace najdete v tématu [Nastavení zotavení po havárii pro nasazení aplikace NETWEAVER SAP ve více vrstvách](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) . 
        6.  Pokud se rozhodnete použít kombinovanou konfiguraci HA/zotavení po havárii s využitím [zóny dostupnosti Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) dobře znáte s oblastmi Azure, kde jsou dostupné zóny dostupnosti a s omezeními, která je možné zavést zvýšením latencí sítě mezi dvěma Zóny dostupnosti  
3.  Zákazník nebo partner by měl vytvořit inventář všech rozhraní SAP (SAP a non-SAP). 
4.  Návrh návrhu základních služeb – tento návrh by zahrnoval položky jako
    1.  Návrh služby Active Directory a DNS
    2.  Topologie sítě v rámci Azure a přiřazování různých systémů SAP
    3.  Struktura [přístupu na základě rolí](https://docs.microsoft.com/azure/role-based-access-control/overview) pro různé týmy, které spravují infrastrukturu a aplikace SAP v Azure
    3.  Topologie skupiny prostředků 
    4.  [Strategie označování](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  Zásady vytváření názvů pro virtuální počítače a další součásti infrastruktury a logické názvy
5.  Microsoft Premier Support smlouva – Identifikujte společnost MS Technical Account Manager (TAM). Informace o požadavcích na podporu pro SAP čtení SAP – Poznámka [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Definujte počet předplatných Azure a kvótu jader pro různá předplatná. [Otevřete žádosti o podporu pro zvýšení kvót předplatných Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) podle potřeby. 
7.  Omezení dat a plán migrace dat pro migraci dat SAP do Azure. V případě systémů SAP NetWeaver má SAP pokyn, jak udržet objem velkého množství dat s omezeným počtem. SAP zveřejnil [Tento podrobný průvodce](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) správou dat v systémech SAP ERP. Nicméně část obsahu platí pro systémy NetWeaver a S/4HANA obecně.
8.  Definování a rozhodnutí o přístupu k automatizovanému nasazení. Cílem automatizace po nasazení infrastruktury v Azure je nasadit deterministickým způsobem a získat deterministické výsledky. Spousta zákazníků používá skripty založené na Power shellu nebo rozhraní příkazového řádku. Existují ale různé Open Source technologie, které se dají použít k nasazení infrastruktury Azure pro SAP a dokonce i k instalaci softwaru SAP. Příklady najdete na webu GitHub:
    1.  [Automatizované nasazení SAP v cloudu Azure](https://github.com/Azure/sap-hana)
    2.  [Instalace SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definování pravidelné kontroly návrhu a nasazení tempo mezi vámi jako zákazníkem, systémovým integrátorem, společností Microsoft a dalšími zúčastněnými stranami

 
## <a name="pilot-phase-strongly-recommended"></a>Pilotní fáze (důrazně doporučeno)
 
Pilotní nasazení lze spustit před nebo souběžně s plánováním a přípravou projektu. Tuto fázi je také možné použít k testování přístupů a návrh provedených ve fázi plánování a přípravy. Pilotní fázi je možné roztáhnout na skutečný důkaz konceptů. Doporučuje se nastavit a ověřit úplné řešení HA/DR i návrh zabezpečení během pilotního nasazení. V některých případech se v této fázi můžou provádět testy škálovatelnosti. Jiní zákazníci používají nasazení systému SAP izolovaného prostoru jako pilotní fáze. Proto předpokládáme, že jste identifikovali systém, který chcete migrovat do Azure za účelem spuštění pilotního projektu.

1. Optimalizujte přenos dat do Azure. Vysoce závislé na přenosu případů zákazníka prostřednictvím [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) z místního prostředí bylo nejrychlejší, pokud má expresní okruh dostatečnou šířku pásma. S ostatními zákazníky budete moct přes Internet zjistit, jak rychleji
2. V případě migrace heterogenní platformy SAP zahrnuje export a import databázových dat, testování a optimalizaci fází exportu a importu. Pro velké migrace, které zahrnují SQL Server jako cílovou platformu, najdete doporučení [zde](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Přístup k monitorování migrace/SWPM můžete vzít v úvahu v případě, že při kombinaci migrace s upgradem na vydání SAP nepotřebujete kombinovaný proces upgradu nebo řešení [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) a splníte určité kombinace platforem pro zdrojové a cílové systémy DBMS jako zdokumentováno například v [Možnosti migrace databáze (DMO) SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportujte do zdroje, exportujte nahrávání souboru do Azure a importujte výkon.  Maximalizovat překryv mezi exportem a importem
   2.  Vyhodnoťte objem databáze mezi cílovou a cílovou platformou, aby odrážela velikost infrastruktury.    
   3.  Ověřit a optimalizovat časování 
3. Technické ověření 
   1. Typy virtuálních počítačů
      1.  Ověřte, jestli se prostředky v poznámkách o podpoře SAP, SAP HANAch hardwarových adresářích a službě SAP PAM znovu ověřují, a ujistěte se, že v podporovaných virtuálních počítačích pro Azure nejsou žádné změny, podporované verze operačního systému pro tyto typy virtuálních počítačů a podporované verze SAP a DBMS.
      2.  Znovu ověřte velikost vaší aplikace a infrastruktury, kterou jste nasadili v Azure. V případě přesunu stávajících aplikací můžete často odvodit potřebné SAP z používané infrastruktury a [webové stránky SAP benchmark](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) a porovnat je s čísly SAP uvedenými v poznámce sap Support [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Pamatujte také na [Tento článek](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) .
      3.  Vyhodnoťte a otestujte velikost vašich virtuálních počítačů Azure s ohledem na maximální propustnost úložiště a propustnost sítě u různých typů virtuálních počítačů, které jste zvolili ve fázi plánování. Data najdete v části:
          1.  [Velikosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Je důležité vzít v úvahu **maximální propustnost disku** neuloženého v mezipaměti pro určení velikosti
          2.  [Velikosti pro virtuální počítače se systémem Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Je důležité vzít v úvahu **maximální propustnost disku** neuloženého v mezipaměti pro určení velikosti
   2. Storage
      1.  Použijte [úložiště Azure SSD úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) jako minimum pro virtuální počítače, které představují vrstvy aplikací SAP a pro nasazení DBMS bez výkonu.
      2.  Doporučujeme, abyste [disky Azure HDD úrovně Standard](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) všeobecně nepoužívali.
      2.  Použití [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) pro všechny virtuální počítače s DBMS, na kterých je vzdálený výkon citlivý
      2.  Použití [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/)
      3.  Použijte Azure Akcelerátor zápisu pro DBMS protokolové jednotky s M-Series. Mějte na paměti, že omezení a použití akcelerátoru zápisu jsou popsány v [akcelerátor zápisu](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)
      4.  Pro různé typy DBMS si Projděte [obecnou dokumentaci k systému DBMS související s SAP a dokumentaci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) ke konkrétním systémům DBMS, na kterou se odkazuje v obecném dokumentu.
      5.  Další podrobnosti o SAP HANA najdete v SAP HANAch [konfiguracích a operacích infrastruktury v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) .
      6.  Nepřipojujte disky Azure data k virtuálnímu počítači Azure Linux pomocí ID zařízení. Místo toho použijte univerzálně jedinečný identifikátor (UUID). Buďte opatrní při použití grafických nástrojů k připojení datových disků Azure, například. Zkontrolujte položky v/etc/fstab a ujistěte se, že jsou disky připojené pomocí identifikátoru UUID.
          1.  Další podrobnosti najdete [zde](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Sítě
      1.  Testování a vyhodnocení vaší infrastruktury virtuální sítě a distribuce aplikací SAP napříč různými virtuálními sítěmi Azure nebo v nich
          1.  Vyhodnoťte přístup k architektuře virtuální sítě rozbočovače a paprsku nebo mikrosegmentaci v rámci jedné virtuální sítě Azure založené na
              1.  Náklady způsobené výměnou dat mezi [partnerskými virtuální sítě Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Pro kontrolu nákladů [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Výhodou rychlého odpojení partnerského vztahu mezi virtuálními sítěmi Azure a změnou NSG k izolaci podsítě v rámci virtuální sítě pro případy, kdy se aplikace nebo virtuální počítače hostované v podsíti virtuální sítě staly bezpečnostním rizikem
              3.  Centrální protokolování a auditování síťového provozu mezi místními, mimo World a virtuálním datovým centrem, které jste vytvořili v Azure
          2.  Vyhodnotit a testovat cestu k datům mezi aplikační vrstvou SAP a vrstvou SAP DBMS. 
              1.  Všechna umístění [síťových virtuálních zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) v cestě komunikace mezi aplikací SAP a vrstvou DBMS systémů SAP NetWeaver, Hybris nebo S/4HANA se vůbec nepodporují.
              2.  Umístění aplikační vrstvy SAP a SAP DBMS v různých virtuálních sítích Azure, které nejsou partnerské vztahy, se nepodporuje.
              3.  [Pravidla Azure ASG a NSG](https://docs.microsoft.com/azure/virtual-network/security-overview) se podporují pro definování tras mezi vrstvou aplikace SAP a vrstvou SAP DBMS.
          3.  Ujistěte se, že je na virtuálních počítačích, které se používají v aplikační vrstvě SAP a ve vrstvě SAP DBMS, povolené [síťové služby Azure akcelerované](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) . Mějte na paměti, že pro podporu akcelerovaných síťových služeb v Azure jsou potřeba různé úrovně operačního systému:
              1.  Windows Server 2012 R2 nebo novější verze
              2.  SUSE Linux 12 SP3 nebo novější verze
              3.  RHEL 7,4 nebo novější verze
              4.  Oracle Linux 7,5. Pomocí jádra RHCKL musí být verze 3.10.0-862.13.1. el7. Vyžaduje se použití nástroje Oracle UEK kernel verze 5.
          4.   Otestujte a vyhodnoťte latenci sítě mezi virtuálním počítačem aplikační vrstvy SAP a virtuálním počítačem DBMS podle poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a poznámkového oddělení podpory SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky z pokynů k latenci sítě pro SAP poznámky k podpoře [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném a dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno [tady](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) .
          5.   Ujistěte se, že nasazení interního nástroje jsou nastavená tak, aby používala přímé vrácení serveru. Toto nastavení omezí latenci v případech, kdy se Azure ILBs používá pro konfigurace s vysokou dostupností na vrstvě DBMS.
          6.   Pokud používáte Azure Load Balancer ve spojení s hostovanými operačními systémy Linux, ověřte, zda je parametr sítě Linux **net. IPv4. TCP _timestamps** nastaven na **hodnotu 0**. Před doporučeními ve starších verzích SAP Upozorňujeme [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Poznámka SAP je mezitím aktualizována tak, aby odrážela skutečnost, že parametr musí být nastaven na hodnotu 0, aby fungoval ve spojení s nástroji pro vyrovnávání zatížení Azure.
          7.   Zvažte použití [skupiny umístění služby Azure Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , jak je popsáno v článku [skupiny umístění blízkosti Azure pro zajištění optimální latence sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md) , abyste získali optimální latenci sítě.
   4. Nasazení s vysokou dostupností a zotavením po havárii. 
      1. Pokud nasadíte vrstvu aplikace SAP bez definování konkrétní zóny dostupnosti Azure, zajistěte, aby všechny virtuální počítače, na kterých běží instance dialogového okna SAP, nebo instance middlewaru v jednom systému SAP byly nasazeny v [sadě dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   V případě, že nepotřebujete vysokou dostupnost pro centrální služby SAP a DBMS, můžou se tyto virtuální počítače nasadit do stejné skupiny dostupnosti jako aplikační vrstva SAP.
      2. Pokud chráníte centrální služby SAP a vrstvu DBMS pro zajištění vysoké dostupnosti s pasivními replikami, měli byste tyto dva uzly pro centrální služby SAP v jedné samostatné skupině dostupnosti a v obou uzlech DBMS v jiné skupině dostupnosti.
      3. Pokud nasadíte do Zóny dostupnosti Azure, nemůžete využít skupiny dostupnosti. Museli byste ale mít jistotu, že nasadíte aktivní a pasivní uzly centrální služby do dvou různých Zóny dostupnosti, které zobrazují nejnižší latenci mezi zónami.
         1.   Mějte na paměti, že při vytváření clusterů s podporou převzetí služeb při selhání systému Windows nebo Pacemaker pro vrstvu služby SAP a Central Services v rámci Zóny dostupnosti potřebujete použít [Azure Standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) . [Základní Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) se nedají použít pro nasazení na oblast. 
   5. Nastavení časového limitu
      1. Zkontrolujte, jestli se v SAP NetWeaver sleduje různé instance SAP, a ujistěte se, že se neúčtují žádné přerušení připojení mezi serverem fronty a pracovními procesy SAP. Tyto přerušení připojení je možné vyhnout nastavením těchto dvou parametrů registru:
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – viz také [Tento článek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – viz také [Tento článek](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Pokud se chcete vyhnout časovým limitům GUI mezi místními nasazenými rozhraními GUI SAP a aplikačními vrstvami SAP nasazenými v Azure, ověřte, jestli jsou ve výchozím nastavení nastavené následující parametry (PFL) nebo profil instance:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/naživu = 20
      3. Aby nedošlo k přerušení navázání připojení mezi procesem zařazení do fronty SAP a SAP workprocesse, parametr enque/encni/set_so_keepalive musí být nastaven na hodnotu "true". Podívejte se také na [poznámku SAP #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Pokud používáte konfiguraci clusteru s podporou převzetí služeb při selhání Windows, ujistěte se, že je čas reakce na nereagující uzly správně nastavený pro Azure. V článku [vyladění prahové hodnoty sítě clusteru s podporou převzetí služeb při selhání](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) se zobrazí seznam parametrů a vlivu na Sensitivities Za předpokladu, že uzly clusteru jsou ve stejné podsíti, je třeba změnit následující parametry:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Otestujte postupy pro vysokou dostupnost a zotavení po havárii.
   1. Simulace převzetí služeb při selhání vypnutím virtuálních počítačů (hostovaného operačního systému Windows) nebo přepnutím operačních systémů do nouzového režimu (hostovaný operační systém Linux), abyste zjistili, jestli vaše konfigurace převzetí služeb při selhání fungují tak, jak jsou navržené. 
   2. Změřte dobu potřebnou k provedení převzetí služeb při selhání. Pokud doba trvá příliš dlouho, zvažte následující:
      1.   Pro urychlení převzetí služeb při selhání použijte pro SUSE Linux zařízení SBD namísto agenta pro oplocení Azure.
      2.   Pro SAP HANA, pokud opakované načtení dat trvá příliš dlouho, zvažte zřízení větší šířky pásma úložiště.
   3. Test zálohování/obnovení sekvence a časování a ladění v případě potřeby. Ujistěte se, že nejsou pouze časy zálohování dostatečné. Také proveďte test obnovení a proveďte časování pro aktivity obnovení. Ujistěte se, že časy obnovení jsou v rámci RTO SLA, kde vaše RTO spoléhá na proces obnovení databáze nebo virtuálního počítače.
   4. Testování napříč oblastí funkce a architektura DR
5. Kontroly zabezpečení
   1.  Otestuje platnost implementované architektury Azure role based Access (RBAC). Cílem je oddělení a omezení přístupu a oprávnění různých týmů. Jako příklad by členové týmu SAP mohli nasazovat virtuální počítače a přiřazovat disky z Azure Storage do dané virtuální sítě Azure. Tým SAP by však neměl být schopný vytvářet vlastní virtuální sítě ani měnit nastavení stávajících virtuálních sítí. Na druhé straně by členové týmu sítě neměli moci nasazovat virtuální počítače do virtuálních sítí, kde jsou spuštěny aplikace SAP a virtuální počítače DBMS. Ani by členové síťového týmu mohli měnit atributy virtuálních počítačů nebo dokonce odstraňovat virtuální počítače nebo disky.  
   2.  Ověřte, že pravidla [NSG a ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) pracují podle očekávání a chrání chráněné prostředky.
   3.  Ujistěte se, že jsou šifrované všechny prostředky, které je třeba šifrovat. Definujte a spusťte procesy pro zálohování certifikátů, ukládání a přístup k těmto certifikátům a obnovení šifrovaných entit. 
   4.  Použití [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) a/nebo pro disky s operačním systémem, pokud je to možné z bodu podpory operačního systému v zobrazení
   5.  Ověřte, že se nepoužilo příliš mnoho vrstev šifrování. Má omezené použití služby Azure Disk Encryption a pak nad jednou z transparentních metod šifrování databáze DBMS.
6. Testování výkonnosti
   1.  V SAP založené na trasování a měřeních SAP Porovnejte prvních 10 online sestav s aktuální implementací, pokud je to možné. 
   2.  V SAP založené na trasování a měřeních SAP Porovnejte horní 10 dávkových úloh s aktuální implementací, pokud je to možné. 
   3.  V SAP založené na trasování a měřeních SAP Porovnejte přenosy dat prostřednictvím rozhraní do systému SAP. Zaměřte se na rozhraní, u kterých víte, že přenos probíhá mezi různými umístěními, třeba z místního prostředí do Azure. 


## <a name="non-production-phase"></a>Fáze mimo produkční prostředí 
V této fázi předpokládáme, že po úspěšném pilotním projektu nebo ověření koncepce začínáte do Azure nasazovat systémy, které neprovozují SAP. Všechna učení a zkušenosti z kontroly konceptů by měly být přizpůsobeny takovému nasazení. Všechna kritéria a kroky uvedené v rámci ověření koncepce se vztahují i na tento typ nasazení. V této fázi obvykle nasazujete vývojové systémy, systémy testování částí a systémy testů Business regrese do Azure. Doporučuje se, aby aspoň jeden neprodukční systém na jednom řádku aplikace SAP měl úplnou konfiguraci s vysokou dostupností, která bude mít budoucí produkční systém. V rámci této fáze je potřeba vzít v úvahu další kroky:  

1.  Před přesunem systémů z původní platformy do Azure Shromážděte data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a data IOPS. Hlavně z jednotek vrstvy DBMS, ale také z jednotek aplikační vrstvy. Také změřit latenci sítě a úložiště.
2.  Poznamenejte si vzory času využití dostupnosti systémů. Cílem je zjistit, jestli je potřeba, aby neprodukční systémy byly nepřetržitě dostupné nebo jestli existují neprodukční systémy, které se dají vypnout v určitých fázích týdne nebo měsíce.
3.  Otestujte a definujte, jestli chcete vytvořit vlastní image operačního systému pro virtuální počítače v Azure, nebo jestli chcete použít image z Galerie imagí Azure. Pokud používáte image mimo galerii Azure, ujistěte se, že jste provedli správnou image, která odráží smlouvu o podpoře s vaším dodavatelem operačního systému. V případě některých dodavatelů operačních systémů nabízí Galerie Azure vlastní image licencí. Pro ostatní image operačních systémů je podpora zahrnutá v ceně uvedené v Azure. Pokud se rozhodnete vytvořit vlastní image operačního systému, najdete dokumentaci v těchto článcích:
    1.  Na základě [této dokumentace](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) můžete vytvořit zobecněnou bitovou kopii virtuálního počítače s Windows nasazenou v Azure.
    2.  Na základě [této dokumentace](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) můžete vytvořit zobecněnou bitovou kopii virtuálního počítače se systémem Linux nasazeného v Azure.
3.  Pokud používáte image SUSE a Red Hat Linux z Galerie virtuálních počítačů Azure, musíte použít image pro SAP poskytované dodavateli pro Linux v galerii virtuálních počítačů Azure.
4.  Ujistěte se, že splňujete požadavky podpory, které SAP obsahuje pro smlouvy o podpoře Microsoftu. Informace najdete v poznámkách k podpoře SAP [#2015553](https://launchpad.support.sap.com/#/notes/2015553). Pro velké instance HANA si přečtěte téma [požadavky na registraci](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements) dokumentu.
4.  Ujistěte se, že přípraví uživatelé získají [oznámení o plánované údržbě](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), takže můžete zvolit výpadky a restartování virtuálních počítačů v čase.
5.  Pro nové funkce, které se můžou vztahovat na vaše nasazení, se neustále kontrolují dokumentace Microsoftu k prezentacím Microsoftu na kanály, jako je [channel9](https://channel9.msdn.com/) .
6.  Zkontrolujte poznámky SAP související s Azure, jako je například Poznámka k podpoře [#1928533](https://launchpad.support.sap.com/#/notes/1928533) pro nové SKU virtuálních počítačů nebo nově podporovanou verzi operačního systému a systému DBMS. Porovnejte nové typy virtuálních počítačů s dřívějšími typy virtuálních počítačů s cenami, abyste mohli nasadit virtuální počítače s nejlepším poměrem cen a výkonu.
7.  Ověřte, že SAP HANA se v těchto virtuálních počítačích nenašly žádné změny v podporovaných virtuálních počítačích pro Azure, podporované verze operačního systému a podporované verze SAP a DBMS.
8.  Podívejte se [sem](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro nové SKU s certifikací Hana v Azure a Porovnejte ceny s těmi, které jste naplánovali a nakonec změnili, abyste získali jednotky s vyšší cenou za cenovou dávku 
9.  Přizpůsobte skripty pro nasazení a využijte nové typy virtuálních počítačů a zahrňte nové funkce Azure, které chcete použít.
10. Po nasazení infrastruktury, testování a vyhodnocení latence sítě mezi virtuálním počítačem aplikační vrstvy SAP a systémem DBMS podle poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a poznámkové oddělení podpory SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky z pokynů k latenci sítě pro SAP poznámky k podpoře [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném a dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno [zde](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance). Ujistěte se, že žádná omezení uvedená v části [požadavky pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) a [Konfigurace infrastruktury SAP Hana a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) se vztahují na vaše nasazení.
11. Ujistěte se, že virtuální počítače se nasazují pomocí správné [skupiny umístění služby Azure Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md) .
11. Proveďte všechny ostatní kontroly, jak je uvedeno ve fázi zkušební využití konceptu před použitím úlohy.
12. Jak zatížení platí, poznamenejte si spotřebu prostředků těchto systémů v Azure a porovnejte je se záznamy, které jste získali ze své staré platformy. Pokud vidíte, že máte větší rozdíly, upravte velikost virtuálních počítačů v budoucích nasazeních. Pamatujte na to, že v případě, že se možnost, úložiště a šířka pásma sítě virtuálního počítače sníží, bude taky:
    1.  [Velikosti virtuálních počítačů s Windows v Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [Velikosti virtuálních počítačů s Linuxem v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Pracujte na funkcích a procesech systémové kopie. Cílem je usnadnit kopírování vývojového systému nebo testovacího systému, takže tyto projektové týmy můžou rychle získat nové systémy. Vezměte v úvahu [SAP Lama](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) jako nástroj provádějící takové úkoly.
14. Optimalizujte a doladitte přístup, oprávnění a procesy založené na rolích týmu, abyste se ujistili, že máte na jednu stranu oddělené poplatky. Na druhé straně chcete, aby všechny týmy povolily provádění svých úkolů v infrastruktuře Azure.
15. Cvičení, testování a dokumentace postupů vysoké dostupnosti a zotavení po havárii, které zaměstnancům umožní provádět takové úkoly. Identifikujte nedostatky a přizpůsobte nové funkce Azure, které integrujete do nasazení.

 
## <a name="production-preparation-phase"></a>Fáze přípravy výroby 
V této fázi chcete shromažďovat všechna prostředí a učení o nasazeních mimo provoz a použít je v budoucích produkčních nasazeních. Kromě fází musíte také připravit práci mezi vaším aktuálním hostováním a Azure. 

1.  Před přechodem do Azure je potřeba, abyste procházeli potřebnými upgrady pro vydání SAP pro produkční systémy
2.  Souhlasím s vlastníky podnikání na funkčních a obchodních testech, které je potřeba provést po migraci produkčního systému
    1.  Ujistěte se, že všechny tyto testy jsou spouštěny se zdrojovými systémy v aktuálním umístění hostování. Chcete zabránit tomu, aby se testy prováděly poprvé při přesunu systému do Azure
2.  Otestujte proces migrace z provozu do Azure. V případě, že nedojde k přesunu všech produkčních systémů do Azure ve stejném časovém rámci, skupiny sestavení produkčních systémů, které musí být ve stejném umístění hostování. Cvičení a testování migrace dat Seznam běžných metod jako:
    1.  Použití metod DBMS, jako je zálohování a obnovení v kombinaci s SQL Server AlwaysOn, replikace systému HANA nebo přesouvání protokolů do počáteční hodnoty a synchronizace obsahu databáze v Azure
    2.  Použití zálohování a obnovení pro menší databáze
    3.  Použití monitorování migrace SAP implementovaného do nástroje SAP SWPM k provádění heterogenních migrací
    4.  Pokud potřebujete kombinovat s upgradem verze SAP, použijte proces [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) . Pamatujte, že nejsou podporovány všechny kombinace mezi zdrojovým a cílovým DBMS. Další informace najdete v poznámkách k podpoře SAP pro různé verze DMO. Například [možnost migrace databáze (DMO) SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Otestujte, jestli je přenos dat přes Internet nebo prostřednictvím ExpressRoute lepší v propustnosti pro případ, že budete potřebovat přesunout zálohy nebo soubory exportu SAP. Pro případ přesunu dat prostřednictvím Internetu možná budete muset změnit některá pravidla zabezpečení NSG/ASG, která je potřeba pro budoucí produkční systémy.
3.  Před přesunem systémů z původní platformy do Azure Shromážděte data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a data IOPS. Hlavně z jednotek vrstvy DBMS, ale také z jednotek aplikační vrstvy. Také změřit latenci sítě a úložiště.
4.  Ověřte, že SAP HANA se v těchto virtuálních počítačích nenašly žádné změny v podporovaných virtuálních počítačích pro Azure, podporované verze operačního systému a podporované verze SAP a DBMS. 
4.  Přizpůsobte skripty nasazení na nejnovější změny, které jste určili na typech virtuálních počítačů a funkcích Azure.
5.  Po nasazení infrastruktury a aplikace projdete řadou kontrol, aby bylo možné ověřit:
    1.  Byly nasazeny správné typy virtuálních počítačů se správnými atributy a velikostmi úložiště.
    2.  Ověřte, že jsou virtuální počítače na správné a požadované verze a opravy operačního systému a jsou Uniform.
    3.  Ověřte, že posílení zabezpečení virtuálních počítačů je podle potřeby a Uniform
    4.  Ověřte, že jsou nainstalované a nasazené správné verze a opravy aplikací.
    5.  Virtuální počítače se nasadily do skupin dostupnosti Azure jako plánované.
    6.  Služba Azure Premium Storage se použila pro citlivé disky s latencí nebo kde se vyžaduje [smlouva SLA s jedním virtuálním počítačem 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) .
    7.  Kontrolovat správné nasazení Akcelerátor zápisu Azure
        1.  Ujistěte se, že se v rámci virtuálních počítačů, prostorů úložiště nebo sad prokládaných disků správně vytvořily na discích, které vyžadují podporu Azure Akcelerátor zápisu.
            1.  Ověření [Konfigurace softwarového pole RAID v systému Linux](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  Ověřte [konfiguraci LVM na virtuálním počítači Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) .
    8.  Služby [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) se používaly výhradně
    9.  Virtuální počítače se nasadily do správných skupin dostupnosti a Zóny dostupnosti
    10. Ujistěte se, že je na virtuálních počítačích, které se používají v aplikační vrstvě SAP a ve vrstvě SAP DBMS, povolené [síťové služby Azure akcelerované](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) .
    11. V komunikačních cestách mezi aplikací SAP a vrstvou DBMS pro SAP NetWeaver, Hybris nebo S/4HANA založené systémy SAP nejsou žádné umístění [virtuálních síťových zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) .
    12. Pravidla ASG a NSG umožňují komunikaci podle potřeby a plánované a blokující komunikace, pokud je to potřeba.
    13. Nastavení časového limitu, jak bylo popsáno dříve, bylo nastaveno správně.
    14. Ujistěte se, že virtuální počítače se nasazují pomocí správné [skupiny umístění služby Azure Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) , jak je popsáno v článku [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md) .
    15. Otestujte a vyhodnoťte latenci sítě mezi virtuálním počítačem aplikační vrstvy SAP a virtuálním počítačem DBMS podle poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a poznámkového oddělení podpory SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky z pokynů k latenci sítě pro SAP poznámky k podpoře [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném a dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno [tady](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) .
    15. Ověřte, jestli je v případě potřeby nasazené šifrování, a s nezbytnou metodou šifrování.
    16. Ověřte, jestli se rozhraní a jiné aplikace můžou připojit k nově nasazené infrastruktuře.
6.  Vytvořte PlayBook, který se bude chovat při plánované údržbě Azure. Definujte pořadí systémů a virtuálních počítačů, které se mají restartovat v případě plánované údržby.
    

## <a name="go-live-phase"></a>Přejít do živé fáze
V případě fáze přechodu do živého provozu je nutné se ujistit, že budete postupovat podle playbooky, které jste vyvinuli v předchozích fázích. Proveďte kroky, které jste otestovali a proučeni. V konfiguracích a procesech nepřijímejte poslední změny v minutách. Kromě toho platí následující míry:

1. Ověřte, zda Azure Portal monitorování a jiné nástroje monitorování fungují.  Doporučené nástroje jsou Perfmon (Windows) nebo správní oblast (Linux): 
    1.  Čítače procesoru 
        1.  Průměrná doba využití procesoru – celkem (všechny PROCESORy)
        2.  Průměrná doba využití procesoru – každý jednotlivý procesor (tedy 128 procesorů na virtuálním počítači s m128)
        3.  Jádro času procesoru – každý jednotlivý procesor
        4.  Uživatel času procesoru – každý jednotlivý procesor
    5.  Memory (Paměť) 
        1.  Volná paměť
        2.  Paměťová stránka za sekundu
        3.  Nedostatek paměti na stránce/s
    4.  Disk 
        1.  Čtení z disku v KB/s – na jednotlivé disky 
        2.  Čtení z disku/s – na jednotlivý disk
        3.  Čtení z disku MS/Read – na jednotlivý disk
        4.  Zápisy na disk KB/s – na jeden disk 
        5.  Zápis na disk/s – na jednotlivý disk
        6.  Zápis na disk MS/Read – na jednotlivý disk
    5.  Síť 
        1.  Síťové pakety za sekundu
        2.  Síťové pakety za sekundu
        3.  Síťové znalostní báze za sekundu
        4.  Síťové maximum KB/s 
2.  Po migraci dat proveďte všechny ověřovací testy, ke kterým jste se dohodli s vlastníky firmy. Přijměte jenom výsledky ověřovacího testu, u kterých máte výsledky pro původní zdrojové systémy.
3.  Ověřte, zda rozhraní fungují a zda mohou jiné aplikace komunikovat s nově nasazenými provozními systémy.
4.  Ověření systému přenosů a oprav přes SAP Transaction STMS
5.  Zálohy databáze provádějte po uvolnění systému pro produkční prostředí.
6.  Provedení záloh virtuálních počítačů pro virtuální počítače aplikační vrstvy SAP po uvolnění systému pro produkční prostředí
7.  Pro systémy SAP, které nebyly součástí aktuální fáze přechodu do provozu, ale komunikují se systémy SAP, které jste přesunuli do Azure, v této fázi pro přechod do provozu je potřeba resetovat vyrovnávací paměť názvu hostitele v SM51. Tento krok odebere staré IP adresy v mezipaměti přidružené k názvům instancí aplikace, které jste přesunuli do Azure.  


## <a name="post-production"></a>Vystavení výroby
V této fázi se jedná o monitorování, provoz a správu systému. Z pohledu SAP se použijí běžné úkoly, které jste museli provést s vaším původním hostováním umístění. Úlohy specifické pro Azure, které chcete provést:

1. Analýza faktur Azure pro vysoce zpoplatněné systémy
2. Optimalizace ceny a výkonu na straně virtuálních počítačů a na straně úložiště
3. Optimalizace časových systémů se dá vypnout.  


## <a name="next-steps"></a>Další kroky
Projděte si dokumentaci:

- [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení Azure Virtual Machines pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

