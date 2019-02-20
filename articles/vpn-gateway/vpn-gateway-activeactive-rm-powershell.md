---
title: 'Konfigurovat připojení S2S VPN typu aktivní aktivní pro brány VPN Gateway: Azure Resource Manageru: Prostředí PowerShell | Dokumentace Microsoftu'
description: Tento článek vás provede konfigurace připojení typu aktivní aktivní s bránami Azure VPN Gateway pomocí Azure Resource Manageru a Powershellu.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 07/24/2018
ms.author: yushwang, cherylmc
ms.openlocfilehash: d1c5ea0ae60894e810d2f4eb0edff2126171a220
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56417104"
---
# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways"></a>Konfigurovat připojení S2S VPN typu aktivní aktivní s bránami Azure VPN Gateway

Tento článek vás provede kroky k vytvoření typu aktivní aktivní mezi různými místy a připojení VNet-to-VNet pomocí modelu nasazení Resource Manageru a Powershellu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="about-highly-available-cross-premises-connections"></a>Informace o připojení s vysokou dostupností mezi různými místy
Abyste dosáhli vysoké dostupnosti pro připojení různých míst a připojení VNet-to-VNet, by měl nasadit více bran VPN a vytvořit více paralelních připojení mezi vaší sítí a Azure. Zobrazit [vysoce dostupná mezi různými místy a VNet-to-VNet připojení](vpn-gateway-highlyavailable.md) přehled topologie a možnosti připojení.

Tento článek obsahuje pokyny k nastavení připojení k síti VPN typu aktivní aktivní mezi různými místy a aktivní aktivní připojení mezi dvěma virtuálními sítěmi.

* [Část 1 – Vytvoření a konfigurace Azure VPN gateway v režimu aktivní aktivní](#aagateway)
* [Část 2 – Vytvoření připojení typu aktivní aktivní mezi různými místy](#aacrossprem)
* [Část 3 – vytvoření připojení typu aktivní aktivní připojení typu VNet-to-VNet](#aav2v)

Pokud už máte bránu VPN, můžete:
* [Aktualizovat z aktivní pohotovostní existující bráně VPN typu aktivní aktivní, nebo naopak](#aaupdate)

Zkombinujete dohromady a vytvářet složitější, vysoce dostupné síťové topologii, která bude vyhovovat vašim potřebám.

> [!IMPORTANT]
> Režim aktivní – aktivní používá pouze následující SKU: 
  * VpnGw1, VpnGw2, VpnGw3
  * HighPerformance (pro staré skladové položky starší verze)
> 
> 

## <a name ="aagateway"></a>Část 1 – Vytvoření a konfigurace brány VPN typu aktivní aktivní
Takto bude konfigurace Azure VPN gateway v režimu aktivní aktivní. Hlavní rozdíly mezi brány typu aktivní aktivní a aktivní pohotovostní:

* Je potřeba vytvořit dvě konfigurace IP brány s dvě veřejné IP adresy
* Je nutné nastavit příznak EnableActiveActiveFeature
* SKU brány musí být VpnGw1, VpnGw2, VpnGw3 nebo HighPerformance (starší verze SKU).

Další vlastnosti jsou stejné jako aktivní aktivní brány. 

### <a name="before-you-begin"></a>Před zahájením
* Ověřte, že máte předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si aktivovat [výhody pro předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
* Budete potřebovat nainstalovat nejnovější verzi rutin prostředí PowerShell pro Azure Resource Manager. Zobrazit [Přehled prostředí Azure PowerShell](/powershell/azure/overview) pro další informace o instalaci rutin prostředí PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Krok 1 – Vytvoření a konfigurace sítě VNet1
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
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-testvnet1"></a>3. Vytvoření virtuální sítě TestVNet1
Následující ukázka vytvoří virtuální síť s názvem TestVNet1 a tři podsítě: jednu s názvem GatewaySubnet, jednu s názvem FrontEnd a jednu s názvem BackEnd. Při nahrazování hodnot je důležité vždy přiřadit podsíti brány konkrétní název GatewaySubnet. Pokud použijete jiný název, vytvoření brány se nezdaří.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1
```

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Krok 2 – Vytvoření brány sítě VPN pro virtuální síť TestVNet1 v režimu aktivní aktivní
#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Vytvoření veřejné IP adresy a brány konfigurací protokolu IP
Požádat o dvě veřejné adresy IP, která bude přidělena pro bránu, kterou vytvoříte pro příslušnou virtuální síť. Budete také definovat podsítě a požadované konfigurace protokolu IP.

```powershell
$gw1pip1 = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$gw1pip2 = New-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

$vnet1 = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
$gw1ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2
```

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. Vytvoření brány VPN typu aktivní aktivní konfigurace
Vytvořte bránu virtuální sítě pro virtuální síť TestVNet1. Všimněte si, že existují dvě GatewayIpConfig položky a je nastavený příznak EnableActiveActiveFeature. Vytvoření brány může nějakou dobu trvat (45 minut nebo déle).

```powershell
New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN -EnableActiveActiveFeature -Debug
```

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. Získat veřejné IP adresy brány a IP adresa partnera BGP
Po vytvoření brány, musíte získat IP adresa partnera BGP ve službě Azure VPN Gateway. Tato adresa je potřeba nakonfigurovat jako partnerský uzel protokolu BGP pro vaše místní zařízení VPN Azure VPN Gateway.

```powershell
$gw1pip1 = Get-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
$gw1pip2 = Get-AzPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
```

Chcete-li zobrazit dvě veřejné IP adresy přidělené vaší brány VPN a jejich odpovídající IP adresu partnera BGP u každé instance brány slouží následující rutiny:

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

Pořadí veřejné IP adresy pro instance brány a odpovídající adresy partnerského vztahu protokolu BGP jsou stejné. V tomto příkladu bude brána virtuální počítač s veřejnou IP Adresou 40.112.190.5 používat 10.12.255.4 jako adresy partnerského vztahu protokolu BGP a brána s 138.91.156.129 používat 10.12.255.5. Tyto informace je potřeba při nastavování vašeho na místními zařízeními VPN připojení typu aktivní aktivní brány. Brána se zobrazí v diagramu níže se všechny adresy:

![brány typu aktivní aktivní](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Po vytvoření brány můžete tuto bránu můžete použít k vytvoření typu aktivní aktivní mezi různými místy nebo připojení VNet-to-VNet. V dalších částech provede kroky k dokončení cvičení.

## <a name ="aacrossprem"></a>Část 2 – Vytvoření připojení typu aktivní aktivní mezi různými místy
K navázání připojení mezi různými místy, budete muset vytvořit bránu místní sítě k reprezentaci vaše místní zařízení VPN a připojení k připojení Azure VPN gateway pomocí brány místní sítě. V tomto příkladu Azure VPN gateway je v režimu aktivní aktivní. V důsledku toho se obě instance brány Azure VPN i v případě, že existuje pouze jeden místní zařízení VPN (brány místní sítě) a jedno připojení prostředků, vytvořit tunel VPN s2s s místním zařízením.

Než budete pokračovat, ujistěte se prosím, že jste dokončili [část 1](#aagateway) tohoto cvičení.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Krok 1 – Vytvoření a konfigurace brány místní sítě
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných
V tomto cvičení bude sestavení konfigurace znázorněné v diagramu. Nezapomeňte nahradit hodnoty těmi, které chcete použít pro svou konfiguraci.

```powershell
$RG5 = "TestAARG5"
$Location5 = "West US"
$LNGName51 = "Site5_1"
$LNGPrefix51 = "10.52.255.253/32"
$LNGIP51 = "131.107.72.22"
$LNGASN5 = 65050
$BGPPeerIP51 = "10.52.255.253"
```

Pár věcí, které poznámka týkající se parametry brány místní sítě:

* Brána místní sítě může být ve stejném nebo jiném umístění a skupině prostředků jako brány sítě VPN. Tento příklad ukazuje, je v různých skupinách prostředků, ale ve stejném umístění Azure.
* Pokud existuje pouze jeden místní zařízení VPN, jak je uvedeno výše, aktivní aktivní připojení můžete pracovat s nebo bez protokolu BGP. Tento příklad používá protokol BGP pro připojení mezi různými místy.
* Pokud je protokol BGP povolen, předpony, které je potřeba deklarovat pro bránu místní sítě je adresa hostitele vaše IP adresa partnera BGP ve vašem zařízení VPN. V tomto případě jde /32 předponu "10.52.255.253/32".
* Připomínáme je nutné použít různá čísla ASN protokolu BGP mezi vaší místní sítí a virtuální sítě Azure. Pokud se shodují, musíte změnit ASN virtuální sítě, pokud vaše místní zařízení VPN již používá číslo ASN pro vytvoření partnerského vztahu s dalším sousedům protokolu BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. Vytvořte bránu místní sítě pro Site5
Než budete pokračovat, zkontrolujte, že jste stále připojeni k předplatnému 1. Vytvořte skupinu prostředků, pokud ještě není vytvořeno.

```powershell
New-AzResourceGroup -Name $RG5 -Location $Location5
New-AzLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51
```

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: připojení brány virtuální sítě a bránu místní sítě
#### <a name="1-get-the-two-gateways"></a>1. Získat dvě brány

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw1 = Get-AzLocalNetworkGateway  -Name $LNGName51 -ResourceGroupName $RG5
```

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Vytvoření virtuální sítě TestVNet1 k Site5 připojení
V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 do Site5_1 s "EnableBGP" nastavit na $True.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. Parametry sítě VPN a protokol BGP pro vaše místní zařízení VPN
V příkladu níže jsou uvedeny parametry, které zadáte do oddílu konfigurace protokolu BGP na vaše místní zařízení VPN pro toto cvičení:

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

Po několika minutách by měl navázat připojení a relace partnerského vztahu protokolu BGP se spustí, jakmile se naváže připojení IPsec. V tomto příkladu má nakonfigurovanou zatím pouze jeden zařízení VPN v místním, což vede k diagramu je znázorněno níže:

![active-active-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Krok 3: připojení dvou místních zařízení VPN k bráně VPN typu aktivní aktivní
Pokud máte dvě zařízení VPN ve stejné místní síti, můžete dosáhnout dvojitá redundance připojení Azure VPN gateway, která ve druhém zařízení VPN.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. Vytvoření druhé brány místní sítě pro Site5
IP adresa brány, předponu adresy a adresa partnerského vztahu protokolu BGP pro druhá brána místní sítě se nesmí překrývat s předchozí brána místní sítě pro stejnou místní síť.

```powershell
$LNGName52 = "Site5_2"
$LNGPrefix52 = "10.52.255.254/32"
$LNGIP52 = "131.107.72.23"
$BGPPeerIP52 = "10.52.255.254"
```

```powershell
New-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
```

#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. Připojení brány virtuální sítě a druhá brána místní sítě
Vytvoření připojení z virtuální sítě TestVNet1 k Site5_2 s "EnableBGP" nastavit na $True

```powershell
$lng5gw2 = Get-AzLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5
```

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
```

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. Parametry sítě VPN a protokolu BGP pro druhý místní zařízení VPN
Obdobně dole najdete parametry zadáte do druhé zařízení VPN:

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

Jakmile se naváže připojení (tunelů), budete mít dva redundantní zařízení VPN a tunely připojení vaší místní sítí a Azure:

![dual-redundancy-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)

## <a name ="aav2v"></a>Část 3 – vytvoření připojení typu aktivní aktivní připojení typu VNet-to-VNet
Tato část vytvoří připojení typu aktivní aktivní připojení typu VNet-to-VNet s protokolem BGP. 

Následující pokyny jsou pokračování kroků uvedených výše. Je třeba provést [část 1](#aagateway) vytvoření a konfigurace virtuální sítě TestVNet1 a bránu VPN s protokolem BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Krok 1: vytvoření TestVNet2 a brány VPN
Je důležité, abyste měli jistotu, že se adresní prostor IP adres nové virtuální sítě, TestVNet2, nepřekrývá s žádným z rozsahů virtuálních sítí.

V tomto příkladu patří virtuální sítě do stejného předplatného. Můžete nastavit připojení VNet-to-VNet mezi různých předplatných; najdete [konfigurace připojení typu VNet-to-VNet](vpn-gateway-vnet-vnet-rm-ps.md) další podrobnosti. Ujistěte se, že přidáte "-EnableBgp $True" při vytváření připojení se povolit protokol BGP.

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

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Vytvoření TestVNet2 v nové skupině prostředků

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2
```

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. Vytvoření brány VPN typu aktivní aktivní pro TestVNet2
Požádat o dvě veřejné adresy IP, která bude přidělena pro bránu, kterou vytvoříte pro příslušnou virtuální síť. Budete také definovat podsítě a požadované konfigurace protokolu IP.

```powershell
$gw2pip1 = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$gw2pip2 = New-AzPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

$vnet2 = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2 = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
$gw2ipconf2 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2
```

Vytvoření brány sítě VPN s číslo AS a příznak "EnableActiveActiveFeature". Všimněte si, že je nutné přepsat výchozí číslo ASN pro Azure VPN Gateway. Čísla ASN pro připojené virtuální sítě musí být odlišný povolit protokol BGP a směrování provozu.

```powershell
New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet2ASN -EnableActiveActiveFeature
```

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: připojení brány virtuální sítě TestVNet1 a TestVNet2
V tomto příkladu jsou obě brány ve stejném předplatném. Můžete tento krok dokončit ve stejné relaci prostředí PowerShell.

#### <a name="1-get-both-gateways"></a>1. Získejte obě brány
Ujistěte se, že jste přihlášeni a připojeni k předplatnému 1.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
```

#### <a name="2-create-both-connections"></a>2. Vytvoření obě připojení
V tomto kroku vytvoříte připojení z virtuální sítě TestVNet1 k TestVNet2 a připojení z TestVNet2 k virtuální síti TestVNet1.

```powershell
New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True
```

> [!IMPORTANT]
> Je nutné povolit protokol BGP pro obě připojení.
> 
> 

Po dokončení těchto kroků, naváže připojení v pár minut a protokolu BGP bude relaci partnerského vztahu až po dokončení připojení VNet-to-VNet s dvojitá redundance:

![active-active-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Aktualizovat existující bráně VPN

Tato část vám pomůže změnit existující bráně Azure VPN z aktivní pohotovostní režim aktivní – aktivní, nebo naopak.

### <a name="change-an-active-standby-gateway-to-an-active-active-gateway"></a>Změnit bránou aktivní pohotovostní do brány typu aktivní aktivní

Následující příklad převádí brány typu aktivní aktivní brány aktivní pohotovostní. Při změně aktivní pohotovostní brány typu aktivní aktivní, můžete vytvořit jinou veřejnou IP adresu a pak přidejte druhá konfigurace IP brány.

#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Nahraďte následující parametry použité příklady s nastavením vyžadují pro vaši vlastní konfiguraci a potom deklarujte tyto proměnné.

```powershell
$GWName = "TestVNetAA1GW"
$VNetName = "TestVNetAA1"
$RG = "TestVPNActiveActive01"
$GWIPName2 = "gwpip2"
$GWIPconf2 = "gw1ipconf2"
```

Po deklarování proměnné, můžete zkopírovat a vložit tohoto příkladu a konzola Powershellu.

```powershell
$vnet = Get-AzVirtualNetwork -Name $VNetName -ResourceGroupName $RG
$subnet = Get-AzVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$location = $gw.Location
```

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Vytvoření veřejné IP adresy a pak přidejte druhá konfigurace IP adresy brány

```powershell
$gwpip2 = New-AzPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
Add-AzVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2
```

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. Povolit režim aktivní – aktivní a aktualizovat bránu

V tomto kroku povolíte režim aktivní – aktivní a aktualizovat bránu. V tomto příkladu je brány VPN gateway aktuálně pomocí starší verze standardní SKU. Aktivní aktivní nepodporuje standardního SKU. Změna velikosti starší verze SKU, který se podporuje (v tomto případě HighPerformance), stačí určit podporované starší verze SKU, kterou chcete použít.

* Nelze změnit starší verze SKU na jednu z nové SKU pomocí tohoto kroku. Můžete pouze změnit velikost starší verze SKU na jiné podporované starší verze SKU. Například je nelze změnit skladovou Položku z Standard na VpnGw1 (i v případě, že pro aktivní aktivní je podporované VpnGw1) protože standardní verze je starší verze SKU a je aktuální skladová položka VpnGw1. Další informace o SKU pro změnu velikosti a migrace, najdete v části [skladové položky brány](vpn-gateway-about-vpngateways.md#gwsku).

* Pokud chcete změnit velikost aktuální skladová položka VpnGw1 například k VpnGw3, lze provést pomocí tohoto kroku, protože SKU jsou stejné řady SKU. Proto byste použili hodnotu: ```-GatewaySku VpnGw3```

Pokud používáte to ve vašem prostředí, pokud nepotřebujete změňte velikost brány, nebudete muset zadat - GatewaySku. Všimněte si, že v tomto kroku musíte nastavit brány objekt v prostředí PowerShell pro aktivaci skutečné aktualizace. Tato aktualizace může trvat 30 až 45 minut, i když nejsou Změna velikosti brány.

```powershell
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance
```

### <a name="change-an-active-active-gateway-to-an-active-standby-gateway"></a>Změnit brány typu aktivní aktivní na aktivní pohotovostní gateway
#### <a name="1-declare-your-variables"></a>1. Deklarace proměnných

Nahraďte následující parametry použité příklady s nastavením vyžadují pro vaši vlastní konfiguraci a potom deklarujte tyto proměnné.

```powershell
$GWName = "TestVNetAA1GW"
$RG = "TestVPNActiveActive01"
```

Po deklarování proměnné, získejte název konfigurace protokolu IP, kterou chcete odebrat.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
$ipconfname = $gw.IpConfigurations[1].Name
```

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. Odebrat konfiguraci IP adresy brány a zakázat režim aktivní – aktivní

V tomto příkladu můžete odebrat konfiguraci IP adresy brány a zakázat režim aktivní – aktivní. Všimněte si, že je nutné nastavit objektu brány v prostředí PowerShell pro aktivaci skutečné aktualizace.

```powershell
Remove-AzVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature
```

Tato aktualizace může trvat až 30 až 45 minut.

## <a name="next-steps"></a>Další postup
Po dokončení připojení můžete do virtuálních sítí přidávat virtuální počítače. Kroky jsou uvedeny v tématu [Vytvoření virtuálního počítače](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
