---
title: Vytvoření brány zónově redundantní virtuální sítě v Azure dostupnost zóny - Preview | Microsoft Docs
description: Nasazení brány, brána sítě VPN a ExpressRoute ve dostupnost zóny - Preview.
services: vpn-gateway
documentationcenter: na
author: cherylmc
Customer intent: As someone with a basic network background, I want to understand how to create zone-redundant gateways.
ms.service: vpn-gateway
ms.topic: article
ms.date: 06/28/2018
ms.author: cherylmc
ms.openlocfilehash: c484358bf98f0121cfc3ce270b162b01c75b5b09
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2018
ms.locfileid: "37096229"
---
# <a name="create-a-zone-redundant-virtual-network-gateway-in-azure-availability-zones---preview"></a>Vytvoření brány zónově redundantní virtuální sítě v Azure dostupnost zóny - Preview

Můžete nasadit v brány sítě VPN a ExpressRoute [Azure dostupnost zóny](../availability-zones/az-overview.md). To přináší vyšší dostupnosti, škálovatelnosti a odolnosti pro brány virtuální sítě. Nasazení v Azure dostupnost zóny brány fyzicky a logicky odděluje brány v rámci oblasti, při ochraně připojení k místní síti Azure selhání úrovni zóny.

Oblastmi a zónově redundantní brány mají vylepšení výkonu základní přes brány regulární virtuální sítě. Vytvoření brány virtuální sítě zónově redundantní nebo oblastmi je navíc rychlejší než vytvoření jiné brány. Místo trvá 45 minut, vytvořte dobu trvat přibližně 15 minut pro bránu ExpressRoute a 19 minut, než se brána sítě VPN.

### <a name="zrgw"></a>Zónově redundantní brány

Automaticky nasazení vaší brány virtuální sítě v rámci zóny dostupnosti, můžete použít brány zónově redundantní virtuální sítě. Zónově redundantní brány můžete plně využívat SLA 99,99 % dostupnost v GA přístup k vašim službám zvláště důležité, škálovatelné v Azure.

<br>
<br>

![Obrázek brány redunant zóny](./media/create-zone-redundant-vnet-gateway/zonered.png)

### <a name="zgw"></a>Oblastmi brány

K nasazení v konkrétní zónu brány, použijete oblastmi brány. Když nasadíte oblastmi brány, obě instance brány nasazených ve stejném pásmu dostupnosti.

<br>
<br>

![Obrázek oblastmi brány](./media/create-zone-redundant-vnet-gateway/zonal.png)

## <a name="gwskus"></a>SKU brány

Zónově redundantní a oblastmi brány musíte použít nové SKU brány. Jednou budete [sami zaregistrovat ve verzi Preview](#enroll), zobrazí se nové SKU ve všech oblastech Azure AZ brány virtuální sítě. Tyto identifikátory SKU jsou podobná odpovídající SKU pro ExpressRoute a VPN Gateway, s tím rozdílem, že jsou specifické pro zónově redundantní a oblastmi brány.

Nová brána SKU jsou:

### <a name="vpn-gateway"></a>VPN Gateway

* VpnGw1AZ
* VpnGw2AZ
* VpnGw3AZ

### <a name="expressroute"></a>ExpressRoute

* ErGw1AZ
* ErGw2AZ
* ErGw3AZ

## <a name="pipskus"></a>Veřejná IP adresa SKU

Zónově redundantní brány a oblastmi brány závisí na Azure prostředek veřejné IP *standardní* SKU. Konfigurace Azure prostředek veřejné IP určuje, zda je zónově redundantní, brány, kterou nasazujete nebo oblastmi. Pokud vytvoříte prostředek veřejné IP s *základní* SKU, brána nebude mít žádné zóny redundance a bude místní prostředky brány.

### <a name="pipzrg"></a>Zónově redundantní brány

Při vytváření veřejné IP adresy pomocí **standardní** veřejné IP SKU bez zadání zónu chování se liší v závislosti na tom, jestli je brána brána sítě VPN nebo brány ExpressRoute. 

* Pro bránu sítě VPN instancí dvě brány nasadí v jakékoli 2 z těchto tří zón redundanci zóny. 
* Pro bránu ExpressRoute vzhledem k tomu může být více než dvě instance může brána rozprostřít do všech tří zón.

### <a name="pipzg"></a>Oblastmi brány

Při vytváření veřejnou IP adresu pomocí **standardní** veřejné IP SKU a zadáte zóny (1, 2 nebo 3), všechny instance brány se nasadí do stejné zóny.

### <a name="piprg"></a>Místní brány

Při vytváření veřejné IP adresy pomocí **základní** veřejné IP SKU brány je nasazená jako místní brány a nemá žádné zóny redundance integrovaný do brány.

## <a name="before-you-begin"></a>Než začnete

Můžete použít buď PowerShell nainstalovány místně na počítači nebo prostředí cloudové služby Azure. Pokud si zvolíte instalaci a použití prostředí PowerShell místně, tato funkce vyžaduje nejnovější verzi modulu prostředí PowerShell.

[!INCLUDE [Cloud shell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

### <a name="to-use-powershell-locally"></a>Místně pomocí prostředí PowerShell

Pokud používáte prostředí PowerShell místně v počítači, nikoli pomocí cloudové prostředí, musíte nainstalovat modul prostředí PowerShell 6.1.1 nebo vyšší. Pokud chcete zkontrolovat verzi prostředí PowerShell, který jste nainstalovali, použijte následující příkaz:

```azurepowershell
Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
```

Pokud potřebujete upgrade, přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [PowerShell login](../../includes/vpn-gateway-ps-login-include.md)]

## <a name="enroll"></a>1. Registrace ve verzi Preview

Abyste mohli konfigurovat bránu zónově redundantní nebo oblastmi, můžete musí nejprve sami zaregistrovat předplatné ve verzi Preview. Jakmile zřízená vašeho předplatného, začněte zobrazíte SKU ve všech oblastech Azure AZ nové brány. 

Ujistěte se, že jsou přihlášení k účtu Azure a používáte předplatné, které chcete do seznamu povolených IP adres pro tuto verzi Preview. Registrovat pomocí následujícího příkladu:

```azurepowershell-interactive
Register-AzureRmProviderFeature -FeatureName AllowVMSSVirtualNetworkGateway -ProviderNamespace Microsoft.Network
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
```

Chcete-li ověřit, že funkci 'AllowVMSSVirtualNetworkGateway' je zaregistrován ve vašem předplatném použijte následující příkaz:

```azurepowershell-interactive
Get-AzureRmProviderFeature -ProviderNamespace Microsoft.Network
```

Výsledek bude vypadat podobně jako tento příklad:

![zřízení](./media/create-zone-redundant-vnet-gateway/verifypreview.png)

## <a name="variables"></a>2. Deklarace proměnných

Hodnoty používané pro příklady kroků jsou uvedeny níže. Kromě toho některé příklady pomocí proměnné deklarované v rámci kroky. Pokud používáte ve svém vlastním prostředí tyto kroky, nezapomeňte nahradit hodnoty vlastními. Při zadávání umístění, zkontrolujte, zda je podporován oblast, kterou zadáte. Další informace najdete v tématu [– nejčastější dotazy](#faq).

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

Podsíť brány obsahuje rezervovaných adres IP, které používají služby brány virtuální sítě. Následující příklady můžete použijte k přidání a nastavit podsíť brány:

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
 
V tomto kroku zvolte pokynů, které se týkají brány, kterou chcete vytvořit. Výběr zóny pro nasazení brány, závisí na zóny zadané pro veřejnou IP adresu.

### <a name="ipzoneredundant"></a>Zónově redundantní bran

Vyžádání veřejné IP adresy s **standardní** PublicIpaddress SKU a nezadávejte žádné zóny. V takovém případě bude standardní veřejnou IP adresu vytvořit zónově redundantní veřejnou IP adresu.   

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard
```

### <a name="ipzonalgw"></a>Pro oblastmi brány

Vyžádání veřejné IP adresy s **standardní** PublicIpaddress SKU. Určete pásmo (1, 2 nebo 3). V této zóně budou nasazeny všechny instance brány.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Static -Sku Standard -Zone 1
```

### <a name="ipregionalgw"></a>Pro místní brány

Vyžádání veřejné IP adresy s **základní** PublicIpaddress SKU. V takovém případě brány je nasazený jako místní brány a nemá žádné zóny redundance integrovaný do brány. Instance brány jsou vytvořené v žádné zóny, v uvedeném pořadí.

```azurepowershell-interactive
$pip1 = New-AzureRmPublicIpAddress -ResourceGroup $RG1 -Location $Location1 -Name $GwIP1 -AllocationMethod Dynamic -Sku Basic
```
## <a name="gwipconfig"></a>6. Vytvořte konfiguraci protokolu IP

```azurepowershell-interactive
$getvnet = Get-AzureRmVirtualNetwork -ResourceGroupName $RG1 -Name $VNet1
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name $GwSubnet1 -VirtualNetwork $getvnet
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GwIPConf1 -Subnet $subnet -PublicIpAddress $pip1
```

## <a name="gwconfig"></a>7. Vytvoření brány

Vytvořte bránu virtuální sítě.

>[!NOTE]
>V tuto chvíli nelze určit SKU brány. Verze SKU bude automaticky použita výchozí ErGw1AZ pro ExpressRoute a VpnGw1AZ pro bránu sítě VPN.
>

### <a name="for-expressroute"></a>Pro ExpressRoute

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType ExpressRoute
```

### <a name="for-vpn-gateway"></a>Pro bránu sítě VPN

```azurepowershell-interactive
New-AzureRmVirtualNetworkGateway -ResourceGroup $RG1 -Location $Location1 -Name $Gw1 -IpConfigurations $GwIPConf1 -GatewayType Vpn -VpnType RouteBased
```

## <a name="feedback"></a>Jak k poskytnutí zpětné vazby

Uvítáme vaše názory. E-mailovou zprávu na aznetworkgateways@microsoft.com nahlaste všechny problémy nebo poskytnout zpětnou vazbu (kladné a záporné) pro zónově redundantní a oblastmi brány VPN a Express Route. V řádku předmětu obsahovat název vaší společnosti v ["]". Pokud chcete ohlásit problém také zahrnovat svoje ID předplatného.

## <a name="faq"></a>Nejčastější dotazy

### <a name="how-do-i-sign-up-for-the-preview"></a>Jak přihlásím ve verzi Preview?

Můžete [sami zaregistrovat](#enroll) pomocí příkazů prostředí PowerShell v tomto článku.

### <a name="what-will-change-when-i-enroll"></a>Co se změní, když zaregistruji?

Z vaší strany verzi Preview, můžete nasadit vaší brány s zálohování zóny. To znamená, že všechny instance brány bude nasazena do Azure dostupnost zóny a každou zónu dostupnosti je jinou doménu selhání a aktualizace. Díky vašich bran spolehlivé, dostupné a odolné vůči selhání zóny.

### <a name="what-regions-are-available-for-the-preview"></a>Které oblasti jsou k dispozici ve verzi Preview?

Zónově redundantní a oblastmi brány jsou k dispozici v oblastech veřejné produkční prostředí nebo Azure.

### <a name="will-i-be-billed-for-participating-in-this-preview"></a>Budou uživatelé účtuje účasti v této verzi Preview?

Nebudou se fakturuje vašich bran verzi Preview. Neexistuje však žádný SLA připojené k vašemu nasazení. Velmi zajímá v vaše názory.

> [!NOTE]
> Pro brány ExpressRoute brána není účtován účtovat. Však bude účtován okruhu samotné (nikoli brány).

### <a name="what-regions-are-available-for-me-to-try-this-in"></a>Které oblasti jsou k dispozici pro mě v akci?

Verze public preview je k dispozici v oblastech střed USA a Francie centrální (oblasti Azure, které mají zóny dostupnosti obecně k dispozici). Do budoucna, jsme budou Zónově redundantní brány k dispozici pro vás v jiných oblastech Azure veřejné.

### <a name="can-i-change-my-existing-virtual-network-gateways-to-zone-redundant-or-zonal-gateways"></a>Můžete změnit mé existující brány virtuální sítě na zónově redundantní nebo oblastmi brány?

Migrace vaší existující brány virtuální sítě na zónově redundantní nebo oblastmi brány se aktuálně nepodporuje. Můžete však odstraňte existující bránu a znovu vytvořit bránu zónově redundantní nebo oblastmi.

### <a name="can-i-deploy-both-vpn-and-express-route-gateways-in-same-virtual-network"></a>Můžete nasadit připojení VPN a Express Route brány ve stejné virtuální síti?

Ve veřejné verzi Preview se podporuje koexistenci brány VPN a Expressroute ve stejné virtuální síti. Nicméně mějte na paměti následující požadavky a omezení:

* Rezerva a /27 rozsah IP adres pro podsíť brány.
* Zóny redundantní nebo oblastmi Express Route brány můžete pouze existují společně s zóny redundantní nebo oblastmi brány sítě VPN.
* Nasazení zóny redundantní nebo oblastmi brány Express Route před nasazením zóny redundantní nebo oblastmi brány VPN.
* Zóny redundantní nebo oblastmi Express Route brány může být připojen k maximálně 4 okruhů.

## <a name="next-steps"></a>Další postup

Uvítáme vaše názory. E-mailovou zprávu na aznetworkgateways@microsoft.com nahlaste všechny problémy nebo poskytnout zpětnou vazbu (kladné a záporné) pro zónově redundantní a oblastmi brány VPN a Express Route. V řádku předmětu obsahovat název vaší společnosti v ["]". Pokud chcete ohlásit problém také zahrnovat svoje ID předplatného.
