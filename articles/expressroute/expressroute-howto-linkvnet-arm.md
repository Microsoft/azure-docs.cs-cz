---
title: 'Propojení virtuální sítě k okruhu ExpressRoute: prostředí PowerShell: Azure | Microsoft Docs'
description: Tento dokument obsahuje přehled o tom, jak propojit virtuální sítě (virtuální sítě) pro okruhy ExpressRoute pomocí modelu nasazení Resource Manager a prostředí PowerShell.
services: expressroute
documentationcenter: na
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: daacb6e5-705a-456f-9a03-c4fc3f8c1f7e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/08/2018
ms.author: ganesr
ms.openlocfilehash: 354f7c455e1a2846bbdd63fa12b1cc01e2a1b9c5
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
ms.locfileid: "29877547"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Připojit virtuální sítě k okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – portál Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek pomáhá propojení virtuálních sítí (virtuální sítě) pro okruhy Azure ExpressRoute pomocí modelu nasazení Resource Manager a prostředí PowerShell. Virtuální sítě může být buď v rámci stejného předplatného nebo součástí jiné předplatné. Tento článek také ukazuje, jak aktualizovat propojení virtuální sítě. 

## <a name="before-you-begin"></a>Než začnete
* Nainstalujte nejnovější verzi modulů prostředí Azure PowerShell. Další informace najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview).

* Zkontrolujte [požadavky](expressroute-prerequisites.md), [požadavky na směrování](expressroute-routing.md), a [pracovních](expressroute-workflows.md) před zahájením konfigurace.

* Musí mít aktivní okruh ExpressRoute. 
  * Postupujte podle pokynů a [vytvoření okruhu ExpressRoute](expressroute-howto-circuit-arm.md) a mějte ho povolený vaším poskytovatelem připojení. 
  * Ujistěte se, abyste měli soukromého partnerského vztahu Azure nakonfigurovaný pro váš okruh. Najdete v článku [konfigurace směrování](expressroute-howto-routing-arm.md) směrování pokyny najdete v článku. 
  * Zajistěte, aby soukromý partnerský vztah Azure je nakonfigurován a partnerském vztahu protokolu BGP mezi vaší sítí a Microsoftem zapnutý tak, že povolíte připojení klient server.
  * Ujistěte se, že máte virtuální sítě a brány virtuální sítě vytvoří a plně zřízený. Postupujte podle pokynů a [vytvořit bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType 'ExpressRoute' není síť VPN.

* Až 10 virtuálních sítí můžete propojit standardní okruh ExpressRoute. Všechny virtuální sítě musí být ve stejné geopolitické oblasti, při použití standardní okruh ExpressRoute. 

* Jeden virtuální sítě může být propojený až čtyři okruhy ExpressRoute. Můžete vytvořit nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete pomocí procesu níže. Okruhy ExpressRoute může být v rámci stejného předplatného, různých předplatných nebo kombinaci obou.

* Můžete propojit virtuální sítě mimo geopolitické oblasti okruhu ExpressRoute nebo větší počet virtuálních sítí připojit k okruhu ExpressRoute, pokud jste povolili doplněk ExpressRoute premium. Zkontrolujte [– nejčastější dotazy](expressroute-faqs.md) další podrobnosti o doplněk premium.


## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojit k okruhu virtuální sítě v rámci stejného předplatného
Pomocí následující rutiny můžete připojit bránu virtuální sítě k okruhu ExpressRoute. Ujistěte se, že bránu virtuální sítě se vytvoří a je připravený pro propojování předtím, než spustíte rutinu:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi více předplatných. Následující obrázek znázorňuje jednoduchý schéma o tom, jak sdílení funguje pro okruhy ExpressRoute napříč více předplatných.

Každý z menší cloudy v rámci velké cloudu se používá k reprezentování odběry, které patří k různým oblastem v rámci organizace. Každé oddělení v organizaci můžete použít vlastní předplatné nasazování jejich služeb – ale můžete sdílet jeden okruh ExpressRoute zpětné připojení k síti na pracovišti. Jednoho oddělení (v tomto příkladu: IT) můžete vlastní okruh ExpressRoute. Okruh ExpressRoute můžete použít jiné odběry v rámci organizace.

> [!NOTE]
> Připojení a šířku pásma poplatky za okruh ExpressRoute se použijí k vlastník předplatného. Všechny virtuální sítě sdílejí stejné šířky pásma.
> 
> 

![Připojení mezi předplatnými](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)


### <a name="administration---circuit-owners-and-circuit-users"></a>Správa - vlastníky okruhu a okruh uživatelů

Vlastníka okruhu je oprávněný uživatel Power prostředku okruhu ExpressRoute. Vlastníka okruhu můžete vytvořit autorizací, které lze uplatnit podle 'okruh uživatele'. Okruh uživatelů se vlastníci brány virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžete uplatnit autorizací (jedním autorizačním na jednu virtuální síť).

Vlastníka okruhu má právo upravit a kdykoli odvolat oprávnění. Odvolání autorizaci má za následek všechna připojení odkaz odstraňuje z předplatného, jejichž přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Chcete-li vytvořit povolení**

Vlastníka okruhu vytvoří povolení. Výsledkem je vytvoření autorizační klíč, které je možné se připojit své brány virtuální sítě k okruhu ExpressRoute uživatelem okruh. Povolení je platný pro jenom jedno připojení.

Následující rutiny fragment kódu ukazuje vytvoření povolení:

```powershell
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



**Chcete-li zkontrolovat autorizací**

Vlastníka okruhu můžete zkontrolovat všechny autorizací, které jsou vydány na konkrétní okruhu spuštěním následující rutiny:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Chcete-li přidat autorizací**

Vlastníka okruhu můžete přidat oprávnění pomocí následující rutiny:

```powershell
$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzureRmExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**K odstranění autorizací**

Vlastníka okruhu můžete odvolání nebo odstranění autorizací pro uživatele tak, že spustíte následující rutinu:

```powershell
Remove-AzureRmExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operace okruh uživatele

ID partnera a autorizační klíč z vlastníka okruhu, musí uživatel okruh. Autorizační klíč je identifikátor GUID.

ID partnera můžete zkontrolovat z následující příkaz:

```powershell
Get-AzureRmExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Pokud chcete uplatnit autorizace připojení**

Okruh může spouštět následující rutiny můžete uplatnit odkaz autorizace:

```powershell
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzureRmVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzureRmVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Chcete-li uvolnit autorizace připojení**

Povolení můžete vydat odstraněním připojení, který odkazuje okruhu ExpressRoute do virtuální sítě.

## <a name="modify-a-virtual-network-connection"></a>Upravit připojení virtuální sítě
Můžete aktualizovat některé vlastnosti připojení k virtuální síti. 

**Chcete-li aktualizovat váhy připojení**

Virtuální sítě může být připojen k více okruhů ExpressRoute. Zobrazí se stejnou předponou z více než jeden okruh ExpressRoute. Chcete-li zvolit, které připojení odesílat provoz určený pro tuto předponu, můžete změnit *RoutingWeight* připojení. Přenosy se odesílají na připojení s nejvyšší *RoutingWeight*.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzureRmVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Rozsah *RoutingWeight* je od 0 do 32 000. Výchozí hodnota je 0.

## <a name="next-steps"></a>Další postup
Další informace o ExpressRoute najdete v tématu [ExpressRoute – nejčastější dotazy](expressroute-faqs.md).
