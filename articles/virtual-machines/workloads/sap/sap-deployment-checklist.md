---
title: Kontrolní seznam pro plánování a nasazení úloh SAP | Microsoft Docs
description: Kontrolní seznam pro plánování nasazení úloh SAP do Azure a nasazení úloh
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 51bfd632e854132be27a7b971cf03e9fe5b00138
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102504299"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení

Tento kontrolní seznam je určený pro zákazníky, kteří přesunují aplikace SAP NetWeaver, S/4HANA a Hybris do infrastruktury Azure jako službu. Po celou dobu trvání projektu by měl partner zákazníka nebo SAP zkontrolovat kontrolní seznam. Je důležité si uvědomit, že mnohé z kontrol jsou dokončeny na začátku projektu a ve fázi plánování. Po dokončení nasazení se můžou jednoduše měnit nasazené infrastruktury Azure nebo vydání softwaru SAP.

Zkontrolujte kontrolní seznam na klíčových milníkech během projektu. To vám umožní detekovat malé problémy dřív, než se stanou velkými problémy. Budete mít také dostatek času na reanalýzování a testování nezbytných změn. Neberte v úvahu tento kontrolní seznam dokončen. V závislosti na vaší situaci může být nutné provést mnoho dalších kontrol.

Kontrolní seznam neobsahuje úlohy nezávislé na Azure. Například rozhraní aplikace SAP se mění během přesunu na platformu Azure nebo poskytovatele hostingu.

Tento kontrolní seznam se dá použít i pro systémy, které už jsou nasazené. Od nasazení se možná přidaly nové funkce, například Akcelerátor zápisu a Zóny dostupnosti, a nové typy virtuálních počítačů. Proto je vhodné pravidelně kontrolovat kontrolní seznam, abyste se ujistili, že máte na platformě Azure informace o nových funkcích.

## <a name="project-preparation-and-planning-phase"></a>Fáze přípravy a plánování projektu
V průběhu této fáze naplánujete migraci úlohy SAP na platformu Azure. V průběhu této fáze je třeba vytvořit následující dokumenty a definovat a diskutovat o následujících prvcích migrace:

1. Dokument návrhu vysoké úrovně. Tento dokument by měl obsahovat:
    - Aktuální inventář komponent a aplikací SAP a cílový inventář aplikací pro Azure.
    - Matice přiřazení zodpovědnosti (RACI), která definuje odpovědnost a přiřazení zúčastněných stran. Začněte na vysoké úrovni a pracujte na podrobnějších úrovních v rámci plánování a první nasazení.
    - Architektura řešení vysoké úrovně.
    - Rozhodnutí o tom, na které oblasti Azure se mají nasadit. Podívejte se na [seznam oblastí Azure](https://azure.microsoft.com/global-infrastructure/regions/). Informace o tom, které služby jsou dostupné v jednotlivých oblastech, najdete v tématu [Dostupné produkty](https://azure.microsoft.com/global-infrastructure/services/)v jednotlivých oblastech.
    - Síťová architektura pro připojení z místního prostředí k Azure. Začněte se seznámení s [plánem Virtual datacentra pro Azure](/azure/architecture/vdc/).
    - Principy zabezpečení pro provoz obchodních dat s vysokým dopadem v Azure. Pokud se chcete dozvědět o zabezpečení dat, začněte s [dokumentací k zabezpečení Azure](../../../security/index.yml).
2.  Technický návrh dokumentu. Tento dokument by měl obsahovat:
    - Blokový diagram pro řešení.
    - Určení velikosti výpočetních, úložných a síťových komponent v Azure. Velikost pro SAP pro virtuální počítače Azure najdete v tématu [SAP 
    -  Poznámka #1928533] ( https://launchpad.support.sap.com/#/notes/1928533) .
    - Provozní kontinuita a architektura zotavení po havárii.
    - Podrobné informace o verzích sad support pack operačního systému, DB, jádra a SAP Nemusí nutně platit, že všechny verze operačního systému podporované SAP NetWeaver nebo S/4HANA se podporují na virtuálních počítačích Azure. Totéž platí pro verze DBMS. Zkontrolujte následující zdroje a zarovnejte je a v případě potřeby upgradujte verze SAP, verze DBMS a vydané verze operačních systémů, aby se zajistila podpora SAP a Azure. Aby bylo možné získat plnou podporu pro SAP a Microsoft, musíte mít k dispozici kombinace vydaných verzí, které podporuje SAP a Azure. V případě potřeby musíte naplánovat upgrade některých softwarových součástí. Další podrobnosti o podporovaném softwaru SAP, OS a DBMS jsou popsané tady:
        - [Poznámka k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tato poznámka definuje minimální verze operačního systému podporované na virtuálních počítačích Azure. Definuje také minimální verze databází, které jsou požadovány pro většinu databází, které nejsou HANA. Nakonec poskytuje určení velikosti SAP pro typy virtuálních počítačů Azure podporovaných v SAP.
        - [Poznámka k podpoře SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Tato poznámka definuje zásady podpory pro službu Azure Storage a vztah podpory, který je potřeba u Microsoftu.
        - [Poznámka k podpoře SAP #2039619](https://launchpad.support.sap.com/#/notes/2039619). Tato poznámka definuje matrici podpory Oracle pro Azure. Oracle podporuje v Azure pro úlohy SAP jenom Windows a Oracle Linux jako hostované operační systémy. Tento příkaz podpory platí i pro vrstvu aplikace SAP, která spouští instance SAP. Oracle ale nepodporuje vysokou dostupnost pro centrální služby SAP v Oracle Linux prostřednictvím Pacemaker. Pokud potřebujete vysokou dostupnost pro ASCS na Oracle Linux, musíte použít sadu s ochranou Suite pro Linux. Podrobná data o certifikaci SAP najdete v poznámkách k podpoře SAP [#1662610 – podrobnosti o podpoře pro sadu s ochranou Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610). Pro Windows se podporuje řešení clusteringu s podporou převzetí služeb při selhání Windows serveru SAP pro služby SAP Central Services společně s Oracle jako s vrstvou DBMS.
        - [Poznámka k podpoře SAP #2235581](https://launchpad.support.sap.com/#/notes/2235581). Tato poznámka poskytuje maticovou podporu pro SAP HANA v různých verzích operačního systému.
        - SAP HANA – podporované virtuální počítače Azure a [velké instance Hana](./hana-overview-architecture.md) jsou uvedené na [webu SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure).
        - [Matice dostupnosti produktu SAP](https://support.sap.com/en/).
        - [Poznámka k podpoře SAP #2555629 – SAP HANA 2,0 dynamické vrstvení – podpora hypervisoru a cloudu](https://launchpad.support.sap.com/#/notes/2555629)
        - [Poznámka k podpoře SAP #1662610 – podrobnosti o podpoře pro sadu s ochranou Suite pro Linux](https://launchpad.support.sap.com/#/notes/1662610)
        - Poznámky SAP pro jiné produkty specifické pro SAP.     
    - Použití konfigurace clusteru s více identifikátory SID pro služby SAP Central Services je podporované v hostovaných operačních systémech Windows, SLES a RHEL v Azure. Mějte na paměti, že poloměr vysokého počtu ASCS/SCS se dá zvýšit na takový cluster s více identifikátory SID. Dokumentaci k příslušnému scénáři hostujícího operačního systému najdete v těchto článcích:
        - [Vysoká dostupnost ASCS/SCS instance SAP pomocí clusteringu s podporou převzetí služeb při selhání Windows serveru a sdíleného disku v Azure](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Vysoká dostupnost ASCS/SCS instance SAP s Clustering s podporou převzetí služeb při selhání Windows serveru a sdílenou složkou v Azure](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro Průvodce pro aplikace SAP s více SID](./high-availability-guide-suse-multi-sid.md)
        - [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v Red Hat Enterprise Linux pro Průvodce pro aplikace SAP s více SID](./high-availability-guide-rhel-multi-sid.md)
    - Architektura vysoké dostupnosti a zotavení po havárii.
        - Na základě RTO a RPO definujte, co má architektura vysoké dostupnosti a zotavení po havárii vypadat jako.
        - V případě vysoké dostupnosti v rámci zóny si přečtěte, co má požadovaný systém DBMS nabídnout v Azure. Většina balíčků DBMS nabízí synchronní metody synchronního aktivního pohotovostního režimu, které doporučujeme pro produkční systémy. Také si projděte dokumentaci týkající se SAP pro různé databáze a začněte s [důležitými informacemi o nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md) a související dokumenty.
           Použití clusteringu s podporou převzetí služeb při selhání Windows serveru s konfigurací sdíleného disku pro vrstvu DBMS, například [popsané pro SQL Server](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), není podporované. Místo toho použijte řešení jako:
           - [AlwaysOn SQL Serveru](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](../oracle/configure-oracle-dataguard.md)
           - [Replikace systému HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - V případě zotavení po havárii napříč oblastmi Azure si Projděte řešení, která nabízí různí dodavatelé systému DBMS. Většina z nich podporuje asynchronní replikaci nebo přesouvání protokolů.
        - Pro vrstvu aplikace SAP určete, jestli budete spouštět testovací systémy pro podnikání, které jsou v ideálním případě repliky nasazení v produkčním prostředí, ve stejné oblasti Azure nebo v oblasti zotavení po havárii. V druhém případě můžete cílit na systém pro podnikání jako cíl DR pro produkční nasazení.
        - Pokud se rozhodnete neuvádět neprodukční systémy na webu DR, vyhledejte Azure Site Recovery jako metodu replikace aplikační vrstvy SAP do oblasti Azure DR. Další informace najdete v tématu [Nastavení zotavení po havárii pro nasazení aplikace NETWEAVER SAP ve více vrstvách](../../../site-recovery/site-recovery-sap.md).
        - Pokud se rozhodnete použít kombinovanou konfiguraci HADR pomocí [zóny dostupnosti Azure](../../../availability-zones/az-overview.md), Seznamte se s oblastmi Azure, kde jsou dostupné zóny dostupnosti. Také Vezměte v úvahu omezení, která lze zavést zvýšením latencí sítě mezi dvěma Zóny dostupnosti.  
3.  Inventář všech rozhraní SAP (SAP a non-SAP).
4.  Návrh základních služeb. Tento návrh by měl zahrnovat následující položky:
    - Návrh služby Active Directory a DNS.
    - Topologie sítě v rámci Azure a přiřazování různých systémů SAP.
    - Struktura [řízení přístupu na základě role v Azure (Azure RBAC)](../../../role-based-access-control/overview.md) pro týmy, které spravují infrastrukturu a aplikace SAP v Azure.
    - Topologie skupiny prostředků.
    - [Strategie označování](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing).
    - Zásady vytváření názvů pro virtuální počítače a další součásti infrastruktury nebo logické názvy.
5.  Kontrakt Microsoft Professional nebo Premier Support. Pokud máte s Microsoftem uzavřenou smlouvu o podpoře Premier, identifikujte svého zástupce TAM (Technical Account Manager) společnosti Microsoft. Požadavky na podporu SAP najdete v [poznámkách k podpoře sap #2015553](https://launchpad.support.sap.com/#/notes/2015553).
6.  Počet předplatných Azure a kvóta jádra pro odběry. [Otevřete žádosti o podporu pro zvýšení kvót předplatných Azure](../../../azure-portal/supportability/resource-manager-core-quotas-request.md) podle potřeby.
7.  Omezení dat a plán migrace dat pro migraci dat SAP do Azure. Pro systémy SAP NetWeaver má SAP pokyny, jak omezit objem velkých objemů dat. Viz [Tato příručka](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) pro systém SAP o správě dat v systému SAP ERP. Část obsahu platí i pro systémy NetWeaver a S/4HANA obecně.
8.  Přístup k automatizovanému nasazení. Cílem automatizace nasazení infrastruktury v Azure je nasadit deterministický způsob a získat deterministické výsledky. Spousta zákazníků používá PowerShell nebo skripty založené na rozhraní příkazového řádku. Existují však různé Open Source technologie, které můžete použít k nasazení infrastruktury Azure pro SAP a dokonce i k instalaci softwaru SAP. Příklady najdete na GitHubu:
    - [Automatizované nasazení SAP v cloudu Azure](https://github.com/Azure/sap-hana)
    - [Instalace SAP HANA](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Definujte normální návrh a tempo kontrolu nasazení mezi vámi jako zákazníkem, integrátorem systému, společností Microsoft a dalšími zúčastněnými stranami.


## <a name="pilot-phase-strongly-recommended"></a>Pilotní fáze (důrazně doporučeno)
 
Pilotní projekt můžete spustit před nebo během plánování a přípravy projektu. Zkušební fázi můžete použít také k testování přístupů a návrhů provedených během fáze plánování a přípravy. A můžete rozbalit fázi pilotního nasazení, abyste si zajistili skutečnou zkoušku konceptu.

Doporučujeme, abyste nastavili a ověřili úplné řešení HADR a návrh zabezpečení během pilotního nasazení. Někteří zákazníci provádějí v této fázi testy škálovatelnosti. Jiní zákazníci používají nasazení systému SAP izolovaného prostoru jako pilotní fáze. Předpokládáme, že už jste identifikovali systém, který chcete migrovat do Azure pro pilotní nasazení.

1. Optimalizujte přenos dat do Azure. Optimální volba je vysoce závislá na konkrétním scénáři. Přenos z místního prostředí prostřednictvím [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) je nejrychlejší, pokud okruh ExpressRoute má dostatečnou šířku pásma. V jiných scénářích je přenos přes Internet rychlejší.
2. Heterogenní migrace platforem SAP, která zahrnuje export a import dat, testování a optimalizaci fází exportu a importu. V případě rozsáhlých migrací, ve kterých je SQL Server cílovou platformou, můžete najít [doporučení](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070). Pokud nepotřebujete kombinovaný upgrade pro vydání, můžete použít nástroj Migrace/SWPM. Proces [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) můžete použít při kombinaci migrace s upgradem verze SAP. K tomu je potřeba splnit určité požadavky na kombinaci zdrojové a cílové platformy DBMS. Tento proces je popsán v [části možnost migrace databáze (DMO) SUM 2,0 SP03](https://launchpad.support.sap.com/#/notes/2631152).
   1.  Exportujte do zdroje, exportujte nahrávání souborů do Azure a importujte výkon. Maximalizovat překryv mezi exportem a importem.
   2.  Vyhodnoťte objem databáze na cílové a cílové platformě pro účely určení velikosti infrastruktury.
   3.  Ověří a optimalizuje časování.
1. Technické ověření.
   1. Typy virtuálních počítačů.
        - Projděte si poznámky o podpoře SAP, v adresáři SAP HANA hardwaru a znovu v modulu PAM služby SAP. Ujistěte se, že neexistují žádné změny podporovaných virtuálních počítačů pro Azure, podporované verze operačního systému pro tyto typy virtuálních počítačů a podporované verze SAP a DBMS.
        - Znovu ověřte velikost vaší aplikace a infrastruktury, kterou jste nasadili v Azure. Pokud přesouváte stávající aplikace, můžete je často odvodit z používané infrastruktury a [webové stránky SAP benchmark](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) a porovnat je s čísly SAP uvedenými v [poznámce SAP Support #1928533](https://launchpad.support.sap.com/#/notes/1928533). Tento článek také zapamatujte [na hodnocení SAP](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) .
        - Vyhodnoťte a otestujte velikost vašich virtuálních počítačů Azure s ohledem na maximální propustnost úložiště a propustnost sítě u typů virtuálních počítačů, které jste zvolili během fáze plánování. Data můžete najít tady:
           -  [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pro určení velikosti je důležité zvážit *maximální propustnost disku* , který není v mezipaměti.
           -  [Velikosti pro virtuální počítače se systémem Linux v Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Pro určení velikosti je důležité zvážit *maximální propustnost disku* , který není v mezipaměti.
   2. Úložiště.
        - Podívejte se na dokument [Azure Storage typy pro úlohy SAP](./planning-guide-storage.md) .
        - Minimálně použijte [úložiště Azure SSD úrovně Standard](../../disks-types.md#standard-ssd) pro virtuální počítače, které reprezentují vrstvy aplikací SAP, a pro nasazení systémů DBMS, které nejsou citlivé na výkon.
        - Obecně nedoporučujeme používat [HDD úrovně Standard disky Azure](../../disks-types.md#standard-hdd).
        - [Azure Premium Storage](../../disks-types.md#premium-ssd) použít pro všechny virtuální počítače s DBMS, na kterých je vzdáleně citlivý výkon.
        - Použijte službu [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/).
        - Použijte Azure Akcelerátor zápisu pro DBMS protokolové jednotky s M-Series. Akcelerátor zápisu omezení a využití, jak je popsáno v [akcelerátor zápisu](../../how-to-enable-write-accelerator.md).
        - Pro různé typy DBMS ověřte [dokumentaci k obecnému systému DBMS související s SAP](./dbms_guide_general.md) a dokumentaci ke konkrétnímu systému DBMS, na kterou se odkazuje obecný dokument.
        - Další informace o SAP HANA najdete v tématu [Konfigurace a operace SAP HANA infrastruktury v Azure](./hana-vm-operations.md).
        - Nepřipojujte disky Azure data k virtuálnímu počítači Azure Linux pomocí ID zařízení. Místo toho použijte univerzálně jedinečný identifikátor (UUID). Buďte opatrní při použití grafických nástrojů k připojení datových disků Azure, například. Zkontrolujte položky v/etc/fstab a ujistěte se, že se k připojení disků používá UUID. Další podrobnosti najdete v [tomto článku](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk).
   3. Sítě.
        - Otestujte a vyhodnoťte infrastrukturu virtuální sítě a distribuci svých aplikací SAP napříč nebo v různých virtuálních sítích Azure.
        -  Vyhodnoťte přístup k architektuře architektury virtuální sítě typu střed a paprsek nebo přístup k mikrosegmentaci v rámci jedné virtuální sítě Azure. Toto hodnocení založte na:
               1. Náklady na výměnu dat mezi [partnerskými virtuálními sítěmi Azure](../../../virtual-network/virtual-network-peering-overview.md). Informace o cenách najdete v tématu [Virtual Network ceny](https://azure.microsoft.com/pricing/details/virtual-network/).
               2. Výhody rychlého odpojení partnerského vztahu mezi virtuálními sítěmi Azure na rozdíl od změny skupiny zabezpečení sítě k izolaci podsítě v rámci virtuální sítě. Toto vyhodnocení platí pro případy, kdy se aplikace nebo virtuální počítače hostované v podsíti virtuální sítě staly bezpečnostním rizikem.
                3. Centrální protokolování a auditování síťového provozu mezi místním prostředím, vnějším světem a virtuálním datovým centrem, které jste vytvořili v Azure.
        - Vyhodnoťte a otestujte cestu k datům mezi aplikační vrstvou SAP a vrstvou SAP DBMS.
            -  Umístění [virtuálních síťových zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) v komunikační cestě mezi aplikací SAP a vrstvou DBMS systémů SAP založených na SAP NetWeaver, Hybris nebo S/4HANA se nepodporuje.
            -  Umístění aplikační vrstvy SAP a SAP DBMS v různých virtuálních sítích Azure, které nejsou partnerské vztahy, se nepodporuje.
            -  Pomocí [skupin zabezpečení aplikace a pravidel skupiny zabezpečení sítě](../../../virtual-network/network-security-groups-overview.md) můžete definovat trasy mezi aplikační vrstvou SAP a vrstvou SAP DBMS.
        - Ujistěte se, že je na virtuálních počítačích, které se používají v aplikační vrstvě SAP a ve vrstvě SAP DBMS, zapnuté [akcelerované síťové služby Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) . Mějte na paměti, že pro podporu akcelerovaných síťových služeb v Azure jsou potřeba různé úrovně operačního systému:
            - Windows Server 2012 R2 nebo novější
            - SUSE Linux 12 SP3 nebo novější.
            - RHEL 7,4 nebo novější.
            - Oracle Linux 7,5. Pokud používáte jádro RHCKL, je vyžadována verze 3.10.0-862.13.1. el7. Pokud používáte jádro Oracle UEK, je vyžadována verze 5.
        - Otestujte a vyhodnoťte latenci sítě mezi virtuálními počítači aplikační vrstvy SAP a virtuálními počítači DBMS podle poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky na základě pokynů pro latenci sítě v části [SAP Support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno v [tomto článku](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
        - Ujistěte se, že nasazení interního nástroje jsou nastavená tak, aby používala přímé vrácení serveru. Toto nastavení omezí latenci při použití Azure ILBs pro konfigurace s vysokou dostupností na vrstvě DBMS.
        - Pokud používáte Azure Load Balancer společně s hostovanými operačními systémy Linux, ověřte, zda je parametr sítě Linux **net.IPv4.tcp_timestamps** nastaven na **hodnotu 0**. Toto doporučení je v konfliktu s doporučeními ve starších verzích [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421). Poznámka SAP je nyní aktualizována na stav, že tento parametr musí být nastaven na **hodnotu 0** , aby fungoval se službou Azure Load Balancer.
        - Pokud chcete dosáhnout optimální latence sítě, zvažte použití [skupin umístění blízkosti Azure](../../co-location.md) . Další informace najdete v tématu [skupiny umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
   4. Nasazení s vysokou dostupností a zotavením po havárii.
        - Pokud nasadíte vrstvu aplikace SAP bez definování konkrétní zóny dostupnosti Azure, ujistěte se, že všechny virtuální počítače, na kterých běží instance dialogových oken SAP nebo instance middlewaru s jedním systémem SAP, jsou nasazené ve [skupině dostupnosti](../../availability-set-overview.md).
        - Pokud nepotřebujete vysokou dostupnost pro centrální služby SAP a DBMS, můžete tyto virtuální počítače nasadit do stejné skupiny dostupnosti jako aplikační vrstva SAP.
        - Pokud chráníte službu SAP Central Services a vrstvu DBMS pro zajištění vysoké dostupnosti pomocí pasivní replikace, umístěte tyto dva uzly pro centrální služby SAP v jedné samostatné skupině dostupnosti a dva uzly DBMS v jiné skupině dostupnosti.
        - Pokud nasadíte do Zóny dostupnosti Azure, nemůžete použít skupiny dostupnosti. Musíte ale mít jistotu, že nasadíte aktivní a pasivní uzly centrální služby do dvou různých Zóny dostupnosti. Použijte Zóny dostupnosti, které mají nejnižší latenci mezi nimi.
          Pamatujte, že je potřeba použít [Azure Standard Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md) pro případ použití pro vytváření clusterů s podporou převzetí služeb při selhání s Windows nebo Pacemaker pro vrstvu DBMS a službu SAP Central Services napříč zóny dostupnosti. [Základní Load Balancer](../../../load-balancer/load-balancer-overview.md) nemůžete použít pro nasazení napříč oblastmi.
   5. Nastavení časového limitu.
        - Zkontrolujte trasování instancí SAP v SAP NetWeaver, abyste se ujistili, že mezi serverem front a pracovními procesy SAP nedochází k přerušení připojení. Těmto přerušením připojení se můžete vyhnout nastavením těchto dvou parametrů registru:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. Další informace naleznete v tématu [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. Další informace naleznete v tématu [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - Chcete-li se vyhnout časovým limitům GUI mezi místními rozhraními grafického uživatelského rozhraní SAP a aplikačními vrstvami SAP nasazenými v Azure, ověřte, zda jsou tyto parametry nastaveny ve výchozím souboru. pfl nebo profilu instance:
            - rdisp/keepalive_timeout = 3600
            - rdisp/naživu = 20
        - Aby nedošlo k přerušení navázání připojení mezi procesem zařazení do fronty SAP a pracovními procesy SAP, je třeba nastavit parametr **enque/encni/set_so_keepalive** na **hodnotu true**. Viz také [SAP note #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Pokud používáte konfiguraci clusteru s podporou převzetí služeb při selhání Windows, ujistěte se, že je čas reakce na nereagující uzly správně nastavený pro Azure. Článek [optimalizace prahové hodnoty sítě clusteru s podporou převzetí služeb při selhání](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) vypíše seznam parametrů a způsob ovlivnění převzetí Za předpokladu, že uzly clusteru jsou ve stejné podsíti, změňte tyto parametry:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Nastavení operačního systému nebo opravy
        - Pro provozování HANA v SAP si přečtěte tyto poznámky a dokumentace:
            -   [Poznámka k podpoře SAP #2814271 – zálohování SAP HANA v Azure se nepovede. chyba kontrolního součtu](https://launchpad.support.sap.com/#/notes/2814271)
            -   [Podpora SAP Poznámka #2753418 – potenciální snížení výkonu z důvodu záložního použití časovače](https://launchpad.support.sap.com/#/notes/2753418)
            -   [Podpora SAP Poznámka #2791572 – snížení výkonu kvůli chybějící podpoře VDSO pro Hyper-V v Azure](https://launchpad.support.sap.com/#/notes/2791572)
            -   [Poznámka k podpoře SAP #2382421 – optimalizace konfigurace sítě v HANA a na úrovni operačního systému](https://launchpad.support.sap.com/#/notes/2382421)
            -   [Poznámka k podpoře SAP #2694118 – Red Hat Enterprise Linux HA Add-On v Azure](https://launchpad.support.sap.com/#/notes/2694118)
            -   [Poznámka k podpoře SAP #1984787 – SUSE LINUX Enterprise Server 12: poznámky k instalaci](https://launchpad.support.sap.com/#/notes/1984787)
            -   [Poznámka k podpoře SAP #2002167-Red Hat Enterprise Linux 7. x: instalace a upgrade](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [Poznámka k podpoře SAP #2292690 – SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 7](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [Poznámka k podpoře SAP #2772999-Red Hat Enterprise Linux 8. x: instalace a konfigurace](https://launchpad.support.sap.com/#/notes/2772999)
            -   [Poznámka k podpoře SAP #2777782 – SAP HANA DB: Doporučená nastavení operačního systému pro RHEL 8](https://launchpad.support.sap.com/#/notes/2777782)
            -   [Poznámka k podpoře SAP #2578899-SUSE Linux Enterprise Server 15: Poznámka k instalaci](https://launchpad.support.sap.com/#/notes/2578899)
            -   [Poznámka k podpoře SAP #2455582-Linux: spouštění aplikací SAP kompilovaných pomocí RSZ 6. x](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [Poznámka k podpoře SAP #2729475 – HWCCT se nezdařilo s chybou "hypervisor není podporován" na virtuálních počítačích Azure certifikovaných pro SAP HANA](https://launchpad.support.sap.com/#/notes/2729475)
1. Otestujte postupy pro vysokou dostupnost a zotavení po havárii.
   1. Simulace převzetí služeb při selhání vypnutím virtuálních počítačů (hostovanými operačními systémy Windows) nebo vložením operačních systémů do nouzového režimu (hostovaných operačních systémů Linux). Tento krok vám pomůže zjistit, jestli vaše konfigurace převzetí služeb při selhání fungují tak, jak jsou navržené.
   1. Změřte, jak dlouho trvá spuštění převzetí služeb při selhání. Pokud jsou časy příliš dlouhé, zvažte následující:
        - Pro urychlení převzetí služeb při selhání použijte pro SUSE Linux zařízení SBD namísto agenta Azure plot.
        - Pro SAP HANA, pokud je opětovné načtení dat trvat příliš dlouho, zvažte zřízení větší šířky pásma úložiště.
   3. Otestujte sekvenci zálohování a obnovování a časování a v případě potřeby proveďte opravy. Ujistěte se, že jsou časy zálohování dostatečné. Také je nutné otestovat aktivity obnovení a doba obnovení. Ujistěte se, že časy obnovení jsou v rámci RTO SLA, kde RTO spoléhá na proces obnovení databáze nebo virtuálního počítače.
   4. Testování funkcí a architektury DR mezi oblastmi
1. Kontroly zabezpečení.
   1. Otestuje platnost architektury Azure řízení přístupu na základě role (Azure RBAC). Cílem je oddělení a omezení přístupu a oprávnění různých týmů. Například členové týmu SAP by měli být schopní nasadit virtuální počítače a přiřadit disky z Azure Storage do dané virtuální sítě Azure. Ale tým pro SAP by neměl mít možnost vytvářet vlastní virtuální sítě ani měnit nastavení stávajících virtuálních sítí. Členové síťového týmu by neměli moci nasadit virtuální počítače do virtuálních sítí, ve kterých jsou spuštěné aplikace SAP a virtuální počítače s DBMS. Ani by členové tohoto týmu nemohli měnit atributy virtuálních počítačů nebo dokonce odstraňovat virtuální počítače nebo disky.  
   1.  Ověřte, že [Skupina zabezpečení sítě a pravidla ASC](../../../virtual-network/network-security-groups-overview.md) pracují podle očekávání a chrání chráněné prostředky.
   1.  Ujistěte se, že jsou šifrované všechny prostředky, které je třeba šifrovat. Definuje a implementuje procesy pro zálohování certifikátů, ukládání a přístup k těmto certifikátům a obnovování šifrovaných entit.
   1.  Použijte [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md) pro disky s operačním systémem, kde je to možné, z pohledu na operační systém – bod podpory.
   1.  Ujistěte se, že nepoužíváte příliš mnoho vrstev šifrování. V některých případech má smysl použít Azure Disk Encryption společně s jednou z transparentní šifrování datch metod DBMS k ochraně různých disků nebo komponent na stejném serveru.  Na serveru SAP DBMS můžete například povolit Azure Disk Encryption (ADE) na spouštěcím disku operačního systému (Pokud operační systém podporuje ADE) a tyto datové disky nepoužívá soubory pro trvalost dat DBMS.  Příkladem je použití ADE na disku, který uchovává šifrovací klíče DBMS TDE.
1. Testování výkonu. V SAP na základě trasování a měření SAP proveďte tato porovnání:
   - V případě potřeby Porovnejte prvních 10 online sestav s vaší aktuální implementací.
   - V případě potřeby Porovnejte prvních 10 dávkových úloh s aktuální implementací.
   - Porovnání přenosů dat prostřednictvím rozhraní do systému SAP. Zaměřte se na rozhraní, u kterých víte, že přenos probíhá mezi různými umístěními, jako je třeba z místního prostředí do Azure.


## <a name="non-production-phase"></a>Fáze mimo produkční prostředí 
V této fázi předpokládáme, že po úspěšném pilotním nasazení nebo testování konceptu (ověření koncepce) začínáte nasazovat systémy neprodukčního SAP do Azure. Zahrňte všechno, co jste se naučili a zjistili během tohoto nasazení v rámci ověření koncepce. Všechna kritéria a kroky uvedené pro POCs se vztahují i na toto nasazení.

Během této fáze obvykle nasazujete vývojové systémy, systémy testování částí a testovací systémy pro obchodní regrese do Azure. Doporučujeme, aby aspoň jeden neprodukční systém na jednom řádku aplikace SAP má úplnou konfiguraci vysoké dostupnosti, kterou bude mít budoucí provozní systém. Tady je několik dalších kroků, které je potřeba dokončit v průběhu této fáze:  

1.  Před přesunutím systémů z původní platformy do Azure Shromážděte data o spotřebě prostředků, jako je využití procesoru, propustnost úložiště a data IOPS. Obzvláště Shromážděte tato data z jednotek vrstvy DBMS, ale také je Shromážděte z jednotek aplikační vrstvy. Také změřit latenci sítě a úložiště.
2.  Poznamenejte si vzory času využití dostupnosti vašich systémů. Cílem je zjistit, jestli musí být neprodukční systémy k dispozici každý den, každý den nebo zda existují neprodukční systémy, které je možné vypnout v určitých fázích týdne nebo měsíce.
3.  Otestujte a určete, jestli chcete vytvořit vlastní image operačního systému pro virtuální počítače v Azure, nebo jestli chcete použít image z Galerie sdílených imagí Azure. Pokud používáte image z Galerie sdílených imagí, nezapomeňte použít image, která odráží smlouvu o podpoře s vaším dodavatelem operačního systému. V případě některých dodavatelů operačního systému vám Galerie sdílených imagí umožní používat vlastní image licencí. U ostatních imagí operačního systému je podpora zahrnutá v ceně uvedené v Azure. Pokud se rozhodnete vytvořit vlastní image operačního systému, najdete dokumentaci v těchto článcích:
    -   [Sestavení generalizované image virtuálního počítače s Windows nasazeného v Azure](../../windows/capture-image-resource.md)
    -   [Sestavení generalizované image virtuálního počítače se systémem Linux nasazeného v Azure](../../linux/capture-image.md)
3.  Pokud používáte image SUSE a Red Hat Linux z Galerie sdílených imagí, musíte použít image pro SAP poskytované dodavateli pro Linux v galerii sdílených imagí.
4.  Ujistěte se, že splňujete požadavky na podporu SAP pro smlouvy o podpoře Microsoftu. Další informace najdete v [poznámkách k podpoře SAP #2015553](https://launchpad.support.sap.com/#/notes/2015553). Pro velké instance HANA si přečtěte článek [požadavky na registraci](./hana-onboarding-requirements.md).
4.  Ujistěte se, že napraví uživatelé získají [oznámení o plánované údržbě](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) , abyste si mohli vybrat nejlepší výpadky.
5.  Pro nové funkce, které se můžou vztahovat na vaše nasazení, se často kontrolují prezentace Azure na kanálech, jako je [Channel 9](https://channel9.msdn.com/) .
6.  Zkontrolujte poznámky SAP související s Azure, jako je například [Poznámka k podpoře #1928533](https://launchpad.support.sap.com/#/notes/1928533), pro nové SKU virtuálních počítačů a nově podporované verze operačních systémů a systémů DBMS. Porovnejte ceny nových typů virtuálních počítačů s využitím starších typů virtuálních počítačů, abyste mohli nasadit virtuální počítače s nejlepším poměrem cen a výkonu.
7.  Znovu Projděte poznámky k podpoře SAP, adresář SAP HANA hardwaru a PAM služby SAP. Ujistěte se, že v podporovaných virtuálních počítačích pro Azure nejsou žádné změny, podporované verze operačního systému na těchto virtuálních počítačích a podporované verze SAP a DBMS.
8.  Podívejte [se na web SAP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pro nové SKU s certifikací Hana v Azure. Porovnejte ceny nových SKU s těmi, které jste naplánovali použít. Nakonec proveďte potřebné změny, abyste mohli používat ty, které mají nejlepší poměr ceny a výkonu.
9.  Přizpůsobte skripty pro nasazení a použijte nové typy virtuálních počítačů a zahrňte nové funkce Azure, které chcete použít.
10. Po nasazení infrastruktury otestujete a vyhodnoťte latenci sítě mezi virtuálními počítači aplikační vrstvy SAP a virtuálními počítači DBMS podle poznámky podpory SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky na základě pokynů pro latenci sítě v části [SAP Support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno v [tomto článku](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Ujistěte se, že žádná omezení uvedená v části [požadavky týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md#azure-network-considerations) a [Konfigurace infrastruktury SAP Hana a operací v Azure](./hana-vm-operations.md) se vztahují na vaše nasazení.
11. Zajistěte, aby byly virtuální počítače nasazené do správné [skupiny umístění služby Azure Proximity](../../co-location.md), jak je popsáno v tématu [skupiny umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
11. Před použitím úlohy proveďte všechny další kontroly uvedené pro fázi ověření konceptu.
12. Jak zatížení platí, zaznamenejte spotřebu prostředků systémů v Azure. Porovnejte tuto spotřebu se záznamy z vaší staré platformy. Pokud zjistíte, že máte velké rozdíly, upravte velikost virtuálních počítačů v budoucích nasazeních. Pamatujte na to, že když se klidnějších, úložiště a šířka pásma sítě virtuálních počítačů, sníží se i.
    - [Velikosti virtuálních počítačů s Windows v Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Sizes for Linux virtual machines in Azure](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Experimentujte s funkcí a procesy systémové kopie. Cílem je zjednodušit zkopírování vývojového systému nebo testovacího systému, takže projektové týmy můžou rychle získat nové systémy. 
14. Optimalizujte a doladit přístup, oprávnění a procesy založené na rolích týmu do Azure, abyste se ujistili, že máte oddělení povinností. Ve stejnou chvíli se ujistěte, že všechny týmy můžou provádět své úkoly v infrastruktuře Azure.
15. Cvičení, testování a dokumentace postupů vysoké dostupnosti a zotavení po havárii, které zaměstnancům umožní provádět tyto úlohy. Identifikujte nedostatky a přizpůsobte nové funkce Azure, které Integrujte do svých nasazení.


## <a name="production-preparation-phase"></a>Fáze přípravy výroby 
V této fázi můžete shromažďovat, co jste se seznámili a zjistili během nasazení mimo produkční prostředí a použít ho pro budoucí produkční nasazení. Také je nutné připravit práci s přenosem dat mezi vaším aktuálním hostováním a Azure.

1.  Před přechodem do Azure proveďte potřebné upgrady pro vydání SAP vašich produkčních systémů.
1.  Souhlasím s vlastníky podnikání na funkčních a obchodních testech, které je potřeba provést po migraci produkčního systému.
1.  Ujistěte se, že jsou tyto testy dokončeny se zdrojovými systémy v aktuálním umístění hostování. Vyhněte se provádění testů poprvé po přesunu systému do Azure.
1.  Otestujte proces migrace produkčních systémů do Azure. Pokud nepřesouváte všechny produkční systémy do Azure během stejného časového rámce, skupiny sestavení produkčních systémů, které musí být ve stejném umístění hostování. Migrace testovacích dat. Tady jsou některé běžné metody:
    - Použijte metody DBMS, jako je zálohování a obnovení, v kombinaci s SQL Server Always On, replikace systému HANA nebo přesouvání protokolů do počáteční hodnoty a synchronizace obsahu databáze v Azure.
    - Pro menší databáze použijte zálohování a obnovení.
    - Pomocí monitorování migrace SAP, který je integrovaný do SAP SWPM, můžete provádět heterogenní migrace.
    - Pokud potřebujete sloučit migraci s upgradem na vydání SAP, použijte proces [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) . Mějte na paměti, že nejsou podporované všechny kombinace zdrojového DBMS a cílového systému DBMS. Další informace najdete v poznámkách k podpoře SAP pro různé verze služby DMO. Například [možnost migrace databáze (DMO) SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872).
    - Pro případ, že budete potřebovat přesunout zálohy nebo soubory exportu SAP, otestujte, jestli je propustnost přenosu dat lepší prostřednictvím Internetu nebo přes ExpressRoute. Pokud přesouváte data přes Internet, možná budete muset změnit některá pravidla skupiny zabezpečení sítě/skupiny zabezpečení aplikací, která budete muset mít pro budoucí produkční systémy.
1.  Než přesunete systémy z vaší staré platformy do Azure, Shromážděte data o spotřebě prostředků. Užitečná data zahrnují využití procesoru, propustnost úložiště a data IOPS. Obzvláště Shromážděte tato data z jednotek vrstvy DBMS, ale také je Shromážděte z jednotek aplikační vrstvy. Také změřit latenci sítě a úložiště.
1.  Znovu Projděte poznámky k podpoře SAP a požadovaná nastavení operačního systému, SAP HANA adresář hardwaru a modul PAM služby SAP. Ujistěte se, že v podporovaných virtuálních počítačích pro Azure nejsou žádné změny, podporované verze operačního systému v těchto virtuálních počítačích a podporované verze SAP a DBMS.
1.  Aktualizujte skripty nasazení a vezměte v úvahu nejnovější rozhodnutí, která jste udělali u typů virtuálních počítačů a funkcí Azure.
1.  Po nasazení infrastruktury a aplikací ověřte, že:
    - Byly nasazeny správné typy virtuálních počítačů se správnými atributy a velikostmi úložiště.
    - Virtuální počítače mají správné a požadované verze a opravy operačního systému a jsou jednotné.
    - Virtuální počítače jsou zpřísněné podle potřeby a jednotným způsobem.
    - Byly nainstalovány a nasazeny správné verze a opravy aplikací.
    - Virtuální počítače se nasadily do skupin dostupnosti Azure jako plánované.
    - Azure Premium Storage se používá pro disky citlivé na latenci nebo kde se vyžaduje [smlouva SLA s jedním virtuálním počítačem 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) .
    - Azure Akcelerátor zápisu je nasazený správně.
        - Ujistěte se, že se v rámci virtuálních počítačů, prostorů úložiště nebo sad prokládaných svazků správně vytvořily na discích, které vyžadují Akcelerátor zápisu.
        - Ověřte [konfiguraci softwarového pole RAID v systému Linux](/previous-versions/azure/virtual-machines/linux/configure-raid).
        - Ověřte [konfiguraci LVM na virtuálních počítačích se systémem Linux v Azure](/previous-versions/azure/virtual-machines/linux/configure-lvm).
    - Služby [Azure Managed disks](https://azure.microsoft.com/services/managed-disks/) se používají výhradně.
    - Virtuální počítače se nasadily do správných skupin dostupnosti a Zóny dostupnosti.
    - [Akcelerace sítě Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) je povolená na virtuálních počítačích, které se používají v aplikační vrstvě SAP a ve vrstvě SAP DBMS.
    - Žádná [virtuální síťová zařízení Azure](https://azure.microsoft.com/solutions/network-appliances/) nejsou v komunikačních cestách mezi aplikací SAP a vrstvou DBMS systémů SAP založenou na SAP NetWeaver, Hybris nebo S/4HANA.
    - Pravidla skupiny zabezpečení aplikace a skupiny zabezpečení sítě umožňují komunikaci podle potřeby a plánované a zablokování komunikace v případě potřeby.
    - Nastavení časového limitu je správně nastaveno, jak je popsáno výše.
    - Virtuální počítače se nasazují do správné [skupiny umístění služby Azure Proximity](../../co-location.md), jak je popsáno v tématu [skupiny umístění pro Azure v blízkosti pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md).
    - Latence sítě mezi virtuálními počítači aplikační vrstvy SAP a virtuálními počítači DBMS se testuje a ověřuje, jak je popsáno v tématu poznámky k podpoře SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) a [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Vyhodnoťte výsledky na základě pokynů pro latenci sítě v části [SAP Support note #1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Latence sítě by měla být v mírném nebo dobrém rozsahu. Výjimky se vztahují na provoz mezi virtuálními počítači a velkými jednotkami instancí, jak je uvedeno v [tomto článku](./hana-network-architecture.md#networking-architecture-for-hana-large-instance).
    - Šifrování bylo implementováno tam, kde je to nutné, a s odpovídající metodou šifrování.
    - Rozhraní a další aplikace mohou připojit nově nasazenou infrastrukturu.
1.  Vytvořte PlayBook, který se bude chovat při plánované údržbě Azure. Určete pořadí, ve kterém se mají restartovat systémy a virtuální počítače pro plánovanou údržbu.
    

## <a name="go-live-phase"></a>Přejít do živé fáze
Během fáze přechodu do živého prostředí nezapomeňte postupovat podle playbooky, který jste vytvořili během dřívějších fází. Proveďte kroky, které jste otestovali a provedli v praxi. V konfiguracích a procesech nepřijímejte poslední změny v minutách. Také proveďte tyto kroky:

1. Ověřte, zda Azure Portal monitorování a jiné nástroje monitorování fungují. Pro Windows a administrativní oblast pro Linux doporučujeme sledování výkonu systému Windows (PerfMon).
    - Čítače procesoru.
        - Průměrná doba využití procesoru, celkem (všechny procesory)
        - Průměrná doba využití procesoru, každý jednotlivý procesor (128 procesorů na virtuálních počítačích s M128)
        - Čas jádra procesoru, každý jednotlivý procesor
        - Uživatelský čas procesoru, každý jednotlivý procesor
    - Memory:
        - Volná paměť
        - Paměťová stránka za sekundu
        - Nedostatek paměťové stránky za sekundu
    - Disk.
        - Čtení z disku v KB/s, na jednotlivé disky
        - Čtení z disku za sekundu, na jednotlivé disky
        - Čtení z disku za sekundu/čtení na jednotlivých discích
        - Zápis na disk v KB/s, na jednotlivé disky
        - Zápis na disk za sekundu, na jednotlivé disky
        - Zápis na disk v mikrosekundách/čteních na jednotlivých discích
    - Síť:
        - Síťové pakety za sekundu
        - Odchozí síťové pakety za sekundu
        - Síť KB za sekundu
        - Síťové KB – mimo jiné
1.  Po migraci dat proveďte všechny ověřovací testy, ke kterým jste se dohodli s vlastníky firmy. Přijměte výsledky ověřovacího testu pouze v případě, že máte výsledky pro původní zdrojové systémy.
1.  Ověřte, zda rozhraní fungují a zda mohou jiné aplikace komunikovat s nově nasazenými provozními systémy.
1.  Pomocí STMS transakcí SAP ověřte přenos a opravný systém.
1.  Zálohujte databázi po uvolnění systému pro produkční prostředí.
1.  Po uvolnění systému pro produkční prostředí proveďte zálohy virtuálních počítačů pro virtuální počítače aplikační vrstvy SAP.
1.  U systémů SAP, které nepatří do aktuální fáze přechodu do provozu, ale které komunikují se systémy SAP, které jste přesunuli do Azure v rámci této fáze pro přechod do provozu, je potřeba resetovat vyrovnávací paměť názvu hostitele v SM51. Tím dojde k odebrání starých IP adres z mezipaměti přidružených k názvům instancí aplikace, které jste přesunuli do Azure.  


## <a name="post-production"></a>Vystavení výroby
Tato fáze se týká monitorování, provozu a správy systému. Z pohledu SAP se použijí obvyklé úkoly, které jste museli provést v původním umístění hostitele. Dokončete i tyto úlohy specifické pro Azure:

1. Projděte si faktury Azure pro vysoce zpoplatněné systémy.
2. Optimalizujte efektivitu cen a výkonu na straně virtuálního počítače a na straně úložiště.
3. Optimalizujte čas, kdy můžete vypnout systémy.  


## <a name="next-steps"></a>Další kroky
Přečtěte si tyto články:

- [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](./planning-guide.md)
- [Nasazení Azure Virtual Machines pro SAP NetWeaver](./deployment-guide.md)
- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md)