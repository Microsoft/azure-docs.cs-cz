---
title: 'Azure ExpressRoute: migrace klasického virtuální sítěu do Správce prostředků'
description: Tato stránka popisuje, jak migrovat virtuální sítě přidružené k ExpressRoute do Správce prostředků po přesunutí okruhu.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: ac6fe619fc1c65b9ced18227d08cde50997eb7d7
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395260"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrace virtuálních sítí přidružených k ExpressRoute z modelu Classic na Správce prostředků

Tento článek vysvětluje, jak migrovat virtuální sítě přidružené k ExpressRoute z modelu nasazení Classic do modelu nasazení Azure Resource Manager po přesunutí okruhu ExpressRoute. 

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Ověřte, že máte nejnovější verze Azure PowerShell modulů. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/). Pokud chcete nainstalovat modul PowerShell Service Management (který je potřebný pro model nasazení Classic), přečtěte si téma [Instalace modulu správy služby Azure PowerShell](/powershell/azure/servicemanagement/install-azure-ps).
* Než začnete s [konfigurací, ujistěte](expressroute-prerequisites.md)se, že jste zkontrolovali požadavky, [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .
* Přečtěte si informace, které jsou k dispozici v části [přesunutí okruhu ExpressRoute z modelu Classic na správce prostředků](expressroute-move.md). Ujistěte se, že plně rozumíte omezením a omezením.
* Ověřte, že okruh je plně funkční v modelu nasazení Classic.
* Ujistěte se, že máte skupinu prostředků vytvořenou v modelu nasazení Správce prostředků.
* Přečtěte si následující dokumentaci k migraci prostředků:

    * [Platformou podporovaná migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
    * [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Nejčastější dotazy: migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Kontrola nejčastějších chyb migrace a zmírnění rizik](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Podporované a nepodporované situace

* Okruh ExpressRoute lze přesunout z klasického prostředí do prostředí Správce prostředků bez výpadků. Libovolný okruh ExpressRoute můžete přesunout z klasického prostředí do prostředí Správce prostředků bez výpadků. Postupujte podle pokynů v tématu [Přesun okruhů ExpressRoute z modelu nasazení Classic do modelu nasazení Správce prostředků pomocí prostředí PowerShell](expressroute-howto-move-arm.md). Je to předpoklad pro přesun prostředků připojených k virtuální síti.
* Virtuální sítě, brány a přidružená nasazení v rámci virtuální sítě, která jsou připojená k okruhu ExpressRoute ve stejném předplatném, se dají migrovat do prostředí Správce prostředků bez výpadků. Můžete postupovat podle kroků popsaných dále v tématu Migrace prostředků, jako jsou virtuální sítě, brány a virtuální počítače nasazené v rámci virtuální sítě. Před migrací je nutné zajistit, aby byly virtuální sítě správně nakonfigurovány. 
* Virtuální sítě, brány a přidružená nasazení v rámci virtuální sítě, které nejsou ve stejném předplatném, jako okruh ExpressRoute, vyžadují pro dokončení migrace nějaké výpadky. Poslední část dokumentu popisuje kroky, které je třeba provést při migraci prostředků.
* Virtuální síť s bránou ExpressRoute a VPN Gateway nejde migrovat.
* Migrace mezi předplatnými okruhů ExpressRoute se nepodporuje. Další informace najdete v tématu [Podpora přesunutí Microsoft. Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Přesunutí okruhu ExpressRoute z modelu Classic na Správce prostředků
Před migrací prostředků, které jsou připojené k okruhu ExpressRoute, je nutné přesunout okruh ExpressRoute z klasického prostředí do prostředí Správce prostředků. Chcete-li provést tuto úlohu, přečtěte si následující články:

* Přečtěte si informace, které jsou k dispozici v části [přesunutí okruhu ExpressRoute z modelu Classic na správce prostředků](expressroute-move.md).
* [Přesun okruhu z modelu Classic na správce prostředků pomocí Azure PowerShell](expressroute-howto-move-arm.md).
* Použijte portál pro správu služeb Azure. Můžete postupovat podle pracovního postupu a [vytvořit nový okruh ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) a vybrat možnost importu. 

Tato operace nezahrnuje výpadky. V průběhu migrace můžete dál přenášet data mezi místními prostory a Microsoftem.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrace virtuálních sítí, bran a přidružených nasazení

Kroky, které provedete při migraci, závisí na tom, jestli jsou vaše prostředky ve stejném předplatném, různých předplatných nebo obojím.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrace virtuálních sítí, bran a přidružených nasazení do stejného předplatného jako okruh ExpressRoute
Tato část popisuje kroky, které je třeba provést při migraci virtuální sítě, brány a přidružených nasazení do stejného předplatného jako okruh ExpressRoute. K této migraci se nevztahují žádné výpadky. V rámci procesu migrace můžete dál používat všechny prostředky. Rovina správy je v průběhu migrace uzamčena. 

1. Zajistěte, aby byl okruh ExpressRoute přesunut z modelu Classic do prostředí Správce prostředků.
2. Ujistěte se, že virtuální síť je pro migraci vhodně připravená.
3. Zaregistrujte své předplatné pro migraci prostředků. Pokud chcete zaregistrovat předplatné pro migraci prostředků, použijte následující fragment kódu prostředí PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Ověřte, připravte a migrujte. Pokud chcete přesunout virtuální síť, použijte následující fragment kódu prostředí PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Migraci můžete také přerušit spuštěním následující rutiny PowerShellu:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Další kroky
* [Platformou podporovaná migrace prostředků IaaS z nasazení Classic do Azure Resource Manageru](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
* [Podrobné technické informace o platformou podporované migraci z modelu Classic na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Nejčastější dotazy: migrace prostředků IaaS podporovaných platformou z klasických na Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Kontrola nejčastějších chyb migrace a zmírnění rizik](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
