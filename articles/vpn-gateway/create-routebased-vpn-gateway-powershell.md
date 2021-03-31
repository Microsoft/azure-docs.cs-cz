---
title: 'Azure VPN Gateway: Vytvoření brány založené na trasách: PowerShell'
description: Pomocí PowerShellu můžete rychle vytvořit bránu Azure VPN Gateway založenou na směrování pro připojení VPN k místní síti nebo k propojení virtuálních sítí.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 8b6ab5dcbd38925afbd98381e427426d27110a53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89393509"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Vytvoření brány sítě VPN založené na trasách pomocí prostředí PowerShell

Tento článek vám pomůže rychle vytvořit bránu Azure VPN Gateway založenou na trasách pomocí prostředí PowerShell. Brána sítě VPN se používá při vytváření připojení VPN k místní síti. K připojení virtuální sítě můžete použít taky bránu VPN.

## <a name="before-you-begin"></a>Než začnete

Kroky v tomto článku vytvoří virtuální síť, podsíť, podsíť brány a bránu VPN založenou na směrování (bránu virtuální sítě). Po dokončení vytváření brány můžete vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

### <a name="working-with-azure-powershell"></a>Práce s Azure PowerShell

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Vytvořte skupinu prostředků. Pokud používáte PowerShell místně, otevřete konzolu PowerShellu se zvýšenými oprávněními a připojte se k Azure pomocí `Connect-AzAccount` příkazu.

```azurepowershell-interactive
New-AzResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem **VNet1** v umístění **EastUS** :

```azurepowershell-interactive
$virtualNetwork = New-AzVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Vytvořte konfiguraci podsítě pomocí rutiny [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) .

```azurepowershell-interactive
$subnetConfig = Add-AzVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Nastavte konfiguraci podsítě pro virtuální síť pomocí rutiny [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .


```azurepowershell-interactive
$virtualNetwork | Set-AzVirtualNetwork
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Přidání podsítě brány

Podsíť brány obsahuje rezervované IP adresy, které používají služby brány virtuální sítě. K přidání podsítě brány použijte následující příklady:

Nastavte proměnnou pro virtuální síť.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Vytvořte podsíť brány pomocí rutiny [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/Add-azVirtualNetworkSubnetConfig) .

```azurepowershell-interactive
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Nastavte konfiguraci podsítě pro virtuální síť pomocí rutiny [set-AzVirtualNetwork](/powershell/module/az.network/Set-azVirtualNetwork) .

```azurepowershell-interactive
$vnet | Set-AzVirtualNetwork
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Vyžádání veřejné IP adresy

Brána sítě VPN musí mít dynamicky přidělenou veřejnou IP adresu. Při vytváření připojení k bráně sítě VPN se jedná o IP adresu, kterou zadáte. Pro vyžádání veřejné IP adresy použijte následující příklad:

```azurepowershell-interactive
$gwpip= New-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="create-the-gateway-ip-address-configuration"></a><a name="GatewayIPConfig"></a>Vytvoření konfigurace IP adresy brány

Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následujícího příkladu vytvořte vlastní konfiguraci brány:

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Vytvoření brány sítě VPN

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení brány můžete vytvořit připojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi vaší virtuální sítí a místním umístěním. Pomocí rutiny [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) vytvořte bránu VPN.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Zobrazit bránu VPN

Bránu VPN můžete zobrazit pomocí rutiny [Get-AzVirtualNetworkGateway](/powershell/module/az.network/Get-azVirtualNetworkGateway) .

```azurepowershell-interactive
Get-AzVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Výstup bude vypadat podobně jako v tomto příkladu:

```
Name                   : VNet1GW
ResourceGroupName      : TestRG1
Location               : eastus
Id                     : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provide
                         rs/Microsoft.Network/virtualNetworkGateways/VNet1GW
Etag                   : W/"0952d-9da8-4d7d-a8ed-28c8ca0413"
ResourceGuid           : dc6ce1de-2c4494-9d0b-20b03ac595
ProvisioningState      : Succeeded
Tags                   :
IpConfigurations       : [
                           {
                             "PrivateIpAllocationMethod": "Dynamic",
                             "Subnet": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet"
                             },
                             "PublicIpAddress": {
                               "Id": "/subscriptions/<subscription ID>/resourceGroups/Te
                         stRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP"
                             },
                             "Name": "default",
                             "Etag": "W/\"0952d-9da8-4d7d-a8ed-28c8ca0413\"",
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                         RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/de
                         fault"
                           }
                         ]
GatewayType            : Vpn
VpnType                : RouteBased
EnableBgp              : False
ActiveActive           : False
GatewayDefaultSite     : null
Sku                    : {
                           "Capacity": 2,
                           "Name": "VpnGw1",
                           "Tier": "VpnGw1"
                         }
VpnClientConfiguration : null
BgpSettings            : {
     
```

## <a name="view-the-public-ip-address"></a><a name="viewgwpip"></a>Zobrazení veřejné IP adresy

Veřejnou IP adresu pro bránu VPN zobrazíte pomocí rutiny [Get-AzPublicIpAddress](/powershell/module/az.network/Get-azPublicIpAddress) .

```azurepowershell-interactive
Get-AzPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

V příkladu odpovědi je hodnota IpAddress veřejná IP adresa.

```
Name                     : VNet1GWIP
ResourceGroupName        : TestRG1
Location                 : eastus
Id                       : /subscriptions/<subscription ID>/resourceGroups/TestRG1/provi
                           ders/Microsoft.Network/publicIPAddresses/VNet1GWIP
Etag                     : W/"5001666a-bc2a-484b-bcf5-ad488dabd8ca"
ResourceGuid             : 3c7c481e-9828-4dae-abdc-f95b383
ProvisioningState        : Succeeded
Tags                     :
PublicIpAllocationMethod : Dynamic
IpAddress                : 13.90.153.3
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                             "Id": "/subscriptions/<subscription ID>/resourceGroups/Test
                           RG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/
                           default"
                           }
DnsSettings              : null
Zones                    : {}
Sku                      : {
                             "Name": "Basic"
                           }
IpTags                   : {}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky, které jste vytvořili, odstraňte skupinu prostředků pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) . Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Další kroky

Po vytvoření brány můžete vytvořit připojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo vytvořte připojení mezi vaší virtuální sítí a místním umístěním.

> [!div class="nextstepaction"]
> [Vytvoření připojení typu Site-to-Site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Vytvoření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)
