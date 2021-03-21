---
title: 'Azure VPN Gateway: Konfigurace protokolu BGP: PowerShell'
description: Tento článek vás provede konfigurací protokolu BGP se službou Azure VPN Gateway pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8573d9e55299382392927b532966a6e6fdd8c439
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659756"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Postup konfigurace protokolu BGP u bran Azure VPN Gateway pomocí PowerShellu
Tento článek vás provede postupem povolení protokolu BGP pro připojení VPN typu Site-to-Site (S2S) a připojení typu VNet-to-VNet pomocí modelu nasazení Správce prostředků a PowerShellu.



## <a name="about-bgp"></a>Informace o protokolu BGP
BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje službám Azure VPN Gateway a místním zařízením VPN, která se nazývají partnerské uzly protokolu BGP nebo sousedé BGP, výměnu „tras“ informujících obě brány o dostupnosti a dosažitelnosti předpon, které procházejí těmito bránami nebo trasami. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP.

Další diskuzi o výhodách protokolu BGP a porozumění technickým požadavkům a doporučeních k použití protokolu BGP najdete v tématu [Přehled protokolu BGP se službou Azure VPN Gateway](vpn-gateway-bgp-overview.md) .

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Začínáme s protokolem BGP u bran Azure VPN Gateway

Tento článek vás provede kroky k provedení následujících úloh:

* [Část 1 – povolení protokolu BGP ve službě Azure VPN Gateway](#enablebgp)
* Část 2 – Vytvoření připojení mezi různými místy pomocí protokolu BGP
* [Část 3 – vytvoření připojení typu VNet-to-VNet s protokolem BGP](#v2vbgp)

Každá část pokynů tvoří základní stavební blok pro povolení protokolu BGP v připojení k síti. Pokud dokončíte všechny tři části, sestavíte topologii, jak je znázorněno na následujícím diagramu:

![Topologie BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Dohromady můžete kombinovat části a vytvořit tak složitější síť s více segmenty směrování, která vyhovuje vašim potřebám.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Část 1 – konfigurace protokolu BGP na Azure VPN Gateway
Kroky konfigurace nastaví parametry protokolu BGP brány VPN Azure, jak je znázorněno na následujícím diagramu:

![Brána BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte rutiny Azure Resource Manager PowerShellu. Další informace o instalaci rutin PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace VNet1
#### <a name="1-declare-your-variables"></a>1. deklarace proměnných
Pro toto cvičení začneme deklarací proměnných. Následující příklad deklaruje proměnné pomocí hodnot pro toto cvičení. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními. Tyto proměnné můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné a pak je zkopírujte a vložte do konzoly prostředí PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subscription_Name"
$RG1 = "TestBGPRG1"
$Location1 = "East US"
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
$GWIPName1 = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection12 = "VNet1toVNet2"
$Connection15 = "VNet1toSite5"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojte se k předplatnému a vytvořte novou skupinu prostředků.
Pokud chcete použít rutiny Správce prostředků, ujistěte se, že jste přešli do režimu PowerShellu. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../azure-resource-manager/management/manage-resources-powershell.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. vytvoření virtuální sítě testvnet1
Následující ukázka vytvoří virtuální síť s názvem virtuální sítě testvnet1 a tři podsítě, jednu s názvem GatewaySubnet, jednu s názvem front-end a jednu s názvem back-end. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2 – vytvoření VPN Gateway pro virtuální sítě testvnet1 s parametry BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. vytvoření konfigurace IP adresy a podsítě
Vyžádejte si veřejnou IP adresu, která bude přidělena bráně, kterou vytvoříte pro příslušnou virtuální síť. Také definujete požadovanou podsíť a konfiguraci protokolu IP.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvořte bránu VPN s číslem AS
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje bránu Route-Based VPN a také parametr sčítání – ASN pro nastavení čísla ASN (AS Number) pro virtuální sítě testvnet1. Pokud nenastavíte parametr ASN, přiřadí se číslo ASN 65515. Vytvoření brány může nějakou dobu trvat (30 minut nebo déle).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. získání IP adresy partnerského uzlu protokolu BGP Azure
Po vytvoření brány je potřeba získat IP adresu partnerského uzlu protokolu BGP na Azure VPN Gateway. Tato adresa je potřeba ke konfiguraci VPN Gateway Azure jako partnerského uzlu protokolu BGP pro vaše místní zařízení VPN.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Poslední příkaz zobrazí odpovídající konfiguraci protokolu BGP na Azure VPN Gateway; například:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po vytvoření brány můžete tuto bránu použít k vytvoření připojení mezi místními nebo VNet-to-VNet s protokolem BGP. V následujících částech najdete postup k dokončení tohoto cvičení.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Část 2 – Vytvoření připojení mezi různými místy pomocí protokolu BGP

K navázání připojení mezi různými místy potřebujete vytvořit bránu místní sítě, která bude představovat vaše místní zařízení VPN, a připojení k připojení brány VPN k bráně místní sítě. I když existují články, které vás provedou těmito kroky, Tento článek obsahuje další vlastnosti, které jsou potřeba k určení parametrů konfigurace protokolu BGP.

![Protokol BGP pro různé prostory](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Než budete pokračovat, ujistěte se, že jste dokončili [část 1](#enablebgp) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Toto cvičení pokračuje v sestavování konfigurace zobrazené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

V souvislosti s parametry brány místní sítě si poznamenejte několik věcí:

* Brána místní sítě může být ve stejném nebo jiném umístění a skupině prostředků jako brána sítě VPN. Tento příklad je zobrazen v různých skupinách prostředků v různých umístěních.
* Předpona, kterou potřebujete deklarovat pro bránu místní sítě, je adresa hostitele vaší IP adresy partnerského uzlu protokolu BGP na vašem zařízení VPN. V tomto případě je to předpona/32 "10.52.255.254/32".
* V rámci připomenutí musíte použít různé čísla ASN protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, musíte změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN už používá ASN k partnerským uzlům s jinými sousedními uzly protokolu BGP.

Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvořte bránu místní sítě pro site5.

Nezapomeňte vytvořit skupinu prostředků, pokud není vytvořená, a teprve potom vytvořte bránu místní sítě. Všimněte si dvou dalších parametrů pro bránu místní sítě: ASN a BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – připojení brány virtuální sítě a brány místní sítě

#### <a name="1-get-the-two-gateways"></a>1. získání dvou bran

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvořte připojení virtuální sítě testvnet1 pro site5.

V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k site5. Pro povolení protokolu BGP pro toto připojení je nutné zadat "-EnableBGP $True". Jak je popsáno výše, je možné mít připojení BGP i bez protokolu BGP pro stejné VPN Gateway Azure. V případě, že ve vlastnosti připojení není povolen protokol BGP, Azure pro toto připojení nepovolí protokol BGP, i když jsou parametry BGP už v obou branách nakonfigurované.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Následující příklad zobrazí seznam parametrů, které zadáte do konfiguračního oddílu protokolu BGP na místním zařízení VPN pro toto cvičení:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení se naváže po několika minutách a relace partnerského vztahu protokolu BGP se spustí, jakmile se naváže připojení IPsec.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Část 3 – vytvoření připojení typu VNet-to-VNet s protokolem BGP

Tato část přidá připojení typu VNet-to-VNet s protokolem BGP, jak je znázorněno na následujícím obrázku:

![Diagram, který ukazuje připojení V síti NET k V NET.](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Následující pokyny pokračují v předchozích krocích. K vytvoření a konfiguraci virtuální sítě testvnet1 a VPN Gateway s protokolem BGP je potřeba vyplnit [část I](#enablebgp) . 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření TestVNet2 a brány VPN

Je důležité se ujistit, že se adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Mezi různými předplatnými můžete nastavit připojení VNet-to-VNet. Další informace najdete v tématu [Konfigurace připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md). Nezapomeňte přidat "-EnableBgp $True" při vytváření připojení pro povolení protokolu BGP.

#### <a name="1-declare-your-variables"></a>1. deklarace proměnných

Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG2 = "TestBGPRG2"
$Location2 = "West US"
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
$GWIPName2 = "VNet2GWIP"
$GWIPconfName2 = "gwipconf2"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. vytvoření TestVNet2 v nové skupině prostředků

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Vytvoření brány VPN Gateway pro TestVNet2 s parametry BGP

Vyžádejte si veřejnou IP adresu, kterou chcete přidělit bráně, kterou vytvoříte pro virtuální síť, a definujte požadované podsítě a konfigurace protokolu IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Vytvořte bránu VPN s číslem AS. Musíte přepsat výchozí číslo ASN na vašich branách Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě musí být odlišné, aby bylo možné povolit směrování protokolu BGP a přenosu.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2 – připojení bran virtuální sítě testvnet1 a TestVNet2

V tomto příkladu jsou obě brány ve stejném předplatném. Tento krok můžete provést ve stejné relaci prostředí PowerShell.

#### <a name="1-get-both-gateways"></a>1. získání obou bran

Ujistěte se, že jste přihlášeni a připojeni k předplatnému 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. vytvoření obou připojení

V tomto kroku vytvoříte připojení z virtuální sítě testvnet1 k TestVNet2 a připojení od TestVNet2 k virtuální sítě testvnet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Nezapomeňte u obou připojení povolit protokol BGP.
> 
> 

Po dokončení těchto kroků se připojení naváže po několika minutách. Relace partnerského vztahu protokolu BGP je po dokončení připojení VNet-to-VNet dokončena.

Pokud jste dokončili všechny tři části tohoto cvičení, zavedli jste následující topologii sítě:

![Protokol BGP pro VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/windows/quick-create-portal.md).