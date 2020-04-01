---
title: Kurz – vytvoření a správa brány pomocí brány Azure VPN Gateway
description: Kurz – Vytvoření a správa brány VPN pomocí modulu Azure PowerShell
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: cherylmc
ms.openlocfilehash: 66efa0f2922e70908616c7c447d782efee8f6b1b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79137173"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Kurz: Vytvoření a správa brány VPN pomocí PowerShellu

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazit veřejnou IP adresu
> * Změna velikosti brány VPN
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

![Virtuální síť a brána VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="working-with-azure-cloud-shell-and-azure-powershell"></a>Spolupráce s Azure Cloud Shell a Azure PowerShellem

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Společné hodnoty parametrů sítě

Níže jsou hodnoty parametrů použité pro tento kurz. V příkladech se proměnné překládají na následující:

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

Změňte níže uvedené hodnoty na základě nastavení prostředí a sítě a potom zkopírujte a vložte a nastavte proměnné pro tento kurz. Pokud vám časový čas relace prostředí Cloud nebo potřebujete použít jiné okno PowerShellu, zkopírujte a vložte proměnné do nové relace a pokračujte v kurzu.

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

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupinu prostředků je potřeba vytvořit jako první. V následujícím příkladu se vytvoří skupina prostředků *TestRG1* v oblasti *USA – východ*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Brána VPN Azure poskytuje pro vaši virtuální síť propojení různých míst a funkce serveru VPN typu Point-to-Site. Přidejte bránu VPN do existující virtuální sítě nebo vytvořte novou virtuální síť a bránu. Všimněte si, že příklad určuje název podsítě brány konkrétně. Vždy musíte zadat název podsítě brány jako "GatewaySubnet", aby správně fungovala. Tento příklad vytvoří novou virtuální síť se třemi podsítěmi: Frontend, Backend a GatewaySubnet pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

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

Brány VPN Azure komunikují s místními zařízeními VPN přes internet za účelem provádění vyjednávání protokolu IKE (Internet Key Exchange) a vytváření tunelů IPsec. Vytvořte a přiřaďte veřejnou IP adresu k bráně VPN, jak je znázorněno v příkladu níže s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) a [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

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

## <a name="create-a-vpn-gateway"></a>Vytvoření brány VPN

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo můžete vytvořit propojení mezi vaší virtuální sítí a místním umístěním. Vytvořte bránu VPN pomocí rutiny [New-AzVirtualNetworkGateway.](/powershell/module/az.network/New-azVirtualNetworkGateway)

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Klíčové hodnoty parametrů:
* GatewayType: Pro připojení typu Site-to-Site a VNet-to-VNet použijte **Vpn**.
* VpnType: Pokud chcete komunikovat s širší škálou zařízení VPN a získat další funkce směrování, použijte **RouteBased**.
* GatewaySku: **VpnGw1** je výchozí; změnit na jinou VpnGw SKU, pokud potřebujete vyšší propustnosti nebo více připojení. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Pokud používáte TryIt, může časový čas relace. To je OK. Brána bude stále vytvářet.

Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí nebo propojení mezi vaší virtuální sítí a místním umístěním. Můžete také nakonfigurovat připojení typu Point-to-Site z klientského počítače k vaší virtuální síti.

## <a name="view-the-gateway-public-ip-address"></a>Zobrazení veřejné IP adresy brány

Pokud znáte název veřejné IP adresy, zobrazte veřejnou IP adresu přiřazenou bráně pomocí [služby Get-AzPublicIpAddress.](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress)

Pokud časový rozsah relace, zkopírujte společné parametry sítě od začátku tohoto kurzu do nové relace a pokračujte a pokračujte.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Změna velikosti brány

Po vytvoření brány VPN můžete změnit její skladovou položku. Různé brány SKU podporují různé specifikace, jako jsou propustnosti, počet připojení atd. Následující příklad používá [Resize-AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) pro změny velikosti brány z VpnGw1 na VpnGw2. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Změna velikosti brány VPN také trvá přibližně 30 až 45 minut, i když tato operace **nepřeruší** ani neodebere stávající připojení a konfigurace.

## <a name="reset-a-gateway"></a>Obnovení brány

V rámci postupu při řešení potíží můžete bránu VPN Azure resetovat a vynutit tak restartování konfigurací tunelů IPsec/IKE. K obnovení brány použijte [reset-AzVirtualNetworkGateway.](/powershell/module/az.network/Reset-azVirtualNetworkGateway)

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Další informace najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud přecházení na [další kurz](vpn-gateway-tutorial-vpnconnection-powershell.md), budete chtít zachovat tyto prostředky, protože jsou předpoklady.

Pokud je však brána součástí prototypu, testu nebo testování konceptu nasazení, můžete pomocí příkazu [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) odebrat skupinu prostředků, bránu VPN a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě brány VPN, jako jsou:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazit veřejnou IP adresu
> * Změna velikosti brány VPN
> * Resetování brány VPN

V následujících kurzech se seznámíte s připojeními typu Site-to-Site, VNet-to-VNet a Point-to-Site.

> [!div class="nextstepaction"]
> * [Vytváření připojení typu Site-to-Site](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Vytváření připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Vytváření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
