---
title: 'Azure ExpressRoute: Migrace klasických virtuálních sítí do Správce prostředků'
description: Tato stránka popisuje, jak migrovat virtuální sítě přidružené k ExpressRoute do Správce prostředků po přesunutí okruhu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061305"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrace virtuálních sítí přidružených k ExpressRoute z klasických do Správce prostředků

Tento článek vysvětluje, jak migrovat virtuální sítě přidružené k ExpressRoute z klasického modelu nasazení do modelu nasazení Azure Resource Manager u přesunutí okruhu ExpressRoute. 

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ověřte, že máte nejnovější verze modulů Azure PowerShellu. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). Pokud chcete nainstalovat modul Správy služeb prostředí PowerShell (který je potřeba pro klasický model nasazení), přečtěte si informace [o instalaci modulu správy služeb Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)
* Zkontrolujte informace, které jsou k dispozici v části [Přesunutí okruhu ExpressRoute z klasického do Správce prostředků](expressroute-move.md). Ujistěte se, že plně rozumíte limitům a omezením.
* Ověřte, zda je okruh plně funkční v modelu klasického nasazení.
* Ujistěte se, že máte skupinu prostředků, která byla vytvořena v modelu nasazení Resource Manager.
* Projděte si následující dokumentaci k migraci prostředků:

    * [Migrace prostředků IaaS podporovaná platformou z klasických na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Časté otázky: Migrace prostředků IaaS podporovaná platformou z klasických na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Zkontrolujte nejčastější chyby migrace a skutečnosti snižující závažnost rizika](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Podporované a nepodporované situace

* Okruh ExpressRoute lze přesunout z klasického do prostředí Správce prostředků bez prostojů. Jakýkoli okruh ExpressRoute můžete přesunout z klasického do prostředí Resource Manager bez prostojů. Postupujte podle pokynů při [přesouvání okruhů ExpressRoute z klasického modelu nasazení Resource Manager pomocí prostředí PowerShell](expressroute-howto-move-arm.md). To je předpokladem pro přesunutí prostředků připojených k virtuální síti.
* Virtuální sítě, brány a přidružená nasazení v rámci virtuální sítě, které jsou připojeny k okruhu ExpressRoute ve stejném předplatném, lze migrovat do prostředí Správce prostředků bez prostojů. Můžete postupovat podle kroků popsaných později k migraci prostředků, jako jsou virtuální sítě, brány a virtuální počítače nasazené v rámci virtuální sítě. Před migrací je nutné zajistit, aby byly virtuální sítě správně nakonfigurovány. 
* Virtuální sítě, brány a přidružená nasazení v rámci virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute vyžadují některé prostoje k dokončení migrace. Poslední část dokumentu popisuje kroky, které je třeba při migraci prostředků.
* Virtuální síť s bránou ExpressRoute a bránou VPN nelze migrovat.
* Migrace okruhu ExpressRoute není podporována. Další informace naleznete v tématu [Podpora přesunutí sítě Microsoft.Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Přesunutí okruhu ExpressRoute z klasického do Správce prostředků
Okruh ExpressRoute je nutné přesunout z klasického do prostředí Správce prostředků před pokusem o migraci prostředků, které jsou připojeny k okruhu ExpressRoute. Chcete-li tento úkol provést, přečtěte si následující články:

* Zkontrolujte informace, které jsou k dispozici v části [Přesunutí okruhu ExpressRoute z klasického do Správce prostředků](expressroute-move.md).
* [Přesuňte okruh z klasického na Správce prostředků pomocí Azure PowerShellu](expressroute-howto-move-arm.md).
* Použijte portál Azure Service Management. Pomocí pracovního postupu můžete [vytvořit nový okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a vybrat možnost importu. 

Tato operace nezahrnuje prostoje. Během probíhající migrace můžete pokračovat v přenosu dat mezi místními prostory a společností Microsoft.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrace virtuálních sítí, bran a přidružených nasazení

Postup migrace závisí na tom, jestli jsou vaše prostředky ve stejném předplatném, v různých předplatných nebo v obou.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrace virtuálních sítí, bran a přidružených nasazení ve stejném předplatném jako okruh ExpressRoute
Tato část popisuje kroky, které je třeba dodržovat při migraci virtuální sítě, brány a přidružených nasazení ve stejném předplatném jako okruh ExpressRoute. S touto migrací nejsou přidruženy žádné prostoje. Můžete pokračovat v používání všech prostředků prostřednictvím procesu migrace. Rovina správy je uzamčena, zatímco probíhá migrace. 

1. Ujistěte se, že okruh ExpressRoute byl přesunut z klasického do prostředí Správce prostředků.
2. Ujistěte se, že virtuální síť byla připravena vhodně pro migraci.
3. Zaregistrujte své předplatné pro migraci prostředků. Pokud chcete zaregistrovat předplatné pro migraci prostředků, použijte následující fragment Prostředí PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Ověřujte, připravte a migrujte. Pokud chcete virtuální síť přesunout, použijte následující fragment Prostředí PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Migraci můžete také přerušit spuštěním následující rutiny prostředí PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Další kroky
* [Migrace prostředků IaaS podporovaná platformou z klasických na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Časté otázky: Migrace prostředků IaaS podporovaná platformou z klasických na Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Zkontrolujte nejčastější chyby migrace a skutečnosti snižující závažnost rizika](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
