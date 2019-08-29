---
title: Řešení Oracle v Microsoft Azure | Microsoft Docs
description: Přečtěte si o možnostech nasazení aplikací a řešení Oracle na Microsoft Azure, včetně spuštění výhradně v infrastruktuře Azure nebo při použití připojení mezi cloudy pomocí Oracle cloudové infrastruktury (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: e9e37c54668ec0343cbfd45e51e90216955b46c4
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100022"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Přehled aplikací a řešení Oracle v Azure

V tomto článku se seznámíte s možnostmi spouštění řešení Oracle pomocí infrastruktury Azure. Přečtěte si také podrobné informace o dostupných [imagích virtuálních počítačů Oracle](oracle-vm-solutions.md) v Azure Marketplace a možnosti verze Preview pro [propojení Azure se službou Oracle Cloud Infrastructure (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Databáze Oracle v infrastruktuře Azure

Spouštějte databáze Oracle v infrastruktuře Azure pomocí imagí pro Linux dostupné v Azure Marketplace:

* Oracle Database 12,1, 12,2 a 18,3 Enterprise Edition 

* Oracle Database 12,1, 12,2 a 18,3 Standard Edition 

Můžete také zvolit, že chcete řešení založit na vlastní imagi, kterou vytvoříte úplně od začátku v Azure, nebo nahrát vlastní image z místního prostředí.

Volitelně můžete nakonfigurovat s více připojenými disky a zvýšit výkon databáze tím, že nainstalujete systém Oracle Automated Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikace na Oracle Linux a WebLogic Server

Spouštějte podnikové aplikace v Azure v podporovaných operačních systémech Oracle. Následující obrázky jsou k dispozici v Azure Marketplace:

* Oracle WebLogic server 12.1.2

* Oracle Linux (UEK) 6,8, 6,9, 6,10, 7,3, 7,4, 7,5 a 7,6

## <a name="high-availability-and-disaster-recovery-options"></a>Možnosti vysoké dostupnosti a zotavení po havárii

* Nakonfigurujte pro službu Oracle data Guard, Active Data Guard nebo GoldenGate na infrastruktuře Azure společně s [zóny dostupnosti](../../../availability-zones/az-overview.md) pro zajištění vysoké dostupnosti.

* Pomocí [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) můžete orchestrovat a spravovat zotavení po havárii pro vaše Oracle Linux virtuální počítače v Azure a vašich místních nebo fyzických serverech. 

* Povolte v Azure reálné clustery aplikací Oracle (RAC) pomocí [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integrace Azure s OCI (Preview)

Spouštějte aplikace Oracle v infrastruktuře Azure, připojené k back-endu databází v Oracle Cloud Infrastructure (OCI). Toto řešení používá následující funkce: 

* **Cloudové sítě** – pomocí přímého propojení dostupného mezi Azure ExpressRoute a Oracle FastConnect můžete navázat připojení s vysokou šířkou pásma, privátní a nízkou latencí mezi aplikací a databázovou vrstvou.
* **Integrovaná identita** – nastavení federované identity mezi Azure AD a Oracle IDCS pro vytvoření jediného zdroje identity pro řešení. Povolte jednotné přihlašování pro správu prostředků napříč systémem OCI a Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Nasazení aplikací Oracle v Azure

Použití šablon Terraformu k nastavení infrastruktury Azure a instalaci aplikací Oracle ověřovaných a podporovaných pro spuštění v konfiguraci mezi cloudy:

* Sada E-Business
* JD Edwards EnterpriseOne
* PeopleSoft
* Maloobchodní aplikace Oracle
* Hyperion finanční správa Oracle

Nasaďte také vlastní aplikace v Azure, které se připojují k OCI a dalším službám Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Nastavení databází Oracle v OCI

V kombinaci s aplikacemi Oracle běžícími v Azure použijte Oracle Database Cloud Services (autonomní databáze, RAC, Exadata, DBaaS, jeden uzel). Přečtěte si další informace o [možnostech databáze OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencování

Nasazení aplikací Oracle v Azure je založené na modelu "Přineste si vlastní licenci". Předpokládáte, že máte řádně licenci na používání softwaru Oracle a máte k dispozici aktuální smlouvu o podpoře pro Oracle. Oracle garantuje mobilitu licencí z místního prostředí do Azure. Podívejte se na [Nejčastější dotazy](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)k Oracle-Azure.

## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o nasazování [imagí virtuálních počítačů Oracle](oracle-vm-solutions.md) v infrastruktuře Azure.

* Přečtěte si další informace o tom, jak [propojit Azure s OCI](oracle-oci-overview.md).