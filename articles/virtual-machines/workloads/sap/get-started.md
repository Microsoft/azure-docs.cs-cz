---
title: Začínáme s SAP na virtuálních počítačích Azure | Microsoft Docs
description: Přečtěte si o řešeních SAP, která běží na virtuálních počítačích v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 07/04/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6f2462e9bba909099ea3bb2910e26596427c0a4e
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2020
ms.locfileid: "85954294"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spouštění scénářů pro úlohy SAP

Když použijete Microsoft Azure, můžete spolehlivě spouštět klíčové úlohy a scénáře SAP na škálovatelné, kompatibilní a podnikové prověřené platformě. Získáte škálovatelnost, flexibilitu a úspory nákladů Azure. Díky rozšířenému partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP napříč vývojovými a testovacími a provozními scénáři v Azure a plně podporovat. Z SAP NetWeaver do SAP S/4HANA, SAP BI v systému Linux do Windows a SAP HANA k SQL, máme na vás přehled.

Kromě hostování scénářů SAP NetWeaver s různými systémy DBMS v Azure můžete hostovat i další scénáře úloh SAP, jako je například SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která nastavuje Azure odděleně. Aby bylo možné hostovat víc paměti a scénářů SAP náročných na prostředky procesoru, které zahrnují SAP HANA, Azure nabízí použití holého hardwaru vyhrazeného pro zákazníky. Pomocí tohoto řešení můžete spouštět SAP HANA nasazení, která vyžadují až 24 TB (120 TB škálování) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénářů pro úlohy SAP v Azure taky může vytvořit požadavky na integraci identit a jednotné přihlašování. K této situaci může dojít, když použijete Azure Active Directory (Azure AD) k propojení různých komponent SAP a SAP software-as-a-Service (SaaS) nebo Platform-as-a-Service (PaaS). Seznam takových scénářů integrace a jednotného přihlašování s entitami služby Azure AD a SAP je popsán v části "AAD SAP Identity Integration and Single přihlašování".

## <a name="changes-to-the-sap-workload-section"></a>Změny v části úlohy SAP
Změny dokumentů v části věnované úlohám SAP v Azure najdete na konci tohoto článku. Záznamy v protokolu změn se uchovávají přibližně po 180 dnech.

## <a name="you-want-to-know"></a>Chcete znát
Pokud máte konkrétní otázky, budeme v této části úvodní stránky ukazovat na konkrétní dokumenty nebo toky. Chcete znát:

- Jaké virtuální počítače Azure a jednotky velkých instancí HANA jsou podporované pro vydávání verzí softwaru SAP a verze operačních systémů. Přečtěte si dokument, [který software SAP podporuje pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) pro odpovědi a proces hledání informací.
- Jaké scénáře nasazení SAP jsou podporovány u virtuálních počítačů Azure a velkých instancí HANA. Informace o podporovaných scénářích najdete v dokumentu:
    - [Podporované scénáře pro úlohy SAP na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Podporované scénáře pro velkou instanci HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Které služby Azure, typy virtuálních počítačů Azure a služby Azure Storage jsou k dispozici v různých oblastech Azure, Prohlédněte si [Dostupné produkty](https://azure.microsoft.com/global-infrastructure/services/) v lokalitě v oblasti 
- Funguje rámec HA třetí strany, kromě Windows a Pacemaker? Zkontrolujte dolní část [poznámky k podpoře SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533)

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řada dokumentů vás provede SAP HANA v Azure (velké instance) nebo pro krátké instance s vysokou kapacitou HANA. Informace o velkých instancích HANA najdete v dokumentu [Přehled a architektura SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a Projděte si související dokumentaci v oddílu velká instance Hana.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace obsahuje různé aspekty SAP HANA. V rámci předpokladů byste měli být obeznámeni s hlavními službami Azure, které poskytují základní služby Azure IaaS. Proto potřebujete znát výpočetní výkon, úložiště a síť Azure. Mnohé z těchto věcí jsou zpracovávány v [Průvodci plánováním Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)NetWeaver souvisejících s SAP. 

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasazené na virtuálních počítačích Azure
Tato část obsahuje dokumentaci pro plánování a nasazení pro SAP NetWeaver a firmy One na Azure. Dokumentace se zaměřuje na základy a používání databází jiných než HANA s úlohou SAP v Azure. Dokumenty a články pro vysokou dostupnost představují také základ vysoké dostupnosti pro HANA v Azure, například:

- [Průvodce plánováním Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Ochrana nasazení aplikace s více vrstvami SAP NetWeaver pomocí Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Konektor SAP LaMa pro Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Informace o databázích, které nejsou HANA v rámci úlohy SAP v Azure, najdete v těchto tématech:

- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Nasazení IBM DB2 Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [MaxDB SAP, živá mezipaměť a nasazení obsahu serveru na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Informace o SAP HANA databázích v Azure najdete v části SAP HANA na virtuálních počítačích Azure.

Informace o vysoké dostupnosti úlohy SAP v Azure najdete v těchto tématech:

- [Vysoká dostupnost Azure Virtual Machines pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Tento dokument odkazuje na různé jiné architektury a dokumenty scénářů. V novějších dokumentech scénářů jsou k dispozici odkazy na podrobné technické dokumenty, které popisují nasazení a konfiguraci různých metod s vysokou dostupností. Různé dokumenty, které ukazují, jak vytvořit a nakonfigurovat vysokou dostupnost pro úlohy SAP NetWeaver, zahrnují systémy Linux a operační systémy Windows.


Informace o integraci mezi Azure Active Directory (Azure AD) a službami SAP a jednotným přihlašováním najdete v těchto tématech:

- [Kurz: Azure Active Directory integrace se SAP cloudem pro zákazníky](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s ověřováním identity cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s cloudovou platformou SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Prostředí S/4HANA: jednotné přihlašování Fiori hlavní panel SAML pomocí Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informace o integraci služeb Azure do komponent SAP najdete v těchto tématech:

- [Použití SAP HANA v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat pro SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Protokol změn

- 7/04/2020: vydání [služby Azure monitor pro řešení SAP (Preview)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/azure-monitor-overview)
- 07/01/2020: Navrhněte levnější konfiguraci úložiště na základě nárůstu funkčnosti Azure Premium Storage v dokumentu [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) . 
- 06/24/2020: Změna v [Nastavení Pacemaker v SLES v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro vydání nového vylepšeného agenta Azure plot a větší odolné konfigurace STONITH pro zařízení na základě agenta Azure plot 
- 06/24/2020: Změna v [Nastavení Pacemaker v RHEL v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pro uvolnění odolné konfigurace STONITH
- 06/23/2020: změny v [Azure Virtual Machines Planning and Implementation pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) Guide a zavedení [Azure Storage typů pro Průvodce úlohami SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide-storage)
- 06/22/2020: Přidejte kroky instalace pro nové rozšíření virtuálních počítačů pro SAP do [Průvodce nasazením](deployment-guide.md) .
- 06/16/2020: Změna [připojení veřejných koncových bodů pro virtuální počítače s využitím Azure Standard interního nástroje ve scénářích SAP ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) pro přidání odkazu do dokumentace k infrastruktuře veřejných cloudů SUSE 101 
- 06/10/2020: přidání nových HLI SKU do [dostupných SKU pro](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-available-skus) [architekturu úložiště HLI a SAP Hana (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-storage-architecture)
- 05/21/2020: Změna [Nastavení Pacemaker v SLES v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) a [Nastavení Pacemaker v RHEL v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pro přidání odkazu na [připojení k veřejnému koncovému bodu pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)  
- 05/19/2020: přidejte důležitou zprávu, která nepoužívá skupinu kořenových svazků při použití LVM pro svazky související se službou HANA v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) .
- 05/19/2020: přidejte nový podporovaný operační systém pro velký typ instance (II) pro HANA a [kompatibilní operační systémy pro velké instance HANA] (https://docs.microsoft.com/
- Azure/virtuální počítače/úlohy/SAP/OS – kompatibilita-Matrix-Hana-velká instance)
- 05/12/2020: Změna [připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) k aktualizaci odkazů a přidání informací pro konfiguraci brány firewall třetích stran
- 05/11/2020: změňte [vysokou dostupnost SAP HANA na virtuálních počítačích Azure v SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) a nastavte vytrvalost prostředků na hodnotu 0 pro prostředek netcat, protože to vede k efektivnějšímu převzetí služeb při selhání. 
- 05/05/2020: změny v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) , které vyjadřují, že Gen2 nasazení jsou k dispozici pro rodinu virtuálních počítačů: Mv1
- 04/24/2020: změny ve [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s ANF v SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse), ve SAP HANA škálování na více instancí [s pohotovostním uzlem na virtuálních počítačích Azure s ANF na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)a s [vysokou dostupností](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro SAP NetWeaver na virtuálních počítačích Azure v SLES s [ANF, které](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) umožňují objasnění, že se automaticky přiřazují IP adresy pro NetWeaver svazky
- 04/22/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) pro odebrání atributu meta `is-managed` z pokynů, protože je v konfliktu s umístěním clusteru do režimu údržby nebo z něj.
- 04/21/2020: Přidání SQL Azure DB jako podporované DBMS pro SAP pro SAP (Hybris) Commerce Platform 1811 a novější v článcích o [tom, jaký software SAP je podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) a [certifikace a konfigurace sap běžící v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/16/2020: Přidání SAP HANA jako podporovaného systému DBMS pro SAP (Hybris) Commerce Platform v článcích o [tom, co je software SAP pro nasazení Azure podporovaný](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) a [certifikace a konfigurace sap spuštěné v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 04/13/2020: správné číslo vydání verze SAP pomocného protokolu SAP ve [službě SAP POmocného softwaru Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 04/07/2020: Změna [Nastavení Pacemaker v SLES v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro vysvětlení cloudu – netconfig – pokyny pro Azure
- 04/06/2020: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) a v [SAP HANA škálování s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) pro odebrání odkazů na NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (nahrazeno pomocí [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf))
- 03/31/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) a [vysokou dostupnost SAP HANA na virtuálních počítačích Azure v RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) , aby se přidaly pokyny, jak určit velikost pruhu při vytváření prokládaných svazků.
- 03/27/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) pro zarovnávání možností připojení systému souborů k NetApp TR-4746 (odebrání možnosti připojení synchronizace)
- 03/26/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro přidání odkazu na NetApp TR-4746
- 03/26/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES s Azure NETAPP Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files)vysoká dostupnost pro [systém souborů NFS na virtuálních počítačích Azure na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Vysoká dostupnost pro SAP NetWeaver na](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)virtuálních počítačích Azure v SLES, s vysokou dostupností pro SAP NetWeaver na virtuálních počítačích Azure v [RHEL pro aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) SAP a pro zajištění [vysoké dostupnosti pro SAP NETWEAVER na virtuálních počítačích Azure v RHEL pomocí Azure NetApp Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro aktualizace diagramů a vysvětlení pokynů pro vytváření fondu back-endu Azure Load Balancer
- 03/19/2020: hlavní revize dokumentu [rychlý Start: ruční instalace SAP HANA s jednou instancí v azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) na [instalaci SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: při [nastavování Pacemaker v Azure na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) odeberte nastavení konfigurace SBD, které už není potřeba.
- 03/16/2020: vyjasnění scénáře certifikace sloupce v SAP HANA certifikované platformě IaaS, v [jakém softwaru SAP se podporuje nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 03/11/2020: Změna v [úlohách SAP v podporovaných scénářích virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) pro vysvětlení podpory více databází na instanci systému DBMS
- 03/11/2020: Změna v [Azure Virtual Machines plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) s vysvětlením virtuálních počítačů generace 1 a generace 2
- 03/10/2020: Změna v [SAP HANA konfigurací úložiště virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pro objasnění stávajících omezení PROPUSTNOSTi ANF
- 03/09/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Azure NETAPP Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) [Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích Azure v SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Nastavení Pacemaker v SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích azure v SUSE Linux Enterprise Server s Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [vysokou dostupností SAP HANA na](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) virtuálních počítačích Azure na SUSE Linux Enterprise Server a [vysoké dostupnosti pro SAP NetWeaver na](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) virtuálních počítačích Azure v SLES – s agentem pro podporu Azure-9,1 
- 03/05/2020: změny struktury a změny obsahu pro oblasti Azure a virtuální počítače Azure v [azure Virtual Machines plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) umožňující změnu na efektivnější rozložení svazku ANF
- 03/01/2020: přepracovaná [Příručka zálohování pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) , aby zahrnovala Azure Backup službu. Zmenšený a zúžený obsah v [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) a odstranil třetí dokument, který se bude řešit zálohováním prostřednictvím snímku disku. Obsah se zpracovává v Průvodci zálohováním pro SAP HANA v Azure Virtual Machines 
- 27. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) a [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure v SLES Průvodce vícenásobnými SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro úpravu neúspěšného parametru clusteru
- 26. února 2020: Změna v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pro upřesnění výběru systému souborů pro Hana v Azure
- 26. února 2020: Změna v [architektuře vysoké dostupnosti a scénářích pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) , aby zahrnovaly odkaz na ha pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID
- 26. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [virtuální počítače Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro odebrání příkazu, který nepodporují cluster/ASCS.
- 26. února 2020: vydání [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci RHEL s více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID
- 02/25/2020: Změna v [architektuře vysoké dostupnosti a scénářích](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) , aby mohl SAP přidat odkazy na novější články ha
- 25. února 2020: Změna [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) , aby odkazovala na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 21. února 2020: kompletní revize článku [SAP POmocného programu sap Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 21. února 2020: Změna [SAP HANA konfigurace úložiště virtuálního počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) tak, aby reprezentovala nové doporučení pro/Hana/data a přidání nastavení v/v Scheduleru
- 21. února 2020: změny ve velkých instancích dokumentů HANA reprezentují nově certifikované SKU S224 a S224m.
- 21. února 2020: Změna [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [virtuálních počítačích Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro úpravu omezení clusteru pro architekturu replikace serveru fronty 2 (ENSA2)
- 20. února 2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro přidání odkazu na průvodce clusterem s podporou SUSE multi-SID
- 13. února 2020: změny [v Azure Virtual Machines plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) implementují odkazy na nové dokumenty
- 13. února 2020: Přidání nové [úlohy SAP dokumentu SAP ve scénáři podporovaném virtuálním počítačem Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
- 13. února 2020: Přidání nového dokumentu, [který podporuje software SAP pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 13. února 2020: Změna [vysoké dostupnosti IBM DB2 LUW na virtuálních počítačích Azure na serveru Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) , aby odkazovala na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 13. února 2020: přidejte nové typy virtuálních počítačů do [certifikace a konfigurací SAP běžících na Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 13. února 2020: Přidání nové poznámky k podpoře SAP [v Azure: kontrolní seznam pro plánování a nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 13. února 2020: Změna [vysoké dostupnosti virtuálních počítačů Azure pro SAP NetWeaver v RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [virtuálních počítačích Azure s vysokou dostupností pro SAP NetWeaver v RHEL s Azure NetApp Files](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro zarovnávání časových limitů pro časový limit Red Hat
- 11. února 2020: vydání [SAP HANA do migrace velkých instancí Azure do azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 7. února 2020: Změna [Možnosti připojení veřejných koncových bodů pro virtuální počítače pomocí Azure Standard interního nástroje ve scénářích SAP ha](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) k aktualizaci ukázkového snímku NSG
- 03. února 2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure v SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) a [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure v SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) pro odebrání upozornění na použití pomlčky v názvech hostitelů uzlů clusteru v SLES
- 28. ledna 2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) za účelem zarovnání časových limitů SAP HANA prostředků clusteru do doporučení Red Hat timeout
- 17. ledna 2020: Změna ve [skupinách umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) pro změnu oddílu přesunutí stávajících virtuálních počítačů do skupiny umístění blízkosti
- 17. ledna 2020: Změna v [konfiguracích úloh SAP pomocí zóny dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) odkazuje na postup, který automatizuje měření latence mezi zóny dostupnosti
- 16. ledna 2020: Změna [způsobu instalace a konfigurace SAP Hana (velkých instancí) v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) pro přizpůsobení verzí operačního systému pro IaaS do adresáře hardwaru Hana
- 16. ledna 2020: změny [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci SLES s více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro přidání pokynů pro systémy SAP s použitím architektury serveru fronty 2 (ENSA2)
- 10. ledna 2020: změny ve [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) a v [SAP HANA škálování s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) pro přidání pokynů k `nfs4_disable_idmapping` trvalému provedení změn.
- 10. ledna 2020: změny [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES s Azure NetApp Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) a v [Azure Virtual Machines vysoké dostupnosti pro SAP NetWeaver v RHEL s Azure NETAPP Files pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro přidání pokynů k připojení Azure NetApp Filesch svazků názvů NFSv4.
- 23. prosince 2019: vydání [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SLES průvodce pro více SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 18. prosince 2019: vydání [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 21. listopadu 2019: změny v [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) pro zjednodušení konfigurace mapování ID NFS a změna doporučeného primárního síťového rozhraní pro zjednodušení směrování.
- 15. listopadu 2019: drobné změny [vysoké dostupnosti pro SAP NetWeaver v SUSE Linux Enterprise Server s Azure NetApp Files pro aplikace SAP](high-availability-guide-suse-netapp-files.md) a [vysokou dostupnost pro sap NetWeaver v Red Hat Enterprise Linux s Azure NETAPP Files pro aplikace SAP](high-availability-guide-rhel-netapp-files.md) pro vysvětlení omezení velikosti fondu kapacity a příkazu Remove, který podporuje jenom NFSv3 verzi.
- 12. listopadu 2019: vydání [vysoké dostupnosti pro SAP NetWeaver v systému Windows s využitím protokolu SMB (Azure NetApp Files)](high-availability-guide-windows-netapp-files-smb.md)
- 8. listopadu 2019: změny [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure v SUSE Linux Enterprise Server](sap-hana-high-availability.md), [nastavení SAP HANA systémové replikace na virtuálních počítačích Azure (virtuální počítače)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines vysoké dostupnosti pro SAP NetWeaver v SUSE Linux Enterprise Server pro aplikace SAP](high-availability-guide-suse.md), Azure Virtual Machines High pro [SAP NetWeaver on SUSE Linux Enterprise Server s Azure NetApp Files](high-availability-guide-suse-netapp-files.md), [Azure Virtual Machines vysoká dostupnost](high-availability-guide-rhel.md)pro systém souborů SAP NetWeaver v Red Hat Enterprise Linux, Azure Virtual Machines vysoká dostupnost pro systém souborů SAP [NetWeaver na Red Hat Enterprise Linux s Azure NetApp Files](high-availability-guide-rhel-netapp-files.md)a [vysokou dostupností pro NFS na virtuálních počítačích Azure](high-availability-guide-suse-nfs.md)v SUSE Linux Enterprise Server, na [virtuálních](high-availability-guide-rhel-glusterfs.md) počítačích Azure v Red Hat Enterprise Linux, které doporučují Azure Load Balancer  
- 8. listopadu 2019: změny v [kontrolním seznamu plánování úloh SAP a nasazení](sap-deployment-checklist.md) k objasnění doporučení pro šifrování  
- 4. listopadu 2019: změny v [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření clusteru přímo s konfigurací jednosměrového vysílání  


