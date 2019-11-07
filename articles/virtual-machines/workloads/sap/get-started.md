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
ms.date: 11/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d444ed21f3eb86975442f2176b8caa8dca6e0c3f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73602430"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spouštění scénářů pro úlohy SAP

Když použijete Microsoft Azure, můžete spolehlivě spouštět klíčové úlohy a scénáře SAP na škálovatelné, kompatibilní a podnikové prověřené platformě. Získáte škálovatelnost, flexibilitu a úspory nákladů Azure. Díky rozšířenému partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP napříč vývojovými a testovacími a provozními scénáři v Azure a plně podporovat. Z SAP NetWeaver do SAP S/4HANA, SAP BI v systému Linux do Windows a SAP HANA k SQL, máme na vás přehled.

Kromě hostování scénářů SAP NetWeaver s různými systémy DBMS v Azure můžete hostovat i další scénáře úloh SAP, jako je například SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která nastavuje Azure odděleně. Aby bylo možné hostovat víc paměti a scénářů SAP náročných na prostředky procesoru, které zahrnují SAP HANA, Azure nabízí použití holého hardwaru vyhrazeného pro zákazníky. Pomocí tohoto řešení můžete spouštět SAP HANA nasazení, která vyžadují až 24 TB (120 – TB škálování) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénářů pro úlohy SAP v Azure taky může vytvořit požadavky na integraci identit a jednotné přihlašování. K této situaci může dojít, když použijete Azure Active Directory (Azure AD) k propojení různých komponent SAP a SAP software-as-a-Service (SaaS) nebo Platform-as-a-Service (PaaS). Seznam takových scénářů integrace a jednotného přihlašování s entitami služby Azure AD a SAP je popsán v části "AAD SAP Identity Integration and Single přihlašování".

## <a name="changes-to-the-sap-workload-section"></a>Změny v části úlohy SAP
Změny dokumentů v části věnované úlohám SAP v Azure najdete na konci tohoto článku.


## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řada dokumentů vás provede SAP HANA v Azure (velké instance) nebo pro krátké instance s vysokou kapacitou HANA. Informace o následujících oblastech velkých instancí HANA najdete v těchto tématech:

- [Přehled SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura a připojení k SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalace SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Vysoká dostupnost a zotavení po havárii SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Řešení potíží a monitorování SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Další kroky:

- Přečtěte si [Přehled a architekturu SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace obsahuje různé aspekty SAP HANA. V rámci předpokladů byste měli být obeznámeni s hlavními službami Azure, které poskytují základní služby Azure IaaS. Proto potřebujete znát výpočetní výkon, úložiště a síť Azure. Mnohé z těchto věcí jsou zpracovávány v [Průvodci plánováním Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)NetWeaver souvisejících s SAP. 

Informace o HANA v Azure najdete v následujících článcích a v jejich podčlánkůch:

- [Rychlý Start: ruční instalace SAP HANA s jednou instancí na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nasazení SAP S/4HANA nebo ČERNOBÍLé/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [SAP HANA vysoké dostupnosti pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Dostupnost SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [SAP HANA dostupnost napříč oblastmi Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Zálohování SAP HANA na základě snímků úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasazené na virtuálních počítačích Azure
Tato část obsahuje dokumentaci pro plánování a nasazení pro SAP NetWeaver a firmy One na Azure. Dokumentace se zaměřuje na základy a používání databází jiných než HANA s úlohou SAP v Azure. Dokumenty a články pro vysokou dostupnost představují také základ vysoké dostupnosti pro HANA v Azure, například:

- [SAP Business One na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6,0 v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Spuštění SAP NetWeaver na virtuálních počítačích s Microsoft Azure SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Plánování a implementace služby Azure Virtual Machines pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení Azure Virtual Machines pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrana nasazení aplikace s více vrstvami SAP NetWeaver pomocí Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Konektor SAP LaMa pro Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Informace o databázích, které nejsou HANA v rámci úlohy SAP v Azure, najdete v těchto tématech:

- [Důvody pro nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [SQL Server nasazení Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
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
- [Kurz: Azure Active Directory integrace s SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace se SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Azure Active Directory integrace s SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Prostředí S/4HANA: jednotné přihlašování Fiori hlavní panel SAML pomocí Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informace o integraci služeb Azure do komponent SAP najdete v těchto tématech:

- [Použití SAP HANA v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat pro SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Protokol změn
- 11/04/2019: změny v [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření clusteru přímo s konfigurací jednosměrového vysílání  
- 10/29/2019: vydání [připojení k veřejnému koncovému bodu pro Virtual Machines s využitím Azure Standard Load Balancer ve scénářích služby SAP pro vysokou dostupnost](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/25/2019: změny v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) a SAP HANA škálování na více instancí [s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) k objasnění protokolu NFS pro/Hana/Shared svazek
- 10/22/2019: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v SUSE Linux Enterprise Server pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NETAPP Files pro aplikace SAP ](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Vysoká dostupnost pro systém souborů NFS na virtuálních počítačích azure při SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm). a [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) pro detekci zesílení služby Vyrovnávání zatížení Azure
- Mění oddíl a hlavičku ANF v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) .
- 10/21/2019: vydání [SAP HANA škálování na více instancí s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: opravte poškozené odkazy v [zálohování a obnovení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Změňte minimální doporučený operační systém z SLES 12 SP3 na SLES 12 SP4 pro [vysokou dostupnost IBM DB2 LUW na virtuálních počítačích Azure v SUSE Linux Enterprise Server s Pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: změny konfigurace úložiště Ultra disk a zavedení ANF v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Změna grafiky [skupin umístění v blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) pro lepší přehlednost
- 10/01/2019: Změna v [SAP HANA konfiguracích infrastruktury a operací v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) pro správné příkazy pro sdílenou složku NFS s vysokou dostupností pro/Hana/Shared. 
- 09/28/2019: Změna [Nastavení Pacemaker v systému Azure na Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pro vysvětlení SBD, protože v CLUSTERech RHEL není podporován mechanismus pro oplocení  
- 09/17/2019: Změna v Průvodci plánováním a nasazením NetWeaver pro sjednocení podmínek v souvislosti s rozšířením virtuálního počítače pro SAP  
- 08/22/2019: změny v [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro aktualizaci adres URL pro vytvoření vlastní role  
- 08/16/2019: změny v [Nastavení Pacemaker na Red Hat Enterprise Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) , které zákazníkům připomínat, aby si aktualizovali akce v vlastní roli, pokud se aktualizuje na novou verzi agenta Azure plot  
- 08/15/2019: změny v [SAP HANA konfiguracích úložiště virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) , aby odrážely obecnou dostupnost Ultra disk (dřív SSD úrovně Ultra)
- 08/01/2019: změny [Nastavení Pacemaker na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro integraci změn konkrétně pro SLES 15 
- 07/23/2019: změny v [clusteru ASCS/SCS instance SAP v clusteru s podporou převzetí služeb při selhání systému Windows pomocí sdílené složky v Azure](sap-high-availability-guide-wsfc-file-share.md) , aby odrážely podporu prostoru úložiště s přímým přístupem Azure Site Recovery službami
- 07/14/2019: vydání [skupin umístění blízkosti Azure pro optimální latenci sítě s aplikacemi SAP](sap-proximity-placement-scenarios.md)
- 07/11/2019: změny v různých dokumentech, které pokrývají velké instance HANA, zahrnují revize 4 velkých instancí HANA
- 07/09/2019: vydání nové příručky pro [IBM DB2 hadr na serveru Red Hat Enterprise](high-availability-guide-rhel-ibm-db2-luw.md)
- 06/13/2019: vydání [vysoké dostupnosti pro SAP NetWeaver v Red Hat Enterprise Linux s Azure NetApp Files pro aplikace SAP](high-availability-guide-rhel-netapp-files.md)


