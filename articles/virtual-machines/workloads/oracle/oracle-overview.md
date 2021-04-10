---
title: Řešení Oracle v Microsoft Azure | Microsoft Docs
description: Přečtěte si o možnostech nasazení aplikací a řešení Oracle na Microsoft Azure, včetně spuštění výhradně v infrastruktuře Azure nebo při použití připojení mezi cloudy pomocí Oracle cloudové infrastruktury (OCI).
documentationcenter: ''
author: dbakevlar
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/05/2020
ms.author: kegorman
ms.openlocfilehash: 971f7c919595f915451faf9266ee3bb18b35087e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101677180"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Přehled aplikací a řešení Oracle v Azure

V tomto článku se seznámíte s možnostmi spouštění řešení Oracle pomocí infrastruktury Azure. Viz také podrobná Úvod k dostupným [aplikacím serveru WebLogic Server Azure](oracle-weblogic.md), [image virtuálních počítačů Oracle](oracle-vm-solutions.md) v Azure Marketplace a schopnost [propojit Azure se službou Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Databáze Oracle v infrastruktuře Azure

Spouštějte databáze Oracle v infrastruktuře Azure pomocí Oracle Database v Oracle Linux imagí dostupných v Azure Marketplace:

* Oracle Database 12,1, 12,2 a 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 a 18,3 Standard Edition

* Oracle Database 19,3

Můžete se také rozhodnout nastavit Oracle Database na neOracle Linux image dostupné v Azure. řešení můžete založit na vlastní imagi, kterou vytvoříte úplně od začátku v Azure, nebo nahrát vlastní image z místního prostředí.

Volitelně můžete nakonfigurovat s více připojenými disky a zvýšit výkon databáze tím, že nainstalujete systém Oracle Automated Storage Management (ASM).

## <a name="weblogic-server-with-azure-service-integrations"></a>WebLogic Server s integrací služeb Azure

Vyberte si z nejrůznějších aplikací serveru WebLogic Server Azure, abyste urychlili cestu do cloudu.  K dispozici je několik předem nakonfigurovaných integrací služeb Azure, včetně databází, Azure App Gateway a Azure Active Directory.

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikace na Oracle Linux a WebLogic Server

Spouštějte podnikové aplikace v Azure na podporovaných Oracle Linux imagí. Následující image virtuálních počítačů jsou k dispozici v Azure Marketplace:

* Oracle WebLogic server 12.1.2

* Oracle Linux s neUEKm podnikovým jádrem () 6,8, 6,9, 6,10, 7,3 až 7,7, 8,0, 8,1. 

## <a name="high-availability-and-disaster-recovery-options"></a>Možnosti vysoké dostupnosti a zotavení po havárii

* Nakonfiguruje [Oracle data Guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), [aktivní ochranu dat pomocí FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [horizontálního dělení](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) nebo [zlaté brány](https://www.oracle.com/middleware/technologies/goldengate.html) v infrastruktuře Azure společně s [zóny dostupnosti](../../../availability-zones/az-overview.md) pro zajištění vysoké dostupnosti v oblasti. Pro přidání dostupnosti a zotavení po havárii můžete také nastavit tyto konfigurace napříč několika oblastmi Azure.

* Pomocí [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) můžete orchestrovat a spravovat zotavení po havárii pro vaše Oracle Linux virtuální počítače v Azure a na vašich fyzických serverech. 

* Pomocí [Řešení Azure VMware](../../../vmware-cloudsimple/oracle-real-application-clusters.md) nebo [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/)povolte v Azure reálné aplikační clustery (RAC).

## <a name="backup-oracle-workloads"></a>Zálohování úloh Oracle

* Zálohování virtuálních počítačů Oracle pomocí [Azure Backup](../../../backup/backup-overview.md)

* Zálohujte Oracle Database pomocí Oracle RMAN a volitelně pomocí služby [Azure Blob pojistky](../../../storage/blobs/storage-how-to-mount-container-linux.md) připojte [vysoce redundantní účet Azure Blob Storage](../../../storage/common/storage-redundancy.md) a zapište do něj zálohy rman pro zvýšení odolnosti.

## <a name="integration-of-azure-with-oci"></a>Integrace Azure s OCI

Spouštějte aplikace Oracle v infrastruktuře Azure, připojené k back-endu databází v Oracle Cloud Infrastructure (OCI). Toto řešení používá následující funkce: 

* **Cloudové sítě** – pomocí přímého propojení dostupného mezi Azure ExpressRoute a Oracle FastConnect můžete navázat připojení s vysokou šířkou pásma, privátní a nízkou latencí mezi aplikací a databázovou vrstvou.
* **Integrovaná identita** – nastavení federované identity mezi Azure AD a Oracle IDCS pro vytvoření jediného zdroje identity pro řešení. Povolte jednotné přihlašování pro správu prostředků napříč systémem OCI a Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Nasazení aplikací Oracle v Azure

Použijte šablony Terraformu k nastavení infrastruktury Azure a instalaci aplikací Oracle. 

Oracle nastavil, aby tyto aplikace běžely v Azure při připojení k databázi Oracle prostřednictvím řešení Cloud Interconnect v Azure/Oracle:

* Sada E-Business
* ŘEŠENÍ JD Edwards EnterpriseOne
* PeopleSoft spouštěných místně
* Maloobchodní aplikace Oracle
* Hyperion finanční správa Oracle

Nasaďte také vlastní aplikace v Azure, které se připojují k OCI a dalším službám Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Nastavení databází Oracle v OCI

V kombinaci se softwarem Oracle běžícím v Azure použijte Oracle Database Cloud Services (autonomní databáze, RAC, Exadata, DBaaS, jeden uzel). Přečtěte si další informace o [možnostech databáze OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencování

Nasazení aplikací Oracle v Azure je založené na modelu "Přineste si vlastní licenci". Předpokládáte, že máte řádně licenci na používání softwaru Oracle a máte k dispozici aktuální smlouvu o podpoře pro Oracle. Oracle garantuje mobilitu licencí z místního prostředí do Azure. Přečtěte si Oracle-Azure [Nejčastější dotazy](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [aplikacích Azure pro WebLogic Server](oracle-weblogic.md) a o integraci služeb Azure, které podporují.

* Přečtěte si další informace o nasazování [imagí virtuálních počítačů Oracle](oracle-vm-solutions.md) v infrastruktuře Azure.

* Přečtěte si další informace o tom, jak [propojit Azure s OCI](oracle-oci-overview.md).

* Podívejte se na [Přehled relace Oracle v Azure](https://www.pluralsight.com/courses/microsoft-ignite-session-57) z Ignite 2019. 
