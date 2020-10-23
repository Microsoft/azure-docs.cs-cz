---
title: Kurz – vytvoření a správa brány pomocí Azure VPN Gateway
description: V tomto kurzu se naučíte, jak pomocí PowerShellu vytvořit, nasadit a spravovat VPN Gateway Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/13/2020
ms.author: cherylmc
ms.openlocfilehash: 91004b9cb545275746f75dbd6ad46981fe4b04d5
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92461154"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Kurz: vytvoření a Správa služby VPN Gateway pomocí PowerShellu

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazení veřejné IP adresy
> * Změna velikosti brány VPN
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

:::image type="content" source="./media/vpn-gateway-tutorial-create-gateway-powershell/gateway-diagram.png" alt-text="Diagram virtuální sítě a brány VPN":::

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Společné hodnoty parametrů sítě

Níže jsou uvedené hodnoty parametrů používané v tomto kurzu. V příkladech se proměnné převádějí na následující:

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

Změňte hodnoty níže na základě vašeho prostředí a nastavení sítě a potom zkopírujte a vložte, abyste nastavili proměnné pro tento kurz. Pokud vypršel časový limit relace Cloud Shell nebo potřebujete použít jiné okno prostředí PowerShell, zkopírujte a vložte proměnné do nové relace a pokračujte v tomto kurzu.

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

Vytvořte skupinu prostředků pomocí příkazu [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupinu prostředků je potřeba vytvořit jako první. V následujícím příkladu se vytvoří skupina prostředků *TestRG1* v oblasti *USA – východ*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Brána VPN Azure poskytuje pro vaši virtuální síť propojení různých míst a funkce serveru VPN typu Point-to-Site. Přidejte bránu VPN do existující virtuální sítě nebo vytvořte novou virtuální síť a bránu. Všimněte si, že příklad určuje konkrétně název podsítě brány. Je-li třeba, aby správně fungovala, je nutné zadat název podsítě brány jako "GatewaySubnet". Tento příklad vytvoří novou virtuální síť se třemi podsítěmi: front-end, back-end a GatewaySubnet pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

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

Brány VPN Azure komunikují s místními zařízeními VPN přes internet za účelem provádění vyjednávání protokolu IKE (Internet Key Exchange) a vytváření tunelů IPsec. Vytvořte a přiřaďte k bráně VPN veřejnou IP adresu, jak je znázorněno v následujícím příkladu s [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) a [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

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

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo můžete vytvořit propojení mezi vaší virtuální sítí a místním umístěním. Pomocí rutiny [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) vytvořte bránu VPN.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Klíčové hodnoty parametrů:
* GatewayType: Pro připojení typu Site-to-Site a VNet-to-VNet použijte **Vpn**.
* VpnType: Pokud chcete komunikovat s širší škálou zařízení VPN a získat další funkce směrování, použijte **RouteBased**.
* GatewaySku: **VpnGw1** je výchozí hodnota. Pokud potřebujete vyšší propustnost nebo více připojení, změňte ji na jinou SKU VpnGw. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Pokud používáte TryIt, vaše relace může vyprší časový limit. To je OK. Brána se pořád vytvoří.

Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí nebo propojení mezi vaší virtuální sítí a místním umístěním. Můžete také nakonfigurovat připojení typu Point-to-Site z klientského počítače k vaší virtuální síti.

## <a name="view-the-gateway-public-ip-address"></a>Zobrazení veřejné IP adresy brány

Pokud znáte název veřejné IP adresy, použijte [příkaz Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress) k zobrazení veřejné IP adresy přiřazené k bráně.

Pokud vypršel časový limit relace, zkopírujte do nové relace společné síťové parametry od začátku tohoto kurzu a pokračujte a pokračujte.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Změna velikosti brány

Po vytvoření brány VPN můžete změnit její skladovou položku. Různé skladové položky brány podporují různé specifikace, jako jsou propustnosti, počet připojení atd. Následující příklad používá [změnu velikosti – AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) pro změnu velikosti brány z VpnGw1 na VpnGw2. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Změna velikosti brány VPN také trvá přibližně 30 až 45 minut, i když tato operace **nepřeruší** ani neodebere stávající připojení a konfigurace.

## <a name="reset-a-gateway"></a>Resetování brány

V rámci postupu při řešení potíží můžete bránu VPN Azure resetovat a vynutit tak restartování konfigurací tunelů IPsec/IKE. K resetování brány použijte [reset-AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) .

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Další informace najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud se chystáte o [Další kurz](vpn-gateway-tutorial-vpnconnection-powershell.md), budete chtít tyto prostředky zachovat, protože se jedná o požadavky.

Pokud je ale brána součástí nasazení prototypu, testu nebo testování konceptu, můžete k odebrání skupiny prostředků, brány VPN a všech souvisejících prostředků použít příkaz [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) .

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě brány VPN, jako jsou:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazení veřejné IP adresy
> * Změna velikosti brány VPN
> * Resetování brány VPN

Pak pokračujte v tomto kurzu:

> [!div class="nextstepaction"]
> * [Vytvoření připojení S2S](vpn-gateway-create-site-to-site-rm-powershell.md)
