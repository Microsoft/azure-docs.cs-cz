---
title: Vytvoření zónově redundantní virtuální síťová brána v zónách dostupnosti Azure – Preview | Dokumentace Microsoftu
description: Nasazení brány ExpressRoute a VPN Gateway v zónách dostupnosti – ve verzi Preview.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/09/2018
ms.author: cherylmc
ms.openlocfilehash: fa349555a5effd41ca519cbd5a29005203d79543
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952551"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Vytvoření zónově redundantní virtuální síťová brána v zónách dostupnosti Azure – Preview

Nasazením brány sítě VPN a ExpressRoute v [zóny dostupnosti Azure](../availability-zones/az-overview.md). To přináší odolnost proti chybám, škálovatelnosti a vysoké dostupnosti do brány virtuální sítě. Nasazení brány v zónách dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, současně připojení k místní síti Azure byla chráněná před výpadky na úrovni zóny.

Zónové a zónově redundantní Perfect Forward základní rychlejší přes brány regulární virtuální sítě. Kromě toho vytváření brány virtuální sítě zónově redundantní nebo oblastmi je vyšší než vytváření jiné brány. Místo trávení 45 minut, vytvoření dobu využijte přibližně 15 minut pro bránu ExpressRoute a 19 minut pro bránu VPN.

### <a name="zrgw"></a>Zónově redundantní brány

Pokud chcete automaticky nasadit vaší brány virtuální sítě napříč zónami dostupnosti, zónově redundantní virtuální sítě brány můžete použít. Zónově redundantní brány mohou využít smlouvu SLA 99,99 % dostupnost ve verzi GA pro přístup k vaší kritických pro chod, škálovatelných služeb v Azure.

<br>
<br>

![Obrázek zóny redunant brány](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Oblastmi brány

K nasazení brány v konkrétní zóně použijte oblastmi brány. Při nasazení oblastmi brány jsou obě instance brány nasazené ve stejné zóně dostupnosti.

<br>
<br>

![Obrázek oblastmi brány](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU brány

Zónové a zónově redundantní brány musíte použít nové SKU brány. Jakmile [sami zaregistrovat ve verzi Preview](#enroll), zobrazí se nové SKU bran virtuálních sítí ve všech oblastech Azure AZ. Tyto SKU jsou podobné odpovídající SKU pro ExpressRoute a VPN Gateway, s tím rozdílem, že jsou specifické pro zónové a zónově redundantní brány.

Nová brána se tyto skladové položky:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>SKU veřejné IP adresy

Zónově redundantní brány a zónové brány využívají Azure prostředek veřejné IP adresy *standardní* SKU. Konfigurace Azure prostředek veřejné IP adresy určuje, zda je brána, který nasadíte zónově redundantní, nebo oblastmi. Pokud vytvoříte prostředek s veřejnou IP *základní* SKU, brána nebude mít žádné redundanci zón a bude místní prostředky brány.

### <a name="pipzrg"></a>Zónově redundantní brány

Když vytvoříte a veřejné IP adresy s použitím **standardní** SKU veřejné IP bez zadání zónu, chování se liší v závislosti na tom, jestli je brána VPN gateway nebo ExpressRoute gateway. 

* Pro bránu sítě VPN se nasadí v aspoň 2 mimo tyto tři zóny poskytnout redundanci zón instance dvě brány. 
* Pro bránu ExpressRoute protože může existovat více než dvě instance, brána lze pracovat nad více všechny tři zóny.

### <a name="pipzg"></a>Oblastmi brány

Když vytvoříte a veřejné IP adresy s použitím **standardní** SKU veřejné IP a určení zóny (1, 2 nebo 3), všechny instance brány se nasadí ve stejné zóně.

### <a name="piprg"></a>Místní brány

Když vytvoříte a veřejné IP adresy s použitím **základní** veřejné IP SKU brány je nasazená jako místní brány a nemá žádné zóny redundance integrovaný do brány.

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

## <a name="enroll"></a>1. Registrace ve verzi Preview

Než budete moct nakonfigurovat bránu zónově redundantní nebo oblastmi, můžete musí nejprve sami zaregistrovat předplatné ve verzi Preview. Po zřízení předplatného, začne se zobrazit nová brána skladové položky ve všech oblastech Azure AZ. 

Ujistěte se, že jste přihlášeni ke svému účtu Azure a používáte předplatné, které chcete přidat na seznam povolených pro tuto verzi Preview. V následujícím příkladu použijte k registraci:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Chcete-li ověřit, že funkce 'AllowVMSSVirtualNetworkGateway' je registrován s vaším předplatným použijte následující příkaz:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Výsledek bude vypadat nějak takto:

![zřízené](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Deklarace proměnných

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

## <a name="configure"></a>3. Vytvoření virtuální sítě

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

## <a name="gwsub"></a>4. Přidání podsítě brány

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
## <a name="publicip"></a>5. Vyžádání veřejné IP adresy
 
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
## <a name="gwipconfig"></a>6. Vytvořte konfiguraci IP adresy

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Vytvoření brány

Vytvořte bránu virtuální sítě.

### <a name="for-expressroute"></a>Pro ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Pro bránu sítě VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Jak chcete poskytnout zpětnou vazbu

Uvítáme vaše zpětná vazba. Odeslání e-mailu aznetworkgateways@microsoft.com ohlásit případné potíže nebo poskytnout zpětnou vazbu (kladná nebo záporná) pro zónové a zónově redundantní brány sítě VPN a Expressroute. V řádku předmětu obsahovat název vaší společnosti v "[]". Pokud oznamujete problém také uvést ID předplatného.

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-do-i-sign-up-for-the-preview"></a>Jak se můžu zaregistrovat k verzi Preview?

Je možné [sami zaregistrovat](#enroll) pomocí příkazů Powershellu v tomto článku.

### <a name="what-will-change-when-i-enroll"></a>Co se změní, když si zaregistruji?

Z pohledu ve verzi Preview, můžete nasadit bran s redundanci zón. To znamená, že všechny instance brány nasadí napříč zónami dostupnosti Azure a každá zóna dostupnosti je jiné selhání a aktualizační domény. Díky tomu vaše brány spolehlivé, dostupné a odolné vůči selhání zóny.

### <a name="can-i-use-the-azure-portal"></a>Můžete použít na webu Azure portal?

Ano, můžete na webu Azure portal ve verzi Preview. Však stále budete muset zaregistrovat pomocí Powershellu nebo nebudete moci používat na portálu ve verzi Preview.

### <a name="what-regions-are-available-for-the-preview"></a>Které oblasti jsou k dispozici ve verzi Preview?

Zónové a zónově redundantní brány jsou dostupné v produkčním prostředí nebo veřejné oblasti Azure.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Se mi bude účtovat účast v této verzi Preview?

Nebudou se vám účtovat vaše brány ve verzi Preview. Neexistuje ale žádná smlouva SLA připojené v rámci vašeho nasazení. Máme hodně zajímá nám řeknete svůj názor.

> [!NOTE]
> Pro brány ExpressRoute brána není účtovat nebo fakturovat. Však bude účtovat okruh samotného (nikoli brány).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Které oblasti jsou k dispozici pro mě to vyzkoušet?

Verze public preview je dostupná v oblastech střed USA a Francie – střed (oblasti Azure, které mají zóny dostupnosti, které jsou obecně dostupné). Od této chvíle jsme zpřístupníte zónově redundantní brány je v jiných veřejných oblastech Azure.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžete změnit Můj existující brány virtuální sítě k bránám zónové a zónově redundantní?

Migrace vaší existující brány virtuální sítě pro zónové a zónově redundantní brány není aktuálně podporováno. Můžete však odstraňte existující bránu a znovu vytvořit bránu zónové a zónově redundantní.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžete nasadit brány sítě VPN a Expressroute ve stejné virtuální síti?

Ve verzi Public Preview se podporuje koexistence bran VPN a Expressroute ve stejné virtuální síti. Nicméně mějte na paměti následující požadavky a omezení:

* Rezervace a velikost/27 rozsah IP adres pro podsíť brány.
* Zóny redundantní/oblastmi brány Expressroute můžete pouze existovat vedle sebe s zóny redundantní/oblastmi bránami VPN.
* Nasazení zóny redundantní/oblastmi brány Expressroute před nasazením zóny redundantní/oblastmi brány sítě VPN.
* Zóny redundantní/oblastmi bránu Expressroute může být připojen k maximálně 4 okruhy.

## <a name="next-steps"></a>Další postup

Uvítáme vaše zpětná vazba. Odeslání e-mailu aznetworkgateways@microsoft.com ohlásit případné potíže nebo poskytnout zpětnou vazbu (kladná nebo záporná) pro zónové a zónově redundantní brány sítě VPN a Expressroute. V řádku předmětu obsahovat název vaší společnosti v "[]". Pokud oznamujete problém také uvést ID předplatného.
