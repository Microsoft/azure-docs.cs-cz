---
title: Začínáme se SAP na virtuálních počítačích Azure | Dokumenty společnosti Microsoft
description: Informace o řešeních SAP, která běží na virtuálních počítačích (VM) v Microsoft Azure
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
ms.date: 04/07/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 86c5f572ba66845467a7e3f4412b8da8368fe772
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811249"
---
# <a name="use-azure-to-host-and-run-sap-workload-scenarios"></a>Použití Azure k hostování a spouštění scénářů úloh SAP

Když používáte Microsoft Azure, můžete spolehlivě spouštět důležité úlohy a scénáře SAP na škálovatelné, kompatibilní a podnikové osvědčené platformě. Získáte škálovatelnost, flexibilitu a úspory nákladů Azure. Díky rozšířenému partnerství mezi Microsoftem a SAP můžete spouštět aplikace SAP napříč vývojovými a testovacími a produkčními scénáři v Azure a být plně podporováni. Od SAP NetWeaver po SAP S/4HANA, SAP BI na Linuxu až po Windows a SAP HANA až po SQL, máme pro vás vše.

Kromě hostování scénářů SAP NetWeaver s různými DBMS v Azure můžete hostovat i další scénáře úloh SAP, jako je SAP BI v Azure. 

Jedinečnost Azure pro SAP HANA je nabídka, která odlišuje Azure od sebe. Aby bylo možné povolit hostování více scénářů SAP náročných na prostředky a prostředků procesoru, které zahrnují SAP HANA, Azure nabízí použití hardwaru pro holé kovy určeného zákazníky. Toto řešení slouží ke spuštění nasazení SAP HANA, která vyžadují až 24 TB (120 TB horizontálnínavýšení kapacity) paměti pro S/4HANA nebo jiné úlohy SAP HANA. 

Hostování scénářů úloh SAP v Azure můžete také vytvořit požadavky na integraci identit a jednotné přihlašování. K této situaci může dojít, když pomocí Služby Azure Active Directory (Azure AD) připojit různé komponenty SAP a SAP software jako služba (SaaS) nebo platformu jako služba (PaaS) nabízí. Seznam takových scénářů integrace a jednotného přihlašování s entitami Azure AD a SAP je popsán a zdokumentován v části "Integrace identity AAD SAP a jednotné přihlašování".

## <a name="changes-to-the-sap-workload-section"></a>Změny v části pracovní zátěže SAP
Změny dokumentů v části úlohy SAP v Azure jsou uvedeny na konci tohoto článku. Položky v protokolu změn jsou uchovávány po dobu přibližně 180 dnů.

## <a name="you-want-to-know"></a>Chcete vědět,
Máte-li konkrétní dotazy, přejdeme vás na konkrétní dokumenty nebo toky v této části úvodní stránky. Chcete vědět:

- Jaké virtuální počítače Azure a jednotky velké instance HANA jsou podporované pro které verze softwaru SAP a verze operačního systému. Přečtěte si dokument [Co sap software je podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure) pro odpovědi a proces najít informace
- Jaké scénáře nasazení SAP jsou podporované s virtuálními počítači Azure a velkými instancemi HANA. Informace o podporovaných scénářích naleznete v dokumentech:
    - [Úlohy SAP ve scénářích podporovaných virtuálním počítačem Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations)
    - [Podporované scénáře pro velkou instanci HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario)
- Jaké služby Azure, typy virtuálních počítačů Azure a služby úložiště Azure jsou dostupné v různých oblastech Azure, podívejte se na produkty webu [dostupné podle oblastí](https://azure.microsoft.com/global-infrastructure/services/) 

 
## <a name="sap-hana-on-azure-large-instances"></a>SAP HANA v Azure (velké instance)

Řada dokumentů vás provede SAP HANA v Azure (velké instance) nebo zkrátka velké instance HANA. Informace o velkých instancích HANA získáte od dokumentu [Přehled a architektura SAP HANA v Azure (velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) a projděte si související dokumentaci v části Velká instance HANA.



## <a name="sap-hana-on-azure-virtual-machines"></a>SAP HANA na virtuálních počítačích Azure
Tato část dokumentace se zabývá různými aspekty SAP HANA. Jako předpoklad byste měli být obeznámeni s hlavními službami Azure, které poskytují základní služby Azure IaaS. Takže potřebujete znalostvýpočetních prostředků, úložiště a sítí Azure. Mnoho z těchto předmětů je zpracováno v [průvodci plánování](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)azure souvisejícím s SAP NetWeaver . 

Informace o HANA v Azure najdete v následujících článcích a jejich dílčí články:

- [Konfigurace infrastruktury SAP HANA a operace v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)
- [Sap HANA vysoká dostupnost pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)
- [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)


 

## <a name="sap-netweaver-deployed-on-azure-virtual-machines"></a>SAP NetWeaver nasazený na virtuálních počítačích Azure
V této části je uvedena dokumentace k plánování a nasazení pro SAP NetWeaver a Business One v Azure. Dokumentace se zaměřuje na základy a použití databází bez HANA s úlohou SAP v Azure. Dokumenty a články pro vysokou dostupnost jsou také základem pro vysokou dostupnost HANA v Azure, jako jsou:

- [Průvodce plánováním Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide). 
- [SAP Business One na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/business-one-azure)
- [Ochrana vícevrstvého nasazení aplikace SAP NetWeaver pomocí site recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)
- [Konektor SAP LaMa pro Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/lama-installation)

Informace o databázích, které nejsou hana v rámci úlohy SAP v Azure, najdete v tématu:

- [Důležité informace o nasazení DBMS virtuálních počítačů Azure pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)
- [Nasazení DBMS virtuálních počítačů SQL Server Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sqlserver)
- [Nasazení DBMS v počítačích Azure Virtual Machines s Oraclem pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_oracle)
- [Nasazení DBMS v počítačích Azure Virtual Machines s IBM DB2 pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_ibm)
- [Nasazení DBMS v počítačích Azure Virtual Machines se SAP ASE pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- [Nasazení SAP MaxDB, Živé mezipaměti a obsahového serveru na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_maxdb)

Informace o databázích SAP HANA v Azure najdete v části "SAP HANA na virtuálních počítačích Azure.".

Informace o vysoké dostupnosti úlohy SAP v Azure najdete v tématu:

- [Vysoká dostupnost virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-start)

Tento dokument odkazuje na různé dokumenty jiné architektury a scénáře. V pozdějších scénářích jsou k dispozici odkazy na podrobné technické dokumenty, které vysvětlují nasazení a konfiguraci různých metod vysoké dostupnosti. Různé dokumenty, které ukazují, jak vytvořit a nakonfigurovat vysokou dostupnost pro úlohy SAP NetWeaver, zahrnují operační systémy Linux a Windows.


Informace o integraci mezi službami Azure Active Directory (Azure AD) a službami SAP a jednotné přihlašování najdete v tématu:

- [Kurz: Integrace Azure Active Directory se SAP Cloud pro zákazníka](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-customer-cloud-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s ověřováním identity cloudové platformy SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory s cloudovou platformou SAP](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP NetWeaver](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-netweaver-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace Azure Active Directory se službou SAP Business ByDesign](https://docs.microsoft.com/azure/active-directory/saas-apps/sapbusinessbydesign-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Kurz: Integrace služby Azure Active Directory se SAP HANA](https://docs.microsoft.com/azure/active-directory/saas-apps/saphana-tutorial?toc=%2fazure%2fvirtual-machines%2fworkloads%2fsap%2ftoc.json)
- [Vaše prostředí S/4HANA: Fiori Launchpad SAML jednotné přihlášení pomocí Azure AD](https://blogs.sap.com/2017/02/20/your-s4hana-environment-part-7-fiori-launchpad-saml-single-sing-on-with-azure-ad/)

Informace o integraci služeb Azure do komponent SAP najdete v tématu:

- [Použití SAP HANA v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-hana)
- [DirectQuery a SAP HANA](https://docs.microsoft.com/power-bi/desktop-directquery-sap-hana)
- [Použití konektoru SAP BW v Power BI Desktopu](https://docs.microsoft.com/power-bi/desktop-sap-bw-connector) 
- [Azure Data Factory nabízí integraci dat pro SAP HANA a Business Warehouse](https://azure.microsoft.com/blog/azure-data-factory-offer-sap-hana-and-business-warehouse-data-integration)


## <a name="change-log"></a>Protokol změn
- 04/07/2020: Změna nastavení [kardiostimulátoru na SLES v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro objasnění pokynů cloud-netconfig-azure
- 04/06/2020: Změny v [sap HANA horizontální navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Soubory na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) a v [SAP HANA horizontální navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp soubory na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) odebrat odkazy na NetApp [TR-4435](https://www.netapp.com/us/media/tr-4746.pdf) (nahrazenTR-4746 ) [TR-4746](https://www.netapp.com/us/media/tr-4746.pdf)
- 03/31/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) a [vysoká dostupnost SAP HANA na virtuálních počítačích Azure na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) přidat pokyny, jak určit velikost proklápěcí při vytváření prokládaných svazků
- 03/27/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure na SLES s ANF pro aplikace SAP zarovnat](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) možnosti připojení systému souborů na NetApp TR-4746 (odebrat možnost připojení synchronizace)
- 03/26/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SLES multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) přidat odkaz na NetApp TR-4746
- 03/26/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SLES s Azure NetApp Files pro sap aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na slezových víceSID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid), [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na RHEL pro aplikace SAP pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na RHEL s aplikacemi Azure NetApp Files for SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) pro aplikace SAP pro aktualizaci diagramů a objasnění pokynů pro vytváření back-endového fondu Azure Load Balancer
- 03/19/2020: Hlavní revize dokumentu [Rychlý start: Ruční instalace sap HANA s jednou instancí na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started) na [instalaci SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-get-started)
- 03/17/2020: Změna nastavení [kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) odebrat nastavení konfigurace SBD, které už není nutné
- 03/16/2020: Objasnění scénáře certifikace sloupců v platformě s certifikací SAP HANA IaaS v [tom, co je software SAP podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 03/11/2020: Změna [zatížení SAP na azure virtual machine podporované scénáře](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) objasnit více databází na podporu instance DBMS
- 11.02.000:Změna [plánování a implementace virtuálních počítačů Azure pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) vysvětlující virtuální počítače generace 1 a generace 2
- 03/10/2020: Změna [konfigurace virtuálního úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pro objasnění skutečných existujících limitů propustnosti ANF
- 03/09/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro SAP aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro sap aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Nastavení pace maker na [SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s pacemakerem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm), [Vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) a [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SLES multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro aktualizaci prostředků clusteru s agentem prostředků azure-lb 
- 03/05/2020: Strukturovat změny a změny obsahu pro oblasti Azure a virtuální počítače Azure ve [virtuálních počítačích Azure plánování a implementace pro SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- 03/03/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure na SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) změnit na efektivnější rozložení svazku ANF
- 03/01/2020: Přepracovaný [průvodce zálohováním pro SAP HANA na virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide) tak, aby zahrnoval službu Azure Backup. Snížený a zhuštěný obsah v [SAP HANA Azure Backup na úrovni souboru](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level) a odstranil třetí dokument zabývající se zálohováním prostřednictvím snímku disku. Obsah se zpracovává v průvodci zálohováním pro SAP HANA na virtuálních počítačích Azure 
- 02/27/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure na SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure na SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) a [vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SLES multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) pro úpravu parametru clusteru "na selhání"
- 02/26/2020: Změna [konfigurace virtuálního úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) pro objasnění volby systému souborů pro HANA v Azure
- 02/26/2020: Změna [architektury s vysokou dostupností a scénářů pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) tak, aby zahrnovaly odkaz na HA pro SAP NetWeaver na virtuálních počítačích Azure v průvodci více SID RHEL
- 02/26/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure na SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NW na virtuálních počítačích Azure na virtuálních počítačích Azure s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), azure virtuální počítače [vysoká dostupnost pro SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [Azure NetWeaver na RHEL se soubory Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) odebrat prohlášení, že multi-SID ASCS/ERS cluster není podporován
- 02/26/2020: Vydání [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na RHEL multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-multi-sid) přidat odkaz na Průvodce clusterem SUSE s více SID
- 02/25/2020: Změna [architektury s vysokou dostupností a scénářů pro SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios) pro přidání odkazů na novější články HA
- 02/25/2020: Změna [vysoké dostupnosti IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Pacemakerem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm) přejděte na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 02/21/2020: Kompletní revize článku [SAP ASE Azure virtuální počítače nasazení DBMS pro úlohy SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_sapase)
- 02/21/2020: Změna [konfigurace úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) představuje nové doporučení ve velikosti proužku pro /hana/data a přidání nastavení plánovače vstupně-vstupně-videa
- 02/21/2020: Změny v dokumentech velké instance HANA, které představují nově certifikované sloky S224 a S224m
- 02/21/2020: Změna ve [virtuálních počítačích Azure vysoká dostupnost pro SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [Azure Virtuální počítače vysoká dostupnost pro SAP NetWeaver na RHEL se soubory Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) upravit omezení clusteru pro zařazení server replikace 2 architektura (ENSA2)
- 02/20/2020: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SLES multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) přidat odkaz na průvodce clusteru SUSE multi-SID
- 02/13/2020: Změny [v plánování a implementaci virtuálních počítačů Azure pro SAP NetWeaver k](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide) implementaci odkazů na nové dokumenty
- 02/13/2020: Přidáno nové [úlohy](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-planning-supported-configurations) dokumentu SAP na scénář i pro virtuální počítač Azure
- 02/13/2020: Přidán nový dokument [Jaký software SAP je podporován pro nasazení Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-supported-product-on-azure)
- 02/13/2020: Změna [vysoké dostupnosti IBM Db2 LUW na virtuálních počítačích Azure na serveru Red Hat Enterprise Linux Na serveru S](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-ibm-db2-luw) přechodem na dokument, který popisuje přístup k veřejnému koncovému bodu se standardním nástrojem pro vyrovnávání zatížení Azure
- 13.02.000.000:Přidání nových typů virtuálních počítačů do [certifikací a konfigurací SAP spuštěných v Microsoft Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-certifications)
- 02/13/2020: Přidání nových poznámek podpory SAP sap v [Azure: plánování a kontrolní seznam nasazení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)
- 02/13/2020: Změna ve [virtuálních počítačích Azure vysoká dostupnost pro SAP NetWeaver na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel) a [Azure Virtuální počítače vysoká dostupnost pro SAP NetWeaver na RHEL se soubory Azure NetApp](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) zarovnat časové rezervy prostředků clusteru na doporučení časového opovězení Red Hat
- 02/11/2020: Vydání [SAP HANA na Azure large instance migrace do virtuálních počítačů Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-large-instance-virtual-machine-migration)
- 02/07/2020: Změna [připojení veřejného koncového bodu pro virtuální počítače pomocí Azure Standard ILB ve scénářích SAP HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections) k aktualizaci ukázkového snímku obrazovky nsg
- 02/03/2020: Změna [vysoké dostupnosti pro SAP NW na virtuálních počítačích Azure na SLES pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse) a [vysoká dostupnost pro SAP NW na virtuálních počítačích Azure na SLES s ANF pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) k odstranění upozornění o použití pomlčky v názvech hostitelů uzlů clusteru na SLES
- 01/28/2020: Změna [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel) zarovnat časové rezervy prostředků clusteru SAP HANA s doporučeními časového omezení Red Hat
- 17. 7.02.000:Změna [skupin umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) a změna části přesunutí stávajících virtuálních počítačů do skupiny umístění bezkontaktní
- 01/17/2020: Změna [konfigurace úloh SAP s zónami dostupnosti Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ha-availability-zones) přejděte na postup, který automatizuje měření latence mezi zónami dostupnosti
- 16.02.2020: Změna instalace [a konfigurace SAP HANA (Velké instance)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-installation) v Azure pro přizpůsobení verzí operačního systému do hardwarového adresáře HANA IaaS
- 01/16/2020: Změny ve [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SLES multi-SID průvodce](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid) přidat pokyny pro systémy SAP, pomocí fronty server 2 architektura (ENSA2)
- 01/10/2020: Změny v [SAP HANA horizontální navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) a v [SAP HANA horizontální navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel) přidat pokyny, jak provést `nfs4_disable_idmapping` změny trvalé.
- 01/10/2020: Změny ve [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SLES s Azure NetApp Soubory pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files) a ve [virtuálních počítačích Azure vysoká dostupnost pro SAP NetWeaver na RHEL s Azure NetApp Soubory pro aplikace SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files) přidat pokyny, jak připojit Azure NetApp soubory NFSv4 svazky.
- 12/23/2019: Vydání [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure v průvodci s lezem s multi-SID](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-multi-sid)
- 12/18/2019: Vydání [škálování SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure se soubory Azure NetApp na RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- 11/ 21 / 2019: Změny v [SAP HANA horizontální navýšení kapacity s pohotovostním uzlem na virtuálních počítačích Azure s Azure NetApp Files na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) zjednodušit konfiguraci pro mapování NFS ID a změnit doporučené primární síťové rozhraní pro zjednodušení směrování.
- 11/ 15 / 2019: Drobné změny ve [vysoké dostupnosti pro SAP NetWeaver na SUSE Linux Enterprise Server s Azure NetApp Soubory pro aplikace SAP](high-availability-guide-suse-netapp-files.md) a [vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux s Azure NetApp Soubory pro aplikace SAP](high-availability-guide-rhel-netapp-files.md) objasnit omezení kapacity fondu a odebrat prohlášení, že je podporována pouze verze NFSv3.
- 11/12/2019: Vydání [vysoké dostupnosti pro SAP NetWeaver ve Windows se soubory Azure NetApp (SMB)](high-availability-guide-windows-netapp-files-smb.md)
- 11/08/2019: Změny ve [vysoké dostupnosti SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](sap-hana-high-availability.md), Nastavení SAP [HANA systémové replikace na virtuálních počítačích Azure (VM)](sap-hana-high-availability-rhel.md), [Azure Virtual Machines vysoká dostupnost pro SAP NetWeaver na SUSE Linux Enterprise Server pro aplikace SUSE Linux Enterprise Server](high-availability-guide-suse.md), Virtuální počítače Azure [vysoká dostupnost pro SAP NetWeaver na SUSE Linux Enterprise Server s Azure NetApp Files](high-availability-guide-suse-netapp-files.md), Azure Virtual Machines [vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux](high-availability-guide-rhel.md), Azure Virtual Machines [vysoká dostupnost pro SAP NetWeaver na Red Hat Enterprise Linux u Azure NetApp Files](high-availability-guide-rhel-netapp-files.md), [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](high-availability-guide-suse-nfs.md), [GlusterFS na virtuálních počítačích Azure na Red Hat Enterprise Linux pro SAP NetWeaver](high-availability-guide-rhel-glusterfs.md) doporučit Azure standardní nástroj pro vyrovnávání zatížení  
- 11/08/2019: Změny v [plánování úloh SAP a kontrolním seznamu nasazení](sap-deployment-checklist.md) pro objasnění doporučení šifrování  
- 11/04/2019: Změny v [nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](high-availability-guide-suse-pacemaker.md) pro vytvoření clusteru přímo s konfigurací jednosměrového vysílání  
- 10/ 29 / 2019: Vydání [připojení veřejného koncového bodu pro virtuální počítače pomocí Nástroje pro vyrovnávání zatížení Azure Standard ve scénářích vysoké dostupnosti SAP](high-availability-guide-standard-load-balancer-outbound-connections.md)
- 10/ 25 / 2019: Změny v [konfiguracích úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) a [škálování SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure se soubory Azure NetApp na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse) za účelem objasnění protokolu NFS pro /hana/sdílený svazek
- 10/22/2019: Změna [vysoké dostupnosti pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server pro SAP aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse), [Vysoká dostupnost pro SAP NetWeaver na virtuálních počítačích Azure na SUSE Linux Enterprise Server s Azure NetApp Files pro sap aplikace](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-netapp-files), [Vysoká dostupnost pro NFS na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs), Nastavení [kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker), [Vysoká dostupnost IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s pacemakerem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)a [vysoká dostupnost SAP HANA na virtuálních počítačích Azure na SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability) for Azure Load-Balancer Detection Hardening
- Změní oddíl a záhlaví ANF v [konfiguracích úložiště virtuálních počítačů SAP HANA Azure.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/21/2019: Vydání [škálování SAP HANA s pohotovostním uzlem na virtuálních počítačích Azure se soubory Azure NetApp na SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- 10/16/2019: Oprava nefunkčních odkazů v [záloze a obnovení](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-backup-restore)
- 10/16/2019: Změna minimálního doporučeného operačního systému ze SLES 12 SP3 na SLES 12 SP4 ve [vysoké dostupnosti IBM Db2 LUW na virtuálních počítačích Azure na SUSE Linux Enterprise Server s pacemakerem](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms-guide-ha-ibm)
- 10/11/2019: Změny konfigurace úložiště Ultra disků a zavedení ANF v [konfiguracích úložiště virtuálních počítačů SAP HANA Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- 10/01/2019: Změna [grafiky skupin umístění bezkontaktní komunikace Azure pro optimální latenci sítě s aplikacemi SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios) pro získání větší přehlednosti
- 10/01/2019: Změna [konfigurace a operace infrastruktury SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) v Azure opravit příkazy kolem vysoce dostupné sdílené složky nfs pro /hana/shared. 
- 09/28/2019: Změna nastavení [kardiostimulátoru na Red Hat Enterprise Linux v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) pro objasnění SBD jako mechanismuš oplocení není podporována v clusterech RHEL  
- 09/17/2019: Změna v NetWeaver plánování a nasazení Průvodce sjednotit podmínky kolem rozšíření virtuálního počítače pro SAP  
- 08/22/2019: Změny v [nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro aktualizaci adres URL pro vytvoření vlastní role  
- 08/16/2019: Změny v [nastavení kardiostimulátoru na Red Hat Enterprise Linux v Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) aby zákazníkům připomněly, že mají aktualizovat akce ve vlastní roli, pokud se aktualizuje te na novou verzi agenta Plot Azure  
- 08/15/2019: Změny v [konfiguracích úložiště virtuálních počítačů SAP HANA Azure,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage) které odrážejí obecnou dostupnost ultra disku (dříve Ultra SSD)
- 08/01/2019: Změny [nastavení kardiostimulátoru na SUSE Linux Enterprise Server v Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) pro integraci změn speciálně pro SLES 15 


