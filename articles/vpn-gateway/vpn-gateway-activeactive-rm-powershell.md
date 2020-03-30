---
title: Konfigurace aktivních připojení brány Vpn Azure S2S
description: Tento článek vás provede konfigurací aktivních a aktivních připojení pomocí bran Azure VPN pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang
ms.reviewer: cherylmc
ms.openlocfilehash: ec3697208434eb971e47136416f2c2cc541b5cea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244638"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurace aktivních připojení S2S VPN pomocí bran Azure VPN

Tento článek vás provede kroky k vytvoření aktivní aktivní cross-premises a připojení virtuální sítě k virtuální síti pomocí modelu nasazení Resource Manager a PowerShell.



## <a name="about-highly-available-cross-premises-connections"></a>Vysoce dostupná meziprostorová připojení
Chcete-li dosáhnout vysoké dostupnosti pro připojení mezi místními a virtuální sítí k virtuální síti, měli byste nasadit více bran VPN a vytvořit více paralelních připojení mezi vašimi sítěmi a Azure. Přehled možností připojení a topologie najdete v tématu [Vysoce dostupné připojení mezi místními sítěmi a](vpn-gateway-highlyavailable.md) virtuální sítí k virtuální síti.

Tento článek obsahuje pokyny k nastavení aktivního aktivního připojení VPN mezi místními prostory a aktivního aktivního připojení mezi dvěma virtuálními sítěmi.

* [Část 1 – Vytvoření a konfigurace brány Azure VPN v aktivním aktivním režimu](#aagateway)
* [Část 2 - Vytvoření aktivních meziprostorových spojení](#aacrossprem)
* [Část 3 – Vytvoření aktivních připojení virtuální sítě k virtuální síti](#aav2v)

Pokud již bránu VPN máte, můžete:
* [Aktualizace existující brány VPN z aktivního pohotovostního režimu na aktivní aktivní nebo naopak](#aaupdate)

Můžete je kombinovat dohromady a vytvořit tak složitější a dostupnější síťovou topologii, která vyhovuje vašim potřebám.

> [!IMPORTANT]
> Aktivní aktivní režim používá pouze následující skutovku: 
>   * VpnGw1, VpnGw2, VpnGw3
>   * Vysoký výkon (pro staré starší skum)

## <a name="part-1---create-and-configure-active-active-vpn-gateways"></a><a name ="aagateway"></a>Část 1 - Vytvoření a konfigurace bran VPN s aktivní aktivní aktivitou
Následující kroky nakonfigurují bránu Azure VPN v aktivních režimech. Hlavní rozdíly mezi aktivními a aktivními pohotovostními branami:

* Musíte vytvořit dvě konfigurace IP adresy brány se dvěma veřejnými IP adresami
* Potřebujete nastavit příznak EnableActiveActiveFeature
* Skladová položka brány musí být VpnGw1, VpnGw2, VpnGw3 nebo HighPerformance (starší skladová položka).

Ostatní vlastnosti jsou stejné jako brány, které nejsou aktivní. 

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Budete potřebovat nainstalovat nejnovější verzi rutin prostředí PowerShell pro Azure Resource Manager. Další informace o instalaci rutin PowerShellu najdete v [tématu Přehled Azure PowerShellu.](/powershell/azure/overview)

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace virtuální sítě1
#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k předplatnému a vytvoření nové skupiny prostředků
Ujistěte se, že jste přešli do režimu prostředí PowerShell, aby bylo možné používat rutiny Resource Manageru. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Vytvořit TestVNet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem BackEnd. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 – Vytvoření brány VPN pro TestVNet1 s aktivním aktivním režimem
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Vytvoření veřejných IP adres a konfigurací IP adres brány
Požádejte o přidělení dvou veřejných IP adres k bráně, kterou vytvoříte pro virtuální síť. Budete také definovat požadované konfigurace podsítě a IP.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Vytvoření brány VPN s aktivní aktivní konfigurací
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Všimněte si, že existují dvě položky GatewayIpConfig a je nastaven příznak EnableActiveActiveFeature. Vytvoření brány může nějakou dobu trvat (45 minut nebo déle).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Získání veřejných IP adres brány a IP adresy Partnerského zařízení BGP
Po vytvoření brány budete muset získat IP adresu Partnerského zařízení BGP v bráně Azure VPN Gateway. Tato adresa je potřeba ke konfiguraci brány Azure VPN jako Partner skály BGP pro vaše místní zařízení VPN.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Následující rutiny slouží k zobrazení dvou veřejných IP adres přidělených pro bránu VPN a jejich odpovídajících IP adres Protokolu BGP pro každou instanci brány:

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

Pořadí veřejných IP adres pro instance brány a odpovídající adresy partnerského vztahu Protokolu BGP jsou stejné. V tomto příkladu bude virtuální počítač brány s veřejnou IP adresou 40.112.190.5 používat 10.12.255.4 jako svou adresu partnerského vztahu Protokolu BGP a brána s 138.91.156.129 použije 10.12.255.5. Tyto informace jsou potřeba při nastavování místních zařízení VPN, která se připojují k bráně aktivní aktivní. Brána je znázorněna na obrázku níže se všemi adresami:

![brána aktivní-aktivní](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po vytvoření brány můžete tuto bránu použít k navázání aktivního aktivního připojení mezi místními sítěmi nebo připojení množení k virtuální síti. Následující části procházejí kroky k dokončení cvičení.

## <a name="part-2---establish-an-active-active-cross-premises-connection"></a><a name ="aacrossprem"></a>Část 2 - Vytvoření aktivního aktivního propojení mezi prostory
Chcete-li vytvořit mezimístní připojení, musíte vytvořit bránu místní sítě, která bude reprezentovat vaše místní zařízení VPN, a připojení k připojení brány Azure VPN k bráně místní sítě. V tomto příkladu je brána Azure VPN v aktivním aktivním režimu. V důsledku toho i v případě, že existuje pouze jedno místní zařízení VPN (brána místní sítě) a jeden prostředek připojení, obě instance brány Azure VPN vytvoří tunely S2S VPN s místním zařízením.

Než budete pokračovat, ujistěte se, že jste dokončili [část 1](#aagateway) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě
#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné
Toto cvičení bude pokračovat v sestavení konfigurace uvedené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Několik věcí, které je třeba poznamenat, pokud jde o parametry brány místní sítě:

* Brána místní sítě může být ve stejném nebo jiném umístění a skupině prostředků jako brána VPN. Tento příklad je zobrazuje v různých skupinách prostředků, ale ve stejném umístění Azure.
* Pokud existuje pouze jedno místní zařízení VPN, jak je uvedeno výše, aktivní aktivní připojení může pracovat s protokolem BGP nebo bez něj. Tento příklad používá protokol BGP pro připojení mezi místními.
* Pokud je protokol BGP povolen, je předponou, kterou je třeba deklarovat pro bránu místní sítě, adresa hostitele ip adresy Partnera partnerského zařízení Protokolu BGP na vašem zařízení VPN. V tomto případě je to /32 předpona "10.52.255.253/32".
* Připomínáme, že musíte používat různé asn protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, musíte změnit asn virtuální sítě, pokud vaše místní zařízení VPN již používá ASN k partnerce s ostatními sousedy Protokolu BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvoření brány místní sítě pro Lokalitu 5
Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1. Vytvořte skupinu prostředků, pokud ještě není vytvořena.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – Připojení brány virtuální sítě a brány místní sítě
#### <a name="1-get-the-two-gateways"></a>1. Získejte dvě brány

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvoření připojení TestVNet1 k Síti 5
V tomto kroku vytvoříte připojení z TestVNet1 na Site5_1 s "EnableBGP" nastavena na $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parametry VPN a BGP pro místní zařízení VPN
V níže uvedeném příkladu jsou uvedeny parametry, které zadáte do konfigurační části Protokolu BGP na místním zařízení VPN pro toto cvičení:

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

Připojení by mělo být navázáno po několika minutách a relace partnerského vztahu Protokolu BGP se spustí po navázání připojení Protokolu IPsec. Tento příklad zatím nakonfiguroval pouze jedno místní zařízení VPN, což má za následek následující diagram:

![aktivní-aktivní crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3 – Připojení dvou místních zařízení VPN k bráně VPN s aktivní aktivitou
Pokud máte dvě zařízení VPN ve stejné místní síti, můžete dosáhnout dvojí redundance připojením brány Azure VPN k druhému zařízení VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Vytvoření druhé brány místní sítě pro Lokalitu 5
Adresa IP brány, předpona adresy a adresa partnerského vztahu Protokolu BGP pro druhou bránu místní sítě se nesmí překrývat s předchozí bránou místní sítě pro stejnou místní síť.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Připojení brány virtuální sítě a druhé brány místní sítě
Vytvořte připojení z TestVNet1 na Site5_2 s "EnableBGP" nastavena na $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametry VPN a BGP pro druhé místní zařízení VPN
Podobně níže uvádí parametry, které zadáte do druhého zařízení VPN:

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

Po navázání připojení (tunelů) budete mít dvě redundantní zařízení VPN a tunely spojující vaši místní síť a Azure:

![dvouredundantní crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name="part-3---establish-an-active-active-vnet-to-vnet-connection"></a><a name ="aav2v"></a>Část 3 – Vytvoření aktivního připojení virtuální sítě k virtuální síti
Tato část vytvoří aktivní aktivní připojení virtuální sítě k virtuální síti s protokolem BGP. 

Následující pokyny jsou pokračování kroků uvedených výše. Chcete-li vytvořit a nakonfigurovat testVNet1 a bránu VPN pomocí protokolu BGP, musíte dokončit [část 1.](#aagateway) 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření testu VNetu2 a brány VPN
Je důležité se ujistit, že ip adresní prostor nové virtuální sítě, TestVNet2, se nepřekrývá s žádným rozsahem virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Můžete nastavit připojení virtuální sítě k virtuální síti mezi různými předplatnými. Další podrobnosti najdete [v části Konfigurace připojení virtuální sítě k virtuální síti.](vpn-gateway-vnet-vnet-rm-ps.md) Ujistěte se, že přidáte "-EnableBGP $True" při vytváření připojení povolit Protokol BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné
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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření testu VNetu2 v nové skupině prostředků

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Vytvoření aktivní aktivní brány VPN pro TestVNet2
Požádejte o přidělení dvou veřejných IP adres k bráně, kterou vytvoříte pro virtuální síť. Budete také definovat požadované konfigurace podsítě a IP.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Vytvořte bránu VPN s číslem AS a příznakem EnableActiveActiveFeature. Všimněte si, že je nutné přepsat výchozí ASN na vašich branách Azure VPN. Asn pro připojené virtuální sítě se musí lišit, aby bylo možné povolit protokol BGP a tranzitní směrování.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 – Připojení bran TestVNet1 a TestVNet2
V tomto příkladu jsou obě brány ve stejném předplatném. Tento krok můžete dokončit ve stejné relaci prostředí PowerShell.

#### <a name="1-get-both-gateways"></a>1. Získejte obě brány
Ujistěte se, že jste přihlášeni a připojeni k předplatnému 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Vytvořte obě připojení
V tomto kroku vytvoříte připojení z TestVNet1 na TestVNet2 a připojení z TestVNet2 na TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Ujistěte se, že povolit Protokol BGP pro obě připojení.
> 
> 

Po dokončení těchto kroků se připojení naváže během několika minut a relace partnerského vztahu Protokolu BGP se bude promítat po dokončení připojení virtuální sítě k virtuální síti s dvojitou redundancí:

![aktivní-aktivní-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name="update-an-existing-vpn-gateway"></a><a name ="aaupdate"></a>Aktualizace existující brány VPN

Tato část vám pomůže změnit existující bránu Azure VPN z aktivního pohotovostního režimu na aktivní aktivní režim nebo naopak.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Změna brány aktivního pohotovostního režimu na bránu aktivní-aktivní

Následující příklad převede bránu aktivního pohotovostního režimu na bránu aktivní aktivní. Když změníte aktivní pohotovostní bránu na aktivní aktivní, vytvoříte jinou veřejnou IP adresu a přidáte druhou konfiguraci IP brány.

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

Nahraďte následující parametry použité pro příklady nastavením, které požadujete pro vlastní konfiguraci, a pak deklarujte tyto proměnné.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarování proměnných můžete tento příklad zkopírovat a vložit do konzoly PowerShellu.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Vytvořte veřejnou IP adresu a přidejte druhou konfiguraci IP brány

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Povolit aktivní aktivní režim a aktualizovat bránu

V tomto kroku povolíte aktivní aktivní režim a aktualizujete bránu. V tomto příkladu brána VPN aktuálně používá starší standardní skladovou položku. Aktivní aktivní však nepodporuje standardní skladovou položku. Chcete-li změnit velikost starší skladové položky na položku, která je podporována (v tomto případě HighPerformance), jednoduše zadejte podporovanou starší skladovou položku, kterou chcete použít.

* Pomocí tohoto kroku nelze změnit starší skladovou položku na jednu z nových skladových částek. Velikost starší soupoložky můžete změnit pouze na jinou podporovanou starší skladovou položku. Například nelze změnit skladovou položku ze standardního na VpnGw1 (i když vpngw1 je podporován pro aktivní aktivní), protože Standard je starší skladové položky a VpnGw1 je aktuální Skladové položky. Další informace o změna velikosti a migraci slok naleznete [v tématu Brána sloky](vpn-gateway-about-vpngateways.md#gwsku).

* Pokud chcete změnit velikost aktuální skladové položky, například VpnGw1 na VpnGw3, můžete tak učinit pomocí tohoto kroku, protože skladové položky jsou ve stejné rodině skladových položk. Chcete-li tak učinit, použijte hodnotu:```-GatewaySku VpnGw3```

Pokud používáte ve vašem prostředí, pokud nepotřebujete změnit velikost brány, nebudete muset zadat -GatewaySku. Všimněte si, že v tomto kroku je nutné nastavit objekt brány v prostředí PowerShell pro aktivaci skutečné aktualizace. Tato aktualizace může trvat 30 až 45 minut, i když nejste změna velikosti brány.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Změna brány aktivní aktivní na bránu aktivního pohotovostního režimu
#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

Nahraďte následující parametry použité pro příklady nastavením, které požadujete pro vlastní konfiguraci, a pak deklarujte tyto proměnné.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarování proměnných získejte název konfigurace IP, kterou chcete odebrat.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Odeberte konfiguraci IP adresy brány a zakažte aktivní aktivní režim

Tento příklad slouží k odebrání konfigurace protokolu IP brány a zakázání aktivního aktivního režimu. Všimněte si, že je nutné nastavit objekt brány v prostředí PowerShell pro aktivaci skutečné aktualizace.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Tato aktualizace může trvat až 30 až 45 minut.

## <a name="next-steps"></a>Další kroky
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
