---
title: 'Propojení virtuální sítě k okruhu ExpressRoute: prostředí PowerShell: Azure | Dokumentace Microsoftu'
description: Tento dokument poskytuje přehled o tom, jak propojit virtuální sítě (Vnet) pro okruhy ExpressRoute pomocí modelu nasazení Resource Manageru a Powershellu.
services: expressroute
documentationcenter: na
author: ganesr
ms.service: expressroute
ms.topic: article
ms.date: 10/30/2018
ms.author: ganesr
ms.openlocfilehash: 02b765b3efe933a1dd8d12d422efff8142bdc553
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51234693"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Připojení virtuální sítě k okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek pomáhá propojení virtuálních sítí (Vnet) pro okruhy Azure ExpressRoute pomocí modelu nasazení Resource Manageru a Powershellu. Virtuální sítě může být buď v rámci stejného předplatného nebo součástí jiného předplatného. Tento článek také ukazuje, jak aktualizovat propojení virtuální sítě.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute. 

* Jedné virtuální sítě nesmí být propojení až čtyři okruhy ExpressRoute. Postupujte podle kroků v tomto článku vytvořte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute může být ve stejném předplatném, různá předplatná nebo kombinaci obou.

* Můžete propojit virtuální sítě mimo geopolitické oblasti okruh ExpressRoute nebo připojit větší počet virtuálních sítí pro váš okruh ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Zkontrolujte, [nejčastější dotazy k](expressroute-faqs.md) podrobné informace o doplněk premium.


## <a name="before-you-begin"></a>Než začnete

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky směrování](expressroute-routing.md), a [pracovních postupů](expressroute-workflows.md) předtím, než začnete s konfigurací.

* Musí mít aktivní okruh ExpressRoute. 
  * Postupujte podle pokynů a [vytvořit okruh ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený poskytovatel připojení. 
  * Ujistěte se, že máte soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Zobrazit [konfigurace směrování](expressroute-howto-routing-arm.md) najdete pokyny pro směrování. 
  * Ověřte, že je nakonfigurovaný soukromého partnerského vztahu Azure a partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem nahoru tak, že povolíte připojení k začátku do konce.
  * Ujistěte se, že máte virtuální síť a Brána virtuální sítě vytvořené a plně zřízený. Postupujte podle pokynů a [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

### <a name="working-with-azure-powershell"></a>Práce s využitím Azure Powershellu
[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě v rámci stejného předplatného k okruhu
Pomocí následující rutiny můžete připojit bránu virtuální sítě k okruhu ExpressRoute. Ujistěte se, že brány virtuální sítě je vytvořen a připraven k propojení předtím, než spustíte rutinu:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet napříč několika předplatnými. Následující obrázek znázorňuje jednoduchý způsob sdílení prací pro okruhy ExpressRoute schéma napříč několika předplatnými.

Každá z menších cloudy v rámci velkých cloudových se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Vlastní předplatné každé oddělení v rámci organizace můžete použít k nasazení svých služeb – ale můžete sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jednoho oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Okruh ExpressRoute můžete použít další předplatná v rámci organizace.

> [!NOTE]
> Poplatky za připojení a šířku pásma pro okruh ExpressRoute se použijí na vlastníka předplatného. Všechny virtuální sítě sdílejí stejnou šířku pásma.
> 
> 

![Připojením mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Správa – okruh uživatelů a vlastníků okruh

Vlastníka okruhu je autorizovaný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvářet autorizace, které můžou uplatnit: uživatelé okruhu". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou ve stejném předplatném jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnění autorizací (jedním autorizačním jednu virtuální síť).

Vlastníka okruhu má schopnost upravovat dokumentů a odvolání přístupu autorizace kdykoli. Odvolává se autorizace výsledkem spojení odstraňuje z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Chcete-li vytvořit povolení**

Vlastníka okruhu vytvoří autorizaci. Výsledkem je vytvoření autorizačního klíče, který je možné připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruhu. Povolení platí jenom jedno připojení.

Následující rutiny fragment kódu ukazuje, jak vytvořit povolení:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```


Odpověď na to bude obsahovat autorizační klíč a stav:

    Name                   : MyAuthorization1
    Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
    Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
    AuthorizationKey       : ####################################
    AuthorizationUseStatus : Available
    ProvisioningState      : Succeeded



**Ke kontrole autorizace**

Vlastníka okruhu můžete zkontrolovat všechny autorizace, které jsou vydány na konkrétní okruhu spuštěním následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Chcete-li přidat autorizace**

Vlastníka okruhu můžete přidat autorizace pomocí následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Chcete-li odstranit autorizace**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací uživateli spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operace pro uživatele okruhu

ID partnera a autorizační klíč z vlastníka okruhu, musí uživatel okruhu. Autorizační klíč je identifikátor GUID.

ID partnera můžete zkontrolovat pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Chcete-li uplatnit autorizaci připojení**

Uživatele okruhu spuštěním následující rutiny můžete uplatnit autorizaci odkaz:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**K uvolnění ověření připojení**

Povolení můžete uvolnit tak, že odstraníte okruh ExpressRoute k virtuální síti, která odkazuje připojení.

## <a name="modify-a-virtual-network-connection"></a>Upravit připojení k virtuální síti
Můžete aktualizovat některé vlastnosti připojení k virtuální síti. 

**Chcete-li aktualizovat váhy připojení**

Virtuální síť lze připojit k více okruhům ExpressRoute. Může se zobrazit stejné předpony z více než jeden okruh ExpressRoute. Zvolte připojení, které chcete posílat provoz určený pro tuto předponu můžete změnit *RoutingWeight* připojení. Provoz se pošle na připojení s nejvyšším *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Rozsah *RoutingWeight* je od 0 do 32 000. Výchozí hodnota je 0.

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
