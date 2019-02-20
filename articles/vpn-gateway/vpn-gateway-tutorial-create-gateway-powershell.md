---
title: Vytvoření a správa brány VPN Azure pomocí PowerShellu | Microsoft Docs
description: Kurz – Vytvoření a správa brány VPN pomocí modulu Azure PowerShell
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: afe71953e9917ccf274742124d59cb790f15521b
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414129"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-powershell"></a>Kurz: Vytvoření a Správa služby VPN gateway pomocí Powershellu

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazení veřejné IP adresy
> * Změna velikosti brány VPN
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

![Virtuální síť a brána VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)

### <a name="azure-cloud-shell-and-azure-powershell"></a>Azure Cloud Shell a Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="common-network-parameter-values"></a>Společné hodnoty parametrů sítě

Změňte podle vašeho prostředí a síťových nastavení, pak kopírování a vkládání k nastavení proměnných pro účely tohoto kurzu níže uvedené hodnoty. Pokud vyprší časový limit vaší relace Cloud Shellu, nebo budete muset použít jiné okno prostředí PowerShell, kopírování a vložení proměnné do nové relace a pokračujte kurz.

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "East US"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
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

Vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupinu prostředků je potřeba vytvořit jako první. V následujícím příkladu se vytvoří skupina prostředků *TestRG1* v oblasti *USA – východ*:

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Brána VPN Azure poskytuje pro vaši virtuální síť propojení různých míst a funkce serveru VPN typu Point-to-Site. Přidejte bránu VPN do existující virtuální sítě nebo vytvořte novou virtuální síť a bránu. Tento příklad vytvoří novou virtuální síť se třemi podsítěmi: Front-endu, back-endu a podsíť brány pomocí [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork):

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Vyžádání veřejné IP adresy pro bránu VPN

Brány VPN Azure komunikují s místními zařízeními VPN přes internet za účelem provádění vyjednávání protokolu IKE (Internet Key Exchange) a vytváření tunelů IPsec. Vytvořit a přiřadit veřejné IP adresy k bráně VPN, jak je znázorněno v příkladu níže se [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) a [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig):

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

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo můžete vytvořit propojení mezi vaší virtuální sítí a místním umístěním. Vytvoření s použitím brány VPN [New-AzVirtualNetworkGateway](/powershell/module/az.network/New-azVirtualNetworkGateway) rutiny.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Klíčové hodnoty parametrů:
* GatewayType: Použití **Vpn** pro site-to-site a připojení VNet-to-VNet
* VpnType: Použití **RouteBased** komunikovat se zařízeními širší rozsah sítě VPN a další funkce směrování
* GatewaySku: **VpnGw1** je výchozí, změňte ho na VpnGw2 nebo VpnGw3, pokud potřebujete vyšší propustnost nebo další připojení. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Pokud používáte TryIt, může vaše relace vypršení časového limitu. To je OK. Brána bude přesto vytvořit.

Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí nebo propojení mezi vaší virtuální sítí a místním umístěním. Můžete také nakonfigurovat připojení typu Point-to-Site z klientského počítače k vaší virtuální síti.

## <a name="view-the-gateway-public-ip-address"></a>Zobrazení veřejné IP adresy brány

Pokud znáte název veřejné IP adresy, použijte [Get-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azurermps-6.8.1) zobrazíte veřejnou IP adresu přiřazenou bráně.

Pokud vypršel časový limit vaší relace, od začátku do nové relace v tomto kurzu zkopírujte společné parametry sítě a pokračovat a pokračovat.

```azurepowershell-interactive
$myGwIp = Get-AzPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="resize-a-gateway"></a>Změňte velikost brány

Po vytvoření brány VPN můžete změnit její skladovou položku. Různé skladové položky brány podporují různé specifikace, jako je propustnost, počet připojení atd. Následující příklad používá [změny velikosti AzVirtualNetworkGateway](/powershell/module/az.network/Resize-azVirtualNetworkGateway) ke změně velikosti brána z VpnGw1 VpnGw2. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Změna velikosti brány VPN také trvá přibližně 30 až 45 minut, i když tato operace **nepřeruší** ani neodebere stávající připojení a konfigurace.

## <a name="reset-a-gateway"></a>Resetování brány

V rámci postupu při řešení potíží můžete bránu VPN Azure resetovat a vynutit tak restartování konfigurací tunelů IPsec/IKE. Použití [resetování AzVirtualNetworkGateway](/powershell/module/az.network/Reset-azVirtualNetworkGateway) resetovat bránu.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Další informace najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud vám přechod [další kurz](vpn-gateway-tutorial-vpnconnection-powershell.md), budete chtít tyto prostředky zachovat, protože jde o kontrolu požadovaných součástí.

Nicméně pokud je brána součást prototypu, testovací nebo testování konceptu nasazení, můžete použít [odebrat AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) příkazu k odebrání skupiny prostředků, VPN gateway a všechny související prostředky.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě brány VPN, jako jsou:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Zobrazení veřejné IP adresy
> * Změna velikosti brány VPN
> * Resetování brány VPN

V následujících kurzech se seznámíte s připojeními typu Site-to-Site, VNet-to-VNet a Point-to-Site.

> [!div class="nextstepaction"]
> * [Vytváření připojení typu Site-to-Site](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Vytváření připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Vytváření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
