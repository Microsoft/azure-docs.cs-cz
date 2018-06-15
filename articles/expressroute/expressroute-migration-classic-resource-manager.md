---
title: 'Migrovat ExpressRoute přidružené virtuální sítě z classic do Resource Manager: Azure: prostředí PowerShell | Microsoft Docs'
description: Tato stránka popisuje, jak migrovat přidružený virtuálních sítí do Resource Manager po přesunutí okruhu.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850823"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrovat ExpressRoute přidružené virtuální sítě z classic do Resource Manager

Tento článek vysvětluje, jak migrovat virtuálních sítí Azure ExpressRoute přidružené z modelu nasazení classic do modelu nasazení Azure Resource Manager po přesunutí okruhu ExpressRoute. 


## <a name="before-you-begin"></a>Než začnete
* Ověřte, zda máte nejnovější verzi modulů prostředí Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
* Ujistěte se, že jste si přečetli [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.
* Zkontrolujte informace, které jsou poskytovány na základě [přesun okruhu ExpressRoute z classic do Resource Manager](expressroute-move.md). Ujistěte se, že rozumíte plně limity a omezení.
* Ověřte, že je okruh v modelu nasazení classic plně funkční.
* Ujistěte se, že máte skupinu prostředků, který byl vytvořen v modelu nasazení Resource Manager.
* Přečtěte si následující dokumentaci k migraci prostředků:

    * [Platforma podporovaná migrace z klasického do Azure Resource Manageru prostředků IaaS](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Nejčastější dotazy: Migrace z klasického do Azure Resource Manageru prostředky infrastruktury podporované platformy](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Zkontrolujte nejběžnějších chyb, migrace a jejich zmírnění](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Podporované a nepodporované scénáře

* Okruh ExpressRoute můžete přesunout z klasického správce prostředků prostředí bez odstávky. Jakýkoli okruh ExpressRoute můžete přesunout z classic do Resource Manager prostředí bez výpadků. Postupujte podle pokynů v [přesun okruhů ExpressRoute z klasického modelu nasazení Resource Manager pomocí prostředí PowerShell](expressroute-howto-move-arm.md). Toto je předpokladem pro přesunout prostředky, které jsou připojené k virtuální síti.
* Virtuální sítě, bran a související nasazení v rámci virtuální sítě, které jsou připojené k okruhu ExpressRoute v rámci stejného předplatného, mohou být migrovány do prostředí Resource Manager bez odstávky. Můžete provést kroky popsané dál migrovat prostředkům, například virtuální sítě, bran a virtuálních počítačů nasazených v rámci virtuální sítě. Je nutné zajistit, že virtuální sítě jsou správně nakonfigurovaná, před jejich migrací. 
* Virtuální sítě, bran a související nasazení v rámci virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute vyžadují výpadky k dokončení migrace. Poslední část dokumentu popisuje kroky pro migraci prostředků.
* Nelze migrovat virtuální síť s bránu ExpressRoute i bránu VPN.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Přesun okruhu ExpressRoute z classic do Resource Manager
Okruh ExpressRoute z klasického je třeba přesunout do prostředí Resource Manager předtím, než se pokusíte migrovat prostředky, které jsou připojené k okruhu ExpressRoute. K provedení této úlohy, najdete v následujících článcích:

* Zkontrolujte informace, které jsou poskytovány na základě [přesun okruhu ExpressRoute z classic do Resource Manager](expressroute-move.md).
* [Přesun okruhu z classic do Resource Manager pomocí Azure PowerShell](expressroute-howto-move-arm.md).
* Pomocí portálu pro správu služby Azure. Můžete postupovat podle pracovního postupu [vytvořit nové okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a zvolte možnost importovat. 

Tato operace nezahrnuje výpadku. Můžete pokračovat k přenosu dat mezi vaší místní a společnosti Microsoft, když probíhá migrace.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrovat virtuální sítě, bran a související nasazení

Kroky, které při migraci závisí na tom, jestli vaše prostředky jsou ve stejném předplatném a různých předplatných.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrovat virtuální sítě, bran a související nasazení ve stejném předplatném jako okruh ExpressRoute
Tato část popisuje kroky pro migraci virtuální sítě, bránu a přidružené nasazení ve stejném předplatném jako okruh ExpressRoute. Odstávka je přidružen k této migrace. Můžete nadále používat všechny prostředky prostřednictvím procesu migrace. Správu roviny uzamčeno, když probíhá migrace. 

1. Ujistěte se, že okruh ExpressRoute byl přesunut z klasického prostředí Resource Manager.
2. Ujistěte se, jestli virtuální sítě má správně připravené pro migraci.
3. Registrace předplatného pro migraci prostředků. Chcete-li zaregistrovat předplatné pro migraci prostředků, použijte následující fragment kódu prostředí PowerShell:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Ověření, přípravě a migrovat. Chcete-li přesunout virtuální síť, použijte následující fragment kódu prostředí PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Migrace může přerušit také spuštěním následující rutiny prostředí PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Další kroky
* [Platforma podporovaná migrace z klasického do Azure Resource Manageru prostředků IaaS](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Technické podrobné informace o platformy podporované migrace z klasického do Azure Resource Manageru](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Nejčastější dotazy: Migrace z klasického do Azure Resource Manageru prostředky infrastruktury podporované platformy](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Zkontrolujte nejběžnějších chyb, migrace a jejich zmírnění](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
