---
title: Začínáme s řešením SAP na virtuálních počítačích Azure | Dokumentace Microsoftu
description: Další informace o řešení SAP, na kterých běží na virtuálních počítačích (VM) v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f14110cec0650927f74dfe3a38f06187c6df39de
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792666"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spuštění scénáře úloh řešení SAP

Pokud používáte Microsoft Azure, můžete využijte možnost spolehlivě spouštět vaše nejdůležitější úlohy SAP a scénáře na škálovatelné a kompatibilní prověřená v podnikovém platformě. Získejte škálovatelnost, flexibilitu a náklady na Azure. V rozšířené partnerství mezi společnostmi Microsoft a SAP můžete spouštět aplikace SAP napříč scénáři vývoje a testování a produkce v Azure a mít přitom plnou podporu. Od SAP Netweaveru po SAP S/4HANA, SAP BI na Linuxu po Windows a SAP HANA po SQL, způsoby, máme všechno, co potřebujete.

Kromě hostování SAP NetWeaver scénáře pomocí různých DBMS v Azure, které můžete hostovat další scénáře úloh řešení SAP, jako je SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která nastaví Azure od sebe. Pokud chcete povolit, hostování více paměti a procesoru prostředků vyhovovat i vašim náročným scénářů řešení SAP, které se týkají SAP HANA, Azure nabízí užívání zákazníků vyhrazený hardware úplné obnovení systému. Toto řešení použijte ke spuštění nasazení SAP HANA, které vyžadují až 24 TB (Škálováním 120 TB) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénáře úloh řešení SAP v Azure můžete vytvořit také požadavky integrace identit a jednotného přihlašování. Tato situace může nastat při použití služby Azure Active Directory (Azure AD) pro připojení různých komponent SAP a SAP software-as-a-service (SaaS) nebo nabízí platforma jako služba (PaaS). Seznam těchto integrace a scénářů jednotné přihlašování s Azure AD a SAP entity je popsané a popsány v části "integrace identit AAD SAP a jednotné přihlašování."

## <a name="latest-changes"></a>Nejnovější změny

- Verzi [velké instance Azure HANA řídit prostřednictvím webu Azure portal](hana-li-portal.md)

- Verzi [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise serveru s Azure Files NetApp pro aplikace SAP](high-availability-guide-suse-netapp-files.md)

- Vysvětlení **operačního systému Linux parametr net.ipv4.tcp_timestamps** nástroj pro vyrovnávání zatížení nastavení ve spojení s Azure

- Verzi [konfigurace úlohy SAP se zónami dostupnosti Azure](sap-ha-availability-zones.md)

- Verzi [SAP úlohy plánování a nasazení kontrolního seznamu](sap-deployment-checklist.md)




## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řadu dokumenty vás povede jednotlivými SAP HANA v Azure (velké instance) nebo zkráceně velkých instancích HANA. Informace v následujících oblastech velkých instancích HANA najdete tady:

- [Přehled systému SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Architektura SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-architecture)
- [Infrastruktura a možnosti připojení k SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-infrastructure-connectivity)
- [Instalace SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation)
- [Vysoká dostupnost a zotavení po havárii SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-high-availability-disaster-recovery)
- [Řešení potíží a monitorování SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/troubleshooting-monitoring)

Další postup:

- Čtení [přehled a architektura SAP Hana v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace obsahuje různé aspekty systému SAP HANA. Předpokladem je měli byste se seznámit s instančního objektu služby Azure, které poskytují základní služby Azure IaaS. Musíte proto znalost výpočetní prostředky Azure, úložiště a sítě. Mnohé z těchto tématech jsou zpracovány v SAP NetWeaver související [Azure Příručka pro plánování](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 

Informace v HANA v Azure najdete v následujících článcích a jejich subarticles:

- [Rychlé zprovoznění: Ruční instalace jedné instance SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- [Nasadit řešení SAP S/4HANA nebo BW/4HANA v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)
- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Vysoká dostupnost SAP HANA pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Dostupnost SAP HANA v rámci jedné oblasti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-one-region)
- [Dostupnost SAP HANA v různých oblastech Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [SAP HANA Azure Backup na úrovni souborů](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Zálohování SAP HANA založené na snímcích úložiště](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasadit v Azure virtual machines
Tato část uvádí dokumentace k plánování a nasazení pro SAP NetWeaver a Business jeden v Azure. V dokumentaci se zaměřuje na základní informace a využívat bez HANA databází s úlohou SAP v Azure. Dokumenty a články pro zajištění vysoké dostupnosti jsou také základem pro zajištění vysoké dostupnosti HANA v Azure, například:

- [SAP Business One na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Nasazení SAP IDES EHP7 SP3 pro SAP ERP 6.0 v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-ides-erp6-erp7-sp3-sql)
- [Spuštění SAP NetWeaver na virtuálních počítačích Microsoft Azure SUSE Linuxem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)
- [Azure Virtual Machines, plánování a implementace SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Nasazení virtuálních počítačů pro SAP NetWeaver v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Ochrana nasazení vícevrstvé aplikace SAP NetWeaver pomocí Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Konektor SAP LaMa pro Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Informace o jiných HANA databáze v rámci úloh SAP v Azure najdete v tématu:

- [Důležité informace týkající se nasazení Azure Virtual Machines DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Nasazení SQL serveru Azure Virtual Machines DBMS pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Nasazení SAP MaxDB, Live mezipaměti a obsahu serveru na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Informace v databázích SAP HANA v Azure najdete v části "SAP HANA na virtuálních počítačích Azure."

Informace o vysoké dostupnosti úloh SAP v Azure najdete tady:

- [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Tento dokument se odkazuje na různé jiné architektury a scénáře dokumenty. V pozdější scénář dokumenty jsou k dispozici odkazy na podrobné technické dokumentace, která popisují nasazení a konfigurace různých metod vysokou dostupnost. Jiné dokumenty, které ukazují, jak vytvořit a nakonfigurovat vysokou dostupnost úloh SAP NetWeaver zahrnují operačních systémů Linux a Windows.


Informace o integraci služby Azure Active Directory (Azure AD) a služby SAP a jednotného přihlašování najdete v tématu:

- [Kurz: Integrace Azure Active Directory se SAP cloudem pro odběratele](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s ověřováním identit cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s využitím cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se SAP Netweaverem](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Prostředí S/4HANA: Fiori Launchpad SAML jednotného přihlašování s Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informace o integraci služby Azure do komponenty SAP najdete tady:

- [Použití SAP HANA v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat pro SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)




