---
title: Vytvoření zóny redundantní virtuální síťové brány v zónách dostupnosti Azure
description: Nasazení bran VPN a bran ExpressRoute v zónách dostupnosti
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77150166"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Vytvoření zóny redundantní virtuální síťové brány v zónách dostupnosti Azure

Brány VPN a ExpressRoute můžete nasadit v zónách dostupnosti Azure. To přináší odolnost proti chybám, škálovatelnost a vyšší dostupnost bran virtuálních sítí. Nasazování bran v rámci Zón dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, přičemž zároveň chrání připojení vaší místní sítě k Azure před výpadky na úrovni zóny. Další informace najdete [v tématech O zónově redundantních privátních síťových branách](about-zone-redundant-vnet-gateways.md) a [o zónách dostupnosti Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="1-declare-your-variables"></a><a name="variables"></a>1. Deklarujte své proměnné

Deklarujte proměnné, které chcete použít. Použijte následující příklad a dle potřeby nahraďte v něm uvedené hodnoty vlastními. Pokud během cvičení kdykoli zavřete relaci prostředí PowerShell/Cloud Shell, stačí znovu zkopírovat a vložit hodnoty, abyste proměnné znovu deklarovali. Při zadávání umístění ověřte, zda je podporovaná zadaná oblast. Další informace naleznete v [nejčastějších dotazech](#faq).

```azurepowershell-interactive
$RG1         = "TestRG1"
$VNet1       = "VNet1"
$Location1   = "CentralUS"
$FESubnet1   = "FrontEnd"
$BESubnet1   = "Backend"
$GwSubnet1   = "GatewaySubnet"
$VNet1Prefix = "10.1.0.0/16"
$FEPrefix1   = "10.1.0.0/24"
$BEPrefix1   = "10.1.1.0/24"
$GwPrefix1   = "10.1.255.0/27"
$Gw1         = "VNet1GW"
$GwIP1       = "VNet1GWIP"
$GwIPConf1   = "gwipconf1"
```

## <a name="2-create-the-virtual-network"></a><a name="configure"></a>2. Vytvoření virtuální sítě

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Vytvořte virtuální síť.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="3-add-the-gateway-subnet"></a><a name="gwsub"></a>3. Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. Pomocí následujících příkladů můžete přidat a nastavit podsíť brány:

Přidejte podsíť brány.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Nastavte konfiguraci podsítě brány pro virtuální síť.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="4-request-a-public-ip-address"></a><a name="publicip"></a>4. Žádost o veřejnou IP adresu
 
V tomto kroku zvolte pokyny, které platí pro bránu, kterou chcete vytvořit. Výběr zón pro nasazení bran závisí na zónách určených pro veřejnou IP adresu.

### <a name="for-zone-redundant-gateways"></a><a name="ipzoneredundant"></a>Pro zóny redundantní brány

Vyžádejte si veřejnou IP adresu se **standardní** lokou SKU publicipaddress a nezadávejte žádnou zónu. V takovém případě bude vytvořená standardní veřejná IP adresa zónově redundantní veřejná IP adresa.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="for-zonal-gateways"></a><a name="ipzonalgw"></a>Pro zonální brány

Požádejte o veřejnou IP adresu se **standardní** sku publicipaddress. Určete zónu (1, 2 nebo 3). V této zóně budou nasazeny všechny instance brány.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="for-regional-gateways"></a><a name="ipregionalgw"></a>Pro regionální brány

Požádejte o veřejnou IP adresu se **základní** položkou SKU publicipaddress. V tomto případě je brána nasazena jako místní brána a nemá žádné redundance zóny zabudované do brány. Instance brány jsou vytvořeny v libovolné zóně, resp.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="5-create-the-ip-configuration"></a><a name="gwipconfig"></a>5. Vytvoření konfigurace IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="6-create-the-gateway"></a><a name="gwconfig"></a>6. Vytvoření brány

Vytvořte bránu virtuální sítě.

### <a name="for-expressroute"></a>Pro expresní trasu

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Pro bránu VPN

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a><a name="faq"></a>Nejčastější dotazy

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní, když nasadím tyto nové souku?

Z vašeho pohledu můžete nasadit brány s redundancí zóny. To znamená, že všechny instance bran se nasadí napříč zónami dostupnosti Azure a každá zóna dostupnosti je jiná doména poruchy a aktualizace. Díky tomu jsou vaše brány spolehlivější, dostupnější a odolnější vůči selhání zóny.

### <a name="can-i-use-the-azure-portal"></a>Můžu používat portál Azure?

Ano, na webu Azure Portal můžete nasadit nové sku- tu. Tyto nové souny však uvidíte pouze v těch oblastech Azure, které mají zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jaké oblasti jsou k dispozici pro použití nových skutých.

Nejnovější seznam dostupných oblastí najdete v [tématu Zóny dostupnosti.](../availability-zones/az-overview.md#services-support-by-region)

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžu změnit/migrovat/upgradovat své stávající brány virtuální sítě na zónově redundantní nebo zónové brány?

Migrace existujících bran virtuální sítě do zónově redundantních nebo zónových bran není momentálně podporována. Můžete však odstranit existující bránu a znovu vytvořit zónovou nebo zónovou bránu.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžu nasadit brány VPN i Express Route ve stejné virtuální síti?

Koexistence bran VPN i Express Route ve stejné virtuální síti je podporována. Měli byste však rezervovat rozsah IP adres /27 pro podsíť brány.
