---
title: Tutorial - Create and manage a gateway using Azure VPN Gateway
description: Kurz – Vytvoření a správa brány VPN pomocí modulu Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: b144a70ee88138966d9cc38a56e1cff1e63fca1b
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/23/2019
ms.locfileid: "74424143"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Tutorial: Create and manage a VPN gateway using PowerShell

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvořit bránu VPN
> * View the public IP address
> * Změna velikosti brány VPN
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

![Virtuální síť a brána VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Společné hodnoty parametrů sítě

Below are the parameter values used for this tutorial. In the examples, the variables translate to the following:

```
#$RG1         = The name of the resource group
#$VNet1       = The name of the virtual network
#$Location1   = The location region
#$FESubnet1   = The name of the first subnet
#$BESubnet1   = The name of the second subnet
#$VNet1Prefix = The address range for the virtual network
#$FEPrefix1   = Addresses for the first subnet
#$BEPrefix1   = Addresses for the second subnet
#$GwPrefix1   = Addresses for the GatewaySubnet
#$VNet1ASN    = ASN for the virtual network
#$DNS1        = The IP address of the DNS server you want to use for name resolution
#$Gw1         = The name of the virtual network gateway
#$GwIP1       = The public IP address for the virtual network gateway
#$GwIPConf1   = The name of the IP configuration
```

Change the values below based on your environment and network setup, then copy and paste to set the variables for this tutorial. If your Cloud Shell session times out, or you need to use a different PowerShell window, copy and paste the variables to your new session and continue the tutorial.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$VNet1ASN    = 65010
$DNS1        = "8.8.8.8"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Create a resource group with the [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) command. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupinu prostředků je potřeba vytvořit jako první. V následujícím příkladu se vytvoří skupina prostředků *TestRG1* v oblasti *USA – východ*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Brána VPN Azure poskytuje pro vaši virtuální síť propojení různých míst a funkce serveru VPN typu Point-to-Site. Přidejte bránu VPN do existující virtuální sítě nebo vytvořte novou virtuální síť a bránu. Notice that the example specifies the name of the gateway subnet specifically. You must always specify the name of the gateway subnet as "GatewaySubnet" in order for it to function properly. This example creates a new virtual network with three subnets: Frontend, Backend, and GatewaySubnet using [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) and [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Vyžádání veřejné IP adresy pro bránu VPN

Brány VPN Azure komunikují s místními zařízeními VPN přes internet za účelem provádění vyjednávání protokolu IKE (Internet Key Exchange) a vytváření tunelů IPsec. Create and assign a public IP address to your VPN gateway as shown in the example below with [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) and [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

> [!IMPORTANT]
> V současné době můžete pro bránu použít pouze dynamickou veřejnou IP adresu. Statickou IP adresu brány VPN Azure nepodporují.

```azurepowershell-interactive
$gwpip    = New-AzPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-a-vpn-gateway"></a>Vytvořit bránu VPN

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo můžete vytvořit propojení mezi vaší virtuální sítí a místním umístěním. Create a VPN gateway using the [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) cmdlet.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Klíčové hodnoty parametrů:
* GatewayType: Pro připojení typu Site-to-Site a VNet-to-VNet použijte **Vpn**.
* VpnType: Pokud chcete komunikovat s širší škálou zařízení VPN a získat další funkce směrování, použijte **RouteBased**.
* GatewaySku: **VpnGw1** is the default; change it to another VpnGw SKU if you need higher throughputs or more connections. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

If you are using the TryIt, your session may time out. That's OK. The gateway will still create.

Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí nebo propojení mezi vaší virtuální sítí a místním umístěním. Můžete také nakonfigurovat připojení typu Point-to-Site z klientského počítače k vaší virtuální síti.

## <a name="view-the-gateway-public-ip-address"></a>View the gateway public IP address

If you know the name of the public IP address, use [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) to show the public IP address assigned to the gateway.

If your session timed out, copy the common network parameters from the beginning of this tutorial into your new session and proceed, then proceed.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Resize a gateway

Po vytvoření brány VPN můžete změnit její skladovou položku. Different gateway SKUs support different specifications such as throughputs, number of connections, etc. The following example uses [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) to resize your gateway from VpnGw1 to VpnGw2. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Změna velikosti brány VPN také trvá přibližně 30 až 45 minut, i když tato operace **nepřeruší** ani neodebere stávající připojení a konfigurace.

## <a name="reset-a-gateway"></a>Reset a gateway

V rámci postupu při řešení potíží můžete bránu VPN Azure resetovat a vynutit tak restartování konfigurací tunelů IPsec/IKE. Use [Reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) to reset your gateway.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Další informace najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

If you're advancing to the [next tutorial](vpn-gateway-tutorial-vpnconnection-powershell.md), you will want to keep these resources because they are the prerequisites.

However, if the gateway is part of a prototype, test, or proof-of-concept deployment, you can use the [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) command to remove the resource group, the VPN gateway, and all related resources.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě brány VPN, jako jsou:

> [!div class="checklist"]
> * Vytvořit bránu VPN
> * View the public IP address
> * Změna velikosti brány VPN
> * Resetování brány VPN

V následujících kurzech se seznámíte s připojeními typu Site-to-Site, VNet-to-VNet a Point-to-Site.

> [!div class="nextstepaction"]
> * [Vytváření připojení typu Site-to-Site](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Vytváření připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Vytváření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
