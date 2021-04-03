---
title: Konfigurace připojení S2S Azure VPN Gateway v aktivním aktivním prostředí
description: Tento článek vás provede konfigurací připojení typu aktivní-aktivní pomocí Azure VPN Gateway pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: 022ccaab0b210cd2d656b69f505791d1a2aa963f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89440775"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurace připojení VPN S2S typu aktivní-aktivní pomocí bran Azure VPN

Tento článek vás provede kroky k vytvoření připojení typu aktivní-aktivní mezi lokalitami a VNet-to-VNet pomocí modelu nasazení Správce prostředků a PowerShellu. V Azure Portal můžete taky nakonfigurovat bránu aktivní-aktivní.

## <a name="about-highly-available-cross-premises-connections"></a>O vysoce dostupných připojeních mezi různými místy
Abyste dosáhli vysoké dostupnosti pro připojení mezi různými místy a VNet-to-VNet, měli byste nasadit víc bran VPN a navázat víc paralelních připojení mezi vašimi sítěmi a Azure. Přehled možností připojení a topologie najdete v tématu [vysoce dostupné možnosti připojení mezi různými místy a VNET-to-VNet](vpn-gateway-highlyavailable.md) .

Tento článek poskytuje pokyny pro nastavení připojení VPN typu aktivní-aktivní mezi místními sítěmi a připojení aktivní-aktivní mezi dvěma virtuálními sítěmi.

* [Část 1 – Vytvoření a konfigurace brány Azure VPN v režimu aktivní-aktivní](#aagateway)
* [Část 2 – Vytvoření připojení mezi různými místy aktivní – aktivní](#aacrossprem)
* [Část 3 – navázání připojení VNet-to-VNet mezi aktivními aktivními virtuálními sítěmi](#aav2v)

Pokud už máte bránu sítě VPN, můžete:
* [Aktualizace stávající brány VPN z aktivního pohotovostního režimu na aktivní-aktivní nebo naopak](#aaupdate)

Dohromady je můžete kombinovat a vytvořit tak složitější a vysoce dostupnou síťovou topologii, která vyhovuje vašim potřebám.

> [!IMPORTANT]
> Režim aktivní-aktivní je k dispozici pro všechny SKU kromě úrovně Basic.

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Část 1 – Vytvoření a Konfigurace bran VPN typu aktivní-aktivní
Následující postup nastaví vaši bránu Azure VPN v režimech aktivní-aktivní. Klíčové rozdíly mezi branami aktivní-aktivní a aktivní-pohotovostní:

* Musíte vytvořit dvě konfigurace protokolu IP brány se dvěma veřejnými IP adresami.
* Je potřeba nastavit příznak EnableActiveActiveFeature.
* SKU brány musí být VpnGw1, VpnGw2, VpnGw3 nebo HighPerformance (starší verze SKU).

Ostatní vlastnosti jsou stejné jako brány mimo aktivní-aktivní. 

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Pokud v prohlížeči nechcete používat Cloudshellu, musíte nainstalovat rutiny prostředí PowerShell Azure Resource Manager. Další informace o instalaci rutin PowerShellu najdete v tématu [přehled Azure PowerShell](/powershell/azure/) .

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace VNet1
#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Tento ukázkový postup zahájíme deklarací proměnných. Pokud použijete Cloud Shell "vyzkoušet si ho", automaticky se připojíte k vašemu účtu. Pokud používáte PowerShell místně, použijte následující příklad, který vám umožní připojit se:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
```

V následujícím příkladu jsou proměnné deklarovány s použitím hodnot pro tento ukázkový postup. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními. Tyto proměnné můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné a pak je zkopírujte a vložte do konzoly prostředí PowerShell.

```azurepowershell-interactive
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

#### <a name="2-create-a-new-resource-group"></a>2. Vytvořte novou skupinu prostředků.

Pomocí následujícího příkladu vytvořte novou skupinu prostředků:

```azurepowershell-interactive
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. vytvoření virtuální sítě testvnet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem BackEnd. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```azurepowershell-interactive
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 – Vytvoření brány VPN pro virtuální sítě testvnet1 s režimem aktivní-aktivní
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Vytvořte konfiguraci veřejných IP adres a IP adresy brány.
Vyžádejte si dvě veřejné IP adresy, které se mají přidělit bráně, kterou vytvoříte pro svou virtuální síť. Také definujete požadovanou podsíť a konfiguraci IP adres.

```azurepowershell-interactive
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Vytvoření brány VPN s konfigurací aktivní-aktivní
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Všimněte si, že existují dvě GatewayIpConfig položky a je nastaven příznak EnableActiveActiveFeature. Vytvoření brány může nějakou dobu trvat (45 minut nebo déle).

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Získejte veřejné IP adresy brány a IP adresu partnerského uzlu protokolu BGP.
Po vytvoření brány budete muset získat IP adresu partnerského uzlu protokolu BGP na Azure VPN Gateway. Tato adresa je potřeba ke konfiguraci VPN Gateway Azure jako partnerského uzlu protokolu BGP pro vaše místní zařízení VPN.

```azurepowershell-interactive
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Pomocí následujících rutin můžete zobrazit dvě veřejné IP adresy přidělené vaší bráně VPN a odpovídající IP adresy partnerského uzlu protokolu BGP pro každou instanci brány:

```azurepowershell-interactive
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

Pořadí veřejných IP adres pro instance brány a odpovídajících adres partnerských vztahů protokolu BGP je stejné. V tomto příkladu bude virtuální počítač brány s veřejnou IP adresou 40.112.190.5 používat jako adresu partnerského vztahu protokolu BGP 10.12.255.4 a brána s 138.91.156.129 bude používat 10.12.255.5. Tyto informace jsou nutné při nastavení místních zařízení VPN, která se připojují k bráně Active-Active. Brána se zobrazuje v následujícím diagramu se všemi adresami:

![aktivní – aktivní brána](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po vytvoření brány můžete tuto bránu použít k vytvoření připojení mezi místními sítěmi nebo VNet-to-VNet. V následujících částech najdete postup k dokončení tohoto cvičení.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Část 2 – Vytvoření připojení mezi různými místy v aktivním prostředí
K navázání připojení mezi různými místy potřebujete vytvořit bránu místní sítě, která bude představovat vaše místní zařízení VPN, a připojení k připojení brány Azure VPN k bráně místní sítě. V tomto příkladu je brána Azure VPN Gateway v režimu aktivní-aktivní. V důsledku toho i v případě, že je k dispozici jenom jedno místní zařízení VPN (Brána místní sítě) a jeden prostředek připojení, vytvoří obě instance služby Azure VPN Gateway tunely S2S VPN s místním zařízením.

Než budete pokračovat, ujistěte se prosím, že jste dokončili [část 1](#aagateway) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě
#### <a name="1-declare-your-variables"></a>1. deklarace proměnných
Toto cvičení bude pokračovat v sestavování konfigurace zobrazené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```azurepowershell-interactive
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

V souvislosti s parametry brány místní sítě si poznamenejte několik věcí:

* Brána místní sítě může být ve stejném nebo jiném umístění a skupině prostředků jako brána sítě VPN. V tomto příkladu se zobrazují v různých skupinách prostředků, ale ve stejném umístění Azure.
* Pokud je v tomto příkladu jenom jedno místní zařízení VPN, může připojení aktivní-aktivní fungovat s protokolem BGP nebo bez něj. Tento příklad používá protokol BGP pro připojení mezi různými místy.
* Pokud je protokol BGP povolený, předpona, kterou potřebujete deklarovat pro bránu místní sítě, je adresa hostitele vaší IP adresy partnerského uzlu protokolu BGP na vašem zařízení VPN. V tomto případě je to předpona/32 "10.52.255.253/32".
* V rámci připomenutí musíte použít různé čísla ASN protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, musíte změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN už používá ASN k partnerským uzlům s jinými sousedními uzly protokolu BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvořte bránu místní sítě pro site5.
Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1. Vytvořte skupinu prostředků, pokud ještě není vytvořená.

```azurepowershell-interactive
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – připojení brány virtuální sítě a brány místní sítě
#### <a name="1-get-the-two-gateways"></a>1. získání dvou bran

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvořte připojení virtuální sítě testvnet1 pro site5.
V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 a Site5_1 s nastavením "EnableBGP" nastaveným na $True.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. parametry VPN a BGP pro vaše místní zařízení VPN
Následující příklad uvádí parametry, které zadáte do konfiguračního oddílu protokolu BGP na místním zařízení VPN pro toto cvičení:

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

Připojení by se mělo navázat po několika minutách a relace partnerského vztahu protokolu BGP se spustí, jakmile se naváže připojení IPsec. V tomto příkladu je nakonfigurované jenom jedno místní zařízení VPN, což vede k tomu, že se zobrazí následující diagram:

![aktivní – aktivní – crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3: připojení dvou místních zařízení VPN k bráně VPN typu aktivní-aktivní
Pokud máte dvě zařízení VPN ve stejné místní síti, můžete dosáhnout dvojí redundance tím, že bránu Azure VPN Gateway připojíte k druhému zařízení VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. vytvořte druhou bránu místní sítě pro site5
IP adresa brány, předpona adresy a adresa partnerského vztahu protokolu BGP pro druhou bránu místní sítě se nesmí překrývat s předchozí bránou místní sítě pro stejnou místní síť.

```azurepowershell-interactive
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```azurepowershell-interactive
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Připojte bránu virtuální sítě a druhou bránu místní sítě.
Vytvořte připojení z virtuální sítě testvnet1 a Site5_2 s nastavením "EnableBGP" nastaveným na $True

```azurepowershell-interactive
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. parametry VPN a BGP pro vaše druhé místní zařízení VPN
Podobně níže jsou uvedeny parametry, které zadáte do druhého zařízení VPN:

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

Jakmile se naváže připojení (tunely), budete mít duální redundantní zařízení VPN a tunely připojující místní síť a Azure:

![Dual-redundance – crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Část 3 – navázání připojení VNet-to-VNet aktivní – aktivní
Tato část vytvoří aktivní aktivní připojení VNet-to-VNet s protokolem BGP. 

Následující pokyny jsou pokračování kroků uvedených výše. Pokud chcete vytvořit a nakonfigurovat virtuální sítě testvnet1 a VPN Gateway s protokolem BGP, musíte dokončit [část 1](#aagateway) . 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření TestVNet2 a brány VPN
Je důležité se ujistit, že se adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Můžete nastavit připojení VNet-to-VNet mezi různými předplatnými. Další informace najdete [v tématu Konfigurace připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) . Nezapomeňte přidat "-EnableBgp $True" při vytváření připojení pro povolení protokolu BGP.

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných
Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```azurepowershell-interactive
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. vytvoření TestVNet2 v nové skupině prostředků

```azurepowershell-interactive
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Vytvoření brány VPN typu aktivní-aktivní pro TestVNet2
Vyžádejte si dvě veřejné IP adresy, které se mají přidělit bráně, kterou vytvoříte pro svou virtuální síť. Také definujete požadovanou podsíť a konfiguraci IP adres.

```azurepowershell-interactive
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Vytvořte bránu VPN s použitím čísla AS a příznaku "EnableActiveActiveFeature". Všimněte si, že musíte přepsat výchozí číslo ASN na vašich branách Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě musí být odlišné, aby bylo možné povolit směrování protokolu BGP a přenosu.

```azurepowershell-interactive
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 – připojení bran virtuální sítě testvnet1 a TestVNet2
V tomto příkladu jsou obě brány ve stejném předplatném. Tento krok můžete provést ve stejné relaci prostředí PowerShell.

#### <a name="1-get-both-gateways"></a>1. získání obou bran
Ujistěte se, že jste přihlášeni a připojeni k předplatnému 1.

```azurepowershell-interactive
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. vytvoření obou připojení
V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k TestVNet2 a připojení od TestVNet2 k virtuální sítě testvnet1.

```azurepowershell-interactive
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Nezapomeňte u obou připojení povolit protokol BGP.
> 
> 

Po dokončení těchto kroků bude připojení navázáno během několika minut a relace partnerského vztahu protokolu BGP bude až do dokončení připojení typu VNet-to-VNet s duální redundancí:

![aktivní – aktivní – V2V](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Aktualizovat existující bránu VPN

Když změníte bránu typu aktivní-pohotovostní na aktivní-aktivní, vytvoříte jinou veřejnou IP adresu a pak přidáte druhou konfiguraci protokolu IP brány. Tato část vám pomůže změnit existující bránu Azure VPN z aktivního režimu na režim aktivní-aktivní nebo naopak pomocí PowerShellu. Bránu můžete také změnit v Azure Portal na stránce **Konfigurace** pro bránu virtuální sítě.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Změna brány aktivní-pohotovostní na bránu aktivní-aktivní

Následující příklad převede bránu aktivní-pohotovostní na bránu aktivní-aktivní. 

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Nahraďte následující parametry používané pro příklady s nastavením, které požadujete pro vlastní konfiguraci, a pak tyto proměnné deklarovat.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarování proměnných můžete tento příklad zkopírovat a vložit do konzoly PowerShellu.

```azurepowershell-interactive
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Vytvořte veřejnou IP adresu a pak přidejte druhou konfiguraci IP adresy brány.

```azurepowershell-interactive
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Povolte režim aktivní-aktivní a aktualizujte bránu.

V tomto kroku povolíte režim aktivní-aktivní a aktualizujete bránu. V tomto příkladu služba VPN Gateway aktuálně používá starší verzi Standard SKU. Aktivní-aktivní ale nepodporuje standardní SKU. Chcete-li změnit velikost starší verze SKU na jednu, která je podporována (v tomto případě HighPerformance), stačí zadat podporovanou položku starší verze SKU, kterou chcete použít.

* V tomto kroku nemůžete změnit starší položku SKU na jednu z nových SKU. Změnit velikost původní SKU můžete jenom na jinou podporovanou starší verzi SKU. Nemůžete například změnit SKLADOVOU položku z úrovně Standard na VpnGw1 (i když je VpnGw1 podporovaná pro aktivní-aktivní), protože Standard je starší skladová položka a VpnGw1 je aktuální SKU. Další informace o změně velikosti a migraci SKU najdete v tématu [SKU brány](vpn-gateway-about-vpngateways.md#gwsku).

* Pokud chcete změnit velikost aktuální SKU, například VpnGw1 na VpnGw3, můžete to udělat pomocí tohoto kroku, protože SKU jsou ve stejné rodině SKU. K tomu byste měli použít tuto hodnotu: ```-GatewaySku VpnGw3```

Pokud ve svém prostředí používáte tuto bránu, nemusíte-li měnit velikost brány, nemusíte zadávat-GatewaySku. Všimněte si, že v tomto kroku musíte nastavit objekt brány v PowerShellu tak, aby aktivoval skutečnou aktualizaci. Tato aktualizace může trvat 30 až 45 minut, i když neměníte velikost brány.

```azurepowershell-interactive
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Změna brány aktivní-aktivní na bránu s aktivním pohotovostním režimem
#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Nahraďte následující parametry používané pro příklady s nastavením, které požadujete pro vlastní konfiguraci, a pak tyto proměnné deklarovat.

```azurepowershell-interactive
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarování proměnných Získejte název konfigurace protokolu IP, kterou chcete odebrat.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. odeberte konfiguraci protokolu IP brány a zakažte režim aktivní-aktivní.

Tento příklad slouží k odebrání konfigurace protokolu IP brány a zakázání režimu aktivní-aktivní. Všimněte si, že musíte nastavit objekt brány v PowerShellu tak, aby aktivoval skutečnou aktualizaci.

```azurepowershell-interactive
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Tato aktualizace může trvat až 30 až 45 minut.

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
