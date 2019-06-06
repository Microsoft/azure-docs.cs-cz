---
title: Řešení Oracle v Microsoft Azure | Dokumentace Microsoftu
description: Další informace o možnostech pro nasazení aplikace Oracle a řešení v Microsoft Azure, včetně spuštěným na infrastrukturu Azure nebo pomocí připojení cloudu s Oracle cloudové infrastruktury (OCI).
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/04/2019
ms.author: rogirdh
ms.openlocfilehash: b5e40975fca491f289d949ee273d13053897fc6d
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743632"
---
# <a name="overview-of-oracle-applications-and-solutions-on-azure"></a>Přehled aplikace Oracle a řešení v Azure

Tento článek představuje možnosti spuštění Oracle řešení s využitím infrastruktury Azure. Viz také podrobný přehled dostupných [imagí virtuálních počítačů Oracle](oracle-vm-solutions.md) v Tržišti Azure Marketplace a funkce ve verzi preview pro [propojení Azure s Oracle cloudové infrastruktury (OCI)](oracle-oci-overview.md).

## <a name="oracle-databases-on-azure-infrastructure"></a>Databáze Oracle na infrastrukturu Azure

Používání databází Oracle na infrastruktuře Azure použití dostupných imagí Linuxu v Tržišti Azure Marketplace:

* Oracle Database 12.1 12.2 a 18.3 Enterprise Edition 

* Oracle Database 12.1 12.2 a 18.3 Standard Edition 

Můžete také základní řešení na vlastní imagi vytvořit od nuly v Azure nebo nahrát vlastní image z místního prostředí.

Volitelně můžete nakonfigurovat více připojených disků a zvyšte výkon databáze nainstalováním Oracle automatizované Storage Management (ASM).

## <a name="applications-on-oracle-linux-and-weblogic-server"></a>Aplikace v Oracle Linuxu a WebLogic Server

Spouštění podnikových aplikací v Azure do podporovaných operačních systémů Oracle. Na následujících obrázcích jsou k dispozici na webu Azure Marketplace:

* Oracle WebLogic Server 12.1.2

* Oracle Linux (UEK) 6.8 6.9, 6.10, 7.3, 7.4, 7.5 a 7.6

## <a name="high-availability-and-disaster-recovery-options"></a>Vysoká dostupnost a možnosti obnovení

* Konfigurace Oracle Data Guard aktivní Data Guard a GoldenGate na infrastrukturu Azure v kombinaci s [zóny dostupnosti](../../../availability-zones/az-overview.md) pro zajištění vysoké dostupnosti.

* Použití [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) orchestrovat a spravovat zotavení po havárii virtuálních počítačů s Oracle Linuxem v Azure a místní nebo fyzické servery. 

* Povolit Oracle skutečné aplikace clusterů (RAC) v Azure s využitím [FlashGrid SkyCluster](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="integration-of-azure-with-oci-preview"></a>Integrace Azure s využitím OCI (preview)

Spouštění aplikací Oracle v Azure infrastruktury připojenou k back-end databáze v Oracle cloudové infrastruktury (OCI). Toto řešení používá následující funkce: 

* **Cloudu sítě** – použití přímá propojení mezi Azure ExpressRoute a Oracle FastConnect k navázání připojení velkou šířkou pásma, privátní a s nízkou latencí mezi aplikací a databázové vrstvě.
* **Integrované identity** – nastavení federovaných identit mezi službami Azure AD a Oracle IDCS vytvořit zdroj jedinou identitu pro řešení. Povolte jednotné přihlašování ke správě prostředků napříč OCI a Azure.

### <a name="deploy-oracle-applications-on-azure"></a>Nasazení aplikací Oracle v Azure

Pomocí Terraformu šablon můžete nastavit infrastrukturu Azure a nainstalovat aplikace Oracle ověření a podporované pro spuštění v konfiguraci cloudu:

* E-Business Suite
* JD Edwards EnterpriseOne
* PeopleSoft
* Aplikace Oracle maloobchodního prodeje
* Oracle, Hyperion finanční správy

Taky nasaďte vlastní aplikace v Azure, které se připojují pomocí OCI a dalšími službami Azure.

### <a name="set-up-oracle-databases-in-oci"></a>Nastavit databází Oracle v OCI

Oracle Database cloudové služby (autonomní databáze, RAC, Exadata, DBaaS, jeden uzel) se používají ve spojení s Oracle aplikace běžící v Azure. Další informace o [možnosti databáze OCI](https://docs.cloud.oracle.com/iaas/Content/Database/Concepts/databaseoverview.htm). 
 

## <a name="licensing"></a>Licencování

Nasazení aplikací Oracle v Azure je založena na modelu "používání vlastní licence". Předpokládá se, že byly řádně licencované k používání softwaru Oracle a mají aktuální smlouvu o podpoře na místě se společností Oracle. Oracle je zaručeno, že mobility licencí v rámci místních do Azure. Prohlédněte si Oracle – Azure [nejčastější dotazy k](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html).

## <a name="next-steps"></a>Další postup

* Další informace o nasazení [imagí virtuálních počítačů Oracle](oracle-vm-solutions.md) v infrastruktuře Azure.

* Další informace o tom, jak [propojení Azure s využitím OCI](oracle-oci-overview.md).