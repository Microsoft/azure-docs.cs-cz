---
title: 'Nakonfigurovat protokol BGP na branách Azure VPN: Správce prostředků: prostředí PowerShell | Microsoft Docs'
description: Tento článek vás provede procesem konfigurace protokolu BGP s Azure VPN Gateway pomocí Azure Resource Manageru a prostředí PowerShell.
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: ''
tags: azure-resource-manager
ms.assetid: 905b11a7-1333-482c-820b-0fd0f44238e5
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/12/2017
ms.author: yushwang
ms.openlocfilehash: fc9337188fd439082c4aa34f0cbebe3eb2da5d99
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-powershell"></a>Postup konfigurace protokolu BGP na Azure VPN Gateway pomocí prostředí PowerShell
Tento článek vás provede kroky k povolení protokolu BGP na připojení Site-to-Site (S2S) VPN mezi různými místy a připojení VNet-to-VNet pomocí modelu nasazení Resource Manager a prostředí PowerShell.

## <a name="about-bgp"></a>Informace o protokolu BGP
BGP je standardní směrovací protokol, na internetu běžně používaný k výměně informací o směrování a dostupnosti mezi dvěma nebo více sítěmi. Protokol BGP umožňuje Azure VPN Gateway a vaše místní zařízení VPN, nazývají partnerské uzly protokolu BGP nebo Sousedé BGP, výměnu "tras" informujících obě brány o dostupnosti a dosažitelnosti předpon, které procházejí těmito bránami nebo trasami související se situací. Protokol BGP také umožňuje směrování přenosu mezi více sítěmi pomocí šíření tras, které brána s protokolem BGP zjistí od jednoho partnerského uzlu protokolu BGP, do všech dalších partnerských uzlů protokolu BGP.

V tématu [přehled protokolu BGP službou Azure VPN Gateways](vpn-gateway-bgp-overview.md) pro další zabývat výhody protokolu BGP a pochopit technickými požadavky a důležité informace o použití protokolu BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Začínáme s protokolem BGP na branách Azure VPN

Tento článek vás provede kroky, jak provést následující úlohy:

* [Část 1 - povolit protokol BGP na bráně Azure VPN](#enablebgp)
* [Část 2 – navázání připojení mezi různými místy pomocí protokolu BGP](#crossprembgp)
* [Část 3 – připojení VNet-to-VNet s protokolem BGP](#v2vbgp)

Jednotlivých součástí pokynů tvoří základní stavební blok pro povolení protokolu BGP v připojení k síti. Pokud dokončíte všechny tři části, jak je znázorněno v následujícím diagramu sestavení topologie:

![Topologii BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Můžete kombinovat částí společně k vytvoření složitějších, vícenásobného předávání přenosu síti, která vyhovuje vašim potřebám.

## <a name ="enablebgp"></a>Část 1: Konfigurace protokolu BGP na bráně Azure VPN
Postup konfigurace nastavení parametrů protokolu BGP brány Azure VPN, jak je znázorněno v následujícím diagramu:

![Protokol BGP brány](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Než začnete
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Nainstalujte rutiny Powershellu pro Azure Resource Manager. Další informace o instalaci rutin PowerShellu najdete v tématu [Instalace a konfigurace Azure PowerShellu](/powershell/azure/overview). 

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace VNet1
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných
Pro toto cvičení začneme deklarací proměnných. V následujícím příkladu jsou proměnné deklarovány s použitím hodnot pro toto cvičení. Při konfiguraci pro ostrý provoz nezapomeňte nahradit hodnoty vlastními. Tyto proměnné můžete použít, pokud procházíte kroky, abyste se seznámili s tímto typem konfigurace. Upravte proměnné a pak je zkopírujte a vložte do konzoly prostředí PowerShell.

```powershell
$Sub1 = "Replace_With_Your_Subcription_Name"
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

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Připojení k vašemu předplatnému a vytvořte novou skupinu prostředků
Pokud chcete používat rutiny Resource Manageru, ujistěte se, že jste přešli do režimu prostředí PowerShell. Další informace najdete v tématu [Použití prostředí Windows PowerShell s Resource Managerem](../powershell-azure-resource-manager.md).

Otevřete konzolu prostředí PowerShell a připojte se ke svému účtu. Připojení vám usnadní následující ukázka:

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName $Sub1
New-AzureRmResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Vytvoření virtuální sítě TestVNet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě, jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem Backend. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2 – Vytvoření brány VPN pro virtuální síť TestVNet1 s parametry protokolu BGP
#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Vytvoření konfigurací IP adresy a podsítě
Vyžádejte si veřejnou IP adresu, která bude přidělena bráně, kterou vytvoříte pro příslušnou virtuální síť. Budete také definovat vyžaduje podsíť a konfigurace protokolu IP.

```powershell
$gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Vytvoření brány VPN s číslo AS
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Protokol BGP vyžaduje brány VPN založené na směrování a také parametr přidání - Asn, chcete-li nastavit ASN (čísla AS) pro virtuální síť TestVNet1. Pokud parametr číslo ASN nenastavíte, bude přiřazeno číslo ASN 65515. Vytvoření brány může nějakou dobu trvat (30 minut nebo déle).

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Získat adresu IP adresa partnera BGP Azure
Po vytvoření brány, budete muset získat adresu IP adresa partnera BGP na bráně Azure VPN. Tato adresa je potřeba ke konfiguraci Azure VPN Gateway jako partnerské zařízení protokolu BGP pro vaše místní zařízení VPN.

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet1gw.BgpSettingsText
```

Poslední příkaz zobrazí odpovídající konfigurace protokolu BGP na bráně VPN Azure; například:

```powershell
$vnet1gw.BgpSettingsText
{
    "Asn": 65010,
    "BgpPeeringAddress": "10.12.255.30",
    "PeerWeight": 0
}
```

Po vytvoření brány, můžete tuto bránu k navázání připojení mezi různými místy nebo připojení VNet-to-VNet s protokolem BGP. V následujících částech provede kroky k dokončení výkonu.

## <a name ="crossprembbgp"></a>Část 2 – navázání připojení mezi různými místy pomocí protokolu BGP

Navázat připojení mezi různými místy, musíte vytvořit bránu místní sítě představují vaše místní zařízení VPN a připojení pro připojení brány sítě VPN s bránu místní sítě. Když jsou články, které vás provedou postupem, tento článek obsahuje další vlastnosti, které jsou zapotřebí zadat parametry konfigurace protokolu BGP.

![Protokol BGP pro více míst](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Než budete pokračovat, ujistěte se, když jste dokončili [část 1](#enablebgp) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Toto cvičení i nadále sestavení konfigurace znázorněné na obrázku. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestBGPRG5"
$Location5 = "East US 2"
$LNGName5 = "Site5"
$LNGPrefix50 = "10.52.255.254/32"
$LNGIP5 = "Your_VPN_Device_IP"
$LNGASN5 = 65050
$BGPPeerIP5 = "10.52.255.254"
```

Několik věcí, které si uvědomit o parametrech brány místní sítě:

* Brána místní sítě může být ve stejné nebo jiné umístění a skupině prostředků jako brána sítě VPN. Tento příklad ukazuje je v různých skupinách prostředků v různých umístěních.
* Minimální předponu, které je třeba deklarovat pro bránu místní sítě je adresa hostitele vaší IP adresy partnera BGP v zařízení VPN. V takovém případě je /32 předponu "10.52.255.254/32".
* Připomínáme je nutné použít různá čísla ASN protokolu BGP mezi vaší místní sítí a virtuální sítí VNet Azure. Pokud se shodují, budete muset změnit číslo ASN vaší virtuální sítě, pokud vaše místní zařízení VPN už používá číslo Autonomního rovnocenných počítačů sousední ostatní směrovače BGP.

Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvoření brány místní sítě pro Site5

Je nutné vytvořit skupinu prostředků, pokud není vytvořená, před vytvořením brány místní sítě. Všimněte si dva další parametry pro bránu místní sítě: číslo Asn a BgpPeerAddress.

```powershell
New-AzureRmResourceGroup -Name $RG5 -Location $Location5

New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2 – připojení brány virtuální sítě a brány místní sítě

#### <a name="1-get-the-two-gateways"></a>1. Získat dvě brány

```powershell
$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvoření virtuální sítě TestVNet1 Site5 připojení

V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k Site5. Je nutné zadat "-EnableBGP $True" Povolit protokol BGP pro toto připojení. Jak už jsme probírali výše, je možné, že připojení protokolu BGP i bez protokolu BGP pro stejné Azure VPN Gateway. Pokud je protokol BGP povolený ve vlastnosti připojení, neumožní Azure pro toto připojení protokolu BGP, i když BGP parametry jsou již nakonfigurované na obě brány.

```powershell
New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

Následující příklad obsahuje parametry, které zadáte do protokolu BGP konfigurační oddíl na vaše místní zařízení VPN pro tento postup:

```

- Site5 ASN            : 65050
- Site5 BGP IP         : 10.52.255.254
- Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
- Azure VNet ASN       : 65010
- Azure VNet BGP IP    : 10.12.255.30
- Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
- eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Připojení po několika minutách a relaci partnerského vztahu protokolu BGP spustí jednou IPsec připojení.

## <a name ="v2vbgp"></a>Část 3 – připojení VNet-to-VNet s protokolem BGP

Tato část přidá připojení VNet-to-VNet s protokolem BGP, jak je znázorněno v následujícím diagramu:

![Protokol BGP pro síť VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Podle následujících pokynů pokračovat z předchozích kroků. Je třeba provést [části I](#enablebgp) vytvořit a nakonfigurovat virtuální síť TestVNet1 a bránu VPN s protokolem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1 – Vytvoření TestVNet2 a brány VPN

Je důležité se ujistit, že adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů vaší virtuální sítě.

V tomto příkladu patří virtuální sítě ve stejném předplatném. Můžete nastavit připojení VNet-to-VNet mezi různých předplatných. Další informace najdete v tématu [konfigurace připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md). Zajistěte, aby přidáte "-EnableBgp $True" při vytváření připojení k povolení protokolu BGP.

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření TestVNet2 do nové skupiny prostředků

```powershell
New-AzureRmResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. Vytvořit bránu sítě VPN pro TestVNet2 s parametry protokolu BGP

Požádat o veřejnou IP adresu, která bude přidělena pro bránu vytvoříte pro virtuální sítě a definujte vyžaduje podsíť a konfigurace protokolu IP.

```powershell
$gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2
```

Vytvoření brány VPN s číslo AS. Na Azure VPN Gateway je nutné přepsat výchozí číslo ASN. Čísla ASN pro připojené virtuální sítě se musí lišit povolit protokol BGP a směrování přenosu.

```powershell
New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN
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

Po dokončení těchto kroků, připojení po několika minutách. Relaci partnerského vztahu protokolu BGP je až po dokončení připojení VNet-to-VNet.

Pokud jste dokončili všechny tři části tohoto cvičení, jste vytvořili následující topologie sítě:

![Protokol BGP pro síť VNet-to-VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Další postup

Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
