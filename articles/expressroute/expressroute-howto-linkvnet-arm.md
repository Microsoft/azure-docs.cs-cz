---
title: 'Kurz: propojení virtuální sítě s okruhem ExpressRoute-Azure PowerShell'
description: Tento kurz poskytuje přehled, jak propojit virtuální sítě (virtuální sítě) se ExpressRoute okruhy pomocí modelu nasazení Správce prostředků a Azure PowerShell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/06/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 69067ca34b231f1b14f8cc854288c3ed4c4ac82a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91855986"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit"></a>Kurz: připojení virtuální sítě k okruhu ExpressRoute
> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure CLI](howto-linkvnet-cli.md)
> * [Video – Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (Classic)](expressroute-howto-linkvnet-classic.md)
>

Tento článek vám pomůže propojit virtuální sítě (virtuální sítě) se okruhy Azure ExpressRoute pomocí modelu nasazení Správce prostředků a prostředí PowerShell. Virtuální sítě mohou být buď ve stejném předplatném, nebo v rámci jiného předplatného. Tento článek také ukazuje, jak aktualizovat odkaz virtuální sítě.

* Na standardní okruh ExpressRoute můžete propojit až 10 virtuálních sítí. Pokud používáte standardní okruh ExpressRoute, musí být všechny virtuální sítě ve stejné geopolitické oblasti. 

* Jedna virtuální síť může být propojená s až čtyřmi okruhy ExpressRoute. Pomocí kroků v tomto článku vytvoříte nový objekt připojení pro každý okruh ExpressRoute, ke kterému se připojujete. Okruhy ExpressRoute můžou být ve stejném předplatném, různých předplatných nebo kombinaci obou.

* Pokud povolíte doplněk ExpressRoute Premium, můžete propojit virtuální sítě mimo geopolitickou oblast okruhu ExpressRoute. Doplněk Premium vám také umožní připojit více než 10 virtuálních sítí k okruhu ExpressRoute v závislosti na zvolené šířce pásma. Další podrobnosti o doplňku Premium najdete v [nejčastějších dotazech](expressroute-faqs.md) .

V tomto kurzu se naučíte:
> [!div class="checklist"]
> - Připojení virtuální sítě ve stejném předplatném k okruhu
> - Připojení virtuální sítě jiného předplatného k okruhu
> - Úprava připojení k virtuální síti
> - Konfigurace ExpressRoute FastPath

## <a name="prerequisites"></a>Předpoklady

* Než začnete s konfigurací, Projděte si [požadavky,](expressroute-prerequisites.md) [požadavky na směrování](expressroute-routing.md)a [pracovní postupy](expressroute-workflows.md) .

* Musí mít aktivní okruh ExpressRoute. 
  * Podle pokynů [vytvořte okruh ExpressRoute](expressroute-howto-circuit-arm.md) a pořiďte okruh povolený vaším poskytovatelem připojení. 
  * Ujistěte se, že máte pro váš okruh nakonfigurovaný privátní partnerský vztah Azure. Pokyny k směrování najdete v článku věnovaném [konfiguraci směrování](expressroute-howto-routing-arm.md) . 
  * Zajistěte, aby se nakonfiguroval privátní partnerský vztah Azure a aby se zajistilo vytvoření partnerského vztahu protokolu BGP mezi vaší sítí a Microsoftem pro koncové připojení.
  * Ujistěte se, že máte virtuální síť a že byla vytvořena a plně zřízena Brána virtuální sítě. Postupujte podle pokynů a [vytvořte bránu virtuální sítě pro ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brána virtuální sítě pro ExpressRoute používá GatewayType "ExpressRoute", ne VPN.

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Připojení virtuální sítě ve stejném předplatném k okruhu
Bránu virtuální sítě můžete připojit k okruhu ExpressRoute pomocí následující rutiny. Ujistěte se, že je brána virtuální sítě vytvořená a je připravená k propojení před spuštěním rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "MyRG" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute
```

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Připojení virtuální sítě jiného předplatného k okruhu
Okruh ExpressRoute můžete sdílet mezi několika předplatnými. Následující obrázek ukazuje jednoduché schéma, jak sdílení funguje pro okruhy ExpressRoute napříč několika předplatnými.

Každý z menších cloudů ve velkém cloudu se používá k reprezentaci předplatných, která patří do různých oddělení v rámci organizace. Každé oddělení v organizaci používá vlastní předplatné pro nasazení svých služeb – ale může sdílet jeden okruh ExpressRoute pro připojení zpět k místní síti. Jeden oddělení (v tomto příkladu: IT) může vlastnit okruh ExpressRoute. Další předplatná v rámci organizace můžou používat okruh ExpressRoute.

> [!NOTE]
> Pro vlastníka předplatného se použijí poplatky za připojení a šířku pásma pro okruh ExpressRoute. Všechny virtuální sítě mají stejnou šířku pásma.
> 

:::image type="content" source="./media/expressroute-howto-linkvnet-classic/cross-subscription.png" alt-text="Připojení mezi předplatnými":::

### <a name="administration---circuit-owners-and-circuit-users"></a>Správa – vlastníci okruhu a uživatelé okruhu

' Okruh Owner ' je autorizovaným uživatelem prostředku okruhu ExpressRoute. Vlastník okruhu může vytvořit autorizaci, které může uplatnit "okruh uživatelů". Uživatelé okruhu jsou vlastníky bran virtuální sítě, které nejsou v rámci stejného předplatného jako okruh ExpressRoute. Uživatelé okruhu můžou uplatnit autorizaci (jednu autorizaci na virtuální síť).

Vlastník okruhu má kdykoliv oprávnění upravovat a odvolat. Odvolání výsledků autorizace ve všech připojeních propojení, která se odstraňují z předplatného, jehož přístup byl odvolán.

### <a name="circuit-owner-operations"></a>Operace vlastníka okruhu

**Vytvoření autorizace**

Vlastník okruhu vytvoří autorizaci, která vytvoří autorizační klíč, který bude používat uživatel okruhu k připojení bran virtuální sítě k okruhu ExpressRoute. Autorizace je platná jenom pro jedno připojení.

Následující fragment rutiny ukazuje, jak vytvořit autorizaci:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$auth1 = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization1"
```

Odpověď na předchozí příkazy bude obsahovat autorizační klíč a stav:

```azurepowershell
Name                   : MyAuthorization1
Id                     : /subscriptions/&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/CrossSubTest/authorizations/MyAuthorization1
Etag                   : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&& 
AuthorizationKey       : ####################################
AuthorizationUseStatus : Available
ProvisioningState      : Succeeded
```

**Kontrola autorizací**

Vlastník okruhu může zkontrolovat všechna oprávnění vydaná určitým okruhem spuštěním následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Přidání autorizací**

Vlastník okruhu může přidat autorizaci pomocí následující rutiny:

```azurepowershell-interactive
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit -Name "MyAuthorization2"
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit

$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
$authorizations = Get-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $circuit
```

**Odstranění autorizací**

Vlastník okruhu může pro uživatele odvolat nebo odstranit autorizaci spuštěním následující rutiny:

```azurepowershell-interactive
Remove-AzExpressRouteCircuitAuthorization -Name "MyAuthorization2" -ExpressRouteCircuit $circuit
Set-AzExpressRouteCircuit -ExpressRouteCircuit $circuit
```    

### <a name="circuit-user-operations"></a>Operace uživatele obvodu

Uživatel okruhu potřebuje partnerského ID a autorizační klíč od vlastníka okruhu. Autorizační klíč je identifikátor GUID.

ID partnerského zařízení lze zkontrolovat pomocí následujícího příkazu:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG"
```

**Uplatnění autorizace připojení**

Uživatel okruhu může spustit následující rutinu pro uplatnění autorizace propojení:

```azurepowershell-interactive
$id = "/subscriptions/********************************/resourceGroups/ERCrossSubTestRG/providers/Microsoft.Network/expressRouteCircuits/MyCircuit"    
$gw = Get-AzVirtualNetworkGateway -Name "ExpressRouteGw" -ResourceGroupName "MyRG"
$connection = New-AzVirtualNetworkGatewayConnection -Name "ERConnection" -ResourceGroupName "RemoteResourceGroup" -Location "East US" -VirtualNetworkGateway1 $gw -PeerId $id -ConnectionType ExpressRoute -AuthorizationKey "^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^"
```

**Uvolnění autorizace připojení**

Autorizaci můžete uvolnit odstraněním připojení, které propojuje okruh ExpressRoute s virtuální sítí.

## <a name="modify-a-virtual-network-connection"></a>Úprava připojení k virtuální síti
Můžete aktualizovat některé vlastnosti připojení k virtuální síti. 

**Aktualizace váhy připojení**

Vaše virtuální síť se může připojit k několika okruhům ExpressRoute. Můžete obdržet stejnou předponu z více než jednoho okruhu ExpressRoute. Pokud chcete zvolit připojení pro odesílání provozu určené pro tuto předponu, můžete změnit *RoutingWeight* připojení. Provoz se pošle na připojení s nejvyšší *RoutingWeight*.

```azurepowershell-interactive
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyVirtualNetworkConnection" -ResourceGroupName "MyRG"
$connection.RoutingWeight = 100
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
```

Rozsah *RoutingWeight* je 0 až 32000. Výchozí hodnota je 0.

## <a name="configure-expressroute-fastpath"></a>Konfigurace ExpressRoute FastPath 
Můžete povolit [ExpressRoute FastPath](expressroute-about-virtual-network-gateways.md) , pokud je vaše brána virtuální sítě v ultra Performance nebo ErGw3AZ. FastPath vylepšuje výkon cesty k datům, jako jsou pakety za sekundu, a připojení za sekundu mezi vaší místní sítí a virtuální sítí. 

**Konfigurace FastPath pro nové připojení**

```azurepowershell-interactive 
$circuit = Get-AzExpressRouteCircuit -Name "MyCircuit" -ResourceGroupName "MyRG" 
$gw = Get-AzVirtualNetworkGateway -Name "MyGateway" -ResourceGroupName "MyRG" 
$connection = New-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" -ExpressRouteGatewayBypass -VirtualNetworkGateway1 $gw -PeerId $circuit.Id -ConnectionType ExpressRoute -Location "MyLocation" 
``` 

**Aktualizace existujícího připojení pro povolení FastPath**

```azurepowershell-interactive 
$connection = Get-AzVirtualNetworkGatewayConnection -Name "MyConnection" -ResourceGroupName "MyRG" 
$connection.ExpressRouteGatewayBypass = $True
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection
``` 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete připojení ExpressRoute, z předplatného, ve kterém je brána umístěná, použijte `Remove-AzVirtualNetworkGatewayConnection` příkaz k odebrání propojení mezi bránou a okruhem.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayConnection "MyConnection" -ResourceGroupName "MyRG"
```

## <a name="next-steps"></a>Další kroky
Další informace o ExpressRoute najdete v tématu ExpressRoute – Nejčastější dotazy.

> [!div class="nextstepaction"]
> [ExpressRoute – nejčastější dotazy](expressroute-faqs.md)
