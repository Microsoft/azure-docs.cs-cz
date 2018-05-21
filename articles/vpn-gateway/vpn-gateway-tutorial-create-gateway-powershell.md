---
title: Vytvoření a správa brány VPN Azure pomocí PowerShellu | Microsoft Docs
description: Kurz – Vytvoření a správa brány VPN pomocí modulu Azure PowerShell
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure
ms.date: 04/24/2018
ms.author: yushwang
ms.custom: mvc
ms.openlocfilehash: 867a1c55c0f96b17f77049d7f24d47f41a90d9f9
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="create-and-manage-vpn-gateway-with-the-azure-powershell-module"></a>Vytvoření a správa brány VPN pomocí modulu Azure PowerShell

Brány VPN Azure poskytují propojení různých míst mezi zákazníkem a Azure. Tento kurz se zabývá základními položkami nasazení brány VPN Azure, jako je vytvoření a správa brány VPN. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Změna velikosti brány VPN
> * Resetování brány VPN

Následující diagram ukazuje virtuální síť a bránu VPN vytvořené v rámci tohoto kurzu.

![Virtuální síť a brána VPN](./media/vpn-gateway-tutorial-create-gateway-powershell/vnet1-gateway.png)


[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Pokud se rozhodnete nainstalovat a používat PowerShell místně, musíte použít modul Azure PowerShell verze 5.3 nebo novější. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable AzureRM`. Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps). Pokud používáte PowerShell místně, je také potřeba spustit příkaz `Login-AzureRmAccount` pro vytvoření připojení k Azure. 

## <a name="common-network-parameter-values"></a>Společné hodnoty parametrů sítě

Upravte níže uvedené hodnoty na základě vašeho prostředí a nastavení sítě.

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

## <a name="create-resource-group"></a>Vytvoření skupiny prostředků

Vytvořte skupinu prostředků pomocí příkazu [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Skupinu prostředků je potřeba vytvořit jako první. V následujícím příkladu se vytvoří skupina prostředků *TestRG1* v oblasti *USA – východ*:

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

## <a name="create-a-virtual-network"></a>Vytvoření virtuální sítě

Brána VPN Azure poskytuje pro vaši virtuální síť propojení různých míst a funkce serveru VPN typu Point-to-Site. Přidejte bránu VPN do existující virtuální sítě nebo vytvořte novou virtuální síť a bránu. Tento příklad pomocí příkazů [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) a [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) vytvoří novou virtuální síť se třemi podsítěmi Frontend, Backend a GatewaySubnet:

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubnet1 -AddressPrefix $GwPrefix1
$vnet   = New-AzureRmVirtualNetwork `
            -Name $VNet1 `
            -ResourceGroupName $RG1 `
            -Location $Location1 `
            -AddressPrefix $VNet1Prefix `
            -Subnet $fesub1,$besub1,$gwsub1
```

## <a name="request-a-public-ip-address-for-the-vpn-gateway"></a>Vyžádání veřejné IP adresy pro bránu VPN

Brány VPN Azure komunikují s místními zařízeními VPN přes internet za účelem provádění vyjednávání protokolu IKE (Internet Key Exchange) a vytváření tunelů IPsec. Pomocí příkazů [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) a [New-AzureRmVirtualNetworkGatewayIpConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayipconfig) vytvořte a přiřaďte k vaší bráně VPN veřejnou IP adresu, jak je znázorněno v následujícím příkladu:

> [!IMPORTANT]
> V současné době můžete pro bránu použít pouze dynamickou veřejnou IP adresu. Statickou IP adresu brány VPN Azure nepodporují.

```azurepowershell-interactive
$gwpip    = New-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroupName $RG1 `
              -Location $Location1 -AllocationMethod Dynamic
$subnet   = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' `
              -VirtualNetwork $vnet
$gwipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 `
              -Subnet $subnet -PublicIpAddress $gwpip
```

## <a name="create-vpn-gateway"></a>Vytvoření brány VPN

Vytvoření brány VPN může trvat 45 minut nebo déle. Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí. Nebo můžete vytvořit propojení mezi vaší virtuální sítí a místním umístěním. Vytvořte bránu VPN pomocí rutiny [New-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/New-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroupName $RG1 `
  -Location $Location1 -IpConfigurations $gwipconf -GatewayType Vpn `
  -VpnType RouteBased -GatewaySku VpnGw1
```

Klíčové hodnoty parametrů:
* GatewayType: Pro připojení typu Site-to-Site a VNet-to-VNet použijte **Vpn**.
* VpnType: Pokud chcete komunikovat s širší škálou zařízení VPN a získat další funkce směrování, použijte **RouteBased**.
* GatewaySku: Výchozí hodnota je **VpnGw1**. Pokud potřebujete větší propustnost nebo více připojení, změňte ji na VpnGw2 nebo VpnGw3. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

Po dokončení vytváření brány můžete vytvořit propojení mezi vaší virtuální sítí a jinou virtuální sítí nebo propojení mezi vaší virtuální sítí a místním umístěním. Můžete také nakonfigurovat připojení typu Point-to-Site z klientského počítače k vaší virtuální síti.

## <a name="resize-vpn-gateway"></a>Změna velikosti brány VPN

Po vytvoření brány VPN můžete změnit její skladovou položku. Různé skladové položky brány podporují různé specifikace, jako je propustnost, počet připojení atd. Následující příklad pomocí příkazu [Resize-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Resize-AzureRmVirtualNetworkGateway) změní velikost vaší brány z VpnGw1 na VpnGw2. Další informace najdete v části [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Resize-AzureRmVirtualNetworkGateway -GatewaySku VpnGw2 -VirtualNetworkGateway $gateway
```

Změna velikosti brány VPN také trvá přibližně 30 až 45 minut, i když tato operace **nepřeruší** ani neodebere stávající připojení a konfigurace.

## <a name="reset-vpn-gateway"></a>Resetování brány VPN

V rámci postupu při řešení potíží můžete bránu VPN Azure resetovat a vynutit tak restartování konfigurací tunelů IPsec/IKE. K resetování brány použijte příkaz [Reset-AzureRmVirtualNetworkGateway](/powershell/module/azurerm.network/Reset-AzureRmVirtualNetworkGateway).

```azurepowershell-interactive
$gw = Get-AzureRmVirtualNetworkGateway -Name $Gw1 -ResourceGroup $RG1
Reset-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gateway
```

Další informace najdete v tématu [Resetování brány VPN](vpn-gateway-resetgw-classic.md).

## <a name="get-the-gateway-public-ip-address"></a>Získání veřejné IP adresy brány

Pokud znáte název veřejné IP adresy, můžete pomocí příkazu [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/Reset-AzureRmPublicIpAddress) zobrazit veřejnou IP adresu přiřazenou k bráně.

```azurepowershell-interactive
$myGwIp = Get-AzureRmPublicIpAddress -Name $GwIP1 -ResourceGroup $RG1
$myGwIp.IpAddress
```

## <a name="delete-vpn-gateway"></a>Odstranění brány VPN

Kompletní konfigurace propojení různých míst a připojení typu VNet-to-VNet vyžaduje kromě brány VPN i několik dalších typů prostředků. Před odstraněním samotné brány odstraňte připojení přidružená k příslušné bráně VPN. Po odstranění brány pak můžete odstranit veřejnou IP adresu (nebo adresy) pro danou bránu. Podrobné pokyny najdete v tématu [Odstranění brány VPN](vpn-gateway-delete-vnet-gateway-powershell.md).

Pokud je brána součástí nasazení prototypu nebo testování konceptu, můžete k odebrání skupiny prostředků, brány VPN a všech souvisejících prostředků použít příkaz [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup).

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $RG1
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o základních úkolech při vytváření a správě brány VPN, jako jsou:

> [!div class="checklist"]
> * Vytvoření brány VPN
> * Změna velikosti brány VPN
> * Resetování brány VPN

V následujících kurzech se seznámíte s připojeními typu Site-to-Site, VNet-to-VNet a Point-to-Site.

> [!div class="nextstepaction"]
> * [Vytváření připojení typu Site-to-Site](vpn-gateway-tutorial-vpnconnection-powershell.md)
> * [Vytváření připojení typu VNet-to-VNet](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Vytváření připojení typu Point-to-Site](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
