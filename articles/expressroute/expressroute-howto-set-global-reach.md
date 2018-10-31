---
title: Nakonfigurujte globální dosah Azure ExpressRoute | Dokumentace Microsoftu
description: Tento článek vám pomůže propojit okruhy ExpressRoute tvořit privátní sítě mezi místními sítěmi a povolit globální dosah.
documentationcenter: na
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mialdrid
ms.openlocfilehash: 67fbf9dc430d615efe3ef894add1a26bbce792bc
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2018
ms.locfileid: "50237974"
---
# <a name="configure-expressroute-global-reach-preview"></a>Konfigurace ExpressRoute s globálním dosahem (Preview)
Tento článek vám pomůže nakonfigurovat ExpressRoute globální dosah pomocí Powershellu. Další informace najdete v tématu [ExpressRouteRoute globální dosah](expressroute-global-reach.md).
 
## <a name="before-you-begin"></a>Než začnete
> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 


Před zahájením konfigurace, je potřeba zkontrolovat následující požadavky.

* Nainstalujte nejnovější verzi Azure Powershellu. Zobrazit [nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/install-azurerm-ps).
* Vysvětlení zřizování okruhů ExpressRoute [pracovních postupů](expressroute-workflows.md).
* Zajistěte, aby že vaše okruhy ExpressRoute jsou ve stavu zřízená.
* Ujistěte se, že soukromý partnerský vztah Azure je nakonfigurovaný na okruhů ExpressRoute.  

### <a name="log-into-your-azure-account"></a>Přihlaste se do svého účtu Azure
Spustit konfiguraci, musíte se přihlásit ke svému účtu Azure. 

Otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se ke svému účtu. Příkaz zobrazí výzvu k přihlášení přihlašovací údaje ke svému účtu Azure.  

```powershell
Connect-AzureRmAccount
```

Pokud máte více předplatných Azure, zkontrolujte předplatná pro daný účet.

```powershell
Get-AzureRmSubscription
```

Určete předplatné, které chcete použít.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
```

### <a name="identify-your-expressroute-circuits-for-configuration"></a>Identifikujte okruhů ExpressRoute pro konfiguraci
Můžete povolit globální dosah ExpressRoute mezi jakékoli dva okruhy ExpressRoute nacházejí v podporované země a jejich vytvoření při různých umístění partnerského vztahu. Pokud vaše předplatné vlastní obou okruzích můžete buď okruh a spustit konfiguraci v následujících částech. Pokud dva okruhy jsou v různých předplatných Azure, bude nutné autorizace z jednoho předplatného Azure a předat autorizační klíč při spuštění příkazu konfigurace v rámci předplatného Azure.

## <a name="enable-connectivity-between-your-on-premises-networks"></a>Povolit připojení mezi místními sítěmi

Použijte následující příkazy k získání okruh 1 a 2 okruhu. Dva okruhy jsou ve stejném předplatném.

```powershell
$ckt_1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
```

Spusťte následující příkaz okruh 1 a předejte mu ID 2 privátní partnerský vztah okruhu společnosti.

> [!NOTE]
> Soukromý partnerský vztah na Id vypadá */subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering*
> 
>

```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
```

> [!IMPORTANT]
> *-AddressPrefix* musí být IPv4 je/29 podsítě, například "10.0.0.0/29". IP adresy v této podsíti použije k navázání připojení mezi dva okruhy ExpressRoute. Ve virtuálních sítích Azure nebo v místních sítí, nesmí používat adresy v této podsíti.
> 



Uložte konfiguraci v okruhu 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení výše uvedené operace by měla mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci autorizace je vygenerována v předplatném okruh 2 a autorizační klíč je předána na okruh 1.

Generovat autorizačního klíče. 
```powershell
$ckt_2 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_2
```
Poznamenejte si okruh 2 soukromého partnerského vztahu pro Id stejně jako autorizační klíč.

Spusťte následující příkaz okruh 1. Předejte 2 privátní partnerský vztah okruhu Id a autorizační klíč 
```powershell
Add-AzureRmExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
```

Uložte konfiguraci v okruhu 1
```powershell
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Po dokončení výše uvedené operace by měla mít připojení mezi vaší místní sítě na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="get-and-verify-the-configuration"></a>Získání a ověřit konfiguraci

Použijte následující příkaz k ověření konfigurace na okruh, kde konfigurace byla provedena, například 1 okruh v předchozím příkladu.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud spustíte jednoduše *$ckt1* v prostředí PowerShell, zobrazí se vám *CircuitConnectionStatus* ve výstupu. To zjistíte, zda připojení se naváže, "Připojeno", nebo Ne, "Odpojené". 

## <a name="disable-connectivity-between-your-on-premises-networks"></a>Zakázat připojení mezi místními sítěmi

Pro jeho zakázání spuštěním příkazů proti okruh, kde konfigurace byla provedena, například 1 okruh v předchozím příkladu.

```powershell
$ckt1 = Get-AzureRmExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzureRmExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Můžete spustit operaci Get, abyste ověřili stav. 

Po dokončení výše uvedené operace se už nebude mít připojení mezi vaší místní síti prostřednictvím okruhů ExpressRoute. 


## <a name="next-steps"></a>Další kroky
1. [Další informace o ExpressRoute globální dosah](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)


