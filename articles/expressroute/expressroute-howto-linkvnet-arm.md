---
title: 'ExpressRoute: Propojení virtuální sítě s okruhem: Azure PowerShell'
description: Tento dokument poskytuje přehled o tom, jak propojit virtuální sítě (Virtuální sítě) s okruhy ExpressRoute pomocí modelu nasazení Resource Manager a Prostředí PowerShell.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 05/20/2018
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 755b1898ee4cbc32de3a65a6bbc368ecf3eb3acf
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80616383"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Připojení virtuální sítě k okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [portál Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (Virtuální sítě) s okruhy Azure ExpressRoute pomocí modelu nasazení Resource Manager a PowerShellu. Virtuální sítě může být buď ve stejném předplatném nebo část jiného předplatného. Tento článek také ukazuje, jak aktualizovat propojení virtuální sítě.

* Ke standardnímu okruhu ExpressRoute můžete propojit až 10 virtuálních sítí. Všechny virtuální sítě musí být ve stejné geopolitické oblasti při použití standardního okruhu ExpressRoute. 

* Jedna virtuální síť může být propojena až ke čtyřem okruhům ExpressRoute. Pomocí kroků v tomto článku vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různých předplatných nebo kombinací obou.

* Můžete propojit virtuální sítě mimo geopolitickou oblast okruhu ExpressRoute nebo připojit větší počet virtuálních sítí k okruhu ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Další podrobnosti o prémiovém doplňku najdete v [nejčastějších](expressroute-faqs.md) dotazech.


## <a name="before-you-begin"></a>Než začnete

* Před zahájením konfigurace zkontrolujte [požadavky](expressroute-prerequisites.md)na směrování , [požadavky na směrování](expressroute-routing.md)a pracovní [postupy.](expressroute-workflows.md)

* Musí mít aktivní okruh ExpressRoute. 
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte obvod povolený poskytovatelem připojení. 
  * Ujistěte se, že máte azure privátní partnerský vztah nakonfigurovaný pro váš okruh. Pokyny pro směrování naleznete v článku [konfigurace směrování.](expressroute-howto-routing-arm.md) 
  * Ujistěte se, že je nakonfigurovaný soukromý partnerský vztah Azure a partnerský vztah Protokolu BGP mezi vaší sítí a Microsoftem je nahoře, abyste mohli povolit připojení od konce do konce.
  * Ujistěte se, že máte vytvořenou a plně zřízené virtuální síť a bránu virtuální sítě. Podle pokynů [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType 'ExpressRoute', ne VPN.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShellem

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu
Bránu virtuální sítě můžete připojit k okruhu ExpressRoute pomocí následující rutiny. Ujistěte se, že brána virtuální sítě je vytvořen a je připraven k propojení před spuštěním rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi více předplatnými. Následující obrázek znázorňuje jednoduché schéma fungování sdílení pro okruhy ExpressRoute napříč více předplatnými.

Každý z menších cloudů v rámci velkého cloudu se používá k reprezentaci předplatných, které patří do různých oddělení v rámci organizace. Každé oddělení v rámci organizace můžete použít své vlastní předplatné pro nasazení svých služeb – ale mohou sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Okruh ExpressRoute může vlastnit jedno oddělení (v tomto příkladu: IT). Ostatní předplatná v rámci organizace můžete použít okruh ExpressRoute.

> [!NOTE]
> Poplatky za připojení a šířku pásma pro okruh ExpressRoute budou použity na vlastníka předplatného. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojení napříč předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Správa - vlastníci obvodů a uživatelů okruhů

"Vlastník okruhu" je oprávněným uživatelem napájení prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizace, které mohou být uplatněny 'uživatelé okruhu'. Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhů mohou uplatnit autorizace (jedna autorizace na virtuální síť).

Vlastník obvodu má pravomoc kdykoli upravit a odvolat autorizace. Odvolání autorizace má za následek odstranění všech připojení propojení z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastník okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který může uživatel okruhu použít k připojení svých bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná pouze pro jedno připojení.

Následující úryvek rutiny ukazuje, jak vytvořit autorizaci:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Odpověď na tuto odpověď bude obsahovat autorizační klíč a stav:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Kontrola autorizací**

Vlastník okruhu může zkontrolovat všechna oprávnění, která jsou vydána na určitém okruhu spuštěním následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Přidání autorizací**

Vlastník okruhu můžete přidat autorizace pomocí následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Odstranění autorizací**

Vlastník okruhu může odvolat nebo odstranit autorizace pro uživatele spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operace uživatele okruhu

Uživatel okruhu potřebuje ID druhé strany a autorizační klíč od vlastníka okruhu. Autorizační klíč je identifikátor GUID.

ID druhé strany lze zkontrolovat z následujícího příkazu:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Uplatnění autorizace připojení**

Uživatel okruhu může spustit následující rutinu pro uplatnění autorizace odkazu:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="modify-a-virtual-network-connection"></a>Úprava připojení k virtuální síti
Můžete aktualizovat určité vlastnosti připojení k virtuální síti. 

**Aktualizace hmotnosti připojení**

Virtuální síť může být připojena k více okruhům ExpressRoute. Můžete obdržet stejnou předponu z více než jednoho okruhu ExpressRoute. Chcete-li zvolit, které připojení má být odesláno pro tuto předponu, můžete změnit *tloušťku směrování* připojení. Provoz bude odeslán na připojení s nejvyšší *směrovací hmotností*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Rozsah *Tloušťka směrování* je 0 až 32000. Výchozí hodnota je 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurace rychlé cesty ExpressRoute 
[ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) můžete povolit, pokud je brána virtuální sítě Ultra Performance nebo ErGw3AZ. FastPath zlepšuje výkon cesty k datům, jako jsou pakety za sekundu a připojení za sekundu mezi místní sítí a virtuální sítí. 

**Konfigurace aplikace FastPath pro nové připojení**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aktualizace existujícího připojení pro povolení rychlé cesty**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="next-steps"></a>Další kroky
Další informace o expressroute naleznete v [nejčastějších dotazech k expressroute](expressroute-faqs.md).
