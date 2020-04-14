---
title: Řešení Oracle v Microsoft Azure | Dokumenty společnosti Microsoft
description: Přečtěte si o možnostech nasazení aplikací a řešení Oracle v Microsoft Azure, včetně úplného spuštění v infrastruktuře Azure nebo využití připojení napříč cloudy pomocí řešení Oracle Cloud Infrastructure (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/16/2020
ms.author: mimckitt
ms.openlocfilehash: 8c266986ac795be48f3a8439afc5ae4752ff3ed7
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263246"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Přehled aplikací a řešení Oracle v Azure

Tento článek představuje funkce pro spouštění řešení Oracle pomocí infrastruktury Azure. Podívejte se také na podrobné úvody dostupných [ibi obrazů virtuálních počítačích Oracle](oracle-vm-solutions.md) na Azure Marketplace a možnost [propojit Azure s infrastrukturou Oracle Cloud Infrastructure (OCI).](oracle-oci-overview.md)

## <a name="oracle-databases-on-azure-infrastructure"></a>Databáze Oracle o infrastruktuře Azure

Spouštění databází Oracle v infrastruktuře Azure pomocí databáze Oracle database na ibi on-Linux, které jsou dostupné na Azure Marketplace:

* Oracle Database 12.1, 12.2 a 18.3 Enterprise Edition 

* Oracle Database 12.1, 12.2 a 18.3 Standard Edition 

Můžete se také rozhodnout nastavit databázi Oracle Database na bitovou kopii jiného než Oracle Linux, která je dostupná v Azure, založit řešení na vlastní image, kterou vytvoříte od začátku v Azure, nebo nahrát vlastní bitovou kopii z místního prostředí.

Volitelně můžete nakonfigurovat pomocí více připojených disků a zlepšit výkon databáze instalací nástroje Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikace na oracle linuxu a weblogickém serveru

Spouštět podnikové aplikace v Azure na podporovaných operačních systémech Oracle. Na Azure Marketplace jsou k dispozici následující obrázky:

* Server Oracle WebLogic 12.1.2

* Oracle Linux s nerozbitným podnikovým jádrem (UEK) 6.8, 6.9, 6.10, 7.3, 7.4, 7.5 a 7.6 

## <a name="high-availability-and-disaster-recovery-options"></a>Vysoká dostupnost a možnosti zotavení po havárii

* Konfigurace [oracle data guard](https://docs.oracle.com/cd/B19306_01/server.102/b14239/concepts.htm#g1049956), active data guard s [FSFO](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/dgbkr/index.html), [Sharding](https://docs.oracle.com/en/database/oracle/oracle-database/12.2/admin/sharding-overview.html) nebo [Golden Gate](https://www.oracle.com/middleware/technologies/goldengate.html) na infrastruktuře Azure ve spojení s [dostupnostzóny](../../../availability-zones/az-overview.md) pro vysokou dostupnost v oblasti. Můžete také nastavit tyto konfigurace ve více oblastech Azure pro větší dostupnost a zotavení po havárii.

* Azure [Site Recovery](../../../site-recovery/site-recovery-overview.md) můžete spravovat a spravovat zotavení po havárii pro virtuální počítače Oracle Linux v Azure a místní nebo fyzické servery. 

* Povolte clustery Oracle Real Application Clusters (RAC) v Azure pomocí [řešení Azure VMWare solution](https://docs.azure.cloudsimple.com/oracle-rac/) nebo [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="backup-oracle-workloads"></a>Zálohování úloh Oracle

* Zálohování virtuálních počítačů Oracle pomocí [služby Azure Backup](https://docs.microsoft.com/azure/backup/backup-overview)

* Zálohujte databázi Oracle pomocí oracle rman a volitelně použijte [Azure Blob Fuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux) k připojení [vysoce redudant Azure Blob Storage účet](https://docs.microsoft.com/azure/storage/common/storage-redundancy) a zapište své zálohy RMAN k němu pro větší odolnost.

## <a name="integration-of-azure-with-oci"></a>Integrace Azure s OCI

Spouštět aplikace Oracle v infrastruktuře Azure, připojené k back-endovým databázím v infrastruktuře Oracle Cloud Infrastructure (OCI). Toto řešení využívá následující funkce: 

* **Sítě mezi cloudy** – pomocí přímého propojení dostupného mezi Azure ExpressRoute a Oracle FastConnect navážete mezi aplikací a databázovou vrstvou připojení s vysokou šířkou pásma, privátní a s nízkou latencí.
* **Integrovaná identita** – Nastavte federované identity mezi Azure AD a Oracle IDCS vytvořit jeden zdroj identity pro řešení. Povolte jednotné přihlašování ke správě prostředků napříč OCI a Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Nasazení aplikací Oracle v Azure

Pomocí šablon Terraform můžete nastavit infrastrukturu Azure a nainstalovat aplikace Oracle. 

> [!IMPORTANT]
> Společnost Oracle bude tyto aplikace certifikovat tak, aby se v Azure spouštěla do května 2020 při používání řešení Propojení Azure / Oracle Cloud.

* E-Business Suite
* JD Edwards EnterpriseOne
* Peoplesoft
* Maloobchodní aplikace Oracle
* Oracle Hyperion Financial Management

Nasadit také vlastní aplikace v Azure, které se připojují k OCI a dalším službám Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Nastavení databází Oracle v OCI

Používejte cloudové služby Oracle Database Cloud Services (Autonomní databáze, RAC, Exadata, DBaaS, jeden uzel) ve spojení s aplikacemi Oracle spuštěnými v Azure. Další informace o [možnostech databáze OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencování

Nasazení aplikací Oracle v Azure je založené na modelu "přineste si vlastní licenci". Předpokládá se, že máte řádnou licenci k používání softwaru Oracle a že máte aktuální smlouvu o podpoře se společností Oracle. Společnost Oracle má zaručenou mobilitu licencí z místního do Azure. Podívejte se na [nejčastější dotazy](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)k řešení Oracle-Azure .

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o nasazení [ibi virtuálních počítačích Oracle](oracle-vm-solutions.md) v infrastruktuře Azure.

* Přečtěte si další informace o [propojení Azure s OCI](oracle-oci-overview.md).

* Podívejte se [na přehledovou relaci Oracle on Azure](https://myignite.techcommunity.microsoft.com/sessions/82915) z Ignite 2019. 
