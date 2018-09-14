---
title: Začínáme s řešením SAP na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Další informace o řešení SAP na virtuálních počítačích (VM) v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/13/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0cf6291cb12366c4f710092c1b36c8cb0b8c14fb
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2018
ms.locfileid: "45578391"
---
# <a name="using-azure-for-hosting-and-running-sap-workload-scenarios"></a>Použití Azure k hostování a spuštění scénáře úloh řešení SAP

Když zvolíte Microsoft Azure jako váš partner cloudové SAP, budete moct využijte možnost spolehlivě spouštět nejdůležitější úlohy SAP a scénářů na škálovatelné a kompatibilní prověřená v podnikovém platformě.  Získejte škálovatelnost, flexibilitu a snížení nákladů, které poskytuje Azure. V rozšířené partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP pro vývoj/testování a produkčními scénáři v Azure – a mít přitom plnou podporu. Od SAP Netweaveru po SAP S4/HANA, SAP BI, Linuxu po Windows, SAP HANA po SQL máme všechno, co potřebujete.

Kromě hostování SAP NetWeaver scénáře pomocí různých DBMS v Azure, které můžete hostovat různé jiné scénáře úloh řešení SAP, jako je SAP BI v Azure. Dokumentace k nasazení SAP NetWeaver na nativních virtuálních počítačích Azure najdete v části "SAP NetWeaver na virtuálních počítačích Azure."

Jedinečnost Azure pro SAP HANA je jedinečný nabídka, která nastavuje Azure kromě soutěže. Chcete-li povolit hostování více paměti a procesoru prostředků náročné SAP scénáře zahrnující SAP HANA, Azure nabízí využití zákazníka vyhrazený hardware úplné obnovení systému za účelem spuštění nasazení SAP HANA, které vyžadují až 20 TB paměti pro (60 TB scale-out) S/4HANA nebo jiné úlohy SAP HANA. Tento jedinečný Azure řešení SAP HANA v Azure (velké instance) umožňuje spouštění řešení SAP HANA na vyhrazeném hardwaru úplné obnovení systému se na aplikační vrstvě SAP nebo úlohy VMware střední vrstvy hostované na nativních virtuálních počítačích Azure. Toto řešení je popsána v několika dokumentů v části "SAP HANA v Azure (velké instance)."   

Hostování scénáře úloh řešení SAP v Azure můžete vytvořit požadavky integrace identit a Single-Sign-On pomocí Azure Active Directory pro různé součásti SAP a SAP SaaS nebo nabízí PaaS. Seznam těchto integrace a scénáře jednotného přihlašování s Azure Active Directory (AAD) a SAP entity je popsané a popsány v části "integrace identit AAD SAP a Single-Sign-On."

## <a name="latest-changes"></a>Nejnovější změny

Dokumentace ke službě kolem SAP HANA dynamické ovládání datových vrstev pro virtuální počítače Azure

- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#sap-hana-dynamic-tiering-20-for-azure-virtual-machines)

Dokumentace ke službě kolem SAP HANA škálovat do M128s virtuálního počítače Azure je teď přidat do:

- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations#configuring-azure-infrastructure-for-sap-hana-scale-out)
- [Dostupnost SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)


## <a name="sap-hana-on-sap-hana-on-azure-large-instances"></a>SAP HANA na systému SAP HANA v Azure (velké instance)

Řada dokumentace vás provede SAP HANA v Azure (velké instance) nebo v krátké velkých instancích HANA. Dokumenty pokrytí oblastech uvedených velkých instancích HANA:

- [Přehled systému SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura a možnosti připojení k SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalace SAP HANA na systému SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Vysoká dostupnost a zotavení po havárii SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Řešení potíží a monitorování systému SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Další kroky:

- Čtení [přehled a architektura SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA ve službě Azure Virtual Machines
Tato část dokumentace obsahuje různé aspekty systému SAP HANA. Předpokladem je měli byste se seznámit s instančního objektu služby Azure, které poskytují základní služby Azure IaaS, tedy většinou znalost výpočetní prostředky Azure, úložiště a sítě. Mnoho z těchto témat jsou zpracovány v SAP NetWeaver související [Azure Planning Guide](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Dokumentace ke službě, specifické pro HANA v Azure se skládá z tohoto seznamu článků a jejich dílčím článků:

- [Rychlý start: Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Vysoká dostupnost SAP HANA pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Dostupnost SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostupnost SAP HANA v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Průvodce zálohováním pro SAP HANA v Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Zálohování SAP HANA založené na snímcích úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasadit v Azure Virtual Machines
V této části najdete dokumentaci k plánování a nasazení pro SAP NetWeaver a Business jeden v Azure. Dokumentace v této kapitole se zaměřuje okolo základy a používání bez HANA databází s úlohami SAP v Azure. Vzhledem k tomu dokumenty a články pro vysokou dostupnost jsou základem pro vysokou dostupnost HANA v Azure i. seznam článků jako:

- [SAP Business One ve službě Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Provoz SAP Netweaveru na virtuálních počítačích Microsoft Azure SUSE Linuxem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines, plánování a implementace SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrana nasazení vícevrstvé aplikace SAP NetWeaver pomocí Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Konektor SAP LaMa pro Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Týkající se jiných HANA databáze v rámci úloh SAP v azure jako seznam dokumentů:

- [Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Nasazení SQL serveru Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [SAP MaxDB liveCache a nasazení obsahu serveru na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Databáze SAP HANA v Azure najdete v části SAP HANA v Azure Virtual Machines.

Pro zajištění vysoké dostupnosti úloh SAP v Azure jsou k dispozici v následujících dokumentech:

- [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)
- [Architektura pro vysokou dostupnost a scénáře pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
- [Využívat infrastrukturu Azure restartování virtuálního počítače k dosažení "vysoké dostupnosti" systému SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-higher-availability-architecture-scenarios)
- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdíleného disku clusteru v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Instance SAP ASCS/SCS clusteru v clusteru převzetí služeb při selhání Windows s použitím sdílené složky v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Připravit infrastrukturu Azure pro SAP HA pomocí Windows cluster převzetí služeb při selhání a sdíleného disku pro SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk)
- [Příprava infrastruktury Azure pro zajištění vysoké dostupnosti SAP pomocí Windows převzetí služeb při selhání clusteru a sdílenou složku pro instance SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)
- [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
- [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)
- [https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk)
- [Vysoká dostupnost SAP NetWeaver nainstalovat Windows převzetí služeb při selhání clusteru a sdílenou složku pro SAP ASCS/SCS instance v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share)
- [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [SAP ASCS/SCS instance s několika SID vysokou dostupnost s možnostmi Windows Server Failover Clustering a sdíleného disku v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
- [SAP ASCS/SCS instance s několika SID vysokou dostupnost s možnostmi Windows Server Failover Clustering a sdílení souborů v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)


Pro integraci mezi službami Azure Active Directory (AAD) a služby SAP a Single-Sign-On, dokumentech jsou uvedeny jako:

- [Kurz: Integrace Azure Active Directory se SAP cloudem pro odběratele](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se SAP Netweaverem](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Prostředí – S/4HANA Fiori Launchpad SAML jednotného přihlašování s Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Integrace služeb Azure do komponenty SAP seznamu dokumentů vypadá jako:

- [Použití SAP HANA v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




