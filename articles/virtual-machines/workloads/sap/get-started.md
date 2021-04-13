---
title: Začínáme s SAP na virtuálních počítačích Azure | Microsoft Docs
description: Přečtěte si o řešeních SAP, která běží na virtuálních počítačích v Microsoft Azure
services: virtual-machines-sap
ms.service: virtual-machines-sap
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/12/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad538dd36301a25396f426301c1ee0bf6dfb0ece
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364069"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spouštění scénářů pro úlohy SAP

Když použijete Microsoft Azure, můžete spolehlivě spouštět klíčové úlohy a scénáře SAP na škálovatelné, kompatibilní a podnikové prověřené platformě. Získáte škálovatelnost, flexibilitu a úspory nákladů Azure. Díky rozšířenému partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP napříč vývojovými a testovacími a provozními scénáři v Azure a plně podporovat. Z SAP NetWeaver do SAP S/4HANA, SAP BI v systému Linux do Windows a SAP HANA k SQL, máme na vás přehled.

Kromě hostování scénářů SAP NetWeaver s různými systémy DBMS v Azure můžete hostovat i další scénáře úloh SAP, jako je například SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která nastavuje Azure odděleně. Aby bylo možné hostovat víc paměti a scénářů SAP náročných na prostředky procesoru, které zahrnují SAP HANA, Azure nabízí použití holého hardwaru vyhrazeného pro zákazníky. Pomocí tohoto řešení můžete spouštět SAP HANA nasazení, která vyžadují až 24 TB (120 TB škálování) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénářů pro úlohy SAP v Azure taky může vytvořit požadavky na integraci identit a jednotné přihlašování. K této situaci může dojít, když použijete Azure Active Directory (Azure AD) k propojení různých komponent SAP a SAP software-as-a-Service (SaaS) nebo Platform-as-a-Service (PaaS). Seznam takových scénářů integrace a jednotného přihlašování s entitami služby Azure AD a SAP je popsán v části "Azure AD SAP Identity Integration and Single přihlašování".

## <a name="changes-to-the-sap-workload-section"></a>Změny v části úlohy SAP
Změny dokumentů v části věnované úlohám SAP v Azure najdete na konci tohoto článku. Záznamy v protokolu změn se uchovávají přibližně po 180 dnech.

## <a name="you-want-to-know"></a>Chcete znát
Pokud máte konkrétní otázky, budeme v této části úvodní stránky ukazovat na konkrétní dokumenty nebo toky. Chcete znát:

- Jaké virtuální počítače Azure a jednotky velkých instancí HANA jsou podporované pro vydávání verzí softwaru SAP a verze operačních systémů. Přečtěte si dokument, [který software SAP podporuje pro nasazení Azure](./sap-supported-product-on-azure.md) pro odpovědi a proces hledání informací.
- Jaké scénáře nasazení SAP jsou podporovány u virtuálních počítačů Azure a velkých instancí HANA. Informace o podporovaných scénářích najdete v dokumentu:
    - [Podporované scénáře pro úlohy SAP na virtuálních počítačích Azure](./sap-planning-supported-configurations.md)
    - [Podporované scénáře pro velkou instanci HANA](./hana-supported-scenario.md)
- Které služby Azure, typy virtuálních počítačů Azure a služby Azure Storage jsou k dispozici v různých oblastech Azure, Prohlédněte si [Dostupné produkty](https://azure.microsoft.com/global-infrastructure/services/) v lokalitě v oblasti 
- Funguje rámec HA třetí strany, kromě Windows a Pacemaker? Zkontrolujte dolní část [poznámky k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- Co je Azure Storage pro můj scénář nejvhodnější? Číst [Azure Storage typy pro úlohy SAP](./planning-guide-storage.md)
- Je jádro Red Hat v Oracle Enterprise Linux podporované systémem SAP? Přečtěte [si poznámku o podpoře SAP sap #1565179](https://launchpad.support.sap.com/#/notes/1565179)
- Proč jsou rodiny virtuálních počítačů Azure [da (s) v4](../../dav4-dasv4-series.md) / [(](../../eav4-easv4-series.md) s) necertifikované pro SAP HANA? Rodiny virtuálních počítačů Azure Das/EAS jsou založené na hardwaru založeném na procesorech AMD. SAP HANA nepodporuje procesory AMD, a to ani ve virtualizovaných scénářích.
- Proč se mi pořád zobrazuje zpráva: příznaky CPU pro instrukci RDTSCP nebo příznaky CPU pro constant_tsc nebo nonstop_tsc nejsou nastavené nebo current_clocksource a available_clocksource nejsou správně nakonfigurované s SAP HANA, i když jsou na tom, že běží nejnovější jádra pro Linux. Odpověď najdete v [poznámce SAP support #2791572](https://launchpad.support.sap.com/#/notes/2791572)
- Kde najdu architektury pro nasazení SAP Fiori v Azure? Podívejte se na blog [SAP v Azure: Application Gateway nastavení brány firewall webových aplikací (WAF) v2 pro internetové aplikace SAP Fiori](https://blogs.sap.com/2020/12/03/sap-on-azure-application-gateway-web-application-firewall-waf-v2-setup-for-internet-facing-sap-fiori-apps/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řada dokumentů vás provede SAP HANA v Azure (velké instance) nebo pro krátké instance s vysokou kapacitou HANA. Informace o velkých instancích HANA najdete v dokumentu [Přehled a architektura SAP HANA v Azure (velké instance)](./hana-overview-architecture.md) a Projděte si související dokumentaci v oddílu velká instance Hana.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace obsahuje různé aspekty SAP HANA. V rámci předpokladů byste měli být obeznámeni s hlavními službami Azure, které poskytují základní služby Azure IaaS. Proto potřebujete znát výpočetní výkon, úložiště a síť Azure. Mnohé z těchto věcí jsou zpracovávány v [Průvodci plánováním Azure](./planning-guide.md)NetWeaver souvisejících s SAP. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasazené na virtuálních počítačích Azure
Tato část obsahuje dokumentaci pro plánování a nasazení pro SAP NetWeaver, SAP LaMa a Business One v Azure. Dokumentace se zaměřuje na základy a používání databází jiných než HANA s úlohou SAP v Azure. Dokumenty a články pro vysokou dostupnost jsou také základem pro SAP HANA vysoké dostupnosti v Azure.

## <a name="sap-netweaver-and-s4hana-high-availability"></a>NetWeaver SAP a S/4HANA High Availability
Vysoká dostupnost aplikační vrstvy SAP a DBMS se zdokumentují v podrobnostech od dokumentů [Azure Virtual Machines vysoké dostupnosti pro SAP NetWeaver](./sap-high-availability-guide-start.md)



## <a name="integrate-azure-ad-with-sap-services"></a>Integrace Azure AD se službami SAP
V této části najdete informace v tématu Postup konfigurace jednotného přihlašování s většinou služeb SAP SaaS a PaaS, NetWeaver a Fiori. 



## <a name="documentation-on-integration-of-azure-services-into-sap-components"></a>Dokumentace k integraci služeb Azure do komponent SAP
V této části najdete dokumenty ke službě Microsoft Power BI Integration do zdrojů dat SAP a také Azure Data Factory integraci do SAP BW.



## <a name="change-log"></a>Protokol změn
- 04/12/2021: vyměněna zálohovací dokumentaci pro SAP HANA dokumenty [SAP HANA zálohování a obnovení pomocí služby Azure Backup](../../../backup/sap-hana-db-about.md) 
- 04/12/2021: vydání služby [SAP HANA Scale-out HSR s Pacemaker na virtuálních počítačích Azure v](./sap-hana-high-availability-scale-out-hsr-suse.md) průvodci konfigurací SLES
- 04/07/2021: vyjasněná podpora SQL Server více instancí a podpora více databází v [SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](./dbms_guide_sqlserver.md)
- 04/07/2021: byly přidány informace související se sekundárními IP adresami v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md)
- 04/07/2021: Přidání podpory pro podporu Oracle DBMS na ANF v [Azure Storagech typech pro úlohy SAP](./planning-guide-storage.md)
- 03/17/2021: Změna v [ha pro SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md), [ha pro SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md) a [ha pro SAP HANA horizontálního NAvýšení kapacity s ANFem na RHEL](./sap-hana-high-availability-netapp-files-red-hat.md) za účelem přidání instrukcí pro replikaci systému Hana s povoleným aktivním/čtením v clusteru Pacemaker
- 03/15/2021: změňte [instanci SAP ASCS/SCS se službou WSFC a sdílenou složkou](./sap-high-availability-guide-wsfc-file-share.md),[Nainstalujte instanci SAP ASCS/SCS s](./sap-high-availability-installation-wsfc-file-share.md) protokolem WSFC a sdílenou složkou a [SAP ASCS/SCS multi-SID s WSFC a sdílením souborů](./sap-ascs-ha-multi-sid-wsfc-file-share.md) a vyjasněte, že instance SAP ASCS/SCS a sdílená složka SOFS musí být nasazeny v samostatných clusterech.
- 03/03/2021: Změna v [Průvodci ha pro SAP ASCS/SCS s WSFC a Azure NetApp Files (SMB)](./high-availability-guide-windows-netapp-files-smb.md) pro přidání příkazu postupovat opatrněji, který vyžaduje zvýšená oprávnění pro uživatele, který používá SWPM, během instalace systému SAP
- 02/11/2021: změny [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure na serveru Red Hat Enterprise Linux](./high-availability-guide-rhel-ibm-db2-luw.md) pro změnu příkazů clusteru Pacemaker pro RHEL 8. x
- 02/03/2021: Změna [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) na aktualizaci pcmk_host_map v příkazu stonith Create
- 02/03/2021: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) pro přidání pcmk_host_map do příkazu stonith Create 
- 02/03/2021: Další podrobnosti o nastaveních v/v Scheduleru pro SUSE v článku [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md)
- 02/01/2021: Změna v [ha pro SAP HANA škálování pomocí ANF na RHEL](./sap-hana-high-availability-netapp-files-red-hat.md), [SAP HANA škálování HSR s Pacemaker na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-scale-out-hsr-rhel.md), [SAP HANA horizontálního](./sap-hana-scale-out-standby-netapp-files-suse.md) navýšení kapacity s pohotovostním uzlem na virtuálních počítačích azure s ANF na SLES a [SAP HANA škálování](./sap-hana-scale-out-standby-netapp-files-rhel.md) na více instancí na virtuálních počítačích Azure pomocí ANF v RHEL pro přidání odkazu na [svazky systému souborů NFS v Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)
- 01/23/2021: zaveďte funkce dělení datových svazků HANA jako funkce k proložení vstupně-výstupních operací s datovými soubory HANA v různých discích Azure nebo sdílených složkách NFS bez použití Správce diskového svazku v článcích [SAP HANA konfigurace úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) a [svazcích NFS v 4.1 na Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)
- 01/18/2021: Přidání podpory souborů NFS Azure NET Apps pro Oracle v [azure Virtual Machines nasazení Oracle DBMS pro úlohy SAP](./dbms_guide_oracle.md) a nastavení desetinných míst v tabulce v dokumentu [systému souborů NFS v 4.1 na Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)
- 01/11/2021: drobné změny v [ha pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP](./high-availability-guide-rhel.md), [ha pro SAP NW na virtuálních počítačích Azure v RHEL s ANF](./high-availability-guide-rhel-netapp-files.md) a [ha pro SAP NW na virtuálních počítačích Azure](./high-availability-guide-rhel-multi-sid.md) v, které umožňují přizpůsobit příkazy pro RHEL8 i RHEL7 a ENSA1 a ENSA2
- 01/05/2021: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s ANF v SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) a SAP HANA škálování na více instancí [s pohotovostním uzlem na virtuálních počítačích Azure s ANF v RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), revize Doporučené konfigurace, která povolí agentovi SAP hosta spravovat rozsah místních portů  
- 01/04/2021: přidejte nové oblasti Azure podporované službou HLI do [toho, co je SAP HANA v Azure (velké instance)](./hana-overview-architecture.md) .
- 12/29/2020: přidejte doporučení architektury pro konkrétní oblasti Azure v [konfiguracích úloh SAP pomocí zóny dostupnosti Azure](./sap-ha-availability-zones.md)
- 12/21/2020: přidejte nové certifikace do SKU velkých instancí HANA v části [dostupné SKU pro HLI](./hana-available-skus.md) .
- 12/12/2020: byl přidán ukazatel na poznámku ke službě SAP poznámky objasňující podrobnosti o podpoře Oracle Enterprise Linux podle SAP pro to, [co je software SAP podporován pro nasazení Azure](./sap-supported-product-on-azure.md#oracle-dbms-support) .
- 11/26/2020: Přizpůsobte [SAP HANA konfigurací úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) a [Azure Storage typů pro úlohy SAP](./planning-guide-storage.md) na změnu jednoho [virtuálního počítače SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)
- 11/05/2020: Změna odkazu na nové poznámky SAP týkající se typů systému souborů podporovaných systémem HANA v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) 
- 10/26/2020: Změna některých tabulek pro konfiguraci služby Azure Premium Storage za účelem objasnění zajištěné a propustnosti v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md)
- 10/22/2020: Změna [dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), ha pro SAP NW na virtuálních počítačích Azure [v SLES s ANF](./high-availability-guide-suse-netapp-files.md), [ha pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP](./high-availability-guide-rhel.md) a ha pro SAP NW na virtuálních počítačích Azure v [RHEL s ANF](./high-availability-guide-rhel-netapp-files.md) pro úpravu doporučení pro NET.IPv4.tcp_keepalive_time  
- 10/16/2020: Změna v [ha IBM DB2 LUW na virtuálních počítačích Azure v SLES s Pacemaker](./dbms-guide-ha-ibm.md), [ha pro SAP NW na virtuálních počítačích Azure v RHEL pro aplikace SAP](./high-availability-guide-rhel.md), [ha IBM Db2 LUW na virtuálních počítačích Azure v RHEL](./high-availability-guide-rhel-ibm-db2-luw.md), [ha pro SAP NW](./high-availability-guide-rhel-multi-sid.md)na virtuálních počítačích Azure v RHEL [s](./high-availability-guide-rhel-netapp-files.md)RHEL, ha pro SAP [NW na virtuálních počítačích Azure v SLES pro aplikace SAP,](./high-availability-guide-suse.md)ha [pro SAP NNW na virtuálních počítačích](./high-availability-guide-suse-multi-sid.md)Azure v SLES, v SLES [s ANF pro aplikace SAP,](./high-availability-guide-suse-netapp-files.md)ha pro systém [souborů NFS na virtuálních počítačích](./high-availability-guide-suse-nfs.md)Azure v SLES, ha [v](./sap-hana-high-availability.md)systému Azure na SLES, ha pro SAP HANA [škálování pomocí ANF v RHEL](./sap-hana-high-availability-netapp-files-red-hat.md) SAP HANA , [HA SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md), [SAP HANA škálování na více instancí s Pacemaker na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-scale-out-hsr-rhel.md), [Připravte infrastrukturu Azure pro SAP ASCS/SCS s WSFC a sdíleným diskem](./sap-high-availability-infrastructure-wsfc-shared-disk.md), [Průvodce pro SAP ASCS/SCS se](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md) službou WSFC a sdílený disk Azure a průvodce pro SAP ASCS/SCS se službou WSFC a [sdíleným diskem](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) a s přidáním příkazu, který není v rámci scénářů vyrovnávání zatížení na sekundárních IP adres podporovaný 
- 10/16/2020: Přidání dokumentace pro řízení snímků úložiště velkých instancí HANA v [zálohování a obnovení SAP HANA ve velkých instancích Hana](./hana-backup-restore.md)
- 10/15/2020: vydání platformy SAP BusinessObjects BI v dokumentaci k Azure, [Průvodce plánováním a implementací platformy SAP BUSINESSOBJECTS BI v](businessobjects-deployment-guide.md) [Průvodci nasazením platformy Azure a SAP BusinessObjects BI Platform pro Linux v Azure](businessobjects-deployment-guide-linux.md)
- 10/05/2020: vydání služby [SAP HANA Scale-out HSR s Pacemaker na virtuálních počítačích Azure v](./sap-hana-high-availability-scale-out-hsr-rhel.md) průvodci konfigurací RHEL
- 09/30/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md), [ha pro SAP HANA horizontálního navýšení kapacity pomocí ANF na RHEL](./sap-hana-high-availability-netapp-files-red-hat.md) a [Nastavení Pacemaker na RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro přizpůsobení pokynů pro RHEL 8,1
- 09/29/2020: v článku [skupiny umístění blízkosti Azure pro zajištění optimální latence sítě s aplikacemi SAP se](./sap-proximity-placement-scenarios.md) PPGy omezení a doporučení týkající se používání. 
- 09/28/2020: Přidání nové Příručky k operaci úložiště pro SAP HANA pomocí Azure NetApp Files se [svazky systému souborů NFS v 4.1 na Azure NetApp Files pro SAP HANA](./hana-vm-operations-netapp.md)
- 09/23/2020: přidejte nové certifikované SKU pro HLI v [dostupných SKU pro HLI](./hana-available-skus.md) . 
- 09/20/2020: změny v dokumentech [týkající se nasazení azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_general.md), [SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](./dbms_guide_sqlserver.md), [Azure Virtual Machines Oracle DBMS pro úlohy SAP](./dbms_guide_oracle.md), nasazení Azure [Db2 Azure Virtual Machines DBMS pro úlohy SAP](./dbms_guide_ibm.md) k přizpůsobení nového návrhu konfigurace, který doporučuje oddělení binárních souborů a binárních souborů SAP do různých disků Azure. Přidejte také doporučení pro ultra disk do různých průvodců.
- 09/08/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md) pro vysvětlení definicí stonith
- 09/03/2020: Změna v [SAP HANA konfigurací úložiště virtuálního počítače Azure](./hana-vm-operations-storage.md) pro přizpůsobení minimálně 2 vstupně-výstupních operací na 1 GB s Ultra diskem
- 09/02/2020: Změna [dostupných SKU pro HLI](./hana-available-skus.md) , aby se lépe transparentní v tom, jaké SKU jsou certifikované pro Hana
- 25. srpna 2020: Změna v [ha pro SAP NW na virtuálních počítačích Azure v SLES s ANF](./high-availability-guide-suse-netapp-files.md) za účelem opravy překlepu
- 25. srpna 2020: Změna v [Průvodci ha pro SAP ASCS/SCS s WSFC a sdíleným diskem](./sap-high-availability-guide-wsfc-shared-disk.md), [Příprava infrastruktury Azure pro SAP ASCS/SCS pomocí služby WSFC a sdílený disk](./sap-high-availability-infrastructure-wsfc-shared-disk.md) a [instalace SAP NW ha pomocí služby WSFC a sdíleného disku](./sap-high-availability-guide-wsfc-shared-disk.md) pro zavedení možnosti použití architektury Azure Shared disk a Document ERS2
- 25. srpna 2020: vydání [příručky multi-SID ha pro SAP ASCS/SCS s WSFC a sdíleným diskem Azure](./sap-ascs-ha-multi-sid-wsfc-azure-shared-disk.md)
- 25. srpna 2020: Změna v [Průvodci ha pro SAP ASCS/SCS s WSFC a Azure NetApp Files (SMB)](./high-availability-guide-windows-netapp-files-smb.md), [Příprava infrastruktury Azure pro SAP ASCS/SCS se službou WSFC a sdílenými složkami](./sap-high-availability-infrastructure-wsfc-file-share.md), [Průvodce vysokou SID pro SAP ASCS/SCS s WSFC a sdíleným diskem](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) a [průvodcem pro SAP ASCS/SCS se službou WSFC a SOFS sdílenou složkou](./sap-ascs-ha-multi-sid-wsfc-file-share.md) v případě aktualizací obsahu a restrukturalizace v průvodcích ha pro SAP ASCS/SCS pomocí WFC a sdíleného disku 
- 21. srpna 2020: Přidání nového vydání operačního systému do [kompatibilních operačních systémů pro velké instance Hana](./os-compatibility-matrix-hana-large-instance.md) jako dostupného operačního systému pro jednotky HLI typu I a II
- 18. srpna 2020: vydání [ha pro SAP HANA horizontální navýšení kapacity pomocí ANF na RHEL](./sap-hana-high-availability-netapp-files-red-hat.md)
- 17. srpna 2020: Přidání informací o použití Azure Site Recovery pro přesun systémů SAP NetWeaver z místního prostředí do Azure v článku [azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md)
- 08/14/2020: Přidání rad konfigurace disku pro Db2 v článku [IBM Db2 Azure Virtual Machines DBMS nasazení pro úlohy SAP](./dbms_guide_ibm.md)
- 11. srpna 2020: Přidání RHEL 7,6 do [kompatibilních operačních systémů pro velké instance Hana](./os-compatibility-matrix-hana-large-instance.md) jako dostupný operační systém pro HLI jednotky typu I
- 10. srpna 2020: představení nákladových SAP HANA konfigurací úložiště v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) a provádění některých aktualizací pro [úlohy SAP v Azure: kontrolní seznam pro plánování a nasazení](./sap-deployment-checklist.md)
- 4. srpna 2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) a [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro zdůraznění důležitosti spolehlivého překladu názvů pro Pacemaker clustery
- 4. srpna 2020: Změna v [SAP NW ha v WFCS se sdílenou složkou](./sap-high-availability-installation-wsfc-file-share.md), [SAP NW ha v WFCS se sdíleným diskem](./sap-high-availability-installation-wsfc-shared-disk.md), [ha pro SAP NW na virtuálních počítačích Azure](./high-availability-guide.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES](./high-availability-guide-suse.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES s ANF](./high-availability-guide-suse-netapp-files.md), [ha pro SAP NW na virtuálních počítačích Azure v SLES průvodci pro více SID](./high-availability-guide-suse-multi-sid.md), [Vysoká dostupnost pro SAP NetWeaver na](./high-availability-guide-rhel.md)virtuálních počítačích Azure v RHEL, ha pro SAP NW na virtuálních počítačích Azure v RHEL [s ANF](./high-availability-guide-rhel-netapp-files.md) a [ha pro SAP NW na](./high-availability-guide-rhel-multi-sid.md) virtuálních počítačích Azure v RHEL – s využitím tohoto průvodce pro vysvětlení použití parametru `enque/encni/set_so_keepalive`
- 23. července 2020: přidání [velké instance SAP HANA uložit na s využitím rezervace Azure s](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md) vysvětlením, co potřebujete znát, než si koupíte velké instance SAP HANA rezervaci a jak si koupit
- 16. července 2020: Popište, jak použít Azure PowerShell k instalaci rozšíření nového virtuálního počítače pro SAP v [Průvodci nasazením](deployment-guide.md) .
- 4. července 2020: vydání  [služby Azure monitor pro řešení SAP (Preview)](./azure-monitor-overview.md)
- Července 01 2020: navržení levnější konfigurace úložiště na základě funkce Azure Premium Storage pro nárůst využití v dokumentu [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) 
- 24. června 2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) pro vydání nového vylepšeného agenta Azure pro vytváření a odolnější konfigurace STONITH pro zařízení na základě agenta Azure plot 
- 24. června 2020: Změna [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro uvolnění odolné konfigurace STONITH
- 23. června 2020: změny pro [Azure Virtual Machines Planning and Implementation for SAP NetWeaver](./planning-guide.md) Guide a zavedení [Azure Storage typů pro Průvodce úlohami SAP](./planning-guide-storage.md)
- 06/22/2020: Přidejte kroky instalace pro nové rozšíření virtuálních počítačů pro SAP do [Průvodce nasazením](deployment-guide.md) .
- 16. června 2020: Změna [Možnosti připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) pro přidání odkazu do dokumentace k infrastruktuře veřejných cloudů SUSE 101 
- 10. června 2020: přidání nových HLI SKU do [dostupných SKU pro](./hana-available-skus.md) [architekturu úložiště HLI a SAP Hana (velké instance)](./hana-storage-architecture.md)
- 21. května 2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) a [Nastavení Pacemaker v RHEL v Azure](./high-availability-guide-rhel-pacemaker.md) pro přidání odkazu na [připojení k veřejnému koncovému bodu pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md)  
- 19. května 2020: přidejte důležitou zprávu, která nepoužívá skupinu kořenových svazků při použití LVM pro svazky související se službou HANA v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) .
- 19. května 2020: přidejte nový podporovaný operační systém pro velký typ instance pro HANA a v části [kompatibilní operační systémy pro velké instance Hana](/- azure/virtual-machines/workloads/sap/os-compatibility-matrix-hana-large-instance) .
- 12. května 2020: Změna [připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](./high-availability-guide-standard-load-balancer-outbound-connections.md) k aktualizaci odkazů a přidání informací pro konfiguraci brány firewall třetích stran
- 11. května 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md) a nastavení vytrvalost prostředků na hodnotu 0 u prostředku netcat, protože to vede k jednoduššímu převzetí služeb při selhání 
- 5. května 2020: změny v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md) představují, aby Gen2 nasazení byla k dispozici pro rodinu virtuálních počítačů.
- 24. dubna 2020: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s ANF na SLES](./sap-hana-scale-out-standby-netapp-files-suse.md), v SAP HANA škálování na více instancí [s pohotovostním uzlem na virtuálních počítačích Azure s ANF na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md), [Vysoká dostupnost pro SAP NetWeaver na](./high-availability-guide-suse-netapp-files.md) virtuálních počítačích Azure v SLES s ANF, aby [bylo možné](./high-availability-guide-rhel-netapp-files.md) vyjasnění automaticky přiřadit IP adresy pro NetWeaver svazky
- 22. dubna 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SLES](./sap-hana-high-availability.md) pro odebrání atributu meta `is-managed` z instrukcí, protože je v konfliktu s umístěním clusteru do nebo z režimu údržby
- 21. dubna 2020: Přidání SQL Azure DB jako podporované DBMS pro SAP pro SAP (Hybris) Commerce Platform 1811 a novější v článcích [co je software SAP podporován pro nasazení Azure](./sap-supported-product-on-azure.md) a [certifikace a konfigurace sap běžící v Microsoft Azure](./sap-certifications.md)
- 16. dubna 2020: Přidání SAP HANA jako podporovaného systému DBMS pro SAP (Hybris) Commerce Platform v článcích o [tom, jaký software SAP je podporován pro nasazení Azure](./sap-supported-product-on-azure.md) a [certifikace a konfigurace sap běžící na Microsoft Azure](./sap-certifications.md)
- 13. dubna 2020: správné čísla vydání SAP pomocného protokolu SAP v systému [SAP POmocného Virtual Machines DBMS pro úlohy SAP](./dbms_guide_sapase.md)
- 7. dubna 2020: Změna [Nastavení Pacemaker v SLES v Azure](./high-availability-guide-suse-pacemaker.md) pro vysvětlení cloudu – netconfig – pokyny pro Azure
- 6. dubna 2020: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](./sap-hana-scale-out-standby-netapp-files-suse.md) a v [SAP HANA škálování s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](./sap-hana-scale-out-standby-netapp-files-rhel.md) pro odebrání odkazů na NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (nahrazeno pomocí [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 31. března 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na SLES](./sap-hana-high-availability.md) a [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v RHEL](./sap-hana-high-availability-rhel.md) , abyste přidali pokyny, jak určit velikost pruhu při vytváření prokládaných svazků.
- 27. března 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) pro zarovnávání možností připojení systému souborů k NetApp TR-4746 (odebrání možnosti připojení synchronizace)
- 26. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](./high-availability-guide-suse-multi-sid.md) pro přidání odkazu na NetApp TR-4746
- 26. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES s Azure NETAPP Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md)vysoká dostupnost pro [systém souborů NFS na virtuálních počítačích Azure na SLES](./high-availability-guide-suse-nfs.md), [Vysoká dostupnost pro SAP NetWeaver na](./high-availability-guide-suse-multi-sid.md)virtuálních počítačích Azure v RHEL, s vysokou dostupností pro SAP NetWeaver na virtuálních počítačích Azure v [RHEL pro aplikace](./high-availability-guide-rhel.md) SAP a pro zajištění [vysoké dostupnosti pro SAP NETWEAVER na virtuálních počítačích Azure v RHEL pomocí Azure NetApp Files pro aplikace SAP](./high-availability-guide-rhel-netapp-files.md) pro aktualizace diagramů a vysvětlení pokynů pro vytváření fondu back-endu Azure Load Balancer
- 19. března 2020: hlavní revize dokumentu [rychlý Start: ruční instalace SAP HANA s jednou instancí v azure Virtual Machines](./hana-get-started.md) na [instalaci SAP HANA na Azure Virtual Machines](./hana-get-started.md)
- 17. března 2020: Změna [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md) pro odebrání nastavení konfigurace SBD, které už není nutné
- Březen 16 2020: vyjasnění scénáře certifikace sloupce v SAP HANA certifikované platformě IaaS, v [jakém softwaru SAP se podporuje nasazení Azure](./sap-supported-product-on-azure.md)
- 03/11/2020: Změna v [úlohách SAP v podporovaných scénářích virtuálních počítačů Azure](./sap-planning-supported-configurations.md) pro vysvětlení podpory více databází na instanci systému DBMS
- 11. března 2020: Změna v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md) s vysvětlením virtuálních počítačů generace 1 a generace 2
- 10. března 2020: Změna v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) pro objasnění stávajících omezení propustnosti v ANF
- 9. března 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NETAPP Files pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) [Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure v SUSE Linux Enterprise Server](./high-availability-guide-suse-nfs.md), [Nastavení Pacemaker v SUSE Linux Enterprise Server v Azure](./high-availability-guide-suse-pacemaker.md), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích azure v SUSE Linux Enterprise Server s Pacemaker](./dbms-guide-ha-ibm.md), [vysokou dostupností SAP HANA na](./sap-hana-high-availability.md) virtuálních počítačích Azure na SUSE Linux Enterprise Server a [vysoké dostupnosti pro SAP NetWeaver na](./high-availability-guide-suse-multi-sid.md) virtuálních počítačích Azure v SLES – s agentem pro podporu Azure-9,1 
- 05. března 2020: změny struktury a změny obsahu pro oblasti Azure a virtuální počítače Azure v [azure Virtual Machines plánování a implementace pro SAP NetWeaver](./planning-guide.md)
- 03/03/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) umožňující změnu na efektivnější rozložení svazku ANF
- Března 01 2020: [Příručka pro zálohování SAP HANA v Azure Virtual Machines](./sap-hana-backup-guide.md) , která zahrnuje Azure Backup službu. Zmenšený a zúžený obsah v [SAP HANA Azure Backup na úrovni souborů](./sap-hana-backup-file-level.md) a odstranil třetí dokument, který se bude řešit zálohováním prostřednictvím snímku disku. Obsah se zpracovává v Průvodci zálohováním pro SAP HANA v Azure Virtual Machines 
- 27. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md) a [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES Průvodce vícenásobnými SID](./high-availability-guide-suse-multi-sid.md) pro úpravu neúspěšného parametru clusteru
- 26. února 2020: Změna v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](./hana-vm-operations-storage.md) pro upřesnění výběru systému souborů pro Hana v Azure
- 26. února 2020: Změna v [architektuře vysoké dostupnosti a scénářích pro SAP](./sap-high-availability-architecture-scenarios.md) , aby zahrnovaly odkaz na ha pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID
- 26. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](./high-availability-guide-suse.md), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](./high-availability-guide-suse-netapp-files.md), [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver na RHEL](./high-availability-guide-rhel.md) a [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) pro odebrání příkazu, který nepodporují cluster/ASCS.
- 26. února 2020: vydání  [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID](./high-availability-guide-rhel-multi-sid.md) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID
- 02/25/2020: Změna v [architektuře vysoké dostupnosti a scénářích](./sap-high-availability-architecture-scenarios.md) , aby mohl SAP přidat odkazy na novější články ha
- 25. února 2020: Změna [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Pacemaker](./dbms-guide-ha-ibm.md) , aby odkazovala na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 21. února 2020: kompletní revize článku [SAP POmocného programu sap Virtual Machines DBMS pro úlohy SAP](./dbms_guide_sapase.md)
- 21. února 2020: Změna [SAP HANA konfigurace úložiště virtuálního počítače Azure](./hana-vm-operations-storage.md) tak, aby reprezentovala nové doporučení pro/Hana/data a přidání nastavení v/v Scheduleru
- 21. února 2020: změny ve velkých instancích dokumentů HANA reprezentují nově certifikované SKU S224 a S224m.
- 21. února 2020: Změna [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL](./high-availability-guide-rhel.md) a [virtuálních počítačích Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md) pro úpravu omezení clusteru pro architekturu replikace serveru fronty 2 (ENSA2)
- 20. února 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](./high-availability-guide-suse-multi-sid.md) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID

