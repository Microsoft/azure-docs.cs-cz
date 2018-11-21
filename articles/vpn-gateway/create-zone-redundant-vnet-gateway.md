---
title: Vytvoření zónově redundantní virtuální síťová brána v zónách dostupnosti Azure | Dokumentace Microsoftu
description: Nasazení brány ExpressRoute a VPN Gateway v zónách dostupnosti
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: ade5fa78191fe670bd898e8edcbb1fab3b9b2379
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2018
ms.locfileid: "52273993"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones"></a>Vytvoření zónově redundantní virtuální síťová brána v zónách dostupnosti Azure

Můžete nasadit připojení VPN a ExpressRoute Gateway v zónách dostupnosti Azure. To přináší odolnost proti chybám, škálovatelnosti a vysoké dostupnosti do brány virtuální sítě. Nasazení brány v zónách dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, současně připojení k místní síti Azure byla chráněná před výpadky na úrovni zóny. Informace najdete v tématu [o branách virtuálních sítí zónově redundantní](about-zone-redundant-vnet-gateways.md) a [o zóny dostupnosti Azure](../availability-zones/az-overview.md).

## <a name="before-you-begin"></a>Než začnete

Můžete použít buď PowerShell nainstalovaný místně na počítači, nebo na Azure Cloud Shell. Pokud se rozhodnete nainstalovat a používat PowerShell místně, tato funkce vyžaduje nejnovější verzi modulu prostředí PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Chcete-li používat PowerShell místně

Pokud používáte PowerShell místně v počítači, místo použití Cloud Shell, musíte nainstalovat modul prostředí PowerShell 6.1.1 nebo vyšší. Pokud chcete zkontrolovat verzi prostředí PowerShell, který jste nainstalovali, použijte následující příkaz:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="variables"></a>1. Deklarace proměnných

Hodnoty používané pro Příklady postupu jsou uvedeny níže. Kromě toho některé z příkladů použít proměnné deklarované v rámci kroků. Pokud používáte tyto kroky ve vašem prostředí, nezapomeňte tyto hodnoty nahradit vlastními. Při zadávání umístění, ověřte, že je podporované oblasti, kterou zadáte. Další informace najdete v tématu [nejčastější dotazy k](#faq).

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

## <a name="configure"></a>2. Vytvoření virtuální sítě

Vytvořte skupinu prostředků.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName $RG1 -Location $Location1
```

Vytvořte virtuální síť.

```azurepowershell-interactive
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubnet1 -AddressPrefix $FEPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubnet1 -AddressPrefix $BEPrefix1
$vnet = New-AzureRmVirtualNetwork -Name $VNet1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNet1Prefix -Subnet $fesub1,$besub1
```

## <a name="gwsub"></a>3. Přidání podsítě brány

Podsíť brány obsahuje vyhrazené IP adresy, které používají služby brány virtuální sítě. Následující příklady slouží k přidání a nastavit podsíť brány:

Přidejte podsíť brány.

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name VNet1
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.255.0/27 -VirtualNetwork $getvnet
```

Nastavte konfiguraci podsítě brány virtuální sítě.

```azurepowershell-interactive
$getvnet | Set-AzureRmVirtualNetwork
```
## <a name="publicip"></a>4. Vyžádání veřejné IP adresy
 
V tomto kroku vyberte pokyny, které se vztahují k bráně, kterou chcete vytvořit. Výběr zóny pro nasazení brány, závisí na zónám pro veřejnou IP adresu.

### <a name="ipzoneredundant"></a>Zónově redundantní bran

Vyžádejte si veřejnou IP adresu s **standardní** PublicIpaddress SKU a nezadávejte žádné zóny. V takovém případě bude standardní veřejnou IP adresu vytvořenou zónově redundantní veřejnou IP adresu.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Pro zónové brány

Vyžádejte si veřejnou IP adresu s **standardní** PublicIpaddress SKU. Určení zóny (1, 2 nebo 3). V této zóně budou nasazeny všechny instance brány.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Pro místní brány

Vyžádejte si veřejnou IP adresu s **základní** PublicIpaddress SKU. Brány v tomto případě je nasazený jako místní bránu a nemá žádné zóny redundance integrovaný do brány. Instance brány se vytvářejí v žádné zóny.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>5. Vytvořte konfiguraci IP adresy

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>6. Vytvoření brány

Vytvořte bránu virtuální sítě.

### <a name="for-expressroute"></a>Pro ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Pro bránu sítě VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-will-change-when-i-deploy-these-new-skus"></a>Co se změní, když nasadím tyto nové SKU?

Z pohledu můžete nasadit bran s redundanci zón. To znamená, že všechny instance brány nasadí napříč zónami dostupnosti Azure a každá zóna dostupnosti je jiné selhání a aktualizační domény. Díky tomu vaše brány spolehlivé, dostupné a odolné vůči selhání zóny.

### <a name="can-i-use-the-azure-portal"></a>Můžete použít na webu Azure portal?

Ano, můžete na webu Azure portal k nasazení nové SKU. Nicméně zobrazí se tyto nové SKU pouze v těchto oblastech Azure, které mají zóny dostupnosti Azure.

### <a name="what-regions-are-available-for-me-to-use-the-new-skus"></a>Které oblasti jsou k dispozici pro mě nejlepší používat na nové SKU?

Nové SKU jsou k dispozici v oblasti Azure, které mají zóny dostupnosti Azure – USA (střed), Francie – střed, Severní Evropa, západní Evropa a oblasti západní USA 2. Od této chvíle jsme zpřístupníte zónově redundantní brány je v jiných veřejných oblastech Azure.

### <a name="can-i-changemigrateupgrade-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Je možné změnit/migrace a upgrade mé existující brány virtuální sítě k bránám zónové a zónově redundantní?

Migrace vaší existující brány virtuální sítě pro zónové a zónově redundantní brány není aktuálně podporováno. Můžete však odstraňte existující bránu a znovu vytvořit bránu zónové a zónově redundantní.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžete nasadit brány sítě VPN a Expressroute ve stejné virtuální síti?

Se podporuje koexistence bran VPN a Expressroute ve stejné virtuální síti. Nicméně, měli byste si rezervovat velikost/27 rozsah IP adres pro podsíť brány.
