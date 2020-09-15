---
title: 'Azure ExpressRoute: Konfigurace Global Reach'
description: Tento článek vám pomůže propojit okruhy ExpressRoute dohromady a vytvořit privátní síť mezi místními sítěmi a povolit Global Reach.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/25/2019
ms.author: duau
ms.openlocfilehash: dd4c6f0b9d518acf06f7d018a65cc2b9b92db33d
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89395447"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurace služby ExpressRoute Global Reach

Tento článek vám pomůže nakonfigurovat Global Reach ExpressRoute pomocí prostředí PowerShell. Další informace najdete v tématu [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Než začnete

Než začnete s konfigurací, potvrďte následující:

* Rozumíte [pracovním](expressroute-workflows.md)postupům zřizování okruhů ExpressRoute.
* Vaše okruhy ExpressRoute jsou ve stavu zřizování.
* Privátní partnerské vztahy Azure se konfigurují na okruhech ExpressRoute.
* Pokud chcete spustit prostředí PowerShell místně, ověřte, zda je v počítači nainstalována nejnovější verze Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifikace okruhů

1. Pokud chcete spustit konfiguraci, přihlaste se ke svému účtu Azure a vyberte předplatné, které chcete použít.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifikujte okruhy ExpressRoute, které chcete použít. Můžete povolit ExpressRoute Global Reach mezi soukromým partnerským vztahem všech dvou okruhů ExpressRoute, pokud se nacházejí v podporovaných zemích nebo oblastech a vytvořily se v různých umístěních partnerských vztahů. 

   * Pokud vaše předplatné vlastní oba okruhy, můžete zvolit buď okruh, aby se konfigurace spustila v následujících oddílech.
   * Pokud se dva okruhy nacházejí v různých předplatných Azure, budete potřebovat autorizaci z jednoho předplatného Azure. Pak předáte autorizační klíč při spuštění příkazu konfigurace v jiném předplatném Azure.

## <a name="enable-connectivity"></a>Povolit připojení

Povolte konektivitu mezi místními sítěmi. Existují samostatné sady instrukcí pro okruhy, které jsou ve stejném předplatném Azure, a okruhy, které jsou různými předplatnými.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Okruhy ExpressRoute ve stejném předplatném Azure

1. Pomocí následujících příkazů můžete získat okruh 1 a okruh 2. Dva okruhy jsou ve stejném předplatném.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Spusťte následující příkaz pro okruh 1 a předejte mu ID privátního partnerského vztahu okruhu 2. Při spuštění příkazu si všimněte následujícího:

   * ID privátního partnerského vztahu vypadá podobně jako v následujícím příkladu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musí být podsíť IPv4 a/29, například "10.0.0.0/29". IP adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. Adresy v této podsíti byste neměli používat ve virtuálních sítích Azure ani ve vaší místní síti.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Konfiguraci na okruhu 1 uložte následujícím způsobem:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace budete mít propojení mezi místními sítěmi na obou stranách prostřednictvím obou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud tyto dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci se autorizace vygeneruje v rámci předplatného okruhu 2 a autorizační klíč se předává okruhu 1.

1. Vygenerujte autorizační klíč.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Poznamenejte si ID privátního partnerského vztahu okruhu 2 a také autorizační klíč.
2. Spusťte následující příkaz proti okruhu 1. Předejte ID privátního partnerského vztahu okruhu 2 a autorizační klíč.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Uložte konfiguraci na okruh 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace budete mít propojení mezi místními sítěmi na obou stranách prostřednictvím obou okruhů ExpressRoute.

## <a name="verify-the-configuration"></a>Ověření konfigurace

Pomocí následujícího příkazu Ověřte konfiguraci v okruhu, ve kterém byla provedena konfigurace (například okruh 1 v předchozím příkladu).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud v PowerShellu jednoduše spustíte *$CKT 1* , ve výstupu se zobrazí *CircuitConnectionStatus* . Dozvíte se, zda je připojení navázáno, připojeno nebo odpojeno. 

## <a name="disable-connectivity"></a>Zákaz připojení

Pokud chcete zakázat připojení mezi místními sítěmi, spusťte příkazy proti okruhu, ve kterém byla provedena konfigurace (například okruh 1 v předchozím příkladu).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Chcete-li ověřit stav, můžete spustit operaci načíst.

Po dokončení předchozí operace už nebudete mít připojení mezi vaší místní sítí přes okruhy ExpressRoute.

## <a name="next-steps"></a>Další kroky
1. [Další informace o ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute k virtuální síti Azure](expressroute-howto-linkvnet-arm.md)
