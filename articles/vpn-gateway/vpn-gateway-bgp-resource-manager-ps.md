---
title: 'Brána Azure VPN: Konfigurace protokolu BGP: PowerShell'
description: Tento článek vás provede konfigurací protokolu BGP pomocí bran Azure VPN pomocí Azure Resource Manager a PowerShellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: 78147a96d6d9e92c2602b6a83cbed743cf2abf37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152036"
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Jak nakonfigurovat bgp na Azure VPN brány pomocí PowerShellu
Tento článek vás provede kroky k povolení protokolu BGP na připojení VPN mezi místními lokalitami (Site-site) a připojení virtuální sítě k virtuální síti pomocí modelu nasazení Resource Manager a Prostředí PowerShell.



## <a name="about-bgp"></a>Informace o protokolu BGP
BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje službám Azure VPN Gateway a místním zařízením VPN, která se nazývají partnerské uzly protokolu BGP nebo sousedé BGP, výměnu „tras“ informujících obě brány o dostupnosti a dosažitelnosti předpon, které procházejí těmito bránami nebo trasami. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP.

Další diskuse o výhodách protokolu BGP a pochopení technických požadavků a aspektů používání protokolu BGP najdete v [tématu Přehled protokolu BGP s bránami Azure VPN.](vpn-gateway-bgp-overview.md)

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Začínáme s BGP na azure vpn brány

Tento článek vás provede kroky k provedení následujících úkolů:

* [Část 1 – Povolení protokolu BGP na bráně Azure VPN](#enablebgp)
* Část 2 - Vytvoření propojení mezi prostory s Protokolem BGP
* [Část 3 – vytvoření připojení virtuální sítě k virtuální síti s protokolem BGP](#v2vbgp)

Každá část pokynů tvoří základní stavební blok pro povolení protokolu BGP v síťovém připojení. Pokud dokončíte všechny tři části, vytvoříte topologii, jak je znázorněno na následujícím diagramu:

![Topologie Protokolu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Součásti můžete kombinovat a vytvořit tak složitější tranzitní síť s více směrováními, která vyhovuje vašim potřebám.

## <a name="part-1---configure-bgp-on-the-azure-vpn-gateway"></a><a name ="enablebgp"></a>Část 1 – Konfigurace protokolu BGP v bráně Azure VPN Gateway
Kroky konfigurace nastavit bgp parametry brány Azure VPN, jak je znázorněno na následujícím diagramu:

![Brána Protokolu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte rutiny prostředí PowerShell Azure Resource Manager. Další informace o instalaci rutin PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace virtuální sítě1
#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné
Pro toto cvičení začneme deklarováním našich proměnných. Následující příklad deklaruje proměnné pomocí hodnoty pro toto cvičení. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními. Tyto proměnné můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné a pak je zkopírujte a vložte do konzoly prostředí PowerShell.

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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k předplatnému a vytvoření nové skupiny prostředků
Chcete-li použít rutiny Správce prostředků, ujistěte se, že přepnete do režimu PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Vytvořit TestVNet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě, jeden s názvem GatewaySubnet, jeden s názvem FrontEnd a jeden s názvem Backend. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2 – Vytvoření brány VPN pro testVNet1 s parametry Protokolu BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Vytvoření konfigurací PROTOKOLU IP a podsítě
Vyžádejte si veřejnou IP adresu, která bude přidělena bráně, kterou vytvoříte pro příslušnou virtuální síť. Budete také definovat požadované konfigurace podsítě a IP.

```powershell
$gwpip1 = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvořte bránu VPN s číslem AS
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje bránu VPN založenou na route a také parametr sčítání -Asn pro nastavení čísla ASN (AS Number) pro TestVNet1. Pokud nenastavíte parametr ASN, asn 65515 je přiřazen. Vytvoření brány může nějakou dobu trvat (30 minut nebo déle).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Získání IP adresy partnerského zařízení Azure BGP
Po vytvoření brány je potřeba získat IP adresu Partnerského zařízení Protokolu BGP v bráně Azure VPN Gateway. Tato adresa je potřeba ke konfiguraci brány Azure VPN jako Partner skály BGP pro vaše místní zařízení VPN.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Poslední příkaz zobrazuje odpovídající konfigurace protokolu BGP v bráně Azure VPN Gateway; například:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po vytvoření brány můžete tuto bránu použít k navázání připojení mezi místními sítěmi nebo připojení k virtuální síti k virtuální síti s protokolem BGP. Následující části procházejí kroky k dokončení cvičení.

## <a name="part-2---establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembbgp"></a>Část 2 - Vytvoření propojení mezi prostory s Protokolem BGP

Chcete-li vytvořit mezimístní připojení, musíte vytvořit bránu místní sítě, která bude reprezentovat vaše místní zařízení VPN, a připojení k bráně VPN k bráně místní sítě. Zatímco existují články, které vás provedou těmito kroky, tento článek obsahuje další vlastnosti potřebné k určení parametrů konfigurace protokolu BGP.

![BGP pro cross-premises](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Než budete pokračovat, ujistěte se, že jste dokončili [část 1](#enablebgp) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

Toto cvičení pokračuje v sestavení konfigurace uvedené v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Několik věcí, které je třeba poznamenat, pokud jde o parametry brány místní sítě:

* Brána místní sítě může být ve stejném nebo jiném umístění a skupině prostředků jako brána VPN. Tento příklad je zobrazuje v různých skupinách prostředků v různých umístěních.
* Předpona, kterou je třeba deklarovat pro bránu místní sítě, je adresa hostitele ip adresy Partnera BGP na vašem zařízení VPN. V tomto případě je to /32 předpona "10.52.255.254/32".
* Připomínáme, že musíte používat různé asn protokolu BGP mezi místními sítěmi a virtuální sítí Azure. Pokud jsou stejné, musíte změnit asn virtuální sítě, pokud vaše místní zařízení VPN již používá ASN k partnerce s ostatními sousedy Protokolu BGP.

Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvoření brány místní sítě pro Lokalitu 5

Nezapomeňte vytvořit skupinu prostředků, pokud není vytvořena, před vytvořením brány místní sítě. Všimněte si dvou dalších parametrů pro bránu místní sítě: Asn a BgpPeerAddress.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – Připojení brány virtuální sítě a brány místní sítě

#### <a name="1-get-the-two-gateways"></a>1. Získejte dvě brány

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvoření připojení TestVNet1 k Síti 5

V tomto kroku vytvoříte připojení z TestVNet1 na Site5. Chcete-li povolit protokol BGP pro toto připojení, je nutné zadat "-EnableBGP $True". Jak již bylo popsáno dříve, je možné mít připojení BGP i non-BGP pro stejnou bránu Azure VPN. Pokud není ve vlastnosti připojení povoleno protokol BGP, Azure nepovolí protokol BGP pro toto připojení, přestože parametry protokolu BGP jsou již nakonfigurované na obou branách.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

V následujícím příkladu jsou uvedeny parametry, které zadáte do konfigurační části Protokolu BGP na místním zařízení VPN pro toto cvičení:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení je navázáno po několika minutách a relace partnerského vztahu Protokolu BGP se spustí po navázání připojení Protokolu IPsec.

## <a name="part-3---establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Část 3 – vytvoření připojení virtuální sítě k virtuální síti s protokolem BGP

Tato část přidá připojení virtuální sítě k virtuální síti s protokolem BGP, jak je znázorněno na následujícím diagramu:

![BGP pro virtuální síť k virtuální síti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Následující pokyny pokračují z předchozích kroků. Chcete-li vytvořit a nakonfigurovat testVNet1 a bránu VPN pomocí protokolu BGP, musíte dokončit [část I.](#enablebgp) 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření testu VNetu2 a brány VPN

Je důležité se ujistit, že ip adresní prostor nové virtuální sítě, TestVNet2, se nepřekrývá s žádným rozsahem virtuální sítě.

V tomto příkladu virtuální sítě patří do stejného předplatného. Můžete nastavit připojení virtuální sítě k virtuální síti mezi různými předplatnými. Další informace najdete [v tématu Konfigurace připojení virtuální sítě k virtuální síti](vpn-gateway-vnet-vnet-rm-ps.md). Ujistěte se, že přidáte "-EnableBGP $True" při vytváření připojení povolit Protokol BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarujte své proměnné

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření testu VNetu2 v nové skupině prostředků

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Vytvoření brány VPN pro TestVNet2 s parametry Protokolu BGP

Požádejte o přidělení veřejné IP adresy k bráně, kterou vytvoříte pro virtuální síť, a definujte požadované konfigurace podsítě a IP.

```powershell
$gwpip2    = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Vytvořte bránu VPN s číslem AS. Musíte přepsat výchozí ASN na vašich branách Azure VPN. Asn pro připojené virtuální sítě se musí lišit, aby bylo možné povolit protokol BGP a tranzitní směrování.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN
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

Po dokončení těchto kroků je připojení navázáno po několika minutách. Relace partnerského vztahu Protokolu BGP je po dokončení připojení virtuální sítě k virtuální síti nahoru.

Pokud jste dokončili všechny tři části tohoto cvičení, vytvořili jste následující topologii sítě:

![BGP pro virtuální síť k virtuální síti](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Další kroky

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
