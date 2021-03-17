---
title: Konfigurace zařízení sítě VPN partnera pro připojení ke službě Azure VPN Gateway
description: Tento článek poskytuje přehled konfigurací partnerských zařízení sítě VPN pro připojení ke službě Azure VPN Gateway.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 484dad65310efaa60e8744b2f122b5e44ae13565
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880148"
---
# <a name="overview-of-partner-vpn-device-configurations"></a>Přehled konfigurací partnerských zařízení sítě VPN
Tento článek poskytuje přehled konfigurace místních zařízení VPN pro připojení ke službě Azure VPN Gateway. Ukázková instalace Azure Virtual Network a brány VPN se používá k zobrazení způsobu připojení k různým konfiguracím místních zařízení VPN pomocí stejných parametrů.



## <a name="device-requirements"></a>Požadavky na zařízení
Brány VPN Azure používají standardní sady protokolů IPsec/IKE pro tunely VPN typu Site-to-Site (S2S). Seznam parametrů protokolu IPsec/IKE a šifrovacích algoritmů pro brány Azure VPN najdete v tématu [informace o zařízeních VPN](vpn-gateway-about-vpn-devices.md). Můžete také zadat přesné algoritmy a síly klíčů pro konkrétní připojení, jak je popsáno v [tématu o kryptografických požadavcích](vpn-gateway-about-compliance-crypto.md).

## <a name="single-vpn-tunnel"></a><a name ="singletunnel"></a>Jedno tunelové připojení VPN
První konfigurace v ukázce se skládá z jednoho tunelu VPN S2S mezi službou Azure VPN Gateway a místním zařízením VPN. Volitelně můžete nakonfigurovat [Border Gateway Protocol (BGP) napříč tunelem sítě VPN](#bgp).

![Diagram jednoho tunelového propojení S2S VPN](./media/vpn-gateway-3rdparty-device-config-overview/singletunnel.png)

Podrobné pokyny k nastavení jediného tunelu VPN najdete v tématu [Konfigurace připojení typu Site-to-site](./tutorial-site-to-site-portal.md). V následujících částech jsou uvedeny parametry připojení pro ukázkovou konfiguraci a zadání skriptu prostředí PowerShell, který vám může pomáhat začít.

### <a name="connection-parameters"></a>Parametry připojení
V této části jsou uvedeny parametry pro příklady, které jsou popsány v předchozích částech.

| **Parametr**                | **Hodnota**                    |
| ---                          | ---                          |
| Předpony adres virtuální sítě        | 10.11.0.0/16<br>10.12.0.0/16 |
| IP adresa brány VPN Azure         | Azure VPN Gateway IP         |
| Předpony místních adres | 10.51.0.0/16<br>10.52.0.0/16 |
| Místní IP adresa zařízení VPN    | Místní IP adresa zařízení VPN    |
| * ASN protokolu BGP virtuální sítě                | 65010                        |
| * IP adresa partnerského uzlu protokolu BGP Azure           | 10.12.255.30                 |
| * Místní ASN BGP         | 65050                        |
| * Místní IP adresa partnerského uzlu protokolu BGP     | 10.52.255.254                |

\* Volitelný parametr jenom pro BGP.

### <a name="sample-powershell-script"></a>Ukázkový skript PowerShellu
V této části najdete ukázkový skript, který vám umožní začít. Podrobné pokyny najdete v tématu [vytvoření připojení S2S VPN pomocí PowerShellu](vpn-gateway-create-site-to-site-rm-powershell.md).

```powershell
# Declare your variables

$Sub1          = "Replace_With_Your_Subscription_Name"
$RG1           = "TestRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$VNet1ASN      = 65010
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GWIPName1     = "VNet1GWIP"
$GWIPconfName1 = "gwipconf1"
$Connection15  = "VNet1toSite5"
$LNGName5      = "Site5"
$LNGPrefix50   = "10.52.255.254/32"
$LNGPrefix51   = "10.51.0.0/16"
$LNGPrefix52   = "10.52.0.0/16"
$LNGIP5        = "Your_VPN_Device_IP"
$LNGASN5       = 65050
$BGPPeerIP5    = "10.52.255.254"

# Connect to your subscription and create a new resource group

Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1

# Create virtual network

$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1 $besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

# Create VPN gateway

$gwpip1    = New-AzPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1     = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1   = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gwipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1 -Asn $VNet1ASN

# Create local network gateway

New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix51,$LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

# Create the S2S VPN connection

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False
```

### <a name="optional-use-custom-ipsecike-policy-with-usepolicybasedtrafficselectors"></a><a name ="policybased"></a>Volitelné Použití vlastních zásad IPsec/IKE s UsePolicyBasedTrafficSelectors
Pokud vaše zařízení VPN nepodporují selektory přenosu dat, jako jsou například konfigurace založené na trasách nebo VTI, vytvořte vlastní zásadu IPsec/IKE s možností [UsePolicyBasedTrafficSelectors](vpn-gateway-connect-multiple-policybased-rm-ps.md) .

> [!IMPORTANT]
> Pokud chcete povolit možnost **UsePolicyBasedTrafficSelectors** na připojení, musíte vytvořit zásadu IPSec/IKE.


Vzorový skript vytvoří zásadu IPsec/IKE s následujícími algoritmy a parametry:
* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA1, PFS24, životnost SA 7 200 sekund a 20 480 000 KB (20 GB)

Skript použije zásady IPsec/IKE a povolí možnost **UsePolicyBasedTrafficSelectors** na připojení.

```powershell
$ipsecpolicy5 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA1 -PfsGroup PFS24 -SALifeTimeSeconds 7200 -SADataSizeKilobytes 20480000

$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng5gw  = Get-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $False -IpsecPolicies $ipsecpolicy5 -UsePolicyBasedTrafficSelectors $True
```

### <a name="optional-use-bgp-on-s2s-vpn-connection"></a><a name ="bgp"></a>Volitelné Použít protokol BGP u připojení S2S VPN
Když vytvoříte připojení S2S VPN, můžete volitelně použít protokol [BGP pro bránu VPN](vpn-gateway-bgp-resource-manager-ps.md). Tento přístup má dva rozdíly:

* Předpony místních adres můžou být jedinou adresou hostitele. Místní IP adresa partnerského uzlu BGP je uvedená níže:

    ```powershell
    New-AzLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5
    ```

* Když vytvoříte připojení, musíte nastavit možnost **-EnableBGP** na $true:

    ```powershell
    New-AzVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True
    ```

## <a name="next-steps"></a>Další kroky
Podrobné pokyny k nastavení bran VPN typu aktivní-aktivní najdete v tématu [Konfigurace bran VPN typu aktivní-aktivní pro připojení mezi různými místy a připojení typu VNet-to-VNet](vpn-gateway-activeactive-rm-powershell.md).