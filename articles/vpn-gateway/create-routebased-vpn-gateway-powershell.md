---
title: 'Vytvoření brány Azure VPN založené na směrování: Prostředí PowerShell | Dokumentace Microsoftu'
description: Rychle vytvořte založené na směrování VPN Gateway pomocí Powershellu
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/18/2018
ms.author: cherylmc
ms.openlocfilehash: 9460f184e3da6769048b30ca743169c5a6044bd0
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55505525"
---
# <a name="create-a-route-based-vpn-gateway-using-powershell"></a>Vytvoření trasové brány VPN pomocí Powershellu

Tento článek vám pomůže rychle vytvořit založené na směrování Azure VPN gateway pomocí Powershellu. Brány VPN se používá při vytváření připojení VPN k místní síti. Bránu sítě VPN můžete použít také k propojení virtuálních sítí.

Kroky v tomto článku se vytvoří virtuální síť, podsíť, podsítě brány a bránu VPN založenou na směrování (brány virtuální sítě). Po dokončení vytvoření brány můžete pak vytvořit připojení. Tyto kroky vyžadují předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.3.0 nebo novější. Verzi zjistíte spuštěním příkazu ` Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Connect-AzureRmAccount` pro vytvoření připojení k Azure.

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků Azure pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

```azurepowershell-interactive
New-AzureRmResourceGroup -Name TestRG1 -Location EastUS
```

## <a name="vnet"></a>Vytvoření virtuální sítě

Vytvořte virtuální síť pomocí rutiny [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). Následující příklad vytvoří virtuální síť s názvem **ze sítě VNet1** v **EastUS** umístění:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName TestRG1 `
  -Location EastUS `
  -Name VNet1 `
  -AddressPrefix 10.1.0.0/16
```

Vytvořte konfiguraci podsítě pomocí [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) rutiny.

```azurepowershell-interactive
$subnetConfig = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Frontend `
  -AddressPrefix 10.1.0.0/24 `
  -VirtualNetwork $virtualNetwork
```

Nastavit konfiguraci podsítě virtuální sítě s využitím [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) rutiny.


```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="gwsubnet"></a>Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. Chcete-li přidat podsíť brány použijte následující příklady:

Nastavte proměnnou pro svoji virtuální síť.

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG1 -Name VNet1
```

Vytvořte podsíť brány pomocí [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/Add-AzureRmVirtualNetworkSubnetConfig) rutiny.

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $vnet
```

Nastavit konfiguraci podsítě virtuální sítě s využitím [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork) rutiny.

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="PublicIP"></a>Vyžádejte si veřejnou IP adresu

Brána VPN musí mít dynamicky přidělené veřejné IP adresy. Při vytváření připojení k bráně VPN, toto je IP adresa, který zadáte. Vyžádejte si veřejnou IP adresu pomocí příkazu v následujícím příkladu:

```azurepowershell-interactive
$gwpip= New-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1 -Location 'East US' -AllocationMethod Dynamic
```

## <a name="GatewayIPConfig"></a>Vytvořit konfiguraci IP adresy brány

Konfigurace brány definuje podsíť a veřejnou IP adresu, která se bude používat. Podle následujícího příkladu vytvořte vlastní konfiguraci brány:

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -Name VNet1 -ResourceGroupName TestRG1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id
```
## <a name="CreateGateway"></a>Vytvoření brány VPN

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místního umístění. Vytvořte bránu VPN pomocí rutiny [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name VNet1GW -ResourceGroupName TestRG1 `
-Location 'East US' -IpConfigurations $gwipconfig -GatewayType Vpn `
-VpnType RouteBased -GatewaySku VpnGw1
```

## <a name="viewgw"></a>Zobrazení brány sítě VPN

Můžete zobrazit pomocí brány VPN [Get-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Get-AzureRmVirtualNetworkGateway) rutiny.

```azurepowershell-interactive
Get-AzureRmVirtualNetworkGateway -Name Vnet1GW -ResourceGroup TestRG1
```

Výstup bude vypadat nějak takto:

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

## <a name="viewgwpip"></a>Zobrazení veřejné IP adresy

Chcete-li zobrazit veřejnou IP adresu pro bránu VPN, použijte [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Get-AzureRmPublicIpAddress) rutiny.

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -Name VNet1GWIP -ResourceGroupName TestRG1
```

V příkladu odpovědi je hodnota IpAddress veřejnou IP adresu.

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

Pokud už vytvořené prostředky nepotřebujete, odstraňte jejich skupinu pomocí příkazu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Tím odstraníte skupinu prostředků a všechny prostředky, které obsahuje.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name TestRG1
```

## <a name="next-steps"></a>Další postup

Po dokončení vytváření brány můžete vytvořit připojení mezi virtuální sítí a jiné virtuální síti. Nebo vytvořte připojení mezi virtuální sítí a místního umístění.

> [!div class="nextstepaction"]
> [Vytvoření připojení site-to-site](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Vytvoření připojení point-to-site](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Vytvoření připojení k jiné virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md)
