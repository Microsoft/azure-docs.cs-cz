---
title: 'Konfigurace připojení S2S VPN aktivní aktivní pro brány sítě VPN: Azure Resource Manager: prostředí PowerShell | Microsoft Docs'
description: Tento článek vás provede procesem konfigurace aktivní aktivní připojení s Azure VPN Gateway pomocí Azure Resource Manageru a prostředí PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 238cd9b3-f1ce-4341-b18e-7390935604fa
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/24/2018
ms.author: yushwang
ms.openlocfilehash: c09abe97d34b7220d76481a403165f1b7e07fcaa
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
ms.locfileid: "31602106"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurace připojení S2S VPN aktivní aktivní službou Azure VPN Gateways

Tento článek vás provede kroky k vytvoření aktivní aktivní mezi různými místy a připojení VNet-to-VNet pomocí modelu nasazení Resource Manager a prostředí PowerShell.

## <a name="about-highly-available-cross-premises-connections"></a>Informace o připojeních mezi různými místy vysokou dostupností
K dosažení vysoké dostupnosti pro mezi různými místy a připojení VNet-to-VNet, doporučujeme nasadit více bran VPN a vytvořit více paralelních připojení mezi vaší sítí a Azure. V tématu [vysoce dostupné mezi různými místy a připojení VNet-to-VNet](vpn-gateway-highlyavailable.md) přehled možností připojení a topologii.

Tento článek obsahuje pokyny k nastavení připojení VPN aktivní aktivní mezi různými místy a aktivní aktivní připojení mezi dvěma virtuálními sítěmi.

* [Část 1 – Vytvoření a konfiguraci brány Azure VPN v režimu aktivní aktivní](#aagateway)
* [Část 2 – navázání připojení mezi různými místy aktivní aktivní](#aacrossprem)
* [Část 3 – vytvoření aktivní aktivní připojení VNet-to-VNet](#aav2v)

Pokud již máte bránu VPN, můžete:
* [Aktualizovat existující bránu VPN z aktivní pohotovostní aktivní aktivní, nebo naopak](#aaupdate)

Můžete kombinovat tyto společně k vytvoření složitějších s vysokou dostupností síťové topologie, která vyhovuje vašim potřebám.

> [!IMPORTANT]
> Aktivní aktivní režim využívá jenom následující SKU: 
  * VpnGw1, VpnGw2, VpnGw3
  * HighPerformance (pro původní starší verze SKU)
> 
> 

## <a name ="aagateway"></a>Část 1 – Vytvoření a konfigurace aktivní aktivní brány sítě VPN
Následující kroky nakonfiguruje bránu Azure VPN v režimech aktivní aktivní. Hlavní rozdíly mezi bránami aktivní aktivní a aktivní pohotovostní:

* Je nutné vytvořit dvě konfigurace IP brány s dvě veřejné IP adresy
* Je nutné nastavit příznak EnableActiveActiveFeature
* Skladová položka brány musí být VpnGw1 VpnGw2, VpnGw3 nebo HighPerformance (starší verze SKU).

Ostatní vlastnosti jsou stejné jako aktivní aktivní brány. 

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Budete potřebovat nainstalovat nejnovější verzi rutin prostředí PowerShell pro Azure Resource Manager. V tématu [Přehled prostředí Azure PowerShell](/powershell/azure/overview) pro další informace o instalaci rutin prostředí PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace VNet1
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných
Tento ukázkový postup zahájíme deklarací proměnných. V následujícím příkladu jsou proměnné deklarovány s použitím hodnot pro tento ukázkový postup. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními. Tyto proměnné můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné a pak je zkopírujte a vložte do konzoly prostředí PowerShell.

```powershell
$Sub1 = "Ross"
$RG1 = "TestAARG1"
$Location1 = "West US"
$VNetName1 = "TestVNet1"
$FESubName1 = "FrontEnd"
$BESubName1 = "Backend"
$GWSubName1 = "GatewaySubnet"
$VNetPrefix11 = "10.11.0.0/16"
$VNetPrefix12 = "10.12.0.0/16"
$FESubPrefix1 = "10.11.0.0/24"
$BESubPrefix1 = "10.12.0.0/24"
$GWSubPrefix1 = "10.12.255.0/27"
$VNet1ASN = 65010
$DNS1 = "8.8.8.8"
$GWName1 = "VNet1GW"
$GW1IPName1 = "VNet1GWIP1"
$GW1IPName2 = "VNet1GWIP2"
$GW1IPconf1 = "gw1ipconf1"
$GW1IPconf2 = "gw1ipconf2"
$Connection12 = "VNet1toVNet2"
$Connection151 = "VNet1toSite5_1"
$Connection152 = "VNet1toSite5_2"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k vašemu předplatnému a vytvořte novou skupinu prostředků
Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Vytvoření virtuální sítě TestVNet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem BackEnd. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 – Vytvoření brány VPN pro virtuální síť TestVNet1 s režimem aktivní aktivní
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Vytvoření veřejné IP adresy a brány konfigurace protokolu IP
Požádat o dvě veřejné adresy IP, která bude přidělena pro bránu, kterou vytvoříte pro virtuální síť. Budete také definujte podsíť a požadované konfigurace protokolu IP.

```powershell
$gw1pip1 = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Vytvoření brány sítě VPN v konfiguraci aktivní aktivní
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Upozorňujeme, že existují dvě položky GatewayIpConfig a je nastaven příznak EnableActiveActiveFeature. Vytvoření brány může nějakou dobu trvat (45 minut nebo déle).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Získat veřejné IP adresy brány a IP adresy partnera BGP
Po vytvoření brány, musíte získat adresu IP adresa partnera BGP na bráně Azure VPN. Tato adresa je potřeba ke konfiguraci Azure VPN Gateway jako partnerské zařízení protokolu BGP pro vaše místní zařízení VPN.

```powershell
$gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Pomocí následující rutiny zobrazíte dvě veřejné IP adresy přidělené vaší brány sítě VPN a jejich odpovídající IP adresu partnera BGP pro každou instanci brány:

```powershell
PS D:\> $gw1pip1.IpAddress
40.112.190.5

PS D:\> $gw1pip2.IpAddress
138.91.156.129

PS D:\> $vnet1gw.BgpSettingsText
{
  "Asn": 65010,
  "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
  "PeerWeight": 0
}
```

Pořadí veřejné IP adresy pro instance brány a odpovídající adresy partnerského vztahu protokolu BGP jsou stejné. V tomto příkladu bude brána virtuálního počítače s veřejnou IP adresu z 40.112.190.5 používat 10.12.255.4 jako adresy partnerského vztahu protokolu BGP a brána s 138.91.156.129 bude používat 10.12.255.5. Tyto informace budete potřebovat při nastavování vaše na zařízení VPN místní připojení k bráně aktivní aktivní. Brána je znázorněno na obrázku níže všechny adresy:

![aktivní aktivní brány](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po vytvoření brány můžete vytvořit aktivní aktivní mezi různými místy nebo připojení VNet-to-VNet tuto bránu. V následujících částech provede kroky k dokončení výkonu.

## <a name ="aacrossprem"></a>Část 2 – Vytvoření připojení mezi různými místy aktivní aktivní
Navázat připojení mezi různými místy, musíte vytvořit bránu místní sítě představují vaše místní zařízení VPN a připojení pro připojení k místní síťová brána Azure VPN gateway. V tomto příkladu se brána Azure VPN je v režimu aktivní aktivní. V důsledku toho budou obě instance brány Azure VPN i v případě, že existuje pouze jeden místní zařízení VPN (brány místní sítě) a jedno připojení k prostředku, vytvořit tunelů S2S VPN s místní zařízení.

Než budete pokračovat, Zkontrolujte prosím, že jste dokončili [část 1](#aagateway) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných
Toto cvičení bude při vytváření konfigurace znázorněné v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Několik věcí, které si uvědomit o parametrech brány místní sítě:

* Brána místní sítě může být ve stejné nebo jiné umístění a skupině prostředků jako brána sítě VPN. Tento příklad ukazuje, je v různých skupinách prostředků, ale ve stejné oblasti Azure.
* Pokud existuje jenom jeden místní zařízení VPN jako v příkladu nahoře, aktivní aktivní připojení můžete pracovat s nebo bez protokolu BGP. Tento příklad používá protokol BGP pro připojení mezi různými místy.
* Pokud je protokol BGP povolený, předpony, které je třeba deklarovat pro bránu místní sítě je adresa hostitele vaší IP adresy partnera BGP v zařízení VPN. V takovém případě je /32 předponu "10.52.255.253/32".
* Připomínáme je nutné použít různá čísla ASN protokolu BGP mezi vaší místní sítí a virtuální sítí VNet Azure. Pokud se shodují, budete muset změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN už používá číslo Autonomního rovnocenných počítačů sousední ostatní směrovače BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvoření brány místní sítě pro Site5
Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1. Vytvořte skupinu prostředků, pokud ještě není vytvořená.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5
New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – připojení brány virtuální sítě a brány místní sítě
#### <a name="1-get-the-two-gateways"></a>1. Získat dvě brány

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzureRmLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvoření virtuální sítě TestVNet1 Site5 připojení
V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k Site5_1 s "EnableBGP" nastavena na $True.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Připojení VPN a BGP parametry pro vaše místní zařízení VPN
Následující příklad uvádí parametry, které budete zadávat do protokolu BGP konfigurační oddíl na vaše místní zařízení VPN pro tento postup:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.253
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení by se mělo vytvořit po několika minutách a relaci partnerského vztahu protokolu BGP se spustí po vytvoření připojení protokolu IPsec. Tento příklad, pokud byl nakonfigurován pouze jeden zařízení VPN místní, což vede k diagramu níže:

![aktivní aktivní crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3 – připojení dvě místní zařízení VPN k bráně VPN aktivní aktivní
Pokud máte dva zařízení VPN ve stejné místní síti, můžete dosáhnout duální redundance připojením bránu Azure VPN a druhý zařízení VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Vytvořte druhý brány místní sítě pro Site5
IP adresa brány, předponu adresy a adresu partnerského vztahu BGP pro bránu místní sítě druhý se nesmí překrývat s předchozí brána místní sítě pro stejnou místní síť.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Připojení brány virtuální sítě a druhá brána místní sítě
Vytvoření připojení z virtuální sítě TestVNet1 k Site5_2 s "EnableBGP" nastavena na $True

```powershell
$lng5gw2 = Get-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Připojení VPN a BGP parametry pro druhý místní zařízení VPN
Podobně níže seznamy parametry zadejte, který bude do druhé zařízení sítě VPN:

```
- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP 1  : 10.12.255.4 for tunnel to 40.112.190.5
- Azure VNet BGP IP 2  : 10.12.255.5 for tunnel to 138.91.156.129
- Static routes        : Destination 10.12.255.4/32, nexthop the VPN tunnel interface to 40.112.190.5
                         Destination 10.12.255.5/32, nexthop the VPN tunnel interface to 138.91.156.129
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Jakmile se navázat připojení (tunely), budete mít dva redundantní zařízení VPN a tunely připojení vaší místní sítí a Azure:

![Dual redundance crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Část 3 – vytvoření aktivní aktivní připojení VNet-to-VNet
V této části vytvoříme připojení k aktivní aktivní VNet-to-VNet s protokolem BGP. 

Následující pokyny jsou pokračování kroků uvedených výše. Je třeba provést [část 1](#aagateway) vytvořit a nakonfigurovat virtuální síť TestVNet1 a bránu VPN s protokolem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření TestVNet2 a brány VPN
Je důležité se ujistit, že adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů vaší virtuální sítě.

V tomto příkladu patří virtuální sítě ve stejném předplatném. Můžete nastavit připojení VNet-to-VNet mezi různých předplatných; naleznete [konfigurace připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) podrobné informace. Zajistěte, aby přidáte "-EnableBgp $True" při vytváření připojení k povolení protokolu BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných
Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG2 = "TestAARG2"
$Location2 = "East US"
$VNetName2 = "TestVNet2"
$FESubName2 = "FrontEnd"
$BESubName2 = "Backend"
$GWSubName2 = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$VNet2ASN = 65020
$DNS2 = "8.8.8.8"
$GWName2 = "VNet2GW"
$GW2IPName1 = "VNet2GWIP1"
$GW2IPconf1 = "gw2ipconf1"
$GW2IPName2 = "VNet2GWIP2"
$GW2IPconf2 = "gw2ipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření TestVNet2 do nové skupiny prostředků

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Vytvoření brány VPN aktivní aktivní pro TestVNet2
Požádat o dvě veřejné adresy IP, která bude přidělena pro bránu, kterou vytvoříte pro virtuální síť. Budete také definujte podsíť a požadované konfigurace protokolu IP.

```powershell
$gw2pip1 = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Vytvoření brány VPN s čísla AS a příznak "EnableActiveActiveFeature". Všimněte si, že je nutné přepsat výchozí číslo ASN na Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě se musí lišit povolit protokol BGP a směrování přenosu.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 – připojení brány virtuální sítě TestVNet1 a TestVNet2
V tomto příkladu jsou obě brány ve stejném předplatném. Tento krok můžete provést ve stejné relaci prostředí PowerShell.

#### <a name="1-get-both-gateways"></a>1. Získat obě brány
Ujistěte se, že jste přihlášeni a připojeni k předplatnému 1.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Vytvoření obě připojení
V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k TestVNet2 a připojení z TestVNet2 do virtuální sítě TestVNet1.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Je nutné povolit protokol BGP pro obě připojení.
> 
> 

Po dokončení těchto kroků, připojení vytvoří pár minut a protokolu BGP bude relaci partnerského vztahu až po dokončení připojení VNet-to-VNet s duální redundance:

![aktivní aktivní v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Aktualizovat existující bránu sítě VPN

Tato část vám pomůže změnit existující bránu Azure VPN z aktivní pohotovostní režim aktivní aktivní, nebo naopak.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Změnit bránu aktivní pohotovostní na aktivní aktivní gateway

Následující příklad převede bránu aktivní pohotovostní bránu aktivní aktivní. Při změně bránu aktivní pohotovostní na aktivní aktivní, můžete vytvořit jinou veřejnou IP adresou a pak přidejte druhý konfigurace IP brány.

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Nahraďte použité příklady s nastavením vyžadují pro vaši vlastní konfiguraci a potom deklarujte tyto proměnné následující parametry.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarování proměnné, můžete zkopírovat a vložit tento příklad pro konzolu prostředí PowerShell.

```powershell
$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Vytvoření veřejné IP adresy a pak přidejte druhý konfigurace IP brány

```powershell
$gwpip2 = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Povolit režim aktivní aktivní a aktualizujte bránu

V tomto kroku povolení režimu aktivní aktivní a aktualizujte bránu. V příkladu služby VPN gateway používá starší verze standardní SKU. Aktivní aktivní však nepodporuje standardní SKU. Ke změně velikosti starší verze SKU pro jednu, která je podporována (v tomto případě HighPerformance), jednoduše zadejte podporované starší verze SKU, který chcete použít.

* Starší verze SKU nelze změnit na jednu z nové skladových položek pomocí tohoto kroku. Pouze můžete změnit velikost starší verze SKU pro jiné podporované starší verze SKU. Například nelze verze SKU z Standard do VpnGw1 (i když VpnGw1 je podporována pro aktivní aktivní) protože Standard je starší verze SKU a VpnGw1 je aktuální identifikátor SKU. Další informace o SKU změny velikosti a migrace najdete v tématu [SKU brány](vpn-gateway-about-vpngateways.md#gwsku).

* Pokud chcete změnit velikost aktuální identifikátor SKU, například VpnGw1 k VpnGw3, můžete tak učinit pomocí tohoto kroku, protože SKU jsou ve stejné řada SKU. Chcete-li to provést, použijte hodnota: ```-GatewaySku VpnGw3```

Pokud používáte to ve vašem prostředí, pokud je nebudete muset změnit velikost brány, nebude muset zadat - GatewaySku. Všimněte si v tomto kroku musí nastavit brána objekt v prostředí PowerShell pro aktivaci skutečné aktualizace. Tato aktualizace může trvat 30 až 45 minut, i když nejsou Změna velikosti brány.

```powershell
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Změnit bránu aktivní aktivní na aktivní pohotovostní gateway
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Nahraďte použité příklady s nastavením vyžadují pro vaši vlastní konfiguraci a potom deklarujte tyto proměnné následující parametry.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarování proměnné, získáte název konfigurace protokolu IP, kterou chcete odebrat.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Odeberte konfigurace IP brány a zakázat režim aktivní aktivní

Tento příklad používá k odebrání konfigurace IP brány a zakázat režim aktivní aktivní. Všimněte si v prostředí PowerShell pro aktivaci Skutečná aktualizace, je nutné nastavit objekt brány.

```powershell
Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Tato aktualizace může trvat až 30 až 45 minut.

## <a name="next-steps"></a>Další postup
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
