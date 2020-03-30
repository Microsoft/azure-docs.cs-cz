---
title: 'Azure ExpressRoute: Konfigurace globálního dosahu'
description: Tento článek vám pomůže propojit okruhy ExpressRoute dohromady a vytvořit privátní síť mezi místními sítěmi a povolit globální dosah.
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: jaredro
ms.openlocfilehash: 76de7a8854a58deb924cbbe3177ad5a7b5fd57a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083468"
---
# <a name="configure-expressroute-global-reach"></a>Konfigurace služby ExpressRoute Global Reach

Tento článek vám pomůže nakonfigurovat globální dosah ExpressRoute pomocí prostředí PowerShell. Další informace naleznete v tématu [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Než začnete

Před zahájením konfigurace potvrďte následující:

* Rozumíte [pracovním postupům](expressroute-workflows.md)zřizování okruhů ExpressRoute .
* Okruhy ExpressRoute jsou v zřízeném stavu.
* Privátní partnerský vztah Azure je nakonfigurovaný na vašich obvodech ExpressRoute.
* Pokud chcete powershell spustit místně, ověřte, jestli je ve vašem počítači nainstalovaná nejnovější verze Prostředí Azure PowerShell.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="identify-circuits"></a>Identifikovat obvody

1. Pokud chcete spustit konfiguraci, přihlaste se ke svému účtu Azure a vyberte předplatné, které chcete použít.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]
2. Identifikujte okruhy ExpressRoute, které chcete použít. Globální dosah ExpressRoute můžete povolit mezi libovolnými dvěma okruhy ExpressRoute, pokud jsou umístěny v podporovaných zemích nebo oblastech a byly vytvořeny v různých partnerských umístěních. 

   * Pokud vaše předplatné vlastní oba okruhy, můžete zvolit jeden okruh pro spuštění konfigurace v následujících částech.
   * Pokud jsou dva okruhy v různých předplatných Azure, budete potřebovat autorizaci z jednoho předplatného Azure. Pak předáte v autorizačním klíči při spuštění příkazu konfigurace v jiném předplatném Azure.

## <a name="enable-connectivity"></a>Povolení připojení

Povolte připojení mezi místními sítěmi. Existují samostatné sady pokynů pro okruhy, které jsou ve stejném předplatném Azure a okruhy, které jsou různé předplatná.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Okruhy ExpressRoute ve stejném předplatném Azure

1. Pomocí následujících příkazů získáte obvod 1 a okruh 2. Dva okruhy jsou ve stejném předplatném.

   ```azurepowershell-interactive
   $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   ```
2. Spusťte následující příkaz proti okruhu 1 a předavěte ID soukromého partnerského vztahu okruhu 2. Při spuštění příkazu si všimněte následujícího:

   * ID soukromého partnerského vztahu vypadá podobně jako v následujícím příkladu: 

     ```
     /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
     ```
   * *-AddressPrefix* musí být podsíť /29 IPv4, například "10.0.0.0/29". Ip adresy v této podsíti používáme k navázání připojení mezi dvěma okruhy ExpressRoute. Adresy v této podsíti byste neměli používat ve virtuálních sítích Azure ani v místní síti.

     ```azurepowershell-interactive
     Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
     ```
3. Uložte konfiguraci na okruhu 1 následujícím způsobem:

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace budete mít připojení mezi místními sítěmi na obou stranách prostřednictvím dvou okruhů ExpressRoute.

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Okruhy ExpressRoute v různých předplatných Azure

Pokud dva okruhy nejsou ve stejném předplatném Azure, budete potřebovat autorizaci. V následující konfiguraci je autorizace generována v předplatném okruhu 2 a autorizační klíč je předán okruhu 1.

1. Vygenerujte autorizační klíč.

   ```azurepowershell-interactive
   $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
   Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
   ```

   Poznamenejte si ID soukromého partnerského vztahu okruhu 2 a také autorizační klíč.
2. Spusťte následující příkaz proti okruhu 1. Projděte id soukromého partnerského vztahu okruhu 2 a autorizačního klíče.

   ```azurepowershell-interactive
   Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
   ```
3. Uložte konfiguraci na okruhu 1.

   ```azurepowershell-interactive
   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
   ```

Po dokončení předchozí operace budete mít připojení mezi místními sítěmi na obou stranách prostřednictvím dvou okruhů ExpressRoute.

## <a name="verify-the-configuration"></a>Ověření konfigurace

Pomocí následujícího příkazu ověřte konfiguraci na okruhu, ve kterém byla konfigurace provedena (například okruh 1 v předchozím příkladu).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

Pokud jednoduše spustit *$ckt1* v Prostředí PowerShell, uvidíte *CircuitConnectionStatus* ve výstupu. To vám řekne, zda je připojení navázáno, "Připojeno" nebo "Odpojeno". 

## <a name="disable-connectivity"></a>Zákaz připojení

Chcete-li zakázat připojení mezi místními sítěmi, spusťte příkazy proti okruhu, ve kterém byla konfigurace provedena (například okruh 1 v předchozím příkladu).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

Můžete spustit get operace k ověření stavu.

Po dokončení předchozí operace již nemáte připojení mezi místní sítí prostřednictvím okruhů ExpressRoute.

## <a name="next-steps"></a>Další kroky
1. [Další informace o globálním dosahu ExpressRoute](expressroute-global-reach.md)
2. [Ověření připojení ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
3. [Propojení okruhu ExpressRoute s virtuální sítí Azure](expressroute-howto-linkvnet-arm.md)
