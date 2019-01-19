---
title: 'Migrace virtuální sítě z classic na Resource Manager – ExpressRoute: Azure: Prostředí PowerShell | Dokumentace Microsoftu'
description: Tato stránka popisuje, jak migrovat ExpressRoute přidružené k virtuálním sítím na Resource Manager po přesunu okruh.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 7b95c8b230714e1ba9306620e58628104cd676c9
ms.sourcegitcommit: c31a2dd686ea1b0824e7e695157adbc219d9074f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2019
ms.locfileid: "54401637"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrace virtuální sítě přidružené k ExpressRoute z modelu nasazení classic do Resource Manageru

Tento článek vysvětluje, jak migrovat virtuální sítě přidružené k ExpressRoute z modelu nasazení classic do modelu nasazení Azure Resource Manageru po přesunu okruh ExpressRoute. 

## <a name="before-you-begin"></a>Před zahájením
* Ověřte, že máte nejnovější verzi modulů prostředí Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).
* Ujistěte se, že jste si prohlédli [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.
* Projděte si informace, které není k dispozici [přesunu okruh ExpressRoute z modelu nasazení classic do Resource Manageru](expressroute-move.md). Ujistěte se, že plně chápete limity a omezení.
* Ověřte, že je okruh v modelu nasazení classic plně funkční.
* Ujistěte se, že máte skupinu prostředků, který byl vytvořen v modelu nasazení Resource Manager.
* Projděte si následující dokumentaci k migraci prostředků:

    * [Platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Nejčastější dotazy: Platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Zkontrolujte běžné chyby při migraci a jejich zmírnění](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Podporované a nepodporované scénáře

* Okruh ExpressRoute je přesunout z classicu na Resource Manageru prostředí bez jakýchkoli prostojů. Jakýkoli okruh ExpressRoute můžete přesunout z classicu na Resource Manageru prostředí bez výpadků. Postupujte podle pokynů v [přesun okruhů ExpressRoute z klasického modelu nasazení Resource Manageru pomocí prostředí PowerShell](expressroute-howto-move-arm.md). Toto je předpokladem pro přesunutí prostředků, které jsou připojené k virtuální síti.
* Virtuální sítě, bran a navazující nasazení v rámci virtuální sítě, které jsou připojeny k okruhu ExpressRoute v rámci stejného předplatného, můžete migrovat do Resource Manageru prostředí bez jakýchkoli prostojů. Provedením kroků popsaných dále migrovat prostředky, jako jsou virtuální sítě, bran a virtuálních počítačů nasazených v rámci virtuální sítě. Musíte zajistit, že virtuální sítě mají správnou konfiguraci před migrací. 
* Virtuální sítě, bran a navazující nasazení v rámci virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute vyžadují určitý výpadek k dokončení migrace. Poslední část dokumentu popisuje kroky, kterými se můžete řídit migrace prostředků.
* Není možné migrovat virtuální síť s bránu ExpressRoute i bránu VPN.
* Migrace mezi předplatnými okruhu ExpressRoute není podporována. Další informace najdete v tématu [služby, které nelze přesunout](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Přesun okruhu ExpressRoute z modelu nasazení classic do Resource Manageru
Okruh ExpressRoute musí přesunout z classic do Resource Manageru prostředí před pokusem o migraci prostředků, které jsou připojeny k okruhu ExpressRoute. Tento úkol provést, najdete v následujících článcích:

* Projděte si informace, které není k dispozici [přesunu okruh ExpressRoute z modelu nasazení classic do Resource Manageru](expressroute-move.md).
* [Přesun okruhu z modelu nasazení classic do Resource Manageru pomocí prostředí Azure PowerShell](expressroute-howto-move-arm.md).
* Použití portálu pro správu služby Azure. Provedením pracovního postupu [vytvořit nový okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a vyberte možnost importu. 

Tato operace nevyžaduje výpadek. Můžete pokračovat k přenosu dat mezi místním prostředí a Microsoft, zatímco probíhá migrace.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrace virtuální sítě, bran a přidružené nasazení

Postup provedením migrace závisí na tom, jestli vaše prostředky jsou ve stejném předplatném nebo různých předplatných.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrace virtuální sítě, bran a přidružené nasazení ve stejném předplatném jako okruh ExpressRoute
Tato část popisuje postup platí pro migraci virtuální sítě, brána a přidružené nasazení ve stejném předplatném jako okruh ExpressRoute. Odstávka není přidružená k této migraci. Můžete nadále využívat všechny prostředky v procesu migrace. Rovina správy je uzamčen, zatímco probíhá migrace. 

1. Ujistěte se, že okruh ExpressRoute se změnila z classic do Resource Manageru prostředí.
2. Ujistěte se, že virtuální síť je odpovídajícím způsobem připraven na migraci.
3. Zaregistrujte své předplatné pro migraci prostředků. Pokud chcete zaregistrovat své předplatné pro migraci prostředků, použijte následující fragment kódu Powershellu:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Ověření, přípravě a migrovat. Pokud chcete přesunout virtuální sítě, použijte následující fragment kódu Powershellu:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Migraci můžete přerušit také spuštěním následující rutiny Powershellu:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Další postup
* [Platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Nejčastější dotazy: Platformou podporované migraci prostředků IaaS z modelu classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Zkontrolujte běžné chyby při migraci a jejich zmírnění](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
