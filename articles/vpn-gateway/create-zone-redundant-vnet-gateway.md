---
title: Vytvoření brány virtuální sítě redundantní v zóně v Zóny dostupnosti Azure
description: Nasazení bran VPN Gateway a ExpressRoute v Zóny dostupnosti
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 02/10/2020
ms.author: cherylmc
ms.openlocfilehash: d8c6b68a38d4b60cf7a3194e6a5ded8804cc416f
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77150166"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Vytvoření brány virtuální sítě redundantní v zóně v Zóny dostupnosti Azure

V Zóny dostupnosti Azure můžete nasadit brány VPN a ExpressRoute. Tím se zvýší odolnost, škálovatelnost a vyšší dostupnost bran virtuální sítě. Nasazování bran v Zóny dostupnosti Azure fyzicky a logicky odděluje brány v rámci určité oblasti a zároveň chrání vaše místní síťové připojení k Azure ze selhání na úrovni zóny. Informace najdete v tématu informace [o branách virtuální sítě redundantních v zóně](about-zone-redundant-vnet-gateways.md) a [o zóny dostupnosti Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Než začnete

[!INCLUDE [powershell](../../includes/vpn-gateway-cloud-shell-powershell-about.md)]

## <a name="variables"></a>1. deklarace proměnných

Deklarujte proměnné, které chcete použít. Použijte následující příklad a dle potřeby nahraďte v něm uvedené hodnoty vlastními. Pokud během cvičení zavřete relaci PowerShell/Cloud Shell, stačí zkopírovat hodnoty a vložit je znovu, aby se proměnné znovu deklarovaly. Při zadávání umístění ověřte, zda je zadaná oblast podporovaná. Další informace najdete v [nejčastějších dotazech](#faq).

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

## <a name="configure"></a>2. vytvoření virtuální sítě

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

## <a name="gwsub"></a>3. Přidejte podsíť brány.

Podsíť brány obsahuje rezervované IP adresy, které používají služby brány virtuální sítě. Pomocí následujících příkladů přidejte a nastavte podsíť brány:

Přidejte podsíť brány.

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Nastavte konfiguraci podsítě brány pro virtuální síť.

```azurepowershell-interactive
$getvnet | Set-AzVirtualNetwork
```
## <a name="publicip"></a>4. Vyžádejte si veřejnou IP adresu.
 
V tomto kroku vyberte pokyny, které se vztahují na bránu, kterou chcete vytvořit. Výběr zón pro nasazení bran závisí na zónách určených pro veřejnou IP adresu.

### <a name="ipzoneredundant"></a>Pro zóny redundantní brány

Vyžádejte si veřejnou IP adresu se **standardní** PublicIpaddress SKU a nezadávejte žádnou zónu. V tomto případě bude vytvořená veřejná IP adresa redundantní zóny.   

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Pro brány s oblastmi

Vyžádejte si veřejnou IP adresu se **standardní** PublicIpaddress SKU. Zadejte zónu (1, 2 nebo 3). Všechny instance brány budou nasazeny v této zóně.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Pro regionální brány

Vyžádejte si veřejnou IP adresu se **základní** PublicIpaddress SKU. V takovém případě je brána nasazená jako místní brána a nemá v bráně vestavěnou redundanci zón. Instance brány se vytvářejí v jakékoli zóně.

```azurepowershell-interactive
$pip1 = New-AzPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. vytvoření konfigurace protokolu IP

```azurepowershell-interactive
$getvnet = Get-AzVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Vytvoření brány

Vytvořte bránu virtuální sítě.

### <a name="for-expressroute"></a>Pro ExpressRoute

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute -GatewaySku ErGw1AZ
```

### <a name="for-vpn-gateway"></a>Pro VPN Gateway

```azurepowershell-interactive
New-AzVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1AZ
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní při nasazení těchto nových SKU?

Z perspektivy můžete nasadit brány s využitím redundance zón. To znamená, že všechny instance bran budou nasazeny v rámci Zóny dostupnosti Azure a každá zóna dostupnosti je jinou chybou a aktualizační doménou. Díky tomu jsou brány spolehlivější, dostupné a odolné vůči selháním zón.

### <a name="can-i-use-the-azure-portal"></a>Můžu použít Azure Portal?

Ano, můžete použít Azure Portal k nasazení nových SKU. Tyto nové SKU se ale zobrazí jenom v oblastech Azure, které mají Zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Jaké oblasti máte k dispozici pro použití nových SKU?

Nejnovější seznam oblastí, které jsou k dispozici, najdete v tématu [zóny dostupnosti](../availability-zones/az-overview.md#services-support-by-region) .

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžu změnit/migrovat nebo upgradovat stávající brány virtuální sítě na brány, které mají zóny redundantní nebo rozbrané?

Migrace stávajících bran virtuální sítě na brány, které nejsou v současné době podporované, se nepodporují. Můžete ale odstranit stávající bránu a znovu vytvořit bránu, která je nadbytečná nebo se zónou.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžu ve stejné virtuální síti nasazovat brány sítě VPN i Express?

Podporuje se souběžná existence bran sítě VPN i Express Route ve stejné virtuální síti. Měli byste si však vyhradit rozsah IP adres a/27 pro podsíť brány.
